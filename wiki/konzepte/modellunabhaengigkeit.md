---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Modellunabhängigkeit

**Zusammenfassung**: Ein Obsidian-Vault aus Markdown-Dateien macht unabhängig vom KI-Anbieter — die KI wird austauschbar, die Daten bleiben.
**Quellen**: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Die Kernidee

Wenn deine Arbeit innerhalb von Claude eingeschlossen ist, bist du ein Gefangener von Anthropic. Wenn Claude morgen ausfällt, die Preise anzieht oder ein Konkurrent ein überlegenes Modell veröffentlicht, bist du blockiert. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

[[obsidian|Obsidian]] durchbricht diese Abhängigkeit. Der Vault besteht nur aus **Markdown-Dateien** — dem universellsten Textformat. Morgen kannst du denselben Vault mit einem beliebigen Agenten öffnen:

- **Codex CLI** (OpenAI)
- **Gemini CLI** (Google)
- **Aider**
- **Cursor** (im Agent-Modus)
- Jeder zukünftige Agent, der Dateien lesen kann

Du änderst nur den Namen der Schema-Datei (`CLAUDE.md` → `AGENTS.md` → `GEMINI.md`) und das System funktioniert identisch. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Die Datenbank-Analogie

> Die KI wird austauschbar, so wie eine Datenbank für eine Anwendung: Du tauschst die Engine aus, nicht die Daten.

Das Schema (die dritte Ebene der [[drei-ebenen-architektur]]) ist die Schnittstelle. Die Rohquellen und das Wiki bleiben unverändert, egal welches Modell die Arbeit macht.

## Strategischer Vorteil: Risikodiversifizierung

Für Unternehmen ist das kein philosophisches Detail — es ist **Risikomanagement**:

- Wenn die Unternehmensleistung perfekt mit einem einzelnen Modell korreliert, bedeutet jede Ausfallzeit einen Stillstand
- Mit einem portablen Vault kann man im laufenden Betrieb wechseln
- 80% der Zeit nutzt man das bevorzugte Modell, aber man hat ein Backup bereitstehen
- Die **Kontinuität wird von den Daten gewährleistet**, nicht vom Anbieter

(Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Bezug zum LLM-Wiki

Das [[llm-wiki-muster]] profitiert direkt von dieser Eigenschaft. Karpathy beschreibt das Wiki als „ein Git-Repository aus Markdown-Dateien" — versionierbar, portabel, zukunftssicher. Die Wahl des LLM-Agenten wird zu einer taktischen, nicht strategischen Entscheidung. (Quelle: clippings/llm-wiki.md)

## Verwandte Seiten

- [[obsidian-claude-code-pillitteri]]
- [[obsidian]]
- [[claude-code]]
- [[drei-ebenen-architektur]]
- [[llm-wiki-muster]]

---

[Zurück zum Index](../index.md)
