---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Das Chat-Problem

**Zusammenfassung**: Das fundamentale Problem der KI-Nutzung — Wissen verschwindet in Chats, wird nicht aufgebaut, nicht strukturiert, nicht verknüpft. Das LLM-Wiki-Muster ist die Lösung.
**Quellen**: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md, clippings/llm-wiki.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Das Problem

Die meisten Menschen nutzen KI so: Dateien hochladen, Fragen stellen, Antworten bekommen. Das funktioniert — solange man im selben Chat bleibt. Aber bei echter Arbeit über Tage und Wochen treten drei Probleme auf:

### 1. Das Context Window ist begrenzt

Irgendwann wird der Chat so lang, dass die KI den Anfang vergisst. Ältere Informationen fallen aus dem Kontextfenster. Die über viele Fragen aufgebaute Synthese geht verloren. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

### 2. Kein System, keine Struktur

Am Ende hat man einen riesigen, unstrukturierten Chat. Keine Querverweise, kein Inhaltsverzeichnis, keine Organisation. Man findet nichts wieder. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

### 3. Neuer Chat = bei null

Wenn der alte Chat zu lang wird und man einen neuen aufmacht, startet man **tatsächlich wieder bei null**. Die Dokumente kann man erneut hochladen — aber die Synthese fehlt, die Verbindungen fehlen, das über 20 Fragen aufgebaute Verständnis ist nicht mehr da. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Das eigentliche Problem

> „Das eigentliche Problem ist nicht, dass KI kein Gedächtnis hat. Das eigentliche Problem ist, dass Wissen in einem Chat verschwindet. Es wird nicht aufgebaut, es wird nicht strukturiert, es wird nicht verknüpft. Es ist einfach ein langer Chat, der irgendwann abläuft."

(Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Die Lösung

Das [llm-wiki-muster](llm-wiki-muster.md) löst dieses Problem, indem Wissen **nicht im Chat** bleibt, sondern in ein **persistentes Wiki** fließt. Neue Erkenntnisse werden sofort in verlinkte Markdown-Seiten integriert. Das Wiki überlebt jeden Chat und wächst mit jeder Quelle.

Karpathy formuliert den Unterschied so: Bei [RAG](rag-vs-wiki.md) wird Wissen bei jeder Frage neu abgeleitet. Beim Wiki-Muster wird es einmal kompiliert und dann aktuell gehalten. (Quelle: clippings/llm-wiki.md)

## Verwandte Seiten

- [llm-wiki-muster](llm-wiki-muster.md)
- [rag-vs-wiki](rag-vs-wiki.md)
- [query-workflow](query-workflow.md)
- [llm-wiki-tutorial-mit-mario](../quellen/llm-wiki-tutorial-mit-mario.md)

---

[Wiki-Index](../index.md)
