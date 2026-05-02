---
date: 2026-04-23
type: konzept
tags: [konzept]
status: active
---

# Fortgeschrittene Architektur

**Zusammenfassung**: Erweiterungen des Basis-LLM-Wiki-Musters: RAG über kompilierte Wiki-Seiten, Routing-Schritt für Kostenkontrolle, Prompt Caching (~90% Kostenreduktion), MCP-Integration und Feintuning-Potenzial.
**Quellen**: clippings/Beyond RAG How Andrej Karpathy's LLM Wiki Pattern Builds Knowledge That Actually Compounds.md, clippings/Karpathy shares 'LLM Knowledge Base' architecture...
**Zuletzt aktualisiert**: 2026-04-23

---

## Überblick

Das Basis-LLM-Wiki-Muster (Ingest → Wiki → Abfrage) funktioniert für kleine bis mittlere Wikis gut. Für größere Wikis oder produktionsreife Systeme gibt es etablierte Erweiterungsmuster.

## RAG über kompilierte Wiki-Seiten

Der wichtigste Schritt über das Basisformat hinaus: Statt das Wiki direkt zu laden (was bei ~50–100K Token zum Flaschenhals wird), werden die Wiki-Seiten in einem Vektor-Index eingebettet.

Das Besondere: Es werden **Wiki-Seiten** eingebettet, nicht Rohchunks. Das macht einen entscheidenden Qualitätsunterschied:

- **Klassisches RAG**: Random-Chunk aus Seite 14 eines PDFs
- **Wiki-RAG**: Vorsynthetisierte, querverlinkte Enzyklopädie-Seite über das Konzept

Die Wiki-Seite integriert bereits alles, was das System je über dieses Konzept gelernt hat. Das Retrieval ist präziser, der Kontext reichhaltiger.

Plaban Nayak implementiert das mit `text-embedding-3-small` (1536-dimensional) und linearer Cosine-Similarity-Suche über flat JSON — ausreichend für bis zu ~500 Seiten ohne ANN-Infrastruktur. (Quelle: clippings/Beyond RAG...)

## Routing-Schritt für Kostenkontrolle

Ohne Routing würde jeder Ingest einer neuen Quelle alle Wiki-Seiten berühren (teuer). Mit Routing:

1. LLM liest Schema-Zusammenfassung (1 Zeile pro Seite: `slug: titel — beschreibung`)
2. LLM gibt JSON-Array der relevanten Slugs zurück
3. Nur relevante Seiten werden synthetisiert

Das reduziert Ingest-Kosten dramatisch bei großen Wikis. (Quelle: clippings/Beyond RAG...)

## Prompt Caching

Wenn derselbe System-Prompt über Routing, Synthese und Index-Updates gesendet wird, trifft er den Anthropic-Cache. Ergebnis: **~90% Kostenreduktion** bei Wiederholungsoperationen. (Quelle: clippings/Beyond RAG...)

Relevant besonders bei häufigen Ingest-Sitzungen oder großen Wikis.

## MCP-Integration

Das Wiki kann als MCP-Server exposiert werden — damit wird es zu einem nativen Werkzeug in Claude Code (und anderen MCP-fähigen Clients):

- `wiki_search` — Semantische Suche über Wiki-Seiten
- `wiki_ingest` — Neue Quelle aufnehmen
- `wiki_lint` — Wiki-Gesundheitsprüfung

Tools wie [jdocmunch](../werkzeuge/jdocmunch.md) und [qmd](../werkzeuge/qmd.md) sind bereits als MCP-Server verfügbar und können das Wiki direkt in den Claude-Code-Workflow integrieren. (Quelle: clippings/Karpathy's LLM Knowledge Bases The Post-Code AI Workflow.md)

## Feintuning-Potenzial

Das reifste Wiki ist auch ein hochwertiger Trainingsdatensatz:

> "As the wiki grows and the data becomes more 'pure' through continuous LLM linting, it becomes the perfect training set." — Carl Franzen, VentureBeat (Quelle: clippings/Karpathy shares 'LLM Knowledge Base' architecture...)

Statt das Wiki im Kontextfenster zu laden, könnte man ein kleineres Modell auf dem Wiki feintunen — das Modell würde die persönliche Wissensbasis in seinen Gewichten "kennen". Noch experimentell, aber konzeptuell der logische Endpunkt.

## Ephemere Mini-Wissensbases

Lex Fridman erwähnte ein nützliches Variante: Für spezifische Aufgaben eine temporäre, fokussierte Mini-Wissensbasis erzeugen — und diese nach dem Abschlussbericht auflösen. Der Vorteil: maximale Relevanz bei minimalem Token-Overhead für die jeweilige Aufgabe.

## Verwandte Seiten

- [skalierungsgrenzen](skalierungsgrenzen.md)
- [jdocmunch](../werkzeuge/jdocmunch.md)
- [qmd](../werkzeuge/qmd.md)
- [ingest-workflow](ingest-workflow.md)
- [drei-ebenen-architektur](drei-ebenen-architektur.md)

---

[Wiki-Index](../index.md)
