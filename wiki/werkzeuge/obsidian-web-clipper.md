---
date: 2026-04-22
type: werkzeug
tags: [werkzeug]
status: active
---

# Obsidian Web Clipper

**Zusammenfassung**: Eine Browser-Erweiterung, die Webartikel in Markdown umwandelt und direkt in den Obsidian-Vault speichert — der schnellste Weg, Quellen für das LLM-Wiki zu sammeln.
**Quellen**: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md, clippings/llm-wiki.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Was ist der Obsidian Web Clipper?

Eine Browser-Erweiterung für Chrome (und kompatible Browser), die Webseiten-Inhalte als Markdown-Dateien in deinen [[obsidian|Obsidian]]-Vault speichert. Ein Klick auf die Erweiterung, und der Artikel landet als `.md`-Datei im `clippings/`-Ordner.

**Download**: https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjfabmlf

## Rolle im LLM-Wiki

Der Web Clipper ist das **Sammelnetz** für neue Quellen. Im [[llm-wiki-muster]] ist der Workflow:

1. Du liest einen interessanten Artikel im Browser
2. Du klickst auf den Web Clipper → der Artikel wird als Markdown in `clippings/` gespeichert
3. Du sagst der KI: „Es gibt neue Quelldateien, bitte verarbeite diese"
4. Die KI führt den [[ingest-workflow]] aus

Die Dateien landen automatisch im Obsidian-Vault, der gerade im Hintergrund geöffnet ist. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Praxisbeispiel

Mario demonstriert im [[llm-wiki-tutorial-mit-mario|Tutorial]], wie er in Sekunden drei Quellen von einer Kochwebseite clippt:

- Ein glutenfreies Keksrezept
- Ein Artikel zum einhändigen Eieraufschlagen
- Ein Artikel zur sicheren Aufbewahrung von Eiern

Jeder Clip dauert nur wenige Sekunden — der eigentliche Aufwand liegt bei der KI, die die Quellen verarbeitet. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Verwandte Seiten

- [[obsidian]]
- [[ingest-workflow]]
- [[llm-wiki-tutorial-mit-mario]]

---

[Zurück zum Index](../index.md)
