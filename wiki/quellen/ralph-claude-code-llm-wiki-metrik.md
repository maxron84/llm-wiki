---
date: 2026-05-11
type: quelle
tags: [quelle, kosten, metrik, automatisierung]
status: active
---

# Quelle: Die fehlende Metrik — Was kostet eine Seite im LLM Wiki?

**Zusammenfassung**: Analyse, die Karpathys LLM-Wiki-Muster mit Geoffrey Huntleys Ralph-Schleife verbindet und die Stückkostenmetrik US$/WP einführt. Kernaussage: Sonnet 4.6 in einer Ralph-Schleife kostet ~$0,42 pro substanziell gepflegter Wiki-Seite über ein Jahr.
**Quellen**: raw/ralph-claude-code-llm-wiki_metrik.md
**Zuletzt aktualisiert**: 2026-05-11

---

## Bibliografische Angaben

- **Datei**: `raw/ralph-claude-code-llm-wiki_metrik.md`
- **Typ**: Analyse / Essay
- **Datum**: Mai 2026
- **Hinweis**: Autor anonym; Berechnungen explizit als Diskussionsgrundlage deklariert, basierend auf Modellpreisen Mai 2026 und Karpathys eigenen Größenangaben.

---

## Das zentrale Argument

Die Quelle beobachtet ein Loch in der öffentlichen Diskussion: Karpathy nennt seine Wiki-Größe in Wörtern, Huntley nennt Stundenraten — aber niemand hat bisher Stückkosten berechnet. Die Einführung von **[US$/WP](../konzepte/usd-pro-wiki-seite.md)** (Dollar pro Wiki-Seite) schließt diese Lücke.

---

## Haupterkenntnisse

### 1. Ralph und Karpathy gehören zusammen

Die [Ralph-Schleife](../konzepte/ralph-schleife.md) und Karpathys LLM-Wiki-Muster werden in der Community parallel diskutiert, aber selten verbunden. Die Quelle argumentiert: Ralph ist die fehlende Automatisierungsschicht des LLM-Wikis.

> "Ralph ist nicht nur ein Coding-Muster — es ist die effizienteste Ingest-Engine, die das LLM Wiki je hatte." (Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

Ohne Ralph-artige Schleife muss jede Ingest-Operation manuell angestoßen werden — das ist die menschliche Wartung, die das Wiki eigentlich ersetzen soll.

### 2. Ingest ist teurer als angenommen

Drei Faktoren, die unter pauschaler Token-Kalkulation verschwinden:

- **Ingest ist nicht atomar**: Eine Quelle berührt 10–15 Seiten, jede mit Lese- und Schreiboperationen
- **Lint ist laufende Kostenstelle**: Wöchentlicher Lint bei 100 Artikeln kostet ~$1,78/Woche bei Sonnet 4.6 (~$85/Jahr)
- **Query-back-fill**: Gute Antworten zurücks ins Wiki schreiben ist auch eine Schreiboperation

Das Wiki ist nicht die billige Alternative zu RAG. Es verschiebt Kosten von Query-Zeit in Ingest-Zeit.

### 3. Die Zahlen

Token-Grundlage pro durchschnittlicher Ingest-Iteration: ~32.000 Input + 15.000 Output-Token.

| Modell | $/WP (roh) | $/WP (Lifetime inkl. Lint) |
|---|---:|---:|
| Haiku 4.5 | $0,014 | ~$0,07 |
| Sonnet 4.6 | $0,043 | **~$0,42** |
| Opus 4.7 | $0,214 | ~$1,80 |

### 4. Drei Szenarien

| Szenario | Kosten | $/WP |
|---|---:|---:|
| Persönliches Wiki (100 Seiten, 3 Monate, Sonnet) | $51 | — |
| Team-Wiki (500 Seiten, 6 Monate, Sonnet) | $658 | $1,32 |
| Enterprise (3.000 Seiten, 1 Jahr, Opus) | $19.870 | $6,60 |

### 5. Wo das Modell zerfällt

- **Halluzinations-Kosten** fehlen in der Metrik — produzierte ≠ korrekte Seiten
- **Context-Window-Klippe** bei ~200.000 Wörtern → hybrides Retrieval nötig, nicht-lineare Kostensteigerung
- **Modellpreisverfall** — Absolutwerte veralten, die Rechenform bleibt

### 6. Drei Folgerungen

1. Wikis als Investitionsentscheidung (ROI, nicht Lifestyle)
2. Ralph-Schleife als Voraussetzung für Wirtschaftlichkeit, nicht als Option
3. Modellwahl als Strategie: Haiku ($0,07/WP) vs. Sonnet ($0,42/WP) vs. Opus ($1,80/WP)

---

## Wichtige Zitate

> "The LLM Wiki doesn't eliminate token cost. It moves it." (J. Gravelle, zitiert zur Kontextualisierung)

> "0,42 US$ pro substanziell gepflegter Wiki-Seite über ein Jahr Lebenszyklus, bei Sonnet 4.6 in einer Ralph-Schleife."

> "US$/WP zur LLM-Wiki-Diskussion ist das, was Cost-per-Click zur Online-Werbung war: keine besonders tiefe Idee, aber eine, die Streitgespräche von 'gut/schlecht' zu 'bei welchen Stückkosten lohnt es sich' verschiebt."

---

## Verwandte Seiten

- [usd-pro-wiki-seite](../konzepte/usd-pro-wiki-seite.md) — Die eingeführte Metrik
- [ralph-schleife](../konzepte/ralph-schleife.md) — Das Automatisierungsmuster
- [geoffrey-huntley](../personen/geoffrey-huntley.md) — Erfinder der Ralph-Schleife
- [wiki-ralph-sh](../werkzeuge/wiki-ralph-sh.md) — Konkrete Implementierung
- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md) — Context-Window-Klippe
- [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md) — Halluzinations-Kostenfaktor
- [enterprise-skalierung](../konzepte/enterprise-skalierung.md) — Szenario C
- [token-sparen](../anleitungen/token-sparen.md) — Praktische Gegenmassnahmen
- [fortgeschrittene-architektur](../konzepte/fortgeschrittene-architektur.md)

---

[Wiki-Index](../index.md)
