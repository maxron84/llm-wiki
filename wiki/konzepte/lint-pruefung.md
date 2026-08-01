---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Lint-Prüfung (Wiki-Gesundheitscheck)

**Zusammenfassung**: Die regelmäßige Prüfung des Wikis auf Widersprüche, verwaiste Seiten, fehlende Querverweise und veraltete Behauptungen.
**Quellen**: clippings/llm-wiki.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Überblick

„Lint" ist die dritte der drei Kernoperationen im [llm-wiki-muster](llm-wiki-muster.md). Wie ein Code-Linter prüft die KI regelmäßig die Gesundheit des Wikis und schlägt Korrekturen vor. Das hält das Wiki konsistent, während es wächst.

## Prüfpunkte

Die KI prüft auf:

1. **Widersprüche zwischen Seiten** — Wenn zwei Seiten sich widersprechende Behauptungen enthalten
2. **Veraltete Behauptungen** — Stellen, die durch neuere Quellen überholt sein könnten
3. **Verwaiste Seiten** — Seiten ohne eingehende Links von anderen Seiten (isolierte Knoten im Graphen)
4. **Fehlende Konzeptseiten** — Konzepte, die in Seiten erwähnt werden, aber keine eigene Seite haben
5. **Fehlende Querverweise** — Stellen, an denen Links zu bestehenden Seiten fehlen
6. **Datenlücken** — Bereiche, die durch eine Web-Suche oder neue Quellen gefüllt werden könnten
7. **Formatprüfung** — Ob alle Seiten dem definierten Seitenformat folgen

(Quelle: clippings/llm-wiki.md)

## Automatisierte Prüfung

Das Skript `~/.claude/scripts/wiki_lint.py` deckt die mechanisch prüfbaren Punkte ab:

```bash
python3 ~/.claude/scripts/wiki_lint.py   # aus dem Projekt-Root
```

| Prüfung | Findet | Deckt Prüfpunkt |
|---|---|---|
| `DEAD LINKS` | Links auf nicht existierende Seiten | 5 |
| `ORPHANED PAGES` | Seiten ohne eingehende Links | 3 |
| `PAGES NOT IN INDEX` | Seiten, die in `index.md` fehlen | 5 |
| `FORMAT CHECK` | Fehlende H1, Zusammenfassung, Quellen, Datum, Verwandte Seiten, Footer | 7 |
| `SOURCE CITATIONS` | Quellenangaben, die nicht exakt auf eine Datei in `raw/`/`clippings/` zeigen | 7 |
| `UNINGESTED SOURCES` | Quelldokumente ohne Erwähnung auf einer Wiki-Seite | 6 |
| `TYPE / FOLDER` | `type:` passt nicht zum Ordner | 7 |
| `LOG ORDER` | Log-Einträge außer der Reihe oder mit unbekanntem Präfix | 7 |

Die letzten vier Prüfungen kamen am 2026-08-01 dazu, nachdem ein Wiki-Audit vier Fehlerklassen gefunden hatte, die das Skript bis dahin nicht sah — darunter eine seit drei Monaten nie aufgenommene Quelle und 50 Quellenangaben, die maschinell nicht auflösbar waren.

**Was das Skript nicht kann**: Prüfpunkte 1, 2 und 4 (Widersprüche, veraltete Behauptungen, fehlende Konzeptseiten) sind Bedeutungsfragen und bleiben Aufgabe der KI-Prüfung. Das Skript ergänzt sie, ersetzt sie nicht — sinnvoll vor jedem Commit oder nach einem größeren Ingest.

## Wann prüfen?

Karpathy empfiehlt, die Prüfung **periodisch** durchzuführen — insbesondere nach mehreren Ingest-Vorgängen, wenn viele neue Seiten entstanden sind. Die KI ist auch gut darin, neue Fragen zum Untersuchen und neue Quellen zum Suchen vorzuschlagen. (Quelle: clippings/llm-wiki.md)

## Ergebnisformat

Die Ergebnisse werden als nummerierte Liste mit Korrekturvorschlägen berichtet. Der Benutzer entscheidet, welche Korrekturen umgesetzt werden.

## Verwandte Seiten

- [llm-wiki-muster](llm-wiki-muster.md)
- [ingest-workflow](ingest-workflow.md)
- [query-workflow](query-workflow.md)
- [claude-md-design](claude-md-design.md) — Designprinzip 3: Keine toten Links in der CLAUDE.md ist eine Lint-Regel

---

[Wiki-Index](../index.md)
