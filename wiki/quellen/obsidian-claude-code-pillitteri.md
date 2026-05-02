---
date: 2026-04-22
type: quelle
tags: [quelle]
status: active
---

# Obsidian + Claude Code: Das zweite Gehirn — Pasquale Pillitteri

**Zusammenfassung**: Blogartikel, der beschreibt, wie man Obsidian als persistenten Speicher für Claude Code nutzt — mit MECE-Ordnerstruktur, täglichem Workflow und strategischer Modellunabhängigkeit.
**Quellen**: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Überblick

Pasquale Pillitteri ist IT-Berater und beschreibt in diesem Artikel einen umfassenden Ansatz, [Obsidian](../werkzeuge/obsidian.md) als externes Gedächtnis für [Claude Code](../werkzeuge/claude-code.md) einzusetzen. Der Artikel geht über das reine Wiki-Konzept hinaus und behandelt Unternehmensanwendungen, Team-Sharing und strategische Aspekte.

**Quelle**: https://pasqualepillitteri.it/de/news/965/obsidian-claude-code-zweites-gehirn-persistenter-speicher
**Veröffentlicht**: 2026-04-17

## Kernthesen

### 1. Claude Code ist zustandslos — das ist das Problem

Claude Code verfügt standardmäßig über drei begrenzte Speicherquellen: die Projekt-`CLAUDE.md`, die globale `CLAUDE.md` und das Kontextfenster der aktiven Konversation. Bei Projekten, die Monate dauern, skaliert das nicht. Siehe [statelessness](../konzepte/statelessness.md). (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

### 2. Obsidian löst die Zustandslosigkeit

Statt den gesamten Kontext in den RAM zu laden, lässt man Claude selbstständig in einem gut organisierten Ordner suchen. Jede Markdown-Notiz wird zu einem persistenten Speicherfragment. Claude öffnet nur die für die aktuelle Frage relevanten Dateien — das reduziert den Token-Verbrauch um ca. **40%** und gibt der KI praktisch unbegrenztes Wissen auf der Festplatte. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

### 3. MECE-Struktur verhindert Chaos

Die [MECE-Ordnerstruktur](../konzepte/mece-prinzip.md) (Mutually Exclusive, Collectively Exhaustive) sorgt dafür, dass der Vault nicht zum Dateichaos wird. 7 Top-Level-Ordner mit klar abgegrenzten Bereichen. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

### 4. Täglicher Workflow mit zwei Befehlen

Ein [minimales tägliches Protokoll](../konzepte/taeglicher-workflow.md) mit Sitzungseröffnung („Guten Morgen") und Sitzungsabschluss („Für heute sind wir fertig") hält das System aktuell, ohne Aufwand. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

### 5. Modellunabhängigkeit als strategischer Vorteil

Der Vault besteht nur aus Markdown. Morgen kann man ihn mit Codex CLI, Gemini oder einem beliebigen Agenten öffnen. Die KI wird austauschbar wie eine Datenbank-Engine. Siehe [modellunabhaengigkeit](../konzepte/modellunabhaengigkeit.md). (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Häufige Fehler

Pillitteri warnt vor vier typischen Fallen:

1. **Zu granulare Struktur** — Am ersten Tag 40 Ordner erstellen ist der schnellste Weg zum Aufgeben. Start mit 6–7 Ordnern.
2. **Inkonsistentes Frontmatter** — Ohne einheitliche [YAML-Tags](../konzepte/yaml-frontmatter.md) findet Claude keine Muster.
3. **Riesige CLAUDE.md** — Die Datei soll erklären, wo was zu finden ist, nicht den gesamten Kontext enthalten. Bei >500 Zeilen liegt ein Architekturfehler vor.
4. **Wartung ignorieren** — Monatliche Prüfung (30 Min) ist Pflicht: Waisen kontrollieren, Tags konsolidieren, abgeschlossene Projekte archivieren.

(Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Wann Obsidian NICHT passt

Pillitteri sagt ehrlich, wo der Ansatz an Grenzen stößt: Er ersetzt kein CRM, kein Projektmanagement mit Abhängigkeiten, keine Transaktionsdatenbank. Schlecht geeignet für kollaborative Echtzeit-Workflows mit 20 Personen, strukturierte Daten mit Validierung oder komplexe API-Integrationen. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Der Compounding-Effekt

> „Die Wertkurve eines zweiten Gehirns verläuft nicht linear, sondern exponentiell."

Im ersten Monat baut man das Gerüst. Ab dem sechsten wird das System zum Multiplikator. Kontext ist „das am wenigsten replizierbare Asset überhaupt." (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Verwandte Seiten

- [pasquale-pillitteri](../personen/pasquale-pillitteri.md) — Autor dieses Artikels
- [obsidian](../werkzeuge/obsidian.md)
- [claude-code](../werkzeuge/claude-code.md)
- [statelessness](../konzepte/statelessness.md)
- [mece-prinzip](../konzepte/mece-prinzip.md)
- [taeglicher-workflow](../konzepte/taeglicher-workflow.md)
- [modellunabhaengigkeit](../konzepte/modellunabhaengigkeit.md)
- [yaml-frontmatter](../konzepte/yaml-frontmatter.md)
- [llm-wiki-muster](../konzepte/llm-wiki-muster.md)
- [chat-problem](../konzepte/chat-problem.md)

---

[Wiki-Index](../index.md)
