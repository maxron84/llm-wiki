---
date: 2026-04-22
type: werkzeug
tags: [werkzeug]
status: active
---

# Dataview (Obsidian-Plugin)

**Zusammenfassung**: Obsidian-Plugin, das SQL-ähnliche Abfragen über YAML-Frontmatter-Felder ermöglicht und dynamische Tabellen und Listen generiert.
**Quellen**: clippings/llm-wiki.md, clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Was ist Dataview?

Dataview ist ein Community-Plugin für [[obsidian|Obsidian]], das den Vault wie eine Datenbank behandelt. Es liest [[yaml-frontmatter|YAML-Frontmatter]]-Felder aller Notizen und erlaubt SQL-ähnliche Abfragen über diese Metadaten.

## Relevanz für das LLM-Wiki

Wenn die KI konsistent Frontmatter zu Wiki-Seiten hinzufügt, entstehen mit Dataview automatisch nützliche Dashboards:

- Alle Seiten eines bestimmten Typs (`type: konzept`)
- Alle aktiven Seiten (`status: active`)
- Seiten nach Datum gruppiert
- Alle Vergleichsseiten (`tags: vergleich`)

(Quelle: clippings/llm-wiki.md)

Das macht [[yaml-frontmatter]] zur Voraussetzung: Ohne konsistente Metadaten liefern Dataview-Abfragen keine sinnvollen Ergebnisse. Pillitteri betont: Eine geschlossene Liste zulässiger Tags ist Pflicht, damit Abfragen zuverlässig funktionieren. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Verwandte Seiten

- [[obsidian]]
- [[yaml-frontmatter]]
- [[llm-wiki-muster]]

---

[Wiki-Index](../index.md)
