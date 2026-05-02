---
date: 2026-04-23
type: werkzeug
tags: [werkzeug]
status: active
---

# qmd

**Zusammenfassung**: qmd ist eine lokale Suchmaschine für Markdown-Wissensbases von Tobi Lütke (Shopify CEO) — mit drei Suchmodi (BM25, Vektorsuche, Hybrid mit LLM-Reranking), vollständig lokal ohne API, und MCP-Server-Integration für Claude Code.
**Quellen**: clippings/LLM Wiki Revolution How Andrej Karpathy's Idea is Changing AI.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Was ist qmd?

qmd ist eine lokale Suchmaschine, die speziell für Markdown-Wissensbases entwickelt wurde. Sie löst das [Skalierungsproblem](../konzepte/skalierungsgrenzen.md), das entsteht, wenn die index.md-Datei für eine einzelne Kontextfenster-Sitzung zu groß wird.

**Entwickler**: [Tobi Lütke](../personen/tobi-luetke.md) (CEO von Shopify)

Technischer Unterbau: `node-llama-cpp` mit GGUF-Modellen — alles lokal, keine API-Verbindungen, keine Datenweitergabe.

## Installation

```bash
npm install -g @tobilu/qmd
```

## Drei Suchmodi

```bash
# Kollektion registrieren
qmd collection add ./wiki --name meine-forschung

# BM25-Keyword-Suche (schnell, exakt)
qmd search "mixture of experts routing"

# Semantische Vektorsuche (findet verwandte Konzepte mit anderen Wörtern)
qmd vsearch "wie gehen sparse models mit load balancing um"

# Hybride Suche mit LLM-Reranking (höchste Qualität)
qmd query "tradeoffs zwischen top-k und expert-choice routing"

# JSON-Output für Pipelines
qmd query "scaling laws" --json | jq '.results[].title'
```

## MCP-Server-Modus

```bash
qmd mcp
```

Im MCP-Server-Modus ist qmd direkt in Claude Code als natives Werkzeug verfügbar — keine manuelle Suche, keine Zwischenschritte. Das ermöglicht einen nahtlosen Workflow: Claude Code sucht beim Bearbeiten einer Frage direkt über qmd.

## Wann qmd vs. jDocMunch?

| | qmd | jDocMunch |
|---|---|---|
| Suchanfragen | Semantisch / Hybrid | Sektionsbasiert |
| Stärke | Findet relevante Seiten | Lädt minimalen Kontext |
| Ideal für | Navigation bei großem Wiki | Gezielte Abschnitte laden |
| Komplementär? | Ja | Ja — kombinierbar |

qmd findet die relevanten Seiten; [jdocmunch](jdocmunch.md) lädt dann nur den nötigen Abschnitt. Zusammen adressieren sie das Skalierungsproblem vollständiger als jedes Tool allein.

## Wann einsetzen

qmd wird relevant bei:
- Mehr als ~100 Wiki-Seiten
- Situationen, in denen index.md zu groß für zuverlässige Navigation wird
- Wunsch nach semantischer Suche ("verwandte Konzepte finden, auch wenn andere Wörter verwendet werden")

Für Wikis unter ~100 Seiten ist direktes Laden in der Regel einfacher.

## Verwandte Seiten

- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md)
- [jdocmunch](jdocmunch.md)
- [fortgeschrittene-architektur](../konzepte/fortgeschrittene-architektur.md)
- [tobi-luetke](../personen/tobi-luetke.md)
- [llm-wiki-analytics-vidhya](../quellen/llm-wiki-analytics-vidhya.md)

---

[Wiki-Index](../index.md)
