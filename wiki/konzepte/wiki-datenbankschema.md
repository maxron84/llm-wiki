---
date: 2026-08-11
type: konzept
tags: [konzept, schema, metrik]
status: active
---

# Wiki-Datenbankschema

**Zusammenfassung**: Der konkrete Tabellenentwurf für ein LLM-Wiki in SQL — Quellen, Seiten, Sektionen, typisierte Kanten, Behauptungen mit Provenienz, Protokoll mit Kostenspalten und Volltextindex. Portabel gehalten, damit der Wechsel von SQLite zu PostgreSQL wenig kostet.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

Notiert in SQLite-Syntax, ohne Dialekt-Exotik. (Quelle: raw/sqlwiki_lokalesmodell_architektur.md) Der Entwurf gehört zur [SQL-Wiki-Architektur](sql-wiki-architektur.md) in Variante B — die Datenbank ist Quelle der Wahrheit, der Markdown-Vault ist ihr [Rendering](markdown-als-rendering.md).

## Ebene 1 — Quellen (unveränderlich)

```sql
CREATE TABLE sources (
  id          INTEGER PRIMARY KEY,
  slug        TEXT NOT NULL UNIQUE,
  path        TEXT NOT NULL UNIQUE,        -- raw/… oder clippings/…
  title       TEXT NOT NULL,
  kind        TEXT NOT NULL CHECK (kind IN ('raw','clipping')),
  sha256      TEXT NOT NULL,               -- erkennt stille Änderungen
  added_at    TEXT NOT NULL,
  ingested_at TEXT                          -- NULL = noch nicht aufgenommen
);
```

Die Unveränderlichkeitsregel der Rohquellen aus der [drei-ebenen-architektur](drei-ebenen-architektur.md) wird hier **strukturell** durchgesetzt: Es existiert schlicht kein Werkzeug, das in `sources` schreibt — der Import läuft über ein separates Skript, nicht über die Modellschicht. Der `sha256` macht nachträgliche Änderungen an einer Quelle sichtbar, was der Dateisystem-Variante bisher fehlt.

## Ebene 2 — Wiki

```sql
CREATE TABLE pages (
  id         INTEGER PRIMARY KEY,
  slug       TEXT NOT NULL UNIQUE,
  title      TEXT NOT NULL,
  type       TEXT NOT NULL CHECK (type IN
             ('konzept','quelle','person','werkzeug',
              'vergleich','vorlage','anleitung','projekt')),
  summary    TEXT NOT NULL,                 -- die eine Zeile für den Index
  status     TEXT NOT NULL DEFAULT 'active'
             CHECK (status IN ('active','draft','archived')),
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL
);

CREATE TABLE sections (
  id         INTEGER PRIMARY KEY,
  page_id    INTEGER NOT NULL REFERENCES pages(id) ON DELETE CASCADE,
  ord        INTEGER NOT NULL,
  heading    TEXT NOT NULL,
  body       TEXT NOT NULL,
  tokens     INTEGER,                       -- beim Schreiben gemessen
  updated_at TEXT NOT NULL,
  UNIQUE (page_id, ord)
);

CREATE TABLE tags (
  id   INTEGER PRIMARY KEY,
  name TEXT NOT NULL UNIQUE
);

CREATE TABLE page_tags (
  page_id INTEGER NOT NULL REFERENCES pages(id) ON DELETE CASCADE,
  tag_id  INTEGER NOT NULL REFERENCES tags(id)  ON DELETE CASCADE,
  PRIMARY KEY (page_id, tag_id)
);
```

Der `type`-CHECK ersetzt eine komplette [Lint-Prüfung](lint-pruefung.md). Der Ordner ist im SQL-Betrieb keine eigene Information mehr — er wird beim Export aus `type` abgeleitet. Damit kann die Prüfung „`type` passt nicht zum Ordner" per Konstruktion nicht mehr fehlschlagen. Die `sections`-Tabelle setzt die Entscheidung aus [sektion-als-atom](sektion-als-atom.md) um; das [yaml-frontmatter](yaml-frontmatter.md) wird zu Spalten mit CHECK-Constraints.

## Kanten — typisiert von Anfang an

```sql
CREATE TABLE links (
  from_page INTEGER NOT NULL REFERENCES pages(id) ON DELETE CASCADE,
  to_page   INTEGER NOT NULL REFERENCES pages(id) ON DELETE RESTRICT,
  rel       TEXT NOT NULL DEFAULT 'related' CHECK (rel IN
            ('related','depends-on','supersedes','implements',
             'owned-by','contradicts')),
  note      TEXT,
  PRIMARY KEY (from_page, to_page, rel)
);
```

Das ist die `relationships.json` aus dem [v2-Muster](llm-wiki-v2.md) — nur als das, was sie ohnehin ist: eine Kantentabelle. Der Unterschied zur JSON-Datei ist nicht kosmetisch:

> „`ON DELETE RESTRICT` auf `to_page` bedeutet: Eine Seite, auf die verlinkt wird, kann nicht gelöscht werden. Tote Links entstehen nicht mehr — nicht ‚werden gefunden', sondern **entstehen nicht**." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Die Kante `contradicts` ist eine Ergänzung gegenüber v2: Widersprüche zwischen Seiten werden damit erfasste Daten statt eines Absatzes im Fließtext, den beim nächsten Umschreiben niemand mehr findet.

