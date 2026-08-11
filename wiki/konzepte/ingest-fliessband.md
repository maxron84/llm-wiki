---
date: 2026-08-11
type: konzept
tags: [konzept, automatisierung, lokale-modelle, schema]
status: active
---

# Ingest-Fließband

**Zusammenfassung**: Der Ingest ist die Operation, bei der die Datenbank am wenigsten hilft — echte Bedeutungsarbeit über ein ganzes Quelldokument. Er lässt sich aber in sechs Schritte zerlegen, deren Zustand in der Datenbank liegt statt im Kontextfenster. Kein Schritt übersteigt 3k Token; aus einem unmöglichen großen Aufruf werden ~20 kleine.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Warum der Ingest der harte Fall bleibt

Lesen, verstehen, zerlegen, mit Bestehendem abgleichen, Widersprüche erkennen, vernetzen — das ist genau das, was ein 14B-Modell nicht in einem Zug kann. Der [Ingest-Workflow](ingest-workflow.md) ist im Dateibetrieb die Operation, die lokal zuerst scheitert. (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Die sechs Schritte

| Schritt | Ausführung | Kontextbedarf |
|---|---|---|
| 1. `split` | Code — Quelle in ~1.500-Token-Abschnitte | 0 |
| 2. `extract` | Modell — pro Abschnitt: Behauptungen als JSON, mit `subject` | ~2,5k je Abschnitt |
| 3. `route` | Code — `subject` → FTS + Graph → Kandidatenseiten | 0 |
| 4. `merge` | Modell — pro betroffener Sektion: alter Text + neue Behauptungen → neuer Text | ~2k je Sektion |
| 5. `link` | Modell — Kantenvorschläge aus der Kandidatenliste | ~1k |
| 6. `verify` | Code — Lint-Queries; Fehler → zurück zu Schritt 4 | 0 |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Drei der sechs Schritte laufen ohne Modell. Ein Clipping von 8.000 Wörtern wird zu etwa 20 kleinen Modellaufrufen statt einem unmöglichen großen. Bei ~10 s pro Aufruf sind das drei bis vier Minuten — langsam gegenüber einer Cloud-API, aber durchführbar, und das ist der relevante Unterschied.

## Der Routing-Schritt wird deterministisch

Schritt 3 ist der interessanteste, weil er im Dateibetrieb selbst eine Modellaufgabe ist: erst eine kompakte Schema-Zusammenfassung lesen, dann entscheiden, welche Seiten relevant sind. → [fortgeschrittene-architektur](fortgeschrittene-architektur.md)

In SQL wird er eine Query:

```sql
WITH RECURSIVE
seeds AS (
  SELECT s.page_id AS id
  FROM sections_fts f
  JOIN sections s ON s.id = f.rowid
  WHERE sections_fts MATCH :q
  ORDER BY bm25(f)
  LIMIT 8
),
nb(id, dist) AS (
  SELECT id, 0 FROM seeds
  UNION
  SELECT l.to_page, nb.dist + 1
  FROM links l JOIN nb ON l.from_page = nb.id
  WHERE nb.dist < 2
)
SELECT DISTINCT p.slug, p.title, p.summary
FROM nb JOIN pages p ON p.id = nb.id;
```

BM25-Volltreffer plus zwei Schritte Graph-Traversal über typisierte Kanten. Ergebnis: eine Kandidatenliste mit je einer Zeile Zusammenfassung — 30 Seiten ergeben ~600 Token.

> „Das Modell bekommt eine fertige Liste und muss nur noch auswählen, nicht suchen." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Was [jdocmunch](../werkzeuge/jdocmunch.md) (sektionsbasierter Zugriff) und [qmd](../werkzeuge/qmd.md) (BM25/Vektor/Hybrid) als externe Werkzeuge liefern, fällt hier als Eigenschaft des [Schemas](wiki-datenbankschema.md) an. Ein Vektorindex lässt sich später ergänzen — `sqlite-vec`, `pgvector`, natives `VECTOR` ab MariaDB 11.7 — aber er ist nicht der erste Schritt: BM25 über sauber geschnittene [Sektionen](sektion-als-atom.md) deckt einen großen Teil ab, und Embeddings lokal zu erzeugen kostet erneut GPU-Zeit.

## Die Ralph-Struktur

Das Fließband ist strukturell die [Ralph-Schleife](ralph-schleife.md): zustandslose Iterationen, frischer Kontext bei jedem Durchgang, Persistenz über einen externen Speicher statt über das Kontextfenster. → [statelessness](statelessness.md)

Der Unterschied: Der externe Speicher ist hier eine Datenbank mit Constraints statt ein Dateisystem ohne. Schritt 6 ist die Abbruchbedingung — die Schleife läuft, bis alle [Lint-Queries](lint-pruefung.md) leer zurückkommen. Damit hat die Schleife erstmals ein maschinell prüfbares Ende statt eines Budget- oder Iterationslimits.

## Was das Fließband nicht liefert

Die Tiefe-Auswahl (minimal/mittel/vollständig) und die Beurteilung, was an einer Quelle *wichtig* ist, bleiben Qualitätsfragen, bei denen ein 14B-Modell messbar schlechter urteilt. Es wird mehr Triviales extrahieren und weniger Verbindungen sehen.

> „Realistisch: `query` und `update` lokal, `ingest` neuer Quellen weiterhin bevorzugt mit einem starken Modell — aber gegen dasselbe Schema und dieselben Werkzeuge, sodass die Wahl des Modells eine Kostenentscheidung pro Operation wird statt einer Architekturentscheidung." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Das ist die eigentliche Pointe: Die Modellwahl wird granular. Statt „lokal oder Cloud?" für das ganze Wiki heißt die Frage „lokal oder Cloud für diesen einen Schritt?" → [wh-pro-wiki-seite](wh-pro-wiki-seite.md)

## Verwandte Seiten

- [ingest-workflow](ingest-workflow.md) — Der Ablauf im Dateibetrieb
- [ralph-schleife](ralph-schleife.md) — Das Muster, dem das Fließband folgt
- [werkzeugschicht](werkzeugschicht.md) — Die Werkzeuge, die die Schritte bedienen
- [wiki-datenbankschema](wiki-datenbankschema.md) — Die Tabellen hinter `route` und `verify`
- [sektion-als-atom](sektion-als-atom.md) — Die Einheit des `merge`-Schritts
- [sql-wiki-architektur](sql-wiki-architektur.md) — Stufe 4 des Migrationspfads
- [statelessness](statelessness.md) — Zustand außerhalb des Kontextfensters
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md) — Routing als Kostenhebel
- [wh-pro-wiki-seite](wh-pro-wiki-seite.md) — Modellwahl als Kostenentscheidung pro Operation
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
