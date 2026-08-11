---
date: 2026-08-11
type: werkzeug
tags: [werkzeug, schema, setup]
status: active
---

# SQLite

**Zusammenfassung**: Eingebettete SQL-Datenbank ohne Serverdienst — eine Datei, kein Port, kein Rechtekonzept. Für ein einbenutziges lokales LLM-Wiki der empfohlene Einstieg; die Volltextsuche FTS5 mit BM25 ist schnell, hat aber keinen deutschen Stemmer.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Was es ist

Eine relationale Datenbank, die als Bibliothek in die Anwendung eingebettet wird statt als Dienst zu laufen. Die gesamte Datenbank ist eine Datei. Kein Server, kein Port, kein Benutzerkonzept, kein Installationsaufwand über die Sprachbibliothek hinaus (in Python ist `sqlite3` Teil der Standardbibliothek).

## Warum es für ein LLM-Wiki passt

> „Ein einbenutziges, lokales Wiki ist der Fall, für den SQLite gebaut wurde." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

| Eigenschaft | Für das Wiki bedeutet das |
|---|---|
| Eine Datei | `wiki.db` — kopieren ist ein vollständiges Backup |
| Kein Dienst | Nichts zu starten, nichts, was abstürzen kann, während man schreibt |
| FTS5 mit BM25 | Antwortet bei einigen tausend Sektionen in unter einer Millisekunde |
| Rekursive CTEs | Graph-Traversal über die Kantentabelle direkt in SQL |
| Ein Schreiber (WAL) | Keine Einschränkung, wenn genau ein Agent schreibt |

Die Einbenutzer-Schreibbeschränkung, die SQLite üblicherweise angelastet wird, ist im [SQL-Wiki-Betrieb](../konzepte/sql-wiki-architektur.md) keine: Es schreibt genau ein Agent. → [datenbankwahl-wiki](../konzepte/datenbankwahl-wiki.md)

## Die relevanten Bausteine

**FTS5** — Volltextindex als virtuelle Tabelle, mit BM25-Ranking eingebaut:

```sql
CREATE VIRTUAL TABLE sections_fts USING fts5(
  heading, body,
  content='sections', content_rowid='id',
  tokenize='unicode61 remove_diacritics 2'
);
```

Plus drei Trigger für `INSERT`/`UPDATE`/`DELETE`, die den Index synchron halten. → [wiki-datenbankschema](../konzepte/wiki-datenbankschema.md)

**Rekursive CTEs** — `WITH RECURSIVE` erlaubt Graph-Traversal über typisierte Kanten, ohne dass eine Graphdatenbank nötig wäre. → [ingest-fliessband](../konzepte/ingest-fliessband.md)

**Foreign Keys** — `ON DELETE RESTRICT` macht tote Links strukturell unmöglich. Achtung: In SQLite müssen Fremdschlüssel pro Verbindung aktiviert werden (`PRAGMA foreign_keys = ON`), sonst sind die Constraints wirkungslos. Genau daran hängt der halbe Gewinn des Umbaus.

**`sqlite-vec`** — Erweiterung für Vektorsuche, falls Embeddings später dazukommen sollen. Nicht der erste Schritt.

## Die Schwäche

FTS5 bringt keinen deutschen Stemmer mit. `unicode61` tokenisiert nur, `porter` stemmt englisch. Für ein durchgehend deutschsprachiges Wiki heißt das: „Kontextfenster" findet nicht „Kontextfenstern" und nicht „Fenster".

Das ist der einzige benannte Grund, später auf [PostgreSQL](postgresql.md) zu wechseln. Ausführlich: [deutsche-volltextsuche](../konzepte/deutsche-volltextsuche.md)

## Zwei Betriebshinweise

**Git.** Eine SQLite-Datei ist ein Binärblob — im Repo erzeugt sie unlesbare Diffs und wächst bei jedem Commit um ihre volle Größe. Praktikabel: `wiki.db` in `.gitignore`, der [exportierte Markdown-Vault](../konzepte/markdown-als-rendering.md) ist das, was versioniert wird, plus optional ein `.sql`-Dump als Textdatei.

**Cloud-Synchronisierung.** SQLite über Dropbox, Nextcloud oder ähnliche Ordner zu synchronisieren ist ein bekannter Weg zu beschädigten Datenbanken — die Sperrmechanik funktioniert über solche Dateisysteme nicht zuverlässig. Bei mehreren Geräten synchronisiert der exportierte Vault, während die Datenbank auf einem Rechner bleibt.

## Verwandte Seiten

- [datenbankwahl-wiki](../konzepte/datenbankwahl-wiki.md) — Vergleich mit MariaDB, PostgreSQL, DuckDB
- [wiki-datenbankschema](../konzepte/wiki-datenbankschema.md) — Der Schemaentwurf in SQLite-Syntax
- [sql-wiki-architektur](../konzepte/sql-wiki-architektur.md) — Wofür die Datenbank eingesetzt wird
- [deutsche-volltextsuche](../konzepte/deutsche-volltextsuche.md) — Die Schwäche von FTS5 im Detail
- [postgresql](postgresql.md) — Der benannte Migrationspfad
- [markdown-als-rendering](../konzepte/markdown-als-rendering.md) — Was stattdessen ins Git geht
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
