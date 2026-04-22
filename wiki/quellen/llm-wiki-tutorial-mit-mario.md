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

Mario ist Webentwickler und betreibt einen YouTube-Kanal zu Webentwicklung und KI. In diesem Video erklärt er [[andrej-karpathy|Andrej Karpathys]] [[llm-wiki-muster|LLM-Wiki-Konzept]] und baut live ein Wiki zum Thema Kochen und Ernährung auf — mit [[claude-code|Claude Cowork]] und [[obsidian|Obsidian]].

**Video**: https://www.youtube.com/watch?v=52LoAEo1C_M
**Veröffentlicht**: 2026-04-17

## Das Chat-Problem

Mario beginnt mit dem [[chat-problem|fundamentalen Problem]]: Wissen verschwindet in Chats. Du lädst PDFs hoch, stellst Fragen, bekommst Antworten — aber sobald der Chat zu lang wird, vergisst die KI den Anfang. Ein neuer Chat bedeutet: bei null anfangen. Die Synthese, die Verbindungen, das über 20 Fragen aufgebaute Verständnis — alles weg. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

> „Das eigentliche Problem ist nicht, dass KI kein Gedächtnis hat. Das eigentliche Problem ist, dass Wissen in einem Chat verschwindet."

## Live-Demo: Kochrezepte-Wiki

### Vorbereitung

Mario richtet ein Obsidian-Projekt ein mit drei Ordnern (`raw/`, `clippings/`, `wiki/`) und einer `CLAUDE.md`-Datei. Die CLAUDE.md definiert:

- Zweck des Wikis (Rezepte und Ernährung)
- Ordnerstruktur
- [[ingest-workflow|Aufnahme-Workflow]] (9 Schritte)
- Seitenformat
- Zitierregeln
- [[query-workflow|Fragebeantwortung]]
- [[lint-pruefung|Prüfregeln]]

### Erste Aufnahme

Er clippt ein glutenfreies Keksrezept mit dem [[obsidian-web-clipper|Obsidian Web Clipper]] und lässt Claude die Quelle verarbeiten. Die KI:

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

Die [[lint-pruefung]] findet einen echten Fehler: Im Rezept fehlt ein Link zur Eier-Seite. Die KI korrigiert das nach Bestätigung.

### Frage stellen

Mario fragt: „Ich brauche ein glutenfreies Rezept. Zudem habe ich Probleme beim Eier aufschlagen." Die KI beantwortet beides aus dem Wiki — nicht aus dem Internet, sondern aus der selbst aufgebauten Wissensbasis. (Quelle: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md)

## Wichtige Beobachtungen

- **Tiefe-Auswahl**: Die KI fragt aktiv, wie tief man gehen will. Der Mensch steuert den Detailgrad — das verhindert Über- oder Unterkategorisierung.
- **Graphansicht**: In Obsidian sieht man die Vernetzung der Artikel visuell wachsen.
- **Modellwahl**: Für diese Art von Aufgaben reicht auch ein schwächeres Modell (z.B. Claude Sonnet). Man muss nicht das stärkste nehmen.
- **Regelmäßige Prüfung**: Nach mehreren Aufnahmen sollte man die KI das Wiki prüfen lassen, damit alles konsistent bleibt.

## Benötigte Werkzeuge

- [[obsidian]] (kostenlos)
- [[claude-code|Claude Code/Cowork]] oder ein anderer Agent
- Eine `CLAUDE.md` Schema-Datei
- [[obsidian-web-clipper]] (Browser-Erweiterung)

## Verwandte Seiten

- [[llm-wiki-muster]]
- [[chat-problem]]
- [[andrej-karpathy]]
- [[mario]] — Autor dieses Videos
- [[claude-code]]
- [[obsidian]]
- [[obsidian-web-clipper]]
- [[ingest-workflow]]
- [[lint-pruefung]]

---

[Zurück zum Index](../index.md)
