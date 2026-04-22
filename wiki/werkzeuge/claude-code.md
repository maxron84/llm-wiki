---
date: 2026-04-22
type: werkzeug
tags: [werkzeug]
status: active
---

# Claude Code / Claude Cowork

**Zusammenfassung**: Anthropics KI-Agent, der lokal mit Dateien arbeitet — im LLM-Wiki-Muster der „Programmierer", der das Wiki schreibt und pflegt.
**Quellen**: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md, clippings/llm-wiki.md, clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Was ist Claude Code?

Claude Code ist ein KI-Agent von Anthropic, der nativ mit lokalen Dateien arbeitet. Er kann Ordner lesen, Dateien schreiben, Strings suchen und Verknüpfungen erstellen. Man startet ihn einfach im Terminal innerhalb des Projektordners.

**Claude Cowork** ist die Desktop-App-Variante — einfacher zu bedienen für Menschen, die nicht aus der Programmierung kommen. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Das Statelessness-Problem

Claude Code ist standardmäßig [[statelessness|zustandslos]]. Jede Sitzung beginnt bei null — die KI erinnert sich an nichts. Die drei eingebauten Speicherquellen (Projekt-CLAUDE.md, globale CLAUDE.md, Kontextfenster) reichen für Projekte über Monate nicht aus. [[obsidian|Obsidian]] als externer Speicher löst dieses Problem: Claude sucht in einem organisierten Ordner statt alles im RAM zu halten. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Rolle im LLM-Wiki

Im [[llm-wiki-muster]] ist Claude Code der „Programmierer":

- **Liest** Rohquellen und versteht den Inhalt
- **Schreibt** Wiki-Seiten in Markdown
- **Verknüpft** verwandte Konzepte über Wiki-Links
- **Aktualisiert** bestehende Seiten, wenn neue Quellen hinzukommen
- **Prüft** das Wiki auf Widersprüche und fehlende Links ([[lint-pruefung]])

Die Steuerung erfolgt über das Schema in der `CLAUDE.md`-Datei — der dritten Ebene der [[drei-ebenen-architektur]].

## Modellwahl

Für Wiki-Aufgaben muss man nicht das stärkste Modell nehmen. Mario bemerkt im Tutorial, dass auch ein schwächeres Modell wie Claude Sonnet für diese Art von Aufgaben gut funktioniert — wenn man Token sparen möchte. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Claude Code vs. Obsidian

Die Rollen sind klar getrennt:

| Werkzeug | Rolle |
|---|---|
| [[claude-code\|Claude Code]] | Schreibt und pflegt das Wiki |
| [[obsidian\|Obsidian]] | Zeigt das Wiki an, ermöglicht Navigation |

Claude Code braucht weder ein Obsidian-Plugin noch einen MCP-Server. Es genügt, das Terminal im Ordner des Vaults zu öffnen. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Verwandte Seiten

- [[llm-wiki-muster]]
- [[drei-ebenen-architektur]]
- [[obsidian]]
- [[statelessness]]
- [[modellunabhaengigkeit]]
- [[llm-wiki-tutorial-mit-mario]]
- [[obsidian-claude-code-pillitteri]]

---

[Zurück zum Index](../index.md)
