---
date: 2026-08-11
type: quelle
tags: [quelle, schema, lokale-modelle, hardware, skalierung]
status: active
---

# Quelle: Das Wiki als Datenbank — SQL-Betrieb mit schwachen lokalen Modellen

**Zusammenfassung**: Interne Architekturanalyse, die den SQL-Umbau des LLM-Wiki-Musters nicht mit dem Wachstum des Wikis begründet, sondern mit der Schwäche des lokalen Modells: SQL macht das große Kontextfenster überflüssig und damit ein 14B-Modell auf 16 GB VRAM überhaupt erst arbeitsfähig.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Bibliografische Angaben

- **Datei**: `raw/sqlwiki_lokalesmodell_architektur.md`
- **Typ**: Interne Analyse (Synthese aus Wiki-internen Seiten, Herstellerdokumentation und Modellarchitektur-Rechnung)
- **Datum**: 2026-08-11
- **Ausgangsfrage**: Lässt sich das LLM-Wiki-Muster mit einer klassischen SQL-Datenbank realisieren, betrieben von einem lokalen Modell bis ~24B auf einer 16-GB-Karte?

Anders als die meisten Quellen im Wiki ist dies kein externer Fundstück, sondern eine Analyse, die auf dem Wiki selbst aufsetzt und an mehreren Stellen ausdrücklich Korrekturen am Bestand einfordert.

---

## Das zentrale Argument

Die naheliegende Begründung für einen SQL-Umbau ist Wachstum: Das Wiki wird zu groß fürs Kontextfenster, also muss man es abfragen statt laden. Die Quelle nennt diese Begründung richtig, aber zu spät greifend, und stellt ihr eine Umkehrung entgegen:

