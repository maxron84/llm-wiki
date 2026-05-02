---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# MECE-Prinzip für Vault-Strukturen

**Zusammenfassung**: Das MECE-Prinzip (Mutually Exclusive, Collectively Exhaustive) aus der Strategieberatung, angewandt auf die Ordnerstruktur eines Obsidian-Vaults — keine Überlappungen, keine Lücken.
**Quellen**: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Was bedeutet MECE?

MECE steht für **Mutually Exclusive, Collectively Exhaustive** — ein Prinzip aus der strategischen Beratung (bekannt durch McKinsey). Übersetzt auf Vault-Strukturen:

- **Gegenseitige Ausschließlichkeit**: Die Ordner dürfen sich nicht überlappen. Jede Datei hat genau einen eindeutigen Platz.
- **Kollektive Vollständigkeit**: Zusammen müssen die Ordner deinen gesamten Wissensbereich abdecken. Keine Lücken.

**Faustregel**: Wenn du im Zweifel bist, wo eine Datei hingehört, ist die Struktur falsch und muss überarbeitet werden. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Empfohlene Ordnerstruktur

Pillitteri schlägt 7 Top-Level-Ordner vor als minimale Basis:

| Ordner | Zweck |
|---|---|
| **/Business** | Aktive Unternehmen, Projekte, Vertriebsstrategien, SOPs |
| **/Context** | Wer du bist: Tonfall, Positionierung, Werte, Zielgruppe |
| **/Daily** | Tägliche Notizen (siehe [taeglicher-workflow](taeglicher-workflow.md)) |
| **/Library** | Referenzwissen: Frameworks, Marktforschung, Meeting-Protokolle |
| **/Projects** | Projekte mit Frist — nicht für den Langzeitspeicher |
| **/Skills** | Wiederverwendbare Anweisungen für die KI |
| **/Team** | Profile von Mitarbeitenden und spezialisierten KI-Agenten |

(Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Abgrenzung zur Wiki-Struktur

Dieses Wiki verwendet eine andere, einfachere Struktur (`raw/`, `clippings/`, `wiki/`), die dem [llm-wiki-muster](llm-wiki-muster.md) von [andrej-karpathy](../personen/andrej-karpathy.md) folgt. Die MECE-Struktur eignet sich besonders für **persönliche Produktivitätssysteme**, bei denen der Vault den gesamten Arbeitsalltag abdeckt — nicht nur eine einzelne Wissensdomain.

Beide Ansätze teilen die Grundidee: **klare Trennung der Bereiche**, damit die KI weiß, wo sie suchen muss.

## Häufiger Fehler: Zu granular starten

Am ersten Tag 40 Ordner zu erstellen, ist der schnellste Weg, das System aufzugeben. Pillitteris Empfehlung: Mit 6–7 Ordnern starten und erst neue hinzufügen, wenn mindestens 30 Notizen eine neue Kategorie rechtfertigen. MECE entsteht aus der Realität, nicht aus der Theorie. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Verwandte Seiten

- [obsidian-claude-code-pillitteri](../quellen/obsidian-claude-code-pillitteri.md)
- [obsidian](../werkzeuge/obsidian.md)
- [drei-ebenen-architektur](drei-ebenen-architektur.md)
- [taeglicher-workflow](taeglicher-workflow.md)

---

[Wiki-Index](../index.md)
