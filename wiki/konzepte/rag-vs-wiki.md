---
date: 2026-04-22
type: konzept
tags: [konzept, vergleich]
status: active
---

# RAG vs. Persistentes Wiki

**Zusammenfassung**: Ein Vergleich zwischen dem klassischen RAG-Ansatz (Retrieval-Augmented Generation) und dem LLM-Wiki-Muster — zwei grundverschiedene Strategien für den Umgang mit Wissen.
**Quellen**: clippings/llm-wiki.md, clippings/LLM Wiki v2 Extending Karpathy's Pattern with Production Lessons.md, clippings/The enterprise LLM wiki scaling Karpathy's pattern to your org (April 2026).md
**Zuletzt aktualisiert**: 2026-05-02

---

## Überblick

RAG (Retrieval-Augmented Generation) und das [llm-wiki-muster](llm-wiki-muster.md) lösen dasselbe Problem — „Wie beantwortet eine KI Fragen über meine Dokumente?" — auf fundamental unterschiedliche Weise.

## Vergleich

| Aspekt | RAG | LLM-Wiki |
|---|---|---|
| **Wann wird Wissen verarbeitet?** | Bei jeder Abfrage | Einmal beim Ingest, dann laufend aktualisiert |
| **Was wird gespeichert?** | Rohe Dokumentenchunks + Embeddings | Strukturierte, verlinkte Markdown-Seiten |
| **Akkumulation** | Keine — Wissen wird jedes Mal neu abgeleitet | Ja — das Wiki wächst mit jeder Quelle |
| **Querverweise** | Müssen jedes Mal neu gefunden werden | Sind bereits vorhanden |
| **Widersprüche** | Werden oft nicht erkannt | Werden beim Ingest markiert |
| **Synthese** | Bei jeder Frage neu | Bereits vorkompiliert |
| **Infrastruktur** | Embedding-Datenbank, Vektorsuche | Nur Markdown-Dateien + Index |
| **Transparenz** | Schwer nachvollziehbar | Vollständig lesbar und navigierbar |

(Quelle: clippings/llm-wiki.md)

## Das Problem mit RAG

Karpathy beschreibt das RAG-Erlebnis so: Du lädst Dateien hoch, die KI sucht relevante Stücke und generiert eine Antwort. Das funktioniert — aber die KI **entdeckt Wissen bei jeder Frage von Neuem**. Stell eine subtile Frage, die fünf Dokumente zusammenführen muss, und die KI muss jedes Mal die Fragmente finden und zusammensetzen. Nichts wird aufgebaut. (Quelle: clippings/llm-wiki.md)

NotebookLM, ChatGPT-Datei-Uploads und die meisten RAG-Systeme funktionieren auf diese Weise. (überprüfungsbedürftig)

## Der Wiki-Vorteil

Beim Wiki-Ansatz ist die Synthese **bereits geschehen**. Die Querverweise sind schon da. Die Widersprüche sind schon markiert. Wenn du eine Frage stellst, arbeitet die KI mit einer **vorbereiteten Wissensbasis**, nicht mit Rohdokumenten.

Außerdem: Gute Antworten können als neue Seiten ins Wiki zurückfließen (siehe [query-workflow](query-workflow.md)). So akkumuliert nicht nur das Quellenmaterial, sondern auch deine eigene Analyse.

## Die Kompilierungs-Metapher

Die prägnanteste Formulierung des Unterschieds kommt von mehreren Community-Autoren:

> "You don't execute source code every time you want to run a program. You compile it once into a binary and run that."

RAG = Quellcode bei jeder Abfrage neu ausführen. LLM-Wiki = einmal kompilieren, günstig abfragen. Mehr: [kompilierungs-metapher](kompilierungs-metapher.md)

## Wann RAG sinnvoll bleibt

RAG hat weiterhin seine Berechtigung bei:
- Sehr großen Dokumentensammlungen (Millionen Dokumente), die nicht vollständig ins Wiki passen
- Situationen, in denen punktuelle Faktensuche wichtiger ist als Synthese
- Szenarien, in denen die Rohdokumente sich häufig ändern

Das LLM-Wiki ist besonders stark bei **kumulativer Wissensarbeit** über Wochen und Monate — etwa 100–500 kuratierte Quellen zu einem Thema.

## Wichtiger Vorbehalt: Kontaminierungsrisiko

Das LLM-Wiki hat einen spezifischen Nachteil gegenüber RAG: Halluzinationen beim Ingest werden als persistente "Fakten" eingebacken und können sich über Querverweise ausbreiten. Bei RAG bleibt ein Fehler lokal in einer Antwort. Mehr: [kontaminierungsrisiko](kontaminierungsrisiko.md)

## RAG als historischer Workaround (LLM Wiki v2)

[LLM Wiki v2](llm-wiki-v2.md) formuliert einen wichtigen historischen Einordnung:

> RAG war ein **2023-Workaround für 8K-Kontextfenster**. Mit 1M-Token-Fenstern als Standard (DeepSeek V4, GPT-5.5, Qwen 3.6) schwächt sich die ökonomische Begründung für Retrieval gegenüber direkter Inclusion jeden Quartal.

Das bedeutet nicht, dass RAG obsolet wird — aber die Kosten-Nutzen-Rechnung verschiebt sich: Was früher zu teuer war (den gesamten Wiki-Kontext zu laden), ist mit wachsenden Kontextfenstern erschwinglich. (Quelle: clippings/LLM Wiki v2 Extending Karpathy's Pattern with Production Lessons.md)

## Enterprise-Kontext: Retrieval-Tools lösen das Wartungsproblem nicht

Glean, Notion AI, Confluence Search sind Retrieval-Werkzeuge. Sie verbessern das Finden von Erfasstem — aber beheben nicht das zugrundeliegende Wartungsproblem. Besseres Retrieval über veraltete Wissensbasis liefert **confident-formulierte falsche Antworten, schneller als schlechtes Retrieval über schlechte Basis**.

Was Karpathys Muster einzigartig macht, ist nicht die Suchschicht — es ist die Wartungsschleife. Mehr: [enterprise-skalierung](enterprise-skalierung.md)

## Verwandte Seiten

- [llm-wiki-muster](llm-wiki-muster.md)
- [query-workflow](query-workflow.md)
- [drei-ebenen-architektur](drei-ebenen-architektur.md)
- [kompilierungs-metapher](kompilierungs-metapher.md)
- [kontaminierungsrisiko](kontaminierungsrisiko.md)
- [skalierungsgrenzen](skalierungsgrenzen.md)
- [llm-wiki-v2](llm-wiki-v2.md) — v2 und der historische RAG-Workaround-Kontext
- [enterprise-skalierung](enterprise-skalierung.md) — Warum Retrieval-Tools Enterprise-Wikis nicht retten

---

[Wiki-Index](../index.md)
