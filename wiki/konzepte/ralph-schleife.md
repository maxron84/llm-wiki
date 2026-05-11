---
date: 2026-05-11
type: konzept
tags: [konzept, werkzeug, automatisierung]
status: active
---

# Ralph-Schleife

**Zusammenfassung**: Geoffrey Huntleys deterministisches Agentenmuster — eine primitive `while true`-Bash-Schleife, die einem Coding-Agenten denselben Prompt wiederholt vorlegt, bis dieser eine Completion-Promise emittiert. Benchmark: $10,42/h mit Sonnet 4.5. Für das LLM-Wiki ist es die fehlende Ingest-Automatisierungsschicht.
**Quellen**: raw/ralph-claude-code-llm-wiki_metrik.md
**Zuletzt aktualisiert**: 2026-05-11

---

## Herkunft und Name

Die Schleife wurde im Januar 2026 von [Geoffrey Huntley](../personen/geoffrey-huntley.md) geprägt und ab Februar 2026 breit diskutiert. Der Name kommt von Ralph Wiggum aus *The Simpsons* — bewusst gewählt, weil das Muster absichtlich naiv und deterministisch ist: keine Intelligenz im Steuerkode, alle Intelligenz im Agenten.

## Mechanik

```bash
while true; do
  claude -p "$PROMPT" --max-turns 30
  # Schleife läuft weiter, bis der Agent
  # <promise>COMPLETE</promise> emittiert
done
```

Der Agent muss aktiv aus der Schleife herausbrechen — durch eine im Prompt definierte Completion-Promise. Die Schleife selbst ist passiv und zustandslos.

## Drei Kerneigenschaften

**1. Zustandslose Iterationen** — Jeder Durchlauf startet mit frischem Kontext. Das vermeidet die „Dumb Zone" jenseits ~60–70 % Kontextfenster-Auslastung, wo Qualität schleichend nachlässt.

**2. Persistenz über Dateisystem** — Zustand lebt nicht im Kontextfenster, sondern in Dateien. Das LLM kann bei jedem Durchgang den aktuellen Stand lesen, ohne vorherige Iterationen im Kontext zu haben.

**3. Deterministische Steuerung** — Keine probabilistische Entscheidungslogik im Steuerkode. Die Schleife macht genau eine Sache: einen Prompt vorlegen, auf Completion warten, wiederholen.

## Kostenbenchmark

| Modell | $/Stunde (Vollauslastung) |
|---|---|
| Haiku 4.5 | ~$3,50 |
| Sonnet 4.5 | **$10,42** *(Huntleys Originalwert)* |
| Sonnet 4.6 | ~$6–8 |
| Opus 4.7 | ~$30 |

Die Zahlen sind **obere Schranken** bei vollem Token-Burn ohne Caching. In der Praxis liegt die effektive Rate niedriger, weil Iterationen auf Tool-Returns warten.

Y-Combinator-Teams bauten mit dieser Schleife über Nacht sechs Repos parallel; ein Team lieferte einen $50.000-Vertrag für $297 API-Kosten.

## Ralph als LLM-Wiki-Ingest-Motor

Das Entscheidende: Huntleys Architektur — zustandslose Iterationen, frischer Kontext, Persistenz über Dateisystem — ist exakt das, was Karpathys [Ingest-Workflow](ingest-workflow.md) implizit voraussetzt, aber nicht operationalisiert.

> "Without a Ralph-style loop, Karpathy's pattern is only half-implemented. The other half is manual triggering — and that's human maintenance, exactly what the wiki is supposed to eliminate." (Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

Konkret: Ein Ralph-Loop, der das `inbox/`-Verzeichnis pollt und neue Quellen autonom ins Wiki einbaut, macht das LLM-Wiki von einem halbmanuellen zu einem vollautomatischen System. Mehr: [wiki-ralph-sh](../werkzeuge/wiki-ralph-sh.md)

## Abgrenzung

Die Ralph-Schleife ist **keine** KI-Planung — die Planung liegt im Agenten. Die Schleife ist der dumbste mögliche Scheduler. Das ist ihr Vorteil: sie versagt nicht auf interessante Art, sie ist deterministisch, und sie skaliert linear mit Token-Kosten.

## Verwandte Seiten

- [geoffrey-huntley](../personen/geoffrey-huntley.md) — Entwickler des Musters
- [wiki-ralph-sh](../werkzeuge/wiki-ralph-sh.md) — Implementierung für das LLM-Wiki
- [usd-pro-wiki-seite](usd-pro-wiki-seite.md) — Kostmetrik, die auf dem Benchmark aufbaut
- [ingest-workflow](ingest-workflow.md) — Was die Schleife automatisiert
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md) — Einbettung in die Gesamtarchitektur
- [statelessness](statelessness.md) — Verwandtes Grundproblem
- [ralph-claude-code-llm-wiki-metrik](../quellen/ralph-claude-code-llm-wiki-metrik.md) — Quellartikel

---

[Wiki-Index](../index.md)
