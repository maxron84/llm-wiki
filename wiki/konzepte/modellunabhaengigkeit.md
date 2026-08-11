---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Modellunabhängigkeit

**Zusammenfassung**: Ein Obsidian-Vault aus Markdown-Dateien macht unabhängig vom KI-Anbieter — die KI wird austauschbar, die Daten bleiben.
**Quellen**: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md, raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Die Kernidee

Wenn deine Arbeit innerhalb von Claude eingeschlossen ist, bist du ein Gefangener von Anthropic. Wenn Claude morgen ausfällt, die Preise anzieht oder ein Konkurrent ein überlegenes Modell veröffentlicht, bist du blockiert. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

[Obsidian](../werkzeuge/obsidian.md) durchbricht diese Abhängigkeit. Der Vault besteht nur aus **Markdown-Dateien** — dem universellsten Textformat. Morgen kannst du denselben Vault mit einem beliebigen Agenten öffnen:

- **Codex CLI** (OpenAI)
- **Gemini CLI** (Google)
- **Aider**
- **Cursor** (im Agent-Modus)
- Jeder zukünftige Agent, der Dateien lesen kann

Du änderst nur den Namen der Schema-Datei (`CLAUDE.md` → `AGENTS.md` → `GEMINI.md`) und das System funktioniert identisch. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Die Datenbank-Analogie

> Die KI wird austauschbar, so wie eine Datenbank für eine Anwendung: Du tauschst die Engine aus, nicht die Daten.

Das Schema (die dritte Ebene der [drei-ebenen-architektur](drei-ebenen-architektur.md)) ist die Schnittstelle. Die Rohquellen und das Wiki bleiben unverändert, egal welches Modell die Arbeit macht.

## Strategischer Vorteil: Risikodiversifizierung

Für Unternehmen ist das kein philosophisches Detail — es ist **Risikomanagement**:

- Wenn die Unternehmensleistung perfekt mit einem einzelnen Modell korreliert, bedeutet jede Ausfallzeit einen Stillstand
- Mit einem portablen Vault kann man im laufenden Betrieb wechseln
- 80% der Zeit nutzt man das bevorzugte Modell, aber man hat ein Backup bereitstehen
- Die **Kontinuität wird von den Daten gewährleistet**, nicht vom Anbieter

(Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Bezug zum LLM-Wiki

Das [llm-wiki-muster](llm-wiki-muster.md) profitiert direkt von dieser Eigenschaft. Karpathy beschreibt das Wiki als „ein Git-Repository aus Markdown-Dateien" — versionierbar, portabel, zukunftssicher. Die Wahl des LLM-Agenten wird zu einer taktischen, nicht strategischen Entscheidung. (Quelle: clippings/llm-wiki.md)

## Woran die Eigenschaft tatsächlich hängt

Ein naheliegender Einwand gegen jede Datenbanklösung lautet: Damit ist die Modellunabhängigkeit weg, die Daten stecken wieder in einem proprietären Format. Der Einwand greift nur halb.

> „Die Eigenschaft, dass das Wiki jeden Agenten überlebt, hängt daran, dass am Ende lesbares Markdown steht, nicht an der Speicherform dazwischen." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Entscheidend ist nicht, wo das Wiki gespeichert wird, sondern dass es **jederzeit vollständig und deterministisch als Markdown vorliegt**. Solange ein Export-Skript aus der Datenbank den kompletten Vault erzeugt — Ordner aus `type`, Dateiname aus `slug`, Frontmatter aus den Spalten —, bleibt die Eigenschaft erhalten. Fällt der Export weg, fällt sie weg.

Deshalb ist der Export in der [SQL-Wiki-Architektur](sql-wiki-architektur.md) kein Nebenprodukt, sondern Architekturbestandteil. → [markdown-als-rendering](markdown-als-rendering.md)

Der Preis ist ehrlich zu benennen: Der Export-Vault wird schreibgeschützt. Wer in Obsidian eine Datei ändert, verliert die Änderung beim nächsten Export. Man behält die Unabhängigkeit vom Anbieter, gibt aber die direkte Bearbeitbarkeit auf.

## Verwandte Seiten

- [markdown-als-rendering](markdown-als-rendering.md) — Der Export als Bedingung der Unabhängigkeit
- [sql-wiki-architektur](sql-wiki-architektur.md) — Wo die Frage aufkommt
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle
- [obsidian-claude-code-pillitteri](../quellen/obsidian-claude-code-pillitteri.md)
- [obsidian](../werkzeuge/obsidian.md)
- [claude-code](../werkzeuge/claude-code.md)
- [drei-ebenen-architektur](drei-ebenen-architektur.md)
- [llm-wiki-muster](llm-wiki-muster.md)

---

[Wiki-Index](../index.md)
