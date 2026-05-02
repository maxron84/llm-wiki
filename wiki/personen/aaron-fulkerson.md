---
date: 2026-05-02
type: person
tags: [person, produktion]
status: active
---

# Aaron Fulkerson

**Zusammenfassung**: Mitgründer von MindTouch (Enterprise-Wiki, top-5 Open Source 2006–2010), jetzt Erbauer des „Exo"-Exokortex — der bisher ausgefeiltesten Produktionsimplementierung von Karpathys LLM-Wiki-Muster.
**Quellen**: clippings/Karpathy's Pattern for an "LLM Wiki" in Production.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Hintergrund

Aaron Fulkerson gründete MindTouch gemeinsam mit Steve Bjorg. MindTouch war von 2006 bis 2010 eines der fünf populärsten Open-Source-Projekte weltweit — ein Enterprise-Wiki mit WYSIWYG, Drag-and-Drop, RESTful API und headless-fähigem Design bevor der Begriff existierte. Die Codebasis treibt heute noch [LibreTexts](https://libretexts.org/) und andere hochfrequentierte Plattformen. MindTouch wurde an NICE Systems verkauft.

> „Twenty years later, I'm building a wiki again. The difference is that this time, I'm not writing the wiki." (Quelle: clippings/Karpathy's Pattern for an "LLM Wiki" in Production.md)

## Warum seine Perspektive besonders ist

Zwei Jahrzehnte Erfahrung mit Enterprise-Wissensmanagement machen Fulkersons Beobachtungen außergewöhnlich wertvoll:

- Er weiß, wie Wikis sterben — unter dem Gewicht ihres eigenen Bookkeeping
- Er kennt das Enterprise-Skalierungsproblem aus praktischer Anschauung
- Er baut das Gegenteil: ein System, das sich selbst pflegt

## Das Exo-System

Fulkerson baute „Exo" (kurz für Exokortex) ab Februar 2026 — nach dem Claude-Code-Update mit Opus 4.6, 1M-Token-Kontextfenster, MCP-Servern und Hooks. Nach zwei Monaten Produktionsbetrieb:

- 26 Skills, 14 MCP-Server, 8 Hooks
- Obsidian Vault mit hunderten Dateien
- Drei formale Lernschleifen

Mehr: [exokortex](../konzepte/exokortex.md), [lernschleifen](../konzepte/lernschleifen.md)

## Mustersammlung

Fulkerson veröffentlichte seinen Erfahrungsschatz als Implementation-Blueprints auf GitHub:
[github.com/AaronRoeF/claude-code-patterns](https://github.com/AaronRoeF/claude-code-patterns) — 153 Muster, davon 13 spezifisch zum Wiki-Pattern.

## Einordnung im Wiki

Fulkerson ist die **reichhaltigste Praxisquelle** in diesem Wiki. Während andere Quellen das Muster beschreiben oder erklären, zeigt er es als produktives Betriebssystem. Seine wichtigste Beobachtung: Theorie und Praxis des LLM-Wiki-Musters klaffen an fünf konkreten Stellen auseinander — und alle fünf sind lösbar.

## Verwandte Seiten

- [llm-wiki-fulkerson-exo](../quellen/llm-wiki-fulkerson-exo.md) — Hauptquellartikel
- [exokortex](../konzepte/exokortex.md)
- [lernschleifen](../konzepte/lernschleifen.md)
- [andrej-karpathy](andrej-karpathy.md)

---

[Wiki-Index](../index.md)
