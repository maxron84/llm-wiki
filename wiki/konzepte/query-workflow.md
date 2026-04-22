---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Query-Workflow (Abfrage)

**Zusammenfassung**: Der Prozess, mit dem Fragen gegen das LLM-Wiki gestellt werden — die KI durchsucht das Wiki, synthetisiert Antworten und speichert wertvolle Ergebnisse zurück.
**Quellen**: clippings/llm-wiki.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Überblick

„Query" ist die zweite der drei Kernoperationen im [[llm-wiki-muster]]. Du stellst eine Frage, und die KI beantwortet sie anhand des Wikis — nicht anhand der Rohdokumente, sondern anhand der bereits aufbereiteten Wissensbasis.

## Ablauf

1. **Index lesen** — Die KI liest `wiki/index.md`, um relevante Seiten zu finden
2. **Seiten lesen** — Die KI liest die relevanten Wiki-Seiten
3. **Antwort synthetisieren** — Die KI fasst eine Antwort zusammen und zitiert spezifische Wiki-Seiten
4. **Lücken benennen** — Wenn die Antwort nicht im Wiki steht, sagt die KI das klar
5. **Zurückspeichern** — Wenn die Antwort wertvoll ist, bietet die KI an, sie als neue Wiki-Seite zu speichern

(Quelle: clippings/llm-wiki.md)

## Der Compounding-Effekt

Der wichtigste Aspekt: **Gute Antworten können als neue Seiten ins Wiki zurückfließen.** Ein Vergleich, den du angefragt hast, eine Analyse, eine Verbindung, die du entdeckt hast — das sind wertvolle Erkenntnisse, die nicht in der Chat-Historie verschwinden sollten. So akkumulieren deine Erkundungen in der Wissensbasis genau wie aufgenommene Quellen. (Quelle: clippings/llm-wiki.md)

## Antwortformate

Antworten können je nach Frage verschiedene Formen annehmen:

- Eine Markdown-Seite
- Eine Vergleichstabelle
- Ein Slide-Deck (Marp)
- Ein Diagramm (matplotlib)
- Eine Canvas-Darstellung

(Quelle: clippings/llm-wiki.md)

## Unterschied zu RAG

Bei [[rag-vs-wiki|RAG-Systemen]] arbeitet die KI mit den Rohdokumenten. Beim Wiki-Muster arbeitet sie mit einer **vorbereiteten Wissensbasis**, die der Mensch selbst aufgebaut hat. Die Synthese ist schon geschehen, die Querverweise sind schon da.

## Verwandte Seiten

- [[llm-wiki-muster]]
- [[ingest-workflow]]
- [[lint-pruefung]]
- [[rag-vs-wiki]]

---

[Zurück zum Index](../index.md)
