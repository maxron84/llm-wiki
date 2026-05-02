---
date: 2026-04-23
type: quelle
tags: [quelle]
status: active
---

# LLM Wiki Revolution (Riya Bansal / Analytics Vidhya)

**Zusammenfassung**: Umfassender Einführungsartikel von Riya Bansal mit 6-Schritt-Anleitung, vollständigem Tool-Stack (Obsidian, qmd, Git) und ehrlicher Diskussion der praktischen Herausforderungen.
**Quellen**: clippings/LLM Wiki Revolution How Andrej Karpathy's Idea is Changing AI.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Über die Quelle

- **Autor**: Riya Bansal (Analytics Vidhya)
- **Erschienen**: 2026-04-07
- **Art**: Umfassender Einführungsartikel mit Tool-Stack

## Kernaussagen

### Der vollständige Tool-Stack nach Karpathy

1. **[obsidian](../werkzeuge/obsidian.md)** — IDE für das Wiki
   - **Graphansicht**: Seiten als Knoten, Wiki-Links als Kanten; Hub-Seiten sind größer, Waisen sind isoliert
   - **[dataview](../werkzeuge/dataview.md)-Plugin**: SQL-ähnliche Abfragen über YAML-Frontmatter aller Seiten
   - **Web Clipper**: Ein-Klick-Artikel-zu-Markdown-Konvertierung

2. **[qmd](../werkzeuge/qmd.md)** — Lokale semantische Suche von Tobi Lütke (Shopify CEO)
   - Drei Suchmodi: BM25-Keyword, Vektorsuche, Hybrid mit LLM-Reranking
   - Vollständig lokal mit GGUF-Modellen (keine API, keine Datenweitergabe)
   - MCP-Server-Modus: Claude Code nutzt qmd als natives Werkzeug
   - Wird wichtig bei mehr als ~100 Seiten

3. **Git** — Versionskontrolle für Wissen
   - Vollständige Geschichte jeder Wissensevolution
   - Diff zwischen Kompilierungsläufen
   - Rollback bei fehlerhafter Kompilierung
   - Kollaboration über Branches und Pull Requests
   - CI/CD für Wissen (automatisierte Health-Checks bei jedem Commit)

### 6-Schritt-Anleitung

1. Ressourcen sammeln (Artikel, Bücher, Notizen, Transkripte)
2. Klassifizieren vor dem Extrahieren — verschiedene Dokumenttypen brauchen verschiedene Extraktionstiefe
3. KI schreibt Wiki-Seiten (strukturierte Abfrage mit Template)
4. Index erstellen (`index.md` als Inhaltsverzeichnis)
5. Fragen speichern — gute Antworten werden zu Wiki-Seiten
6. Lint-Durchläufe — regelmäßige Widerspruchs- und Waisen-Prüfung

### Praktische Herausforderungen

Bansal benennt 5 realistische Hürden:

1. **Prompt Engineering** ist die erste Hürde — Seiten strukturieren, Erstellen vs. Aktualisieren entscheiden, Widersprüche lösen
2. **Skalierbarkeit** ist versteckt — einfache Setups brechen nach einigen hundert Seiten zusammen
3. **Konsistenz über Zeit** — ohne regelmäßige Wartung häufen sich veraltete Infos und Waisen an
4. **Agenten-Kompetenz** — KI effektiv steuern ist eine Fähigkeit, die Übung erfordert
5. **Aufbau ist komplex** — mehrere Herausforderungen über Setup, Struktur und Langzeitpflege

## Bezug zum bestehenden Wiki

Wichtigste neue Quelle für [qmd](../werkzeuge/qmd.md) als Skalierungslösung neben [jdocmunch](../werkzeuge/jdocmunch.md). Die 5 Herausforderungen sind eine nützliche Ergänzung für [ingest-workflow](../konzepte/ingest-workflow.md) und [lint-pruefung](../konzepte/lint-pruefung.md).

## Verwandte Seiten

- [qmd](../werkzeuge/qmd.md)
- [obsidian](../werkzeuge/obsidian.md)
- [dataview](../werkzeuge/dataview.md)
- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md)
- [lint-pruefung](../konzepte/lint-pruefung.md)
- [tobi-luetke](../personen/tobi-luetke.md)

---

[Wiki-Index](../index.md)
