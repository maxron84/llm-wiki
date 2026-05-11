---
date: 2026-05-11
type: person
tags: [person]
status: active
---

# Geoffrey Huntley

**Zusammenfassung**: Indie Hacker und Softwareentwickler, bekannt für die Erfindung der Ralph-Schleife — eines deterministischen Agentenmusters, dessen $10,42/Stunden-Benchmark (Sonnet 4.5 auf GCP) zu einem Referenzwert für agentische Arbeit geworden ist.
**Quellen**: raw/ralph-claude-code-llm-wiki_metrik.md
**Zuletzt aktualisiert**: 2026-05-11

---

## Beitrag zur agentischen Praxis

Huntley prägte im Januar 2026 die sogenannte [Ralph-Schleife](../konzepte/ralph-schleife.md) — eine absichtlich primitive `while true`-Bash-Schleife, die einem Coding-Agenten denselben Prompt wiederholt vorlegt, bis dieser eine Completion-Promise emittiert. Das Muster wurde ab Februar 2026 breit diskutiert.

Sein Durchbruch war weniger die Mechanik (die ist trivial) als der konkrete Kostenbenchmark, den er damit lieferte:

> **$10,42 US$ pro Stunde** — Sonnet 4.5, Vollauslastung, GCP-Instanz, ohne Caching-Tricks.

Diese Zahl schlug in der agentischen Community wie eine Glocke ein. Y-Combinator-Teams bauten damit über Nacht sechs Repos parallel; ein Team lieferte einen 50.000-Dollar-Vertrag für 297 Dollar API-Kosten.

## Warum die Zahl wichtig ist

Huntley lieferte etwas, das vorher fehlte: eine **obere Schranke** für agentische Kostenplanung. Die Zahl hat zwei Eigenschaften:

1. **Konservativ** — Sie nimmt an, dass der Agent ohne Pause schreibt und denkt. In der Praxis warten viele Iterationen auf Tool-Returns, sodass die effektive Rate niedriger liegt.
2. **Modellunabhängig parametrisierbar** — Mit Opus 4.7 (~3× teurer) sind es ~$30/h, mit Haiku 4.5 (~⅓ von Sonnet) ~$3,50/h.

## Bedeutung für das LLM-Wiki

Der Artikel [ralph-claude-code-llm-wiki-metrik](../quellen/ralph-claude-code-llm-wiki-metrik.md) argumentiert, dass Huntleys Muster nicht nur ein Coding-Werkzeug ist, sondern die fehlende Automatisierungsschicht für Karpathys [LLM-Wiki-Muster](../konzepte/llm-wiki-muster.md): Die Ralph-Schleife übernimmt den Ingest-Workflow autonom, während das Wiki über das Dateisystem Zustand hält.

## Verwandte Seiten

- [ralph-schleife](../konzepte/ralph-schleife.md) — Das Muster, das er entwickelt hat
- [usd-pro-wiki-seite](../konzepte/usd-pro-wiki-seite.md) — Die Metrik, die auf seinem Benchmark aufbaut
- [wiki-ralph-sh](../werkzeuge/wiki-ralph-sh.md) — Konkrete Implementierung für das LLM-Wiki
- [ralph-claude-code-llm-wiki-metrik](../quellen/ralph-claude-code-llm-wiki-metrik.md) — Quellartikel
- [andrej-karpathy](andrej-karpathy.md)

---

[Wiki-Index](../index.md)
