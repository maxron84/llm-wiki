---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, automatisierung, setup]
status: archived
---

# Ralph Wiggum Plugin (Claude Code)

**Zusammenfassung**: ⚠️ Diese Seite beschreibt ein Plugin, das nicht existiert. Das `/plugin install ralph-wiggum@claude-plugins-official`-Kommando schlägt fehl — es gibt kein solches Plugin im Claude-Code-Ökosystem. Die korrekte Implementierung der Ralph-Schleife ist das externe Bash-Skript.
**Quellen**: Ursprünglich aus raw/ralph-claude-code-llm-wiki_metrik.md — Angaben dort nicht verifiziert.
**Zuletzt aktualisiert**: 2026-05-25

---

## ⚠️ Korrektur (2026-05-25)

Dieses Plugin existiert nicht. Geprüft: Das `anthropics/claude-code`-Repository enthält kein `plugins/ralph-wiggum/`-Verzeichnis. Das `/plugin install`-System in Claude Code existiert, aber `ralph-wiggum@claude-plugins-official` ist kein realer Eintrag darin.

Die ursprünglichen Quellen dieser Seite (The Register, paddo.dev) konnten die Plugin-Existenz nicht bestätigen.

## Was stattdessen funktioniert

**Die [wiki-ralph.sh](wiki-ralph-sh.md)-Implementierung** — ein externes Bash-Skript, das `claude -p` in einer `while`-Schleife aufruft. Das ist die verifizierte, funktionierende Variante der Ralph-Schleife.

```bash
while true; do
  claude -p "$PROMPT" --max-turns 30
done
```

Claude Code hat einen eingebauten `/loop`-Skill, der aber ein anderes Muster ist: zeitbasiertes Polling, keine autonome Arbeitsschleife bis zur Completion-Promise.

## Verwandte Seiten

- [ralph-schleife](../konzepte/ralph-schleife.md) — Das zugrundeliegende Konzept (korrekt)
- [wiki-ralph-sh](wiki-ralph-sh.md) — Die funktionierende Bash-Implementierung

---

[Wiki-Index](../index.md)
