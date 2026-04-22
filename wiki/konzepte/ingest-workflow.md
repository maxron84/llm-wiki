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

„Ingest" ist die erste der drei Kernoperationen im [[llm-wiki-muster]]. Du legst eine neue Quelle in den `raw/`- oder `clippings/`-Ordner und sagst der KI, sie soll die Quelle verarbeiten. Die KI liest, bespricht, schreibt und verknüpft — das Wiki wächst.

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

Ein wichtiger Aspekt, den Mario im [[llm-wiki-tutorial-mit-mario|Tutorial]] demonstriert: Die KI fragt aktiv, **wie tief** man gehen will. Drei typische Stufen:

- **Minimal** — Nur die Kernseite (z.B. nur das Rezept)
- **Mittel** — Kernseite + übergreifende Konzeptseiten (z.B. Rezept + „Glutenfreies Backen")
- **Vollständig** — Alles oben + eigene Seiten für jedes Detail (z.B. jede Zutat einzeln)

Diese Auswahl gibt dem Menschen die Kontrolle über den Detailgrad und verhindert Über- oder Unterkategorisierung. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Arbeitsweise

Karpathy bevorzugt es, Quellen **einzeln** aufzunehmen und dabei involviert zu bleiben — er liest die Zusammenfassungen, prüft die Aktualisierungen und leitet die KI an, was betont werden soll. Man kann aber auch mehrere Quellen im Batch mit weniger Aufsicht aufnehmen. Den eigenen Workflow zu finden und im Schema zu dokumentieren, ist Teil des Prozesses. (Quelle: clippings/llm-wiki.md)

## Bezug zur Architektur

Der Ingest-Workflow verbindet alle drei Ebenen der [[drei-ebenen-architektur]]:
- Er **liest** aus Ebene 1 (Rohquellen)
- Er **schreibt** in Ebene 2 (Wiki)
- Er **folgt** Ebene 3 (Schema)

## Verwandte Seiten

- [[llm-wiki-muster]]
- [[drei-ebenen-architektur]]
- [[query-workflow]]
- [[lint-pruefung]]
- [[obsidian-web-clipper]]

---

[Zurück zum Index](../index.md)
