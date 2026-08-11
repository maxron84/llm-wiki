---
date: 2026-08-11
type: konzept
tags: [konzept, schema, lokale-modelle]
status: active
---

# Die Sektion als Atom

**Zusammenfassung**: Die folgenreichste Schemaentscheidung im SQL-Wiki ist nicht die Tabellenwahl, sondern die Granularität der kleinsten schreibbaren Einheit. Der H2-Abschnitt mit 200–600 Token ist groß genug für Bedeutung und klein genug, dass ein 14B-Modell ihn fehlerfrei neu schreibt — und er ist zugleich die natürliche Einheit für Retrieval und Frischheit.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Drei Kandidaten

| Granularität | Umfang | Urteil |
|---|---|---|
| Seite | 2.000–4.000 Token | Ein `UPDATE` ersetzt den ganzen Text — das ist [Variante A](sql-wiki-architektur.md) mit anderen Mitteln, der Fehler bleibt |
| Absatz | 30–100 Token | Zu fein. Bedeutungszusammenhänge zerfallen, die Zahl der Objekte explodiert, das Modell verliert den Überblick |
| **Sektion (H2-Abschnitt)** | **200–600 Token** | **Groß genug, dass ein Abschnitt für sich Sinn ergibt; klein genug, dass ein 14B-Modell ihn fehlerfrei neu schreibt** |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Die Entscheidung folgt direkt aus der Leitfrage von [Engpass B](engpass-groesse-vs-session.md): Wie macht man jede einzelne Schreiboperation klein genug, dass ein schwaches Modell sie zuverlässig schafft? Eine Seite komplett neu auszugeben, nur um einen Absatz zu ändern, ist genau die Operation, bei der die Frontmatter verlorengeht oder die Ausgabe mitten im Satz endet.

## Drei Aufgaben, eine Einheit

Die Sektion ist nicht nur die richtige Schreibeinheit — sie ist zufällig auch für zwei weitere Zwecke die richtige.

**Schreiben.** Sektion lesen (0,4k) + Sektion neu schreiben (0,4k) = 1k Token statt 6k für die ganze Seite.

**Retrieval.** Auf eine Frage antwortet selten eine ganze Seite, aber fast immer ein Abschnitt. Die BM25-Volltextsuche läuft deshalb über Sektionen, nicht über Seiten — das ist derselbe Ansatz, den [jdocmunch](../werkzeuge/jdocmunch.md) als externes Werkzeug liefert (~95 % Token-Reduktion), hier aber als Eigenschaft des Schemas.

**Frischheit.** Der Abschnitt „VRAM-Bedarf" veraltet, während „Die Grundidee" auf derselben Seite gültig bleibt.

> „Frischheitsmetadaten auf Seitenebene sind zu grob, um nützlich zu sein." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Das ist eine Präzisierung gegenüber [llm-wiki-v2](llm-wiki-v2.md), das Lifecycle- und Trust-Score-Metadaten pro Seite führt. Im SQL-Schema wandern `last_verified` und `against_version` auf die Behauptung, nicht einmal nur auf die Sektion. → [wiki-datenbankschema](wiki-datenbankschema.md)

## Die offene empirische Frage

Ob sich ein Wiki gut in 200–600-Token-Blöcke schneiden lässt, ohne dass Argumentationsbögen zerfallen, ist nicht theoretisch entscheidbar. Die Quelle benennt das ausdrücklich als offenen Punkt und verweist auf Stufe 2 des [Migrationspfads](sql-wiki-architektur.md): Der reine Lesebetrieb beantwortet die Frage, bevor sie teuer wird — dort zeigt sich, ob Antworten aus einzelnen Sektionen tragen oder ob ständig Nachbarabschnitte fehlen. (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Verwandte Seiten

- [sql-wiki-architektur](sql-wiki-architektur.md) — Der Rahmen, in dem die Entscheidung fällt
- [wiki-datenbankschema](wiki-datenbankschema.md) — Die `sections`-Tabelle
- [engpass-groesse-vs-session](engpass-groesse-vs-session.md) — Die Leitfrage hinter der Granularität
- [llm-wiki-v2](llm-wiki-v2.md) — Frischheitsmetadaten auf Seitenebene, hier präzisiert
- [ingest-fliessband](ingest-fliessband.md) — Die Sektion als Einheit des `merge`-Schritts
- [jdocmunch](../werkzeuge/jdocmunch.md) — Sektionsbasierter Zugriff als externes Werkzeug
- [qmd](../werkzeuge/qmd.md) — BM25/Vektor/Hybrid als externes Werkzeug
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
