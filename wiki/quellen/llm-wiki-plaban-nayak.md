---
date: 2026-04-23
type: quelle
tags: [quelle]
status: active
---

# Beyond RAG: Knowledge That Compounds (Plaban Nayak)

**Zusammenfassung**: Technisch tiefgehender Artikel von Plaban Nayak mit Python-Implementierung eines vollständigen LLM-Wiki-Systems: 5-Schritt-Ingest-Pipeline, RAG über kompiliertes Wiki, Query-Templates und Prompt Caching für ~90% Kostenreduktion.
**Quellen**: clippings/Beyond RAG How Andrej Karpathy's LLM Wiki Pattern Builds Knowledge That Actually Compounds.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Über die Quelle

- **Autor**: Plaban Nayak (Level Up Coding / Medium)
- **Erschienen**: 2026-04-12
- **Art**: Technischer Deep-Dive mit Python-Implementierung

## Kernaussagen

### Die 5-Schritt-Ingest-Pipeline

Nayak beschreibt einen präzisen Ingest-Workflow, der über das Basiskonzept hinausgeht:

1. **Resolve Source** — Quelltyp erkennen (Datei, YouTube-URL, HTTP) und Text extrahieren
2. **Route** — LLM liest Schema-Zusammenfassung + Quelle → gibt JSON-Array relevanter Slugs zurück. Kritisch für Kostenkontrolle: ohne Routing würde jeder Ingest alle Seiten berühren.
3. **Synthesize** — Für jeden relevanten Slug: LLM erhält bestehende Seite + neue Quelle und schreibt die komplette Seite neu. Invariante: *"Preserve and extend existing content — never discard information already on the page."*
4. **Embed** — Aktualisierte Seite wird neu eingebettet (OpenAI `text-embedding-3-small`)
5. **Update Index + Log** — Index regeneriert, Log-Eintrag angehängt

### RAG über kompiliertes Wissen

Das Besondere an Nayaks Implementierung: Die Retrieval-Schicht arbeitet auf den **Wiki-Seiten**, nicht auf Rohchunks. Das Query-Modell:

1. Frage einbetten
2. Cosine-Similarity-Suche über Wiki-Seiten-Index
3. Vollständige Seitentexte als Kontext laden
4. Antwort streamen

Der Qualitätsunterschied zu klassischem RAG: Das LLM liest keine zufälligen PDF-Chunk. Es liest eine **vorsynthetisierte, querverlinkte Enzyklopädie-Seite**, die alles integriert, was das System je über dieses Konzept gelernt hat. Mehr: [[fortgeschrittene-architektur]]

### Prompt Caching

Nayak hebt hervor: Denselben System-Prompt über Routing, Synthese und Index-Updates zu senden trifft den Anthropic-Cache → ~90% Kostenreduktion bei Wiederholungsoperationen. (Quelle: clippings/Beyond RAG...)

### Query-Templates: 6 Typen

Benannte Abfragetypen, die das Wiki vom Frage-Antwort-System zum Denkpartner machen:

| Typ | Beispielfrage |
|---|---|
| Synthese | „Die eine wichtigste Erkenntnis, die alles verbindet?" |
| Lückensuche | „Welche Themen fehlen komplett? Wo brauche ich mehr Quellen?" |
| Debatte | „Der größte Widerspruch zwischen meinen Quellen?" |
| Output | „Erstelle eine Cheat Sheet / ein Lernmodul" |
| Health | Duplikate, Konsistenz, Integrität prüfen |
| Persönliche Anwendung | „Welche Fehler mache ich gerade wahrscheinlich?" |

Mehr: [[query-templates]]

### Python-Modulstruktur

Saubere Trennung: `embeddings.py`, `index.py` (EmbeddingIndex über flat JSON), `wiki.py` (CRUD mit python-frontmatter), `prompts.py` (einzige Quelle aller Prompt-Texte), `ingest.py`, `query.py`, `lint.py`, `cli.py` (Click-Interface mit 6 Commands).

## Verwandte Seiten

- [[fortgeschrittene-architektur]]
- [[query-templates]]
- [[ingest-workflow]]
- [[rag-vs-wiki]]
- [[skalierungsgrenzen]]

---

[Wiki-Index](../index.md)
