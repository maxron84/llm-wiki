---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Statelessness: Das Zustandslosigkeits-Problem

**Zusammenfassung**: KI-Agenten wie Claude Code sind standardmäßig zustandslos — jede Sitzung beginnt bei null. Obsidian als externer Speicher löst dieses Problem.
**Quellen**: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Das Problem

Jede Konversation mit [[claude-code|Claude Code]] beginnt wieder bei null. Die KI erinnert sich an nichts — weder an gestrige Sitzungen noch an das Projekt, an dem du seit drei Monaten arbeitest. Du erklärst jedes Mal alles neu. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

Das ist die „frustrierendste Einschränkung jedes KI-Agenten" und erklärt, warum viele Projekte nach zwei Wochen in der Schublade landen.

## Die drei Standard-Speicherquellen

Claude Code verfügt über drei eingebaute Speicherquellen — alle begrenzt:

| Speicherquelle | Limitation |
|---|---|
| Projekt-`CLAUDE.md` | Wächst, bis sie unüberschaubar wird |
| Globale `~/.claude/CLAUDE.md` | Nur für allgemeine Anweisungen |
| Kontextfenster der Konversation | Wird beim Schließen zurückgesetzt |

Bei Projekten, die Monate dauern, skaliert dieses Modell nicht. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Die Lösung: Externer Speicher

Die Idee dreht das Problem um: Statt den gesamten Kontext in den RAM zu laden, lässt man Claude selbstständig in einem **gut organisierten Ordner** suchen. Jede Markdown-Notiz wird zu einem persistenten Speicherfragment.

Vorteile:
- Claude öffnet nur die für die aktuelle Frage **relevanten Dateien**
- **Token-Verbrauch sinkt um ca. 40%** (30–50k statt 70–80k pro Sitzung)
- Praktisch **unbegrenztes Wissen** auf der Festplatte
- Keine externe API nötig — alles lokal

(Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Bezug zum Chat-Problem

Statelessness ist die technische Ursache des [[chat-problem|Chat-Problems]]. Während das Chat-Problem beschreibt, *was* der Benutzer erlebt (Wissen verschwindet), beschreibt Statelessness, *warum* das passiert (die KI hat kein persistentes Gedächtnis).

Die Lösungen sind komplementär:
- Das [[llm-wiki-muster]] löst das Chat-Problem auf **Wissensebene** (Quellen → Wiki)
- [[obsidian|Obsidian]] als externer Speicher löst die Statelessness auf **Systemebene** (Dateien → Kontext)

## Verwandte Seiten

- [[chat-problem]]
- [[claude-code]]
- [[obsidian]]
- [[obsidian-claude-code-pillitteri]]
- [[taeglicher-workflow]]

---

[Zurück zum Index](../index.md)
