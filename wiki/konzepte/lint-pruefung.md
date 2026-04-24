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

„Lint" ist die dritte der drei Kernoperationen im [[llm-wiki-muster]]. Wie ein Code-Linter prüft die KI regelmäßig die Gesundheit des Wikis und schlägt Korrekturen vor. Das hält das Wiki konsistent, während es wächst.

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

## Wann prüfen?

Karpathy empfiehlt, die Prüfung **periodisch** durchzuführen — insbesondere nach mehreren Ingest-Vorgängen, wenn viele neue Seiten entstanden sind. Die KI ist auch gut darin, neue Fragen zum Untersuchen und neue Quellen zum Suchen vorzuschlagen. (Quelle: clippings/llm-wiki.md)

## Ergebnisformat

Die Ergebnisse werden als nummerierte Liste mit Korrekturvorschlägen berichtet. Der Benutzer entscheidet, welche Korrekturen umgesetzt werden.

## Verwandte Seiten

- [[llm-wiki-muster]]
- [[ingest-workflow]]
- [[query-workflow]]
- [[claude-md-design]] — Designprinzip 3: Keine toten Links in der CLAUDE.md ist eine Lint-Regel

---

[Zurück zum Index](../index.md)
