---
date: 2026-05-11
type: konzept
tags: [konzept, metrik, kosten]
status: active
---

# US$/WP — Dollar pro Wiki-Seite

**Zusammenfassung**: Eine Stückkostenmetrik für LLM-Wikis: (Input- + Output-Token-Kosten) geteilt durch die Anzahl substanziell neu entstandener oder geänderter Wiki-Seiten. Lifetime-Wert bei Sonnet 4.6 in einer Ralph-Schleife: ~$0,42/WP. Die Metrik verschiebt die Diskussion von „viel oder wenig" zu messbaren ROI-Entscheidungen.
**Quellen**: raw/ralph-claude-code-llm-wiki_metrik.md
**Zuletzt aktualisiert**: 2026-05-11

---

## Definition

> **US$/WP = (Input-Token-Kosten + Output-Token-Kosten) / Anzahl neu entstandener oder substanziell geänderter Wiki-Seiten**

„Substanziell geändert" = ≥ 100 Wörter Delta gegenüber der Vorversion, prüfbar via `git diff --stat`. (Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

## Warum diese Metrik?

Bisherige Diskussionen nutzten entweder Stundenraten ([Ralph-Schleife](ralph-schleife.md): $10,42/h) oder Größenangaben (Karpathy: „~100 Artikel, ~400.000 Wörter"). Keine dieser Einheiten beantwortet die Frage: **Was kostet ein Wiki, das man tatsächlich betreibt?**

US$/WP macht drei Dinge:
1. Sie ist vergleichbar quer über Modelle und Projektgrößen
2. Sie amortisiert Lint-Kosten (die eine dauerhaft laufende Kostenstelle sind)
3. Sie ermöglicht ROI-Entscheidungen: „Lohnt sich das Wiki für diesen Use Case?"

## Token-Grundlage pro Ingest-Iteration

| Komponente | Input-Token | Output-Token |
|---|---|---|
| CLAUDE.md (Schema) | 2.500 | — |
| Quelle (~5.000 Wörter) | 7.500 | — |
| Index + 12 betroffene Seiten | 18.000 | — |
| LLM-Reasoning + Tool-Calls | 4.000 | 6.000 |
| Updates auf 10–15 Seiten | — | 8.500 |
| Log-Eintrag | — | 500 |
| **Summe** | **32.000** | **15.000** |

(Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

## Rohe Token-Kosten pro Iteration und Seite

| Modell | $/Iteration | Seiten/Iteration | $/WP (roh) |
|---|---:|---:|---:|
| Haiku 4.5 | $0,11 | 7,5 | **$0,014** |
| Sonnet 4.6 | $0,32 | 7,5 | **$0,043** |
| Opus 4.7 | $1,61 | 7,5 | **$0,214** |

## Lifetime-$/WP (inkl. Lint-Amortisierung)

Wöchentlicher Lint-Pass über 100 Artikel kostet bei Sonnet 4.6 ~$1,78/Woche (~$85/Jahr). Amortisiert auf 200 stabile Wiki-Seiten über ein Jahr ergibt das einen Lifetime-Aufschlag von ~$0,43/WP.

**Ehrlichste Zahl, die vertretbar ist:**

| Modell | $/WP (Lifetime) |
|---|---:|
| Haiku 4.5 | ~$0,07 |
| **Sonnet 4.6** | **~$0,42** |
| Opus 4.7 | ~$1,80 |

> „0,42 US$ pro substanziell gepflegter Wiki-Seite über ein Jahr Lebenszyklus, bei Sonnet 4.6 in einer Ralph-Schleife." (Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

## Drei durchgerechnete Szenarien

### Szenario A — Persönliches Forschungs-Wiki (Karpathys Use-Case)
100 Artikel, ~70 Quellen, 3 Monate, Sonnet 4.6:

| Posten | Kosten |
|---|---:|
| Initiale Ingestion (70 × $0,32) | $22,40 |
| Lint × 12 Wochen (12 × $1,78) | $21,36 |
| Query-back-fill (~50 × $0,15) | $7,50 |
| **Gesamt** | **$51,26** |

*Weniger als ein Monatsabo gängiger Notiz-Tools.*

### Szenario B — Kleines Team-Wiki
500 Seiten, 1.200 Quellen, 6 Monate, Sonnet 4.6 → **~$658 gesamt / ~$1,32/WP**

Vergleichbar mit einer Confluence-Seat-Lizenz — aber die Wiki-Wartung entfällt als menschlicher Aufwand komplett.

### Szenario C — Enterprise-Onboarding-Wiki
3.000 Seiten, 5.000 Quellen, 1 Jahr, Opus 4.7, Human-in-the-Loop → **~$19.870/Jahr / ~$6,60/WP**

Verhältnis zu einem menschlichen Knowledge-Manager (≥$80.000/Jahr fully loaded): ca. 1:4. (Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

## Wo das Modell zerfällt

**Halluzinations-Kosten** — US$/WP zählt produzierte Seiten, nicht korrekte Seiten. Wenn 5 % der Behauptungen falsch sind und eine teure Halluzination einen Mitarbeiter eine Stunde kostet, kippt die Rechnung. Abhilfe: Confidence Scores, Source Linking auf jeder Seite. Mehr: [kontaminierungsrisiko](kontaminierungsrisiko.md)

**Context-Window-Klippen** — Ab ~200.000 Wörtern passt der Index nicht mehr in den Schemakontext. Dann sind hybride Retrieval-Strategien nötig, die die Stückkosten nicht-linear erhöhen. Mehr: [skalierungsgrenzen](skalierungsgrenzen.md)

**Modellpreisverfall** — Die Zahlen sind auf Mai 2026 geeicht. Anthropic hat Sonnet zwischen 4.5 und 4.6 bereits im Preis halbiert. Die *Form* der Rechnung bleibt stabil; die Absolutwerte werden in 12 Monaten um Faktor 3–5 zu hoch sein.

## Drei Konsequenzen

1. **Wiki als Investitionsentscheidung** — Nicht „ist das ein gutes Konzept?", sondern „lohnt sich die Wartung beim Marginalwert meiner Wiederabfragen?"

2. **Ralph wird zur Standardarchitektur** — 0,42 US$/WP ist nur erreichbar, wenn Ingest-Operationen automatisiert ablaufen. Manuelle Einzeloperationen haben denselben Token-Verbrauch plus die eigene Arbeitszeit. Die [Ralph-Schleife](ralph-schleife.md) ist damit nicht optional, sondern Voraussetzung für Wirtschaftlichkeit.

3. **Modellwahl als Strategie** — Haiku schreibt für $0,07/WP, aber mit niedrigerer Synthese-Qualität. Opus für $1,80/WP, aber mit besserer Cross-Reference-Qualität. Sonnet 4.6 ist der vermutliche Sweet Spot — aber das ist eine Hypothese, die erst empirisch klärbar ist.

## Verwandte Seiten

- [ralph-schleife](ralph-schleife.md) — Benchmark-Quelle und Voraussetzung für die Metrik
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md) — Prompt Caching als Kostenhebel
- [skalierungsgrenzen](skalierungsgrenzen.md) — Context-Window-Klippe ab ~200K Wörtern
- [kontaminierungsrisiko](kontaminierungsrisiko.md) — Der blinde Fleck der Metrik
- [enterprise-skalierung](enterprise-skalierung.md) — Szenario C (Enterprise-Onboarding)
- [token-sparen](../anleitungen/token-sparen.md) — Praktische Strategien zur Kostensenkung
- [ralph-claude-code-llm-wiki-metrik](../quellen/ralph-claude-code-llm-wiki-metrik.md) — Quellartikel

---

[Wiki-Index](../index.md)
