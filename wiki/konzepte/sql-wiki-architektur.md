---
date: 2026-08-11
type: konzept
tags: [konzept, schema, lokale-modelle, skalierung]
status: active
---

# SQL-Wiki-Architektur

**Zusammenfassung**: Das LLM-Wiki in einer relationalen Datenbank statt in Markdown-Dateien — nicht wegen des Wachstums, sondern weil SQL das große Kontextfenster überflüssig macht und damit ein schwaches lokales Modell erst arbeitsfähig wird. Entschieden wird die Architektur beim Schreiben, nicht beim Lesen.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Die Umkehrung

Die übliche Denkrichtung lautet: *„Ich brauche ein Modell mit großem Kontext, um mit meinem Wiki zu arbeiten."* Das führt zu einer Hardware-Kaufentscheidung.

Die Umkehrung lautet: *„Wenn jede Operation nur die Daten sieht, die sie tatsächlich braucht, brauche ich das große Kontextfenster nicht."* Das führt zu einer Software-Entscheidung. (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Das ist der eigentliche Kern des Musters. J. Gravelles Formulierung — „Ihr Wiki ist kein Dokument. Es ist eine Datenbank. Hört auf, es zu laden. Fangt an, es abzufragen." — beschreibt dasselbe Werkzeug, aber mit einer anderen Begründung: Gravelle adressiert das gewachsene Wiki ([Engpass A](engpass-groesse-vs-session.md)), die SQL-Architektur adressiert die einzelne Operation (Engpass B). Der Unterschied entscheidet über den Zeitpunkt: Engpass A tritt bei ~300 Seiten auf, Engpass B lokal ab Seite 1.

## Was der Umbau kostet und was er einspart

| Operation | Markdown-Vault | SQL-Betrieb |
|---|---|---|
| Frage beantworten | `index.md` (5–15k) + 3–8 Seiten (je 2–4k) = **20–45k** | 5 Sektionen à 400 Token + Seitenzusammenfassungen = **3–5k** |
| Absatz aktualisieren | Seite lesen (3k) + Seite komplett neu schreiben (3k) = **6k** | Sektion lesen (0,4k) + Sektion neu schreiben (0,4k) = **1k** |
| Betroffene Seiten finden | Modell liest Index und rät | FTS-Query + Graph-Traversal, **0 Token** |
| Lint | Vault lesen = **100k+** | Acht SELECTs, **0 Token** |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Ein Kontextfenster von 32k ist im SQL-Betrieb nicht knapp, sondern großzügig. Das 14B-Modell arbeitet dann unterhalb der 20k-Latenzschwelle, mit dichtem statt halb gelesenem Kontext. → [kv-cache-rechnung](kv-cache-rechnung.md)

Zwei Nebeneffekte, die oft übersehen werden:

**Lost in the middle entfällt.** Modelle verlieren Information in der Mitte langer Kontexte. Ein 40k-Kontext mit 90 % Irrelevantem ist nicht nur langsamer, sondern auch qualitativ schlechter als ein 4k-Kontext mit 100 % Relevantem. Bei schwachen Modellen ist der Effekt ausgeprägter als bei starken.

**Die Latenz wird berechenbar.** Wenn jede Operation zwischen 2k und 8k Token liegt, ist die Antwortzeit konstant statt mit dem Gesprächsverlauf wachsend. Das ändert die Bedienbarkeit mehr als jede Tokenzahl.

## Die Architekturgabel

Es gibt zwei ernsthafte Bauformen, und sie unterscheiden sich weniger im Lesen als im Schreiben.

**Variante A — SQL als Index über Markdown.** Der Vault bleibt Quelle der Wahrheit. Ein Indexer parst die Dateien und schreibt Seiten, Sektionen, Links und Tags in eine Datenbank, die nur für Suche und Lint dient. Das Modell liest über die Datenbank, schreibt aber weiterhin Markdown-Dateien.

**Variante B — SQL als Quelle der Wahrheit.** Die Datenbank hält den Inhalt. Ein Export-Skript erzeugt daraus jederzeit den vollständigen Markdown-Vault für Obsidian und Git. Das Modell liest *und* schreibt ausschließlich über die Datenbank.

| | Variante A | Variante B |
|---|---|---|
| Leseoperation | klein ✅ | klein ✅ |
| Schreiboperation | **ganze Datei neu ausgeben** ❌ | einzelne Sektion ✅ |
| Tote Links | Lint findet sie nachträglich | **strukturell unmöglich** (FK) |
| Index/Log-Pflege | Modellaufgabe, fehleranfällig | Nebeneffekt der Transaktion |
| Frontmatter-Konsistenz | Modell muss sie reproduzieren | Spalten mit CHECK-Constraints |
| Obsidian | direkt ✅ | über Export ⚠️ |
| Git-Diff | direkt ✅ | über Export ⚠️ |
| Invasivität | gering | hoch |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Warum das Schreiben entscheidet

Variante A ist verlockend, weil sie nichts kaputt macht. Sie löst aber nur die halbe Aufgabe:

> „Beim Schreiben liegt der Fehler, und beim Schreiben skaliert das Problem mit der Wiki-Größe." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Je reifer eine Seite, desto länger, desto größer die Ausgabe, desto wahrscheinlicher der Abbruch. Variante A wird mit wachsendem Wiki *schlechter*, obwohl sie das Leseproblem gelöst hat.

