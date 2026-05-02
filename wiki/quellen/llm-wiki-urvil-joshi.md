---
date: 2026-04-23
type: quelle
tags: [quelle]
status: active
---

# LLM Wiki: Create Your Own Knowledge Base (Urvil Joshi)

**Zusammenfassung**: Detaillierter Tutorial-Artikel von Urvil Joshi, der das LLM-Wiki-Muster Schritt für Schritt erklärt — mit der Kompilierungs-Metapher, einer Live-Demo (Sutton + Karpathy-Essays), und einem ehrlichen Vergleich mit RAG.
**Quellen**: clippings/Andrej Karpathy's LLM Wiki Create your own knowledge base.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Über die Quelle

- **Autor**: Urvil Joshi (Medium)
- **Erschienen**: 2026-04-20
- **Art**: Tutorial mit Praxis-Demo

## Kernaussagen

### Die Kompilierungs-Metapher

Joshi erklärt die zentrale Analogie prägnant:

> "You don't execute source code every time you want to run a program. You compile it once into a binary and run that."

Rohquellen = Quellcode. Wiki = kompiliertes Binärformat. Die Kompilierung ist teuer — aber sie zahlt sich bei jeder nachfolgenden Nutzung aus. Mehr dazu: [kompilierungs-metapher](../konzepte/kompilierungs-metapher.md)

### Live-Demo: Wissens-Kompoundierung

Joshi demonstriert das Konzept mit zwei Quellen:

1. **Rich Suttons "The Bitter Lesson"** → Erzeugt ~10 verlinkte Seiten (Konzepte, Beispiele, Personenseite)
2. **Karpathys "Software 2.0"** → Aktualisiert bestehende Seiten, erstellt nur neue wo nötig — und fügt automatisch einen `[[bitter-lesson]]`-Link ein, den *kein Mensch angelegt hat*

Das Ergebnis: Das Wiki wird dichter, nicht nur größer. Verbindungen entstehen, die in den Rohdokumenten nicht explizit waren.

### RAG vs. LLM-Wiki: Die ehrliche Einschätzung

Joshi betont: Keines "gewinnt" — sie lösen verschiedene Probleme.

**RAG** ist besser bei:
- Millionen Dokumenten, die sich häufig ändern
- Präziser Zitatsuche auf Chunk-Ebene
- Enterprise-Faktensuche

**LLM-Wiki** ist besser bei:
- 100–500 kuratierten Quellen zu einem Thema
- Forschungsprojekten, bei denen Synthese wichtiger ist als Retrieval
- Büchern, Kursen, persönlichem Wissen

### Kontaminierungsrisiko

Joshi nennt den wichtigsten Kritikpunkt des Musters offen: Wenn das LLM eine Verbindung halluziniert, lebt dieser Irrtum im Wiki und beeinflusst zukünftige Abfragen. Bei klassischem RAG bleibt ein Fehler lokal. Beim LLM-Wiki kann er sich über verlinkte Seiten ausbreiten. Deshalb ist der Lint-Schritt kritisch. Mehr: [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md)

## Bezug zum bestehenden Wiki

Erklärt dasselbe wie [llm-wiki-karpathy](llm-wiki-karpathy.md), aber mit einer Tutorial-Perspektive und dem Fokus auf den Kompoundierungseffekt. Die Kompilierungs-Metapher ist prägnanter als jede bisherige Formulierung im Wiki.

## Verwandte Seiten

- [kompilierungs-metapher](../konzepte/kompilierungs-metapher.md)
- [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md)
- [rag-vs-wiki](../konzepte/rag-vs-wiki.md)
- [ingest-workflow](../konzepte/ingest-workflow.md)
- [lint-pruefung](../konzepte/lint-pruefung.md)
- [drei-ebenen-architektur](../konzepte/drei-ebenen-architektur.md)

---

[Wiki-Index](../index.md)