## Behauptungen — Provenienz als Pflichtfeld

```sql
CREATE TABLE claims (
  id              INTEGER PRIMARY KEY,
  page_id         INTEGER NOT NULL REFERENCES pages(id) ON DELETE CASCADE,
  section_id      INTEGER REFERENCES sections(id) ON DELETE SET NULL,
  subject         TEXT NOT NULL,            -- normalisiert, z.B. 'kv-cache-bedarf-24b'
  statement       TEXT NOT NULL,
  source_id       INTEGER REFERENCES sources(id),
  confidence      TEXT NOT NULL DEFAULT 'sourced' CHECK (confidence IN
                  ('sourced','derived','unverified')),
  last_verified   TEXT,
  against_version TEXT
);
```

Diese Tabelle ist die direkte Antwort auf das [kontaminierungsrisiko](kontaminierungsrisiko.md). Im Dateisystem ist die Zitierregel `(Quelle: dateiname.md)` eine **Konvention** — sie kann vergessen werden, und ein Lint-Lauf fand nachweislich 50 maschinell nicht auflösbare Quellenangaben. In der Datenbank ist `source_id` ein Fremdschlüssel: Er zeigt auf eine existierende Quelle oder ist `NULL`, und `NULL` ist per Definition `confidence = 'unverified'` — eine Zeile in einem Report, keine unsichtbare Lücke im Fließtext.

`last_verified` und `against_version` übernehmen die Frischheitsmetadaten aus dem v2-Muster, aber auf der richtigen Granularität: pro Behauptung, nicht pro Seite. Das `subject`-Feld ist zugleich der Schlüssel für die Widerspruchserkennung.

## Protokoll — mit Kostenspalten

```sql
CREATE TABLE log (
  id        INTEGER PRIMARY KEY,
  at        TEXT NOT NULL,
  op        TEXT NOT NULL CHECK (op IN ('ingest','query','lint','update')),
  subject   TEXT NOT NULL,
  detail    TEXT,
  model     TEXT,
  tokens_in INTEGER,
  tokens_out INTEGER,
  seconds   REAL,
  wh        REAL,        -- Energie, für lokalen Betrieb
  cost_usd  REAL         -- API-Kosten, für Cloud-Betrieb
);
```

Damit wird die Metrik [US$ pro Wiki-Seite](usd-pro-wiki-seite.md) von einer Schätzung zu einer Abfrage:

```sql
SELECT SUM(cost_usd) / (SELECT COUNT(*) FROM pages) AS usd_pro_seite FROM log;
```

Für lokalen Betrieb ist die Dollarspalte uninteressant — dort zählen `wh` und `seconds`. → [wh-pro-wiki-seite](wh-pro-wiki-seite.md)

Die `op`-Werte entsprechen exakt den vier Log-Präfixen des Dateisystem-Wikis (`ingest`, `query`, `lint`, `update`), und die Prüfung `LOG ORDER` entfällt, weil die Reihenfolge beim Export aus `ORDER BY at` entsteht statt aus Disziplin beim Voranstellen.

## Volltext

```sql
CREATE VIRTUAL TABLE sections_fts USING fts5(
  heading, body,
  content='sections', content_rowid='id',
  tokenize='unicode61 remove_diacritics 2'
);
```

Plus die drei üblichen Trigger für `INSERT`/`UPDATE`/`DELETE`, die den Index synchron halten. Hier liegt die ernsteste Schwäche der SQLite-Variante — FTS5 bringt keinen deutschen Stemmer mit. → [deutsche-volltextsuche](deutsche-volltextsuche.md)

## Portabilität

Der Entwurf ist bewusst auf einen späteren Wechsel zu [postgresql](../werkzeuge/postgresql.md) hin gebaut: `INTEGER PRIMARY KEY` statt `ROWID`-Tricks, Datumsangaben als ISO-8601-Text, keine dynamische Typisierung ausnutzen, kein SQLite-Exotisches. Dann ist die Migration ein Dump plus Anpassung der FTS-Schicht. (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Verwandte Seiten

- [sql-wiki-architektur](sql-wiki-architektur.md) — Der Rahmen und die Entscheidung für Variante B
- [sektion-als-atom](sektion-als-atom.md) — Warum `sections` die zentrale Tabelle ist
- [deutsche-volltextsuche](deutsche-volltextsuche.md) — Die offene Schwäche des FTS-Teils
- [werkzeugschicht](werkzeugschicht.md) — Wie das Modell auf das Schema zugreift
- [kontaminierungsrisiko](kontaminierungsrisiko.md) — Was `claims` adressiert
- [llm-wiki-v2](llm-wiki-v2.md) — Typisierte Beziehungen und Frischheitsmetadaten als Vorläufer
- [drei-ebenen-architektur](drei-ebenen-architektur.md) — Rohquellen → Wiki → Schema
- [yaml-frontmatter](yaml-frontmatter.md) — Was zu Spalten wird
- [lint-pruefung](lint-pruefung.md) — Welche Prüfungen das Schema übernimmt
- [wh-pro-wiki-seite](wh-pro-wiki-seite.md) — Was die Kostenspalten messbar machen
- [sqlite](../werkzeuge/sqlite.md) — Die Zielplattform des Entwurfs
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