> „SQL macht das große Kontextfenster überflüssig — und damit das schwache lokale Modell erst arbeitsfähig." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Damit wird aus einer Hardware-Kaufentscheidung („ich brauche mehr VRAM für mehr Kontext") eine Software-Entscheidung („ich sorge dafür, dass jede Operation nur die Daten sieht, die sie braucht"). Mehr: [sql-wiki-architektur](../konzepte/sql-wiki-architektur.md)

---

## Haupterkenntnisse

### 1. Zwei Engpässe, die ständig verwechselt werden

Wiki-Größe (Engpass A) und Session-Kapazität (Engpass B) äußern sich beide als „Kontextfenster voll", haben aber verschiedene Auslöser und verschiedene Lösungen. A tritt bei Claude ab ~300 Seiten auf, B lokal ab Seite 1. Ein Wiki mit ~120 Seiten liegt nach den Schwellenwerten klar im Bereich „direkt laden" — wer daraus schließt, der Umbau sei verfrüht, hat nur A im Blick. Mehr: [engpass-groesse-vs-session](../konzepte/engpass-groesse-vs-session.md)

### 2. Die VRAM-Rechnung schließt 24B mit 100–200k Kontext aus

Der KV-Cache wächst linear mit der Kontextlänge und hängt an Layer-Zahl und GQA-Konfiguration, nicht an der Parameterzahl. Für ein 24B-Modell der Mistral-Small-Klasse (40 Layer, 8 KV-Heads, head_dim 128) sind das 160 KiB pro Token bei FP16.

> „Es geht sich nicht knapp nicht aus, sondern um den Faktor 2–3." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Bemerkenswert: `qwen3:14b` hat dieselbe Geometrie und damit denselben KV-Bedarf pro Token. Ein kleineres Modell spart bei den Gewichten, nicht beim Kontext. Mehr: [kv-cache-rechnung](../konzepte/kv-cache-rechnung.md)

### 3. Die machbaren Zahlen sind Papierzahlen

Die rechnerisch möglichen 75k Token eines 14B-Modells bei Q8-KV sind praktisch irrelevant, weil Transformer-Attention quadratisch ist. Die vorliegende Messreihe zeigt: Request 1 mit ~13,9k Prompt-Token braucht 7,5 s, Request 3 mit ~19,6k braucht 23,9 s — Faktor 3 bei 40 % mehr Kontext.

> „Die praktische Nutzungsgrenze liegt bei ~20–25k Token, unabhängig davon, wie viel VRAM man dem KV-Cache widmet." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

### 4. Beim Schreiben liegt der Fehler

Die Quelle unterscheidet zwei Bauformen: SQL als Index über Markdown (Variante A, Vault bleibt Quelle der Wahrheit) und SQL als Quelle der Wahrheit mit Markdown-Export (Variante B). Variante A löst nur das Leseproblem und wird mit wachsendem Wiki *schlechter*, weil das Modell weiterhin ganze Dateien neu ausgeben muss. Entscheidung: **Variante B, mit verpflichtendem Markdown-Export.**

### 5. Die Sektion ist das richtige Atom

Seite als kleinste schreibbare Einheit heißt 2.000–4.000 Token pro `UPDATE` — Variante A mit anderen Mitteln. Absatz ist zu fein. Der H2-Abschnitt mit 200–600 Token ist groß genug für Bedeutung und klein genug für ein 14B-Modell. Er ist zugleich die natürliche Einheit für Retrieval und für Frischheitsmetadaten. Mehr: [sektion-als-atom](../konzepte/sektion-als-atom.md)

### 6. Vier von acht Lint-Prüfungen verschwinden ersatzlos

`DEAD LINKS` wird zum Foreign Key, `TYPE / FOLDER` zum CHECK-Constraint, `LOG ORDER` zu `ORDER BY at`, `PAGES NOT IN INDEX` entfällt, weil der Index generiert wird. Nicht schneller — die Fehlerklasse existiert nicht mehr. Mehr: [lint-pruefung](../konzepte/lint-pruefung.md)

### 7. Widerspruchserkennung wird zerlegbar

Statt dass ein Modell den gesamten Vault nach Widersprüchen durchsucht (auf 16 GB VRAM undurchführbar), liefert eine Query die Kandidatenliste: gleiches `subject`, verschiedene `source_id`. Das Modell entscheidet je Paar in 300 Token.

> „Eine unmögliche Aufgabe wird zu zehn trivialen." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

### 8. Provenienz wird von einer Konvention zu einem Fremdschlüssel

Die Zitierregel `(Quelle: dateiname.md)` ist im Dateisystem eine Konvention, die vergessen werden kann — ein Lint-Lauf fand nachweislich 50 maschinell nicht auflösbare Quellenangaben. Als Spalte `source_id` in einer `claims`-Tabelle zeigt sie auf eine existierende Quelle oder ist `NULL`, und `NULL` ist per Definition `confidence = 'unverified'`. Mehr: [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md)

### 9. Die eine Regel: das Modell schreibt niemals SQL

Ein Modell, das an Dateipfaden scheitert, schreibt kein korrektes SQL — und im Fehlerfall ist der Schaden nicht eine kaputte Datei, sondern ein `UPDATE` ohne `WHERE`. Stattdessen sechs enge, flache Werkzeuge. Mehr: [werkzeugschicht](../konzepte/werkzeugschicht.md)

### 10. Die Metrik muss für lokalen Betrieb neu gefasst werden

> „Wattstunden pro Wiki-Seite und Minuten pro Wiki-Seite statt Dollar pro Wiki-Seite." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Bei 300 W GPU-Last und 4 Minuten pro Seite sind das 20 Wh, bei 0,30 €/kWh rund 0,006 € Strom pro Seite — zwei Größenordnungen unter dem Sonnet-Wert von ~0,42 US$. Die Quelle nennt den Vergleich selbst unfair, solange die Qualität nicht gleichzieht. Mehr: [wh-pro-wiki-seite](../konzepte/wh-pro-wiki-seite.md)

### 11. Datenbankwahl: SQLite als Einstieg, PostgreSQL als benannter Migrationspfad

DuckDB scheidet aus (analytische Engine, langsam bei vielen kleinen Updates). MariaDB ist die schwächste der drei relationalen Optionen für diesen Fall und wird erst beim Mehrbenutzerbetrieb relevant. Der konkrete Auslöser für den Wechsel zu PostgreSQL ist die deutsche Volltextsuche. Mehr: [deutsche-volltextsuche](../konzepte/deutsche-volltextsuche.md), [sqlite](../werkzeuge/sqlite.md), [postgresql](../werkzeuge/postgresql.md)

### 12. Ingest bleibt hart — aber zerlegbar

Der Ingest ist die Operation, bei der die Datenbank am wenigsten hilft. Er lässt sich aber in ein sechsstufiges Fließband zerlegen, dessen Zustand in der Datenbank liegt statt im Kontextfenster; kein Schritt übersteigt 3k Token. Die Struktur ist die der [Ralph-Schleife](../konzepte/ralph-schleife.md) — nur mit Constraints statt ohne. Mehr: [ingest-fliessband](../konzepte/ingest-fliessband.md)

---

## Eingeforderte Korrektur am Bestand

Die Quelle enthält einen ausdrücklichen Prüfpunkt gegen das bestehende Wiki:

> „Die vorhandene Angabe `qwen3:14b-40k` (Q8, ~9,3 GB Gewichte + ~6,7 GB KV-Cache = ~16 GB) ist in sich widersprüchlich. […] Die Messung von 15,1 GiB Gesamtbelegung ist plausibel, das Label „Q8" ist es nicht. **Zu korrigieren beim Ingest.**" (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Begründung: 6,7 GB KV-Cache entsprechen exakt 40k × 160 KiB bei **FP16**, nicht bei Q8-KV (das wären ~3,3 GB). Und 9,3 GB Gewichte entsprechen bei 14B einer Q4/Q5-Quantisierung, nicht Q8 (~15,7 GB).

Der Widerspruch stand an drei Stellen und wurde beim Ingest am 2026-08-11 korrigiert: [hardware-vergleich-sonnet-vs-lokal](../konzepte/hardware-vergleich-sonnet-vs-lokal.md), [quantisierung](../konzepte/quantisierung.md), [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md). Die gemessenen 15,1 GiB Gesamtbelegung bleiben unangetastet — falsch war nur das Label, nicht die Messung.

---

## Was die Quelle ausdrücklich nicht löst

- **Qualität der Synthese** — ein 14B-Modell schreibt schlechtere Zusammenfassungen als Sonnet, in jeder Architektur. „SQL macht es arbeitsfähig, nicht klüger."
- **Deutsche Volltextsuche in SQLite** — offener Punkt mit drei Behelfen und einem benannten Ausweg
- **Bidirektionales Editieren** — wer im Export-Vault eine Datei ändert, verliert die Änderung beim nächsten Export
- **Mehrere Geräte** — SQLite über Cloud-Ordner zu synchronisieren führt zu beschädigten Datenbanken
- **Sektionsgranularität** — ob sich ein Wiki gut in 200–600-Token-Blöcke schneiden lässt, ohne dass Argumentationsbögen zerfallen, ist eine empirische Frage
- **Die Metrik für lokalen Betrieb** — Wattstunden und Minuten pro Seite sind vorgeschlagen, aber nicht gemessen

---

## Wichtige Zitate

> „Ihr Wiki ist kein Dokument. Es ist eine Datenbank. Hört auf, es zu laden. Fangt an, es abzufragen." (J. Gravelle, in der Quelle zur Kontextualisierung von Engpass A zitiert)

> „Der Umbau verschiebt Arbeit vom Modell in den Code: Referenzintegrität wird zum Foreign Key, die Lint-Prüfung zu acht SELECTs, der Routing-Schritt zu einer FTS-Query mit rekursivem CTE. Was bleibt, ist Bedeutungsarbeit — und nur die braucht ein Modell."

> „Wer 150k Kontext konfiguriert, konfiguriert eine Zahl, die er nie erreicht, weil er vorher aufgibt."

> „Tote Links entstehen nicht mehr — nicht ‚werden gefunden', sondern **entstehen nicht**."

> „Der Freiheitsgrad, an dem schwache Modelle scheitern, wird ihnen genommen. Das ist kein Kompromiss, sondern der Zweck der Übung."

> „Die Datenbank verschiebt diese Grenze spürbar nach unten — sie hebt sie nicht auf."

---

## Verwandte Seiten

- [sql-wiki-architektur](../konzepte/sql-wiki-architektur.md) — Das Kernkonzept: die Umkehrung und die Architekturgabel
- [engpass-groesse-vs-session](../konzepte/engpass-groesse-vs-session.md) — Die beiden verwechselten Engpässe
- [kv-cache-rechnung](../konzepte/kv-cache-rechnung.md) — Die VRAM-Rechnung
- [sektion-als-atom](../konzepte/sektion-als-atom.md) — Granularität der kleinsten schreibbaren Einheit
- [wiki-datenbankschema](../konzepte/wiki-datenbankschema.md) — Die Tabellen
- [werkzeugschicht](../konzepte/werkzeugschicht.md) — Sechs enge Werkzeuge statt SQL
- [ingest-fliessband](../konzepte/ingest-fliessband.md) — Der zerlegte Ingest
- [markdown-als-rendering](../konzepte/markdown-als-rendering.md) — Der Export als Architekturbestandteil
- [deutsche-volltextsuche](../konzepte/deutsche-volltextsuche.md) — Die ernsteste Schwäche der SQLite-Variante
- [wh-pro-wiki-seite](../konzepte/wh-pro-wiki-seite.md) — Die Metrik für lokalen Betrieb
- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md) — Die Schwellenwerte, die Engpass A beschreiben
- [lint-pruefung](../konzepte/lint-pruefung.md) — Vier von acht Prüfungen entfallen strukturell
- [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md) — Provenienz als Fremdschlüssel
- [ralph-schleife](../konzepte/ralph-schleife.md) — Die Struktur des Ingest-Fließbands
- [sqlite](../werkzeuge/sqlite.md) — Der empfohlene Einstieg
- [postgresql](../werkzeuge/postgresql.md) — Der benannte Migrationspfad

---

[Wiki-Index](../index.md)
