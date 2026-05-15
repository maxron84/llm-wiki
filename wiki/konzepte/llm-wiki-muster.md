---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# LLM-Wiki-Muster

**Zusammenfassung**: Das Kernkonzept hinter dem LLM-Wiki — ein Muster, bei dem KI ein persistentes, wachsendes Wiki aufbaut und pflegt, statt Wissen bei jeder Abfrage neu abzuleiten.
**Quellen**: clippings/llm-wiki.md, clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md, clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Die Grundidee

Die meisten Menschen nutzen KI mit Dokumenten so: Dateien hochladen, KI sucht relevante Stücke, generiert eine Antwort. Das funktioniert — aber die KI entdeckt das Wissen **bei jeder Frage von Neuem**. Es gibt keine Akkumulation. Frag eine subtile Frage, die fünf Dokumente zusammenführen muss, und die KI muss jedes Mal die relevanten Fragmente finden und zusammensetzen. Nichts wird aufgebaut. (Quelle: clippings/llm-wiki.md)

Das LLM-Wiki-Muster dreht das um: Die KI baut **inkrementell ein persistentes Wiki** auf — eine strukturierte, verlinkte Sammlung von Markdown-Dateien zwischen dir und den Rohquellen. Wenn du eine neue Quelle hinzufügst, wird sie nicht einfach für spätere Suche indexiert. Die KI liest sie, extrahiert die Schlüsselinformationen und **integriert sie in das bestehende Wiki** — aktualisiert Entitätsseiten, überarbeitet Themenzusammenfassungen, markiert Widersprüche.

## Der Schlüsselunterschied

> **Das Wiki ist ein persistentes, kumulatives Artefakt.**

- Die Querverweise sind schon da
- Die Widersprüche sind schon markiert
- Die Synthese spiegelt bereits alles wider, was du gelesen hast
- Das Wiki wird mit jeder Quelle reicher

Im Gegensatz zu [RAG-Systemen](rag-vs-wiki.md), wo Wissen bei jeder Abfrage neu zusammengesetzt wird, ist das Wiki eine **vorkompilierte Wissensbasis**, die einmal erstellt und dann laufend aktualisiert wird.

## Rollenverteilung

| Rolle | Zuständig für |
|---|---|
| **Mensch** | Quellen kuratieren, Fragen stellen, Analyse leiten |
| **KI** | Zusammenfassen, Querverweisen, Ablegen, Buchführung |

Du schreibst das Wiki nie (oder selten) selbst. Die KI übernimmt das gesamte „Grunt Work". Du bestimmst die Richtung. (Quelle: clippings/llm-wiki.md)

## Praxisaufbau

In der Praxis hat Karpathy den LLM-Agenten auf einer Seite und [Obsidian](../werkzeuge/obsidian.md) auf der anderen. Die KI bearbeitet Dateien basierend auf dem Gespräch, und er browst die Ergebnisse in Echtzeit — folgt Links, prüft die Graphansicht, liest aktualisierte Seiten.

Das Muster wird durch die [drei-ebenen-architektur](drei-ebenen-architektur.md) realisiert und über drei Operationen betrieben: [Ingest](ingest-workflow.md), [Query](query-workflow.md) und [Lint](lint-pruefung.md).

## Modellunabhängigkeit

Ein strategischer Vorteil: Da das Wiki nur aus Markdown-Dateien besteht, ist es [vollständig modellunabhängig](modellunabhaengigkeit.md). Man kann den KI-Agenten wechseln, ohne das Wiki zu verlieren. Die KI wird austauschbar wie eine Datenbank-Engine — die Daten bleiben. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Warum Menschen Wikis aufgeben — und KI das ändert

Menschen geben Wikis auf, weil der **Pflegeaufwand schneller wächst als der Nutzen**. Querverweise aktualisieren, Zusammenfassungen pflegen, Konsistenz sicherstellen — das ist mühsam und langweilig. LLMs haben dieses Problem nicht: Sie werden nicht müde, vergessen keine Querverweise und können 15 Dateien in einem Durchgang bearbeiten. Die Pflegekosten sind nahe null. (Quelle: clippings/llm-wiki.md)

## Verwandte Seiten

- [llm-wiki-karpathy](../quellen/llm-wiki-karpathy.md) — Die Originalquelle
- [llm-wiki-tutorial-mit-mario](../quellen/llm-wiki-tutorial-mit-mario.md) — Live-Demo des Konzepts
- [drei-ebenen-architektur](drei-ebenen-architektur.md)
- [chat-problem](chat-problem.md) — Das Problem, das das Muster löst
- [rag-vs-wiki](rag-vs-wiki.md)
- [ingest-workflow](ingest-workflow.md)
- [query-workflow](query-workflow.md)
- [lint-pruefung](lint-pruefung.md)
- [memex](memex.md)
- [andrej-karpathy](../personen/andrej-karpathy.md) — Der Urheber des Konzepts
- [llm-wiki-mehul-gupta](../quellen/llm-wiki-mehul-gupta.md) — Küchenmetapher und Rollenverschiebung
- [llm-wiki-venturebeat](../quellen/llm-wiki-venturebeat.md) — Community-Reaktionen und Enterprise-Perspektive
- [statelessness](statelessness.md) — Das technische Problem, das das Muster löst
- [modellunabhaengigkeit](modellunabhaengigkeit.md) — Strategischer Vorteil
- [mece-prinzip](mece-prinzip.md) — Alternative Ordnerstruktur
- [obsidian-claude-code-pillitteri](../quellen/obsidian-claude-code-pillitteri.md)
- [llm-wiki-v2](llm-wiki-v2.md) — Community-Erweiterung des Grundmusters

---

[Wiki-Index](../index.md)