Die beiden Nachteile von Variante B — Obsidian und Git — lösen sich mit demselben Mittel: einem Export-Skript, das nach jeder Transaktion oder vor jedem Commit den kompletten Vault schreibt. Der Markdown-Vault verliert seinen Status als Original und wird zum Rendering. → [markdown-als-rendering](markdown-als-rendering.md)

**Entscheidung: Variante B, mit verpflichtendem Markdown-Export.** Ohne den Export wäre der Umbau ein Rückschritt, weil er die Eigenschaft aufgäbe, die das Muster überhaupt robust macht — dass alles am Ende lesbares Markdown ist. → [modellunabhaengigkeit](modellunabhaengigkeit.md)

## Was aus dem Modell in den Code wandert

Der Umbau ist im Kern eine Verschiebung von Verantwortung:

| Bisher Modellaufgabe | Wird zu |
|---|---|
| Referenzintegrität (keine toten Links) | Foreign Key |
| `type` passt zum Ordner | CHECK-Constraint + generierter Export |
| Lint-Prüfung | Acht SELECTs → [lint-pruefung](lint-pruefung.md) |
| Routing: welche Seiten sind betroffen? | FTS-Query mit rekursivem CTE |
| Index- und Log-Pflege | Nebeneffekt der Transaktion |
| Quellenangabe nicht vergessen | `source_id` als Fremdschlüssel → [kontaminierungsrisiko](kontaminierungsrisiko.md) |
| Widersprüche im ganzen Vault finden | Kandidatenliste per Query, Urteil je Paar |

Was bleibt, ist Bedeutungsarbeit — und nur die braucht ein Modell. → [wiki-datenbankschema](wiki-datenbankschema.md), [werkzeugschicht](werkzeugschicht.md)

## Migrationspfad in vier Stufen

Ein Umbau in einem Zug ist unnötig riskant. Jede Stufe ist für sich nützlich: (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

**Stufe 1 — Spiegel, schreibgeschützt.** Ein Importer liest den bestehenden Vault und füllt die Datenbank. Nichts ändert sich am Arbeitsablauf. Gewinn: Der Lint läuft als SQL-Queries, `search` wird als Werkzeug verfügbar. Risiko: null, die Datenbank ist wegwerfbar.

**Stufe 2 — Lesebetrieb.** Das lokale Modell beantwortet Fragen ausschließlich über `search` und `get_section`. Geschrieben wird weiterhin Markdown von Hand oder per starkem Modell. Hier zeigt sich, ob die [Sektionsgranularität](sektion-als-atom.md) stimmt und ob die [deutsche Volltextsuche](deutsche-volltextsuche.md) trägt.

**Stufe 3 — Schreibbetrieb, Umkehrung der Richtung.** Die Datenbank wird Quelle der Wahrheit, der Export erzeugt den Vault. `upsert_section` geht scharf. Ab hier ist der Vault ein Rendering.

**Stufe 4 — Ingest-Fließband.** Der zerlegte Ingest, zunächst halbautomatisch mit Bestätigung je Schritt. → [ingest-fliessband](ingest-fliessband.md)

Zwischen Stufe 2 und 3 liegt die einzige wirklich schwer umkehrbare Entscheidung. Sie sollte erst fallen, wenn Stufe 2 im Alltag getragen hat.

## Was das Muster nicht löst

- **Qualität der Synthese.** Ein 14B-Modell schreibt schlechtere Zusammenfassungen als Sonnet, in jeder Architektur. SQL macht es arbeitsfähig, nicht klüger. Der Hauptgewinn liegt bei mechanischer Zuverlässigkeit, nicht bei inhaltlicher Tiefe. → [hardware-vergleich-sonnet-vs-lokal](hardware-vergleich-sonnet-vs-lokal.md)
- **Deutsche Volltextsuche in SQLite.** Ein offener Punkt mit drei Behelfen und einem benannten Ausweg.
- **Bidirektionales Editieren.** Wer im Export-Vault eine Datei in Obsidian ändert, verliert die Änderung beim nächsten Export. Ein Rückimport ist technisch möglich, aber Konfliktauflösung ist der aufwendigste Teil jedes Sync-Systems.
- **Mehrere Geräte.** SQLite über Cloud-Ordner zu synchronisieren ist ein bekannter Weg zu beschädigten Datenbanken.

## Verwandte Seiten

- [engpass-groesse-vs-session](engpass-groesse-vs-session.md) — Warum der Umbau lokal ab Seite 1 begründet ist
- [wiki-datenbankschema](wiki-datenbankschema.md) — Die Tabellen im Detail
- [sektion-als-atom](sektion-als-atom.md) — Die folgenreichste Schemaentscheidung
- [werkzeugschicht](werkzeugschicht.md) — Die eine Regel: das Modell schreibt niemals SQL
- [markdown-als-rendering](markdown-als-rendering.md) — Warum der Export Teil der Architektur ist
- [ingest-fliessband](ingest-fliessband.md) — Der zerlegte Ingest
- [kv-cache-rechnung](kv-cache-rechnung.md) — Warum das große Kontextfenster auf 16 GB nicht zu haben ist
- [skalierungsgrenzen](skalierungsgrenzen.md) — Die Schwellenwerte, die Engpass A beschreiben
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md) — RAG über Wiki-Seiten, Routing, Prompt Caching
- [lint-pruefung](lint-pruefung.md) — Vier von acht Prüfungen entfallen ersatzlos
- [sqlite](../werkzeuge/sqlite.md) — Der empfohlene Einstieg
- [postgresql](../werkzeuge/postgresql.md) — Der benannte Migrationspfad
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
