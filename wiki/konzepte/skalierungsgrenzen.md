---
date: 2026-04-23
type: konzept
tags: [konzept]
status: active
---

# Skalierungsgrenzen des LLM-Wikis

**Zusammenfassung**: Das LLM-Wiki hat strukturelle Skalierungsgrenzen: index.md wird bei ~50–100K Token zum Flaschenhals, Qualitätsdegradierung beginnt bei ~200–300K Token. Lösungen: jDocMunch (sektionsbasiert) und qmd (semantische Suche).
**Quellen**: clippings/J. Gravelle — DEV Community Profile.md, clippings/LLM Wiki Revolution..., clippings/What Is Andrej Karpathy's LLM Wiki...
**Zuletzt aktualisiert**: 2026-04-23

---

## Das Kernproblem

> "The LLM Wiki doesn't eliminate token cost. It moves it." — J. Gravelle (Quelle: clippings/J. Gravelle — DEV Community Profile.md)

Der Tausch des LLM-Wiki-Musters:
- **Vorher**: Kosten pro Abfrage (RAG retrieval)
- **Nachher**: Kosten pro Sitzung (Wiki kompilieren/laden)

Das ist ein guter Tausch — *bis das Wiki das Kontextfenster übersteigt*.

## Konkrete Schwellenwerte

| Größe | Was passiert |
|---|---|
| < 50K Token | Smooth — index.md + Navigation funktionieren gut |
| ~50K–100K Token | index.md wird zum Flaschenhals; Navigation wird unzuverlässig |
| ~200K–300K Token | Qualitätsdegradierung beginnt; verpasste Links, schwächere Synthese |
| > 300K Token | Das Wiki ist de facto ein neues Retrieval-Problem |

(Quelle: clippings/J. Gravelle — DEV Community Profile.md; clippings/What Is Andrej Karpathy's LLM Wiki...)

## Symptome

Das Scheitern ist nicht plötzlich — es ist schleichend:

- Abfragen werden langsamer
- Antworten werden "weicher", ungenauer
- Das Modell übersieht Dinge, die definitiv im Wiki stehen
- Kein Fehler, kein Crash — einfach schlechter

Karpathys eigenes Wiki (~100 Artikel, ~400.000 Wörter zu *einem* Forschungsthema) nähert sich diesen Grenzen bereits. (Quelle: clippings/Karpathy shares 'LLM Knowledge Base' architecture...)

## Drei Lösungsansätze

### 1. jDocMunch — Sektionsbasierte Abfrage

Statt das Wiki als Dokument zu laden, wird es als Datenbank abgefragt:

```
"Lies index.md"  →  ERSETZEN DURCH  →  search_sections → get_section
```

Benchmark: ~95% Token-Reduktion bei 5 Abfragen über 7 Seiten. Mehr: [jdocmunch](../werkzeuge/jdocmunch.md)

### 2. qmd — Lokale semantische Suche

Drei Suchmodi (BM25, Vektor, Hybrid mit LLM-Reranking), vollständig lokal, MCP-Server-Integration. Wird relevant ab ~100+ Seiten. Mehr: [qmd](../werkzeuge/qmd.md)

### 3. RAG über kompiliertes Wiki

Fortgeschrittener Ansatz: Wiki-Seiten in einem Vektor-Index einbetten — nicht die Rohquellen. Die Embeddings der vorsynthetisierten Seiten liefern bessere Retrieval-Qualität als Embeddings von Rohchunks. Mehr: [fortgeschrittene-architektur](fortgeschrittene-architektur.md)

## Das mentale Modell (J. Gravelle)

> "Your wiki is not a document. It's a database. Stop loading it. Start querying it."

Die Wahl der Lösung hängt von der Wiki-Größe ab:

| Größe | Empfohlener Ansatz |
|---|---|
| < 50–100 Seiten | Direkt laden (Karpathys Standard-Muster) |
| 100–500 Seiten | qmd oder jDocMunch |
| > 500 Seiten | RAG über kompilierte Wiki-Seiten |

## Verwandte Seiten

- [jdocmunch](../werkzeuge/jdocmunch.md)
- [qmd](../werkzeuge/qmd.md)
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md)
- [rag-vs-wiki](rag-vs-wiki.md)
- [query-workflow](query-workflow.md)
- [ingest-workflow](ingest-workflow.md)

---

[Wiki-Index](../index.md)
