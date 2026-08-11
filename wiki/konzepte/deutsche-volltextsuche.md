---
date: 2026-08-11
type: konzept
tags: [konzept, schema, lokale-modelle]
status: active
---

# Deutsche Volltextsuche im SQL-Wiki

**Zusammenfassung**: Die ernsteste Schwäche der SQLite-Variante. FTS5 bringt keinen deutschen Stemmer mit — Flexion und Komposita fallen durchs Raster. Drei Behelfe halten es eine Weile aus, aber der Punkt ist zugleich der konkreteste technische Grund, PostgreSQL als Migrationsziel zu benennen.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Das Problem

Der FTS5-Index im [Wiki-Datenbankschema](wiki-datenbankschema.md) verwendet:

```sql
tokenize='unicode61 remove_diacritics 2'
```

`unicode61` tokenisiert nur, `porter` stemmt englisch. Für ein durchgehend deutschsprachiges Wiki bedeutet das zwei konkrete Ausfälle: (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

- **Flexion**: „Kontextfenster" findet nicht „Kontextfenstern"
- **Komposita**: „Kontextfenster" findet nicht „Fenster"

Für ein Wiki, dessen Seiten durchweg deutsch geschrieben sind und dessen Themen zu Komposita neigen (Kontextfenster, Skalierungsgrenzen, Kontaminierungsrisiko, Modellunabhängigkeit), ist das kein Randfall. Der Ausfall trifft direkt den [Routing-Schritt](ingest-fliessband.md), der auf BM25 über Sektionen aufsetzt — wenn die Suche den Kandidaten nicht findet, sieht ihn auch das Modell nicht.

## Drei Behelfe

1. **Trigram-Tokenizer** (`tokenize='trigram'`) — findet Teilzeichenketten, löst das Kompositaproblem brauchbar, kostet Indexgröße und Präzision.

2. **Zweiter Index** — ein normalisiertes Schlüsselwortfeld pro Sektion, vom Modell beim Schreiben mitgeliefert. Das verschiebt Arbeit zurück ins Modell, aber in kleiner, klar umrissener Form: ein Feld beim `upsert_section`. → [werkzeugschicht](werkzeugschicht.md)

3. **Query-Erweiterung** — das Modell generiert 3–5 Suchvarianten statt einer. Billig (wenige hundert Token), aber nicht deterministisch.

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Der benannte Ausweg

PostgreSQL löst das mit `to_tsvector('german', …)` ab Werk — Snowball-Stemmer und deutsche Stoppwortliste sind eingebaut.

> „Das ist der konkreteste technische Grund, PostgreSQL überhaupt in Betracht zu ziehen." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Der Auslöser für die Migration ist damit klar benannt statt vage: Solange BM25 über `unicode61` plus Trigram-Fallback ausreicht, bleibt es bei [SQLite](../werkzeuge/sqlite.md). Sobald Suchtreffer erkennbar an Flexion und Komposita scheitern, ist der Wechsel zu [PostgreSQL](../werkzeuge/postgresql.md) begründet — und die Quelle hält das bei einem rein deutschen Wiki für eine Frage der Zeit, nicht des Ob.

Das Schema ist auf diesen Wechsel vorbereitet: `INTEGER PRIMARY KEY` statt `ROWID`-Tricks, Datumsangaben als ISO-8601-Text, keine dynamische Typisierung. Dann ist die Migration ein Dump plus Anpassung der FTS-Schicht — die Anwendungslogik bleibt.

## Wo Vektorsuche hilft und wo nicht

Ein Vektorindex (`sqlite-vec`, `pgvector`) würde das Kompositaproblem umgehen, weil Embeddings semantisch statt lexikalisch treffen. Er ist trotzdem nicht der erste Schritt: BM25 über sauber geschnittene [Sektionen](sektion-als-atom.md) deckt einen großen Teil ab, und Embeddings lokal zu erzeugen kostet erneut GPU-Zeit — dieselbe GPU-Zeit, die der ganze Umbau eigentlich einsparen soll. → [wh-pro-wiki-seite](wh-pro-wiki-seite.md)

Das externe Werkzeug [qmd](../werkzeuge/qmd.md) bietet BM25, Vektor und Hybrid bereits fertig an; im SQL-Betrieb wäre es die Alternative zum Selbstbau, mit dem Nachteil einer zweiten Datenhaltung.

## Verwandte Seiten

- [wiki-datenbankschema](wiki-datenbankschema.md) — Der FTS5-Teil des Schemas
- [sqlite](../werkzeuge/sqlite.md) — Wo die Schwäche liegt
- [postgresql](../werkzeuge/postgresql.md) — Wo sie gelöst ist
- [ingest-fliessband](ingest-fliessband.md) — Der Routing-Schritt, der auf BM25 aufsetzt
- [sektion-als-atom](sektion-als-atom.md) — Die Einheit, über die gesucht wird
- [sql-wiki-architektur](sql-wiki-architektur.md) — Stufe 2 des Migrationspfads prüft genau das
- [qmd](../werkzeuge/qmd.md) — Externe Suchmaschine mit Hybrid-Modus
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
