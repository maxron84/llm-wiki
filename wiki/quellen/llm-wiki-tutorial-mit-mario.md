---
date: 2026-04-22
type: quelle
tags: [quelle]
status: active
---

# LLM Wiki Tutorial — Mit Mario

**Zusammenfassung**: YouTube-Video von „Mit Mario", das Karpathys LLM-Wiki-Konzept erklärt und live ein Wiki für Kochrezepte mit Claude Cowork und Obsidian aufbaut.
**Quellen**: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Überblick

Mario ist Webentwickler und betreibt einen YouTube-Kanal zu Webentwicklung und KI. In diesem Video erklärt er [Andrej Karpathys](../personen/andrej-karpathy.md) [LLM-Wiki-Konzept](../konzepte/llm-wiki-muster.md) und baut live ein Wiki zum Thema Kochen und Ernährung auf — mit [Claude Cowork](../werkzeuge/claude-code.md) und [Obsidian](../werkzeuge/obsidian.md).

**Video**: https://www.youtube.com/watch?v=52LoAEo1C_M
**Veröffentlicht**: 2026-04-17

## Das Chat-Problem

Mario beginnt mit dem [fundamentalen Problem](../konzepte/chat-problem.md): Wissen verschwindet in Chats. Du lädst PDFs hoch, stellst Fragen, bekommst Antworten — aber sobald der Chat zu lang wird, vergisst die KI den Anfang. Ein neuer Chat bedeutet: bei null anfangen. Die Synthese, die Verbindungen, das über 20 Fragen aufgebaute Verständnis — alles weg. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

> „Das eigentliche Problem ist nicht, dass KI kein Gedächtnis hat. Das eigentliche Problem ist, dass Wissen in einem Chat verschwindet."

## Live-Demo: Kochrezepte-Wiki

### Vorbereitung

Mario richtet ein Obsidian-Projekt ein mit drei Ordnern (`raw/`, `clippings/`, `wiki/`) und einer `CLAUDE.md`-Datei. Die CLAUDE.md definiert:

- Zweck des Wikis (Rezepte und Ernährung)
- Ordnerstruktur
- [Aufnahme-Workflow](../konzepte/ingest-workflow.md) (9 Schritte)
- Seitenformat
- Zitierregeln
- [Fragebeantwortung](../konzepte/query-workflow.md)
- [Prüfregeln](../konzepte/lint-pruefung.md)

### Erste Aufnahme

Er clippt ein glutenfreies Keksrezept mit dem [Obsidian Web Clipper](../werkzeuge/obsidian-web-clipper.md) und lässt Claude die Quelle verarbeiten. Die KI:

1. Liest die Quelle und erkennt die Schlüsselkonzepte
2. **Fragt nach der gewünschten Tiefe** — drei Optionen:
   - **Minimal**: Nur die Rezeptseite
   - **Mittel**: Rezeptseite + Konzeptseite (z.B. glutenfreies Backen)
   - **Vollständig**: Alles oben + eigene Seite für jede Zutat
3. Mario wählt „Mittel"
4. Die KI erstellt die Seiten, verlinkt sie und aktualisiert Index + Log

### Weitere Quellen

Dann fügt Mario zwei weitere Quellen hinzu (Eier aufschlagen, Eier aufbewahren). Die KI verknüpft automatisch die Eier-Seite mit dem Keksrezept, weil dort Eier verwendet werden.

### Prüfung

Die [lint-pruefung](../konzepte/lint-pruefung.md) findet einen echten Fehler: Im Rezept fehlt ein Link zur Eier-Seite. Die KI korrigiert das nach Bestätigung.

### Frage stellen

Mario fragt: „Ich brauche ein glutenfreies Rezept. Zudem habe ich Probleme beim Eier aufschlagen." Die KI beantwortet beides aus dem Wiki — nicht aus dem Internet, sondern aus der selbst aufgebauten Wissensbasis. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Wichtige Beobachtungen

- **Tiefe-Auswahl**: Die KI fragt aktiv, wie tief man gehen will. Der Mensch steuert den Detailgrad — das verhindert Über- oder Unterkategorisierung.
- **Graphansicht**: In Obsidian sieht man die Vernetzung der Artikel visuell wachsen.
- **Modellwahl**: Für diese Art von Aufgaben reicht auch ein schwächeres Modell (z.B. Claude Sonnet). Man muss nicht das stärkste nehmen.
- **Regelmäßige Prüfung**: Nach mehreren Aufnahmen sollte man die KI das Wiki prüfen lassen, damit alles konsistent bleibt.

## Benötigte Werkzeuge

- [obsidian](../werkzeuge/obsidian.md) (kostenlos)
- [Claude Code/Cowork](../werkzeuge/claude-code.md) oder ein anderer Agent
- Eine `CLAUDE.md` Schema-Datei
- [obsidian-web-clipper](../werkzeuge/obsidian-web-clipper.md) (Browser-Erweiterung)

## Verwandte Seiten

- [llm-wiki-muster](../konzepte/llm-wiki-muster.md)
- [chat-problem](../konzepte/chat-problem.md)
- [andrej-karpathy](../personen/andrej-karpathy.md)
- [mario](../personen/mario.md) — Autor dieses Videos
- [claude-code](../werkzeuge/claude-code.md)
- [obsidian](../werkzeuge/obsidian.md)
- [obsidian-web-clipper](../werkzeuge/obsidian-web-clipper.md)
- [ingest-workflow](../konzepte/ingest-workflow.md)
- [lint-pruefung](../konzepte/lint-pruefung.md)

---

[Wiki-Index](../index.md)
