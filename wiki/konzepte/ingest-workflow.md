---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Ingest-Workflow (Aufnahme)

**Zusammenfassung**: Der Prozess, mit dem neue Quellen ins LLM-Wiki aufgenommen werden — von der Lektüre über die Besprechung bis zur Integration in bestehende Wiki-Seiten.
**Quellen**: clippings/llm-wiki.md, clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Überblick

„Ingest" ist die erste der drei Kernoperationen im [llm-wiki-muster](llm-wiki-muster.md). Du legst eine neue Quelle in den `raw/`- oder `clippings/`-Ordner und sagst der KI, sie soll die Quelle verarbeiten. Die KI liest, bespricht, schreibt und verknüpft — das Wiki wächst.

## Ablauf

Ein typischer Ingest-Workflow sieht so aus:

1. **Quelle lesen** — Die KI liest das vollständige Quelldokument
2. **Übersetzen** — Falls nötig, ins Deutsche übersetzen (ohne Inhalt zu verändern)
3. **Besprechen** — Die KI bespricht die wichtigsten Erkenntnisse mit dem Benutzer, *bevor* sie etwas schreibt
4. **Zusammenfassungsseite erstellen** — Eine Seite in `wiki/quellen/`, benannt nach der Quelle
5. **Konzeptseiten erstellen/aktualisieren** — Für jede wichtige Idee oder Entität
6. **Verknüpfen** — Wiki-Links `[[seitenname]]` hinzufügen, um verwandte Seiten zu verbinden
7. **Organisieren** — Wiki-Seiten thematisch in Ordner und Unterordner gruppieren
8. **Index aktualisieren** — `wiki/index.md` mit neuen Seiten und Beschreibungen ergänzen
9. **Log aktualisieren** — Eintrag in `wiki/log.md` mit Datum, Quellennamen und Änderungen

(Quelle: clippings/llm-wiki.md)

## Umfang

Eine einzelne Quelle kann **10–15 Wiki-Seiten** betreffen. Das ist normal und gewollt — das Wissen einer Quelle verteilt sich auf verschiedene Konzeptseiten und stärkt die Vernetzung. (Quelle: clippings/llm-wiki.md)

## Tiefe-Auswahl

Ein wichtiger Aspekt, den Mario im [Tutorial](../quellen/llm-wiki-tutorial-mit-mario.md) demonstriert: Die KI fragt aktiv, **wie tief** man gehen will. Drei typische Stufen:

- **Minimal** — Nur die Kernseite (z.B. nur das Rezept)
- **Mittel** — Kernseite + übergreifende Konzeptseiten (z.B. Rezept + „Glutenfreies Backen")
- **Vollständig** — Alles oben + eigene Seiten für jedes Detail (z.B. jede Zutat einzeln)

Diese Auswahl gibt dem Menschen die Kontrolle über den Detailgrad und verhindert Über- oder Unterkategorisierung. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Arbeitsweise

Karpathy bevorzugt es, Quellen **einzeln** aufzunehmen und dabei involviert zu bleiben — er liest die Zusammenfassungen, prüft die Aktualisierungen und leitet die KI an, was betont werden soll. Man kann aber auch mehrere Quellen im Batch mit weniger Aufsicht aufnehmen. Den eigenen Workflow zu finden und im Schema zu dokumentieren, ist Teil des Prozesses. (Quelle: clippings/llm-wiki.md)

### Lokale Modelle und Ingest

Der Ingest ist die Token-intensivste Operation im Wiki. System-Prompt, CLAUDE.md, das Clipping selbst und die gelesenen Verweisseiten summieren sich auf 18–42k Tokens — noch bevor die neuen Seiten geschrieben sind. Bei lokalen 14B-Modellen mit 40k Kontextfenster (z.B. `qwen3:14b-40k`) liegt die praktische Grenze bereits bei ~20–25k Tokens, ab der die Latenz quadratisch steigt.

**Fazit**: Ingest mit lokalen Modellen ist nur für kleine Clippings möglich und selbst dort knapp. Für den regulären Ingest-Betrieb wird Claude via API empfohlen. → [lokale-modelle](../anleitungen/lokale-modelle.md), [ollama-kontextfenster](ollama-kontextfenster.md)

## Routing-Schritt (Kostenkontrolle bei großen Wikis)

Bei größeren Wikis (viele Seiten) würde ein naiver Ingest alle Seiten berühren — teuer. Ein effizienter Ingest-Workflow trennt daher **Routing** von **Synthese**:

1. LLM liest eine kompakte Schema-Zusammenfassung (1 Zeile pro Seite)
2. LLM gibt zurück, welche Seiten tatsächlich relevant sind
3. Nur relevante Seiten werden neu synthetisiert

Dieser Routing-Schritt kann die Ingest-Kosten dramatisch reduzieren. (Quelle: clippings/Beyond RAG...)

## Skalierung

Bei wachsendem Wiki entstehen Skalierungsgrenzen: index.md wird bei ~50–100K Token zum Navigationsproblem. Tools wie [qmd](../werkzeuge/qmd.md) (semantische Suche) und [jdocmunch](../werkzeuge/jdocmunch.md) (sektionsbasierter Zugriff) adressieren das. Mehr: [skalierungsgrenzen](skalierungsgrenzen.md)

## Bezug zur Architektur

Der Ingest-Workflow verbindet alle drei Ebenen der [drei-ebenen-architektur](drei-ebenen-architektur.md):
- Er **liest** aus Ebene 1 (Rohquellen)
- Er **schreibt** in Ebene 2 (Wiki)
- Er **folgt** Ebene 3 (Schema)

## Verwandte Seiten

- [llm-wiki-muster](llm-wiki-muster.md)
- [drei-ebenen-architektur](drei-ebenen-architektur.md)
- [query-workflow](query-workflow.md)
- [lint-pruefung](lint-pruefung.md)
- [obsidian-web-clipper](../werkzeuge/obsidian-web-clipper.md)
- [skalierungsgrenzen](skalierungsgrenzen.md)
- [kontaminierungsrisiko](kontaminierungsrisiko.md)

---

[Wiki-Index](../index.md)
