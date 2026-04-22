---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# YAML-Frontmatter

**Zusammenfassung**: Standardisierte Metadaten am Anfang jeder Markdown-Notiz, die gezielte Abfragen und dynamische Dashboards ermöglichen.
**Quellen**: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Was ist YAML-Frontmatter?

Ein YAML-Block am Anfang einer Markdown-Datei, der den Inhalt mit Metadaten klassifiziert. Er wird von drei Bindestrichen (`---`) eingerahmt:

```yaml
---
date: 2026-04-22
tags: [claude-code, obsidian, wiki]
status: active
project: llm-wiki
type: konzept
---
```

## Warum ist es wichtig?

Ohne Frontmatter behandelt [[claude-code|Claude]] alle Dateien gleich und verliert wertvolle Informationen. Mit standardisiertem Frontmatter werden gezielte Abfragen möglich:

- „Finde alle Notizen mit `status=active` und `tag=claude-code`"
- „Zeige alle Konzeptseiten, die seit letzter Woche aktualisiert wurden"
- „Liste alle Seiten eines bestimmten Projekts auf"

(Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Empfohlener Mindeststandard

| Feld | Zweck | Beispiel |
|---|---|---|
| `date` | Erstellungsdatum | `2026-04-22` |
| `tags` | Thematische Einordnung | `[obsidian, workflow]` |
| `status` | Aktueller Zustand | `active`, `draft`, `archived` |
| `project` | Projektzugehörigkeit | `llm-wiki` |
| `type` | Art der Notiz | `konzept`, `quelle`, `person` |

## Dataview-Integration

Das [[obsidian|Obsidian]]-Plugin [[dataview|Dataview]] kann Abfragen über Frontmatter-Felder ausführen und dynamische Tabellen und Listen generieren. Wenn die KI konsistent Frontmatter zu Seiten hinzufügt, entstehen automatisch nützliche Dashboards. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Häufiger Fehler: Inkonsistente Tags

Wenn jede Notiz unterschiedliche Tags ohne Konvention hat, funktionieren Abfragen nicht und Claude findet keine Muster. Pillitteri empfiehlt: eine **geschlossene Liste zulässiger Tags** definieren und in jeder Sitzung laden. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Verwandte Seiten

- [[obsidian-claude-code-pillitteri]]
- [[obsidian]]
- [[mece-prinzip]]

---

[Zurück zum Index](../index.md)
