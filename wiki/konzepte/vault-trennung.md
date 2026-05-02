---
date: 2026-04-23
type: konzept
tags: [konzept]
status: active
---

# Vault-Trennung

**Zusammenfassung**: Steph Angos Empfehlung: Persönliches, sauberes Vault (menschlich kuratiert) und separates "unordentliches Vault" für KI-generierte Inhalte — um Kontaminierung durch KI-Halluzinationen zu verhindern.
**Quellen**: clippings/Karpathy shares 'LLM Knowledge Base' architecture..., clippings/Karpathy's LLM Knowledge Bases The Post-Code AI Workflow.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Die Empfehlung

[Steph Ango](../personen/steph-ango.md) (kepano, Obsidian-Mitgründer) antwortete auf Karpathys Post mit einer konkreten architektonischen Empfehlung:

> Behalte dein persönliches Vault **sauber und hochwertig** — du weißt den Ursprung jedes Inhalts. Erstelle ein separates, "unordentliches Vault" für KI-generierte Inhalte.

(Quelle: clippings/Karpathy shares 'LLM Knowledge Base' architecture...)

## Das Problem, das es löst

Ohne Trennung vermischt sich:
- Menschlich kuratiertes, vertrauenswürdiges Wissen
- KI-synthetisiertes, potenziell halluziniertes Wissen

Das Ergebnis: Du weißt nicht mehr, welche Informationen du direkt aus einer Quelle kennst und welche das LLM "kompiliert" hat. Bei zukünftigen Abfragen ist das schwer zu unterscheiden.

Das VentureBeat-Artikel formuliert es so: Es ist das Wissensmanagement-Äquivalent von "Produktions- von Staging-Umgebung trennen."

## Die Zwei-Vault-Architektur

**Sauberes Vault** (persönlich):
- Menschlich geschriebene Notizen
- Direkt aus Quellen extrahierte Fakten
- Hohe Vertrauenswürdigkeit, niedriges Volumen
- Hoher Signal-Rausch-Abstand

**Agenten-Vault** (KI-betrieben):
- KI-kompilierte Wiki-Seiten
- Synthetisierte Querverweise
- Inkrementell durch LLM aktualisiert
- Höheres Volumen, geringere Vertrauenswürdigkeit per Claim

Wertvolle Artefakte aus dem Agenten-Vault können nach Prüfung ins saubere Vault übertragen werden.

## Verhältnis zum Kontaminierungsrisiko

Vault-Trennung ist eine der Haupt-Minderungsstrategien für das [kontaminierungsrisiko](kontaminierungsrisiko.md). Sie verhindert nicht, dass Halluzinationen ins Agenten-Vault gelangen — aber sie verhindert, dass sie unkontrolliert mit vertrauenswürdigem Wissen vermischt werden.

Weitere Schutzmaßnahmen: Quellenverweise auf jede Seite, regelmäßige [Lint-Prüfungen](lint-pruefung.md), [jdocmunch](../werkzeuge/jdocmunch.md)-basierter Zugriff statt Volllade.

## Praktische Umsetzung in diesem Projekt

In diesem Wiki-Projekt entspricht das Schema der CLAUDE.md dem "sauberen Vault"-Konzept durch:
- `raw/` und `clippings/` als unveränderliche Rohquellen (Quelle der Wahrheit)
- `wiki/` als KI-betriebener Bereich (entspricht dem "Agenten-Vault")
- Quellenangaben auf jeder Seite als Brücke zwischen den Ebenen

## Verwandte Seiten

- [kontaminierungsrisiko](kontaminierungsrisiko.md)
- [steph-ango](../personen/steph-ango.md)
- [drei-ebenen-architektur](drei-ebenen-architektur.md)
- [obsidian](../werkzeuge/obsidian.md)
- [lint-pruefung](lint-pruefung.md)

---

[Wiki-Index](../index.md)
