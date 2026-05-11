---
date: 2026-04-23
type: konzept
tags: [konzept]
status: active
---

# Kontaminierungsrisiko

**Zusammenfassung**: Das zentrale Kritikpunkt am LLM-Wiki-Muster: Wenn das LLM beim Aufbau des Wikis halluziniert, werden diese Fehler als persistente "Fakten" eingebacken und können sich über Querverweise ausbreiten — im Gegensatz zu RAG, wo Fehler lokal bleiben.
**Quellen**: clippings/Andrej Karpathy's LLM Wiki Create your own knowledge base.md, clippings/Karpathy shares 'LLM Knowledge Base' architecture..., clippings/Karpathy's LLM Knowledge Bases The Post-Code AI Workflow.md, clippings/The LLM Wiki How Karpathy's AI Memory Idea Sparked a Movement.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Das Problem

Bei klassischem RAG ist ein Fehler lokal: Eine falsche Antwort bleibt eine falsche Antwort in einem Chat-Verlauf.

Beim LLM-Wiki ist eine Halluzination **persistent**: Wenn das LLM beim Ingest einer Quelle fälschlicherweise eine Verbindung zwischen zwei Konzepten herstellt, lebt dieser falsche Link im Wiki. Bei der nächsten Abfrage zieht das System diesen Link heran. Wenn das Wiki wächst, kann eine kleine Fehlinterpretation durch Querverweise auf anderen Seiten propagieren.

> "Because the LLM summarizes and compresses sources into wiki pages, there's a risk of hallucinations getting baked in as *facts*." — Urvil Joshi (Quelle: clippings/Andrej Karpathy's LLM Wiki Create your own knowledge base.md)

## Warum es beim LLM-Wiki stärker ist

| | RAG | LLM-Wiki |
|---|---|---|
| Fehlerlokalität | Lokal (eine Antwort) | Persistent (Wiki-Seite) |
| Ausbreitung | Keine | Über Querverweise möglich |
| Erkennbarkeit | Leicht (Quellen direkt zitiert) | Schwerer (Synthese-Schicht) |
| Korrektur | Automatisch (bei nächster Abfrage) | Manuell oder durch Lint |

## Minderungsstrategien

### 1. Quellenverweise auf jeder Seite
Jede faktische Behauptung sollte ihre Quelldatei referenzieren (Format: `(Quelle: dateiname.md)`). So ist jeder Claim zur Rohdatei rückverfolgbar. Das ist die wichtigste Schutzmaßnahme.

### 2. Regelmäßige Lint-Prüfungen
Der [Lint-Workflow](lint-pruefung.md) prüft auf Widersprüche zwischen Seiten — also genau das Symptom, das sich aus Halluzinationen ergibt. Empfehlung: nach jeweils 5–10 neuen Quellen oder einmal im Monat.

### 3. Vault-Trennung (Steph Ango)
Persönliches Vault sauber halten, KI-generiertes Wissen in separatem Vault. So bleibt klar, welches Wissen menschlich kuratiert ist. Mehr: [vault-trennung](vault-trennung.md)

### 4. Rohdateien als unveränderliche Quelle der Wahrheit
Das Drei-Ebenen-Muster sorgt dafür, dass `raw/` und `clippings/` niemals verändert werden. Bei Zweifeln kann das Wiki aus den Rohdaten neu kompiliert werden. Mehr: [drei-ebenen-architektur](drei-ebenen-architektur.md)

### 5. Spot-Checking
Wichtige Wiki-Seiten gegen die Rohdateien prüfen — besonders vor wichtigen Entscheidungen auf Basis des Wikis.

### 6. Constrained Pipelines (Community-Konsens)

Die Community entwickelte nach Karpathys Gist ein Set von vier Absicherungsmechanismen, die zusammen als „Constrained Pipeline" funktionieren: (Quelle: clippings/The LLM Wiki How Karpathy's AI Memory Idea Sparked a Movement.md)

- **Strenge Schemas** — erzwingen Formatierungs- und Organisationsregeln, damit der LLM nicht „kreativ" wird
- **Confidence Scoring** — KI flaggt unsichere Outputs statt zu raten; ermöglicht gezielte menschliche Prüfung
- **Human-in-the-Loop** — Bei Widersprüchen, die das System nicht lösen kann: To-do-Liste für Menschen statt schlechte automatische Entscheidung
- **Source Linking** — Wiki zeigt immer auf Originaldokumente zurück; verhindert Drift von der Quelle

Das Ziel verschob sich von „perfekte KI" zu „perfektes System um imperfekte KI": Co-Pilot für 90% der Arbeit, Mensch für die 10% mit hohem Risiko.

### 7. Trust Score und Verfallslogik (LLM Wiki v2)

[LLM Wiki v2](llm-wiki-v2.md) fügt einen **Trust Score pro Eintrag** hinzu, der über Zeit verfällt, wenn keine Verifikation stattfindet. Erzwingt periodische Überprüfung systematisch, statt auf manuelle Disziplin zu vertrauen.

### 8. Halluzinations-Kosten in der Wirtschaftlichkeitsrechnung

Die [US$/WP-Metrik](usd-pro-wiki-seite.md) zählt produzierte Seiten — nicht korrekte Seiten. Das ist ihr blinder Fleck: Wenn 5 % der ingestierten Behauptungen falsch sind und eine teure Halluzination einen Mitarbeiter eine Stunde kostet, kippt die Wirtschaftlichkeitsrechnung.

Quantifiziert: Ein Stundensatz von $80 bei einer Stunde verlorener Arbeitszeit pro Quartal durch Halluzinationen entspricht $320/Jahr — das kann bei kleinen Wikis die gesamten Token-Kosten übersteigen.

Abhilfe: Confidence Scores (LLM Wiki v2), harte Source-Linking-Regel (eine Behauptung = eine Quelldatei), regelmäßiger Lint. (Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

## Karpathys Einschätzung

Karpathy erwähnt Lint-Checks als direkte Reaktion auf das Kontaminierungsrisiko. Die Community (besonders [Steph Ango](../personen/steph-ango.md)) hob das Thema als den wichtigsten offenen Punkt des Musters hervor.

Das Konsens in der Community: Das Risiko ist real, aber handhabbar — wenn man es aktiv adressiert statt ignoriert.

## Verwandte Seiten

- [lint-pruefung](lint-pruefung.md)
- [vault-trennung](vault-trennung.md)
- [steph-ango](../personen/steph-ango.md)
- [drei-ebenen-architektur](drei-ebenen-architektur.md)
- [rag-vs-wiki](rag-vs-wiki.md)
- [llm-wiki-v2](llm-wiki-v2.md) — Trust Score und automatische Verfallslogik
- [llm-wiki-tecadrise](../quellen/llm-wiki-tecadrise.md) — Constrained Pipelines

---

[Wiki-Index](../index.md)
