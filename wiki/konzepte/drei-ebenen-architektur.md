---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Drei-Ebenen-Architektur

**Zusammenfassung**: Die Architektur des LLM-Wiki-Musters besteht aus drei Ebenen — Rohquellen, Wiki und Schema —, die klar getrennte Verantwortlichkeiten haben.
**Quellen**: clippings/llm-wiki.md, clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Überblick

Das [[llm-wiki-muster]] basiert auf einer klaren Trennung in drei Ebenen. Jede Ebene hat eine eigene Rolle und eigene Regeln, wer sie lesen und bearbeiten darf.

## Ebene 1: Rohquellen

- **Ordner**: `raw/` und `clippings/`
- **Inhalt**: Artikel, Papers, PDFs, Bilder, Datendateien, Web-Clippings
- **Regel**: **Unveränderlich** — die KI liest daraus, aber fasst die Dateien nie an
- **Rolle**: Deine „Source of Truth" (Quelle der Wahrheit)

Die Rohquellen sind das Fundament. Alles, was im Wiki steht, lässt sich auf eine Rohquelle zurückführen. Wenn Quellen sich widersprechen, wird der Widerspruch explizit im Wiki vermerkt. (Quelle: clippings/llm-wiki.md)

## Ebene 2: Das Wiki

- **Ordner**: `wiki/`
- **Inhalt**: Von der KI erstellte Markdown-Dateien — Zusammenfassungen, Entitätsseiten, Konzeptseiten, Vergleiche, Index, Synthesen
- **Regel**: Die KI **besitzt** diese Ebene vollständig. Sie erstellt Seiten, aktualisiert sie bei neuen Quellen, pflegt Querverweise und hält alles konsistent
- **Rolle**: Die eigentliche Wissensbasis

Zwei Spezialdateien helfen bei der Navigation:
- **`index.md`** — Inhaltsorientiert, ein Katalog aller Seiten
- **`log.md`** — Chronologisch, ein Protokoll aller Vorgänge

Der Index funktioniert überraschend gut bei moderater Größe (~100 Quellen, ~hunderte Seiten) und macht embedding-basierte RAG-Infrastruktur überflüssig. (Quelle: clippings/llm-wiki.md)

## Ebene 3: Das Schema

- **Datei**: `CLAUDE.md` (bei Claude Code), `AGENTS.md` (bei Codex), o.ä.
- **Inhalt**: Regelwerk, das der KI sagt, wie sie das Wiki strukturieren soll
- **Regel**: Wird gemeinsam von Mensch und KI weiterentwickelt
- **Rolle**: Die Konfigurationsdatei, die aus der KI einen disziplinierten Wiki-Pfleger macht

Das Schema definiert:
- Zweck des Wikis
- Wie neue Quellen verarbeitet werden ([[ingest-workflow]])
- Wie Seiten formatiert werden
- Wie die KI Fragen beantwortet ([[query-workflow]])
- Wie Prüfungen ablaufen ([[lint-pruefung]])

Ohne Schema verhält sich die KI wie ein generischer Chatbot. Das Schema macht den Unterschied. (Quelle: clippings/llm-wiki.md)

Für die Gestaltung einer effektiven Schema-Datei gibt es [[claude-md-design|6 Designprinzipien]] aus der Praxis — u.a. dass operative Regeln nie in Code-Fences stecken dürfen und Link-Stil sowie Frontmatter konsistent mit dem Wiki sein müssen.

### CLAUDE.md als Orchestrator

Pillitteri beschreibt die `CLAUDE.md` als **Einstiegspunkt des Systems**. Jede Claude Code-Sitzung liest sie als Erstes. Eine gute CLAUDE.md enthält: die Ordnerkarte mit Rollen, Namenskonventionen, das Standard-[[yaml-frontmatter]], benutzerdefinierte Befehle und verfügbare Skills. Wichtig: Die CLAUDE.md ist ein lebendiges Dokument und sollte monatlich aktualisiert werden. Bei >500 Zeilen liegt ein Architekturfehler vor — der eigentliche Inhalt gehört in den Vault, nicht in die Schema-Datei. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Zusammenspiel der Ebenen

```
Rohquellen (unveränderlich)
    ↓ KI liest
Das Wiki (KI schreibt & pflegt)
    ↑ gesteuert durch
Das Schema (Mensch & KI co-evolvieren)
```

Der Mensch kuratiert die Rohquellen und gestaltet das Schema. Die KI liest die Rohquellen und baut das Wiki gemäß dem Schema auf. Das Wiki wächst mit jeder neuen Quelle.

## Verwandte Seiten

- [[llm-wiki-muster]]
- [[llm-wiki-karpathy]]
- [[obsidian-claude-code-pillitteri]]
- [[ingest-workflow]]
- [[query-workflow]]
- [[lint-pruefung]]
- [[modellunabhaengigkeit]]
- [[yaml-frontmatter]]
- [[claude-md-design]] — 6 Designprinzipien für die Schema-Datei (Ebene 3)

---

[Zurück zum Index](../index.md)
