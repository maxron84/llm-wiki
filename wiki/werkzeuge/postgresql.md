---
date: 2026-08-11
type: werkzeug
tags: [werkzeug, schema, setup]
status: active
---

# PostgreSQL

**Zusammenfassung**: Relationale Serverdatenbank mit deutscher Volltextsuche ab Werk (`to_tsvector('german', …)`, Snowball-Stemmer, Stoppwortliste). Für das LLM-Wiki der benannte Migrationspfad von SQLite — mit einem konkreten Auslöser statt eines diffusen „später vielleicht".
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Warum es überhaupt in Betracht kommt

Für ein einbenutziges lokales Wiki bringt ein Serverdienst zunächst nichts, was [SQLite](sqlite.md) nicht auch kann — mit Ausnahme eines Punkts:

> „PostgreSQL löst das mit `to_tsvector('german', …)` ab Werk — Snowball-Stemmer und deutsche Stoppwortliste sind eingebaut. Das ist der konkreteste technische Grund, PostgreSQL überhaupt in Betracht zu ziehen." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Für ein durchgehend deutschsprachiges Wiki mit vielen Komposita ist das kein Randfall, sondern der Punkt, an dem die [Volltextsuche](../konzepte/deutsche-volltextsuche.md) trägt oder nicht.

## Was PostgreSQL mitbringt

| Baustein | Nutzen für das Wiki |
|---|---|
| `to_tsvector('german', …)` | Flexion wird gestemmt, deutsche Stoppwörter fallen raus |
| `ts_rank` / `ts_rank_cd` | Ranking analog zu BM25 |
| GIN-Index auf `tsvector` | Schnelle Suche über Sektionen |
| `pgvector` | Vektorsuche, falls Embeddings dazukommen |
| Rekursive CTEs | Graph-Traversal wie in SQLite |
| Volle Nebenläufigkeit | Relevant erst beim Team-Wiki |
| Netzwerkzugriff | Relevant erst bei mehreren Geräten |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Die letzten beiden Zeilen sind Nebengewinne, keine Gründe. Der Grund ist die erste Zeile. → [datenbankwahl-wiki](../konzepte/datenbankwahl-wiki.md)

## Der Auslöser für den Wechsel

Nicht Größe, nicht Geschwindigkeit, nicht Nutzerzahl:

> „Sobald Suchtreffer erkennbar an Flexion und Komposita scheitern — und das ist bei einem rein deutschen Wiki eine Frage der Zeit, nicht des Ob —, ist der Wechsel begründet." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Solange BM25 über `unicode61` plus Trigram-Fallback ausreicht, bleibt es bei SQLite. Das ist eine bewusst hoch gelegte Schwelle: Sie verhindert, dass „für später gerüstet sein" zur vorzeitigen Serverinstallation führt.

## Was die Migration einfach hält

Das [Wiki-Datenbankschema](../konzepte/wiki-datenbankschema.md) ist bewusst portabel entworfen:

- `INTEGER PRIMARY KEY` statt `ROWID`-Tricks
- Datumsangaben als ISO-8601-Text
- Keine dynamische Typisierung ausnutzen
- Kein SQLite-Exotisches

Dann ist die Migration ein Dump plus Anpassung der FTS-Schicht — die Anwendungslogik der [Werkzeugschicht](../konzepte/werkzeugschicht.md) bleibt unverändert, weil das Modell ohnehin nie SQL sieht. Konkret zu ersetzen sind die `sections_fts`-Tabelle samt Triggern durch eine `tsvector`-Spalte mit GIN-Index und der `MATCH`-Ausdruck im Routing-Query durch `@@`.

## Der Preis

Serverbetrieb statt einer Datei: Dienst starten, Port, Rechtekonzept, Dump statt `cp` fürs Backup. Für ein persönliches Wiki auf einem Rechner ist das ein echter Aufschlag an Betriebsaufwand — und der Grund, warum die Empfehlung SQLite zuerst nennt und PostgreSQL als *benannten* Pfad, nicht als Ziel.

## Verwandte Seiten

- [sqlite](sqlite.md) — Der Einstieg, von dem aus migriert wird
- [deutsche-volltextsuche](../konzepte/deutsche-volltextsuche.md) — Das Problem, das PostgreSQL löst
- [datenbankwahl-wiki](../konzepte/datenbankwahl-wiki.md) — Vergleich mit SQLite, MariaDB, DuckDB
- [wiki-datenbankschema](../konzepte/wiki-datenbankschema.md) — Der portabel gehaltene Entwurf
- [werkzeugschicht](../konzepte/werkzeugschicht.md) — Warum die Migration die Modellschicht nicht berührt
- [sql-wiki-architektur](../konzepte/sql-wiki-architektur.md) — Der Rahmen
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
