---
date: 2026-04-22
type: werkzeug
tags: [werkzeug]
status: active
---

# Obsidian

**Zusammenfassung**: Obsidian ist eine kostenlose Notizen-App auf Basis lokaler Markdown-Dateien, die im LLM-Wiki-Muster als Viewer und IDE für das Wiki dient.
**Quellen**: clippings/llm-wiki.md, clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md, clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Was ist Obsidian?

[[obsidian|Obsidian]] ist eine kostenlose, flexible App für Notizen und Wissensmanagement. Technisch ist es einfach: **Alle Notizen sind `.md`-Dateien in deinem Dateisystem**, organisiert in Ordnern wie jedes andere Verzeichnis. Keine proprietäre Datenbank, keine obligatorische Cloud, kein Vendor-Lock-in. (Quelle: clippings/llm-wiki.md)

**Website**: https://obsidian.md

## Warum Obsidian?

Obsidian entstand 2020 als Notizen-App für Entwickler und Forscher. Seine strategische Stärke: Keine proprietäre Datenbank, keine obligatorische Cloud, kein Vendor-Lock-in. Sollte Obsidian morgen schließen, bleiben deine Daten erhalten. Dieses technische Detail wird strategisch, sobald du es mit [[claude-code|Claude Code]] kombinierst — der KI-Agent arbeitet nativ mit lokalen Dateien und braucht nur Zugriff auf den Vault-Ordner. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Rolle im LLM-Wiki

Im [[llm-wiki-muster]] hat Obsidian eine klar definierte Rolle:

> „Obsidian ist die IDE; die KI ist der Programmierer; das Wiki ist die Codebase."

Du nutzt Obsidian, um:
- Das Wiki in Echtzeit zu lesen und zu navigieren
- Links zu folgen und Zusammenhänge zu entdecken
- Die **Graphansicht** zu nutzen, um die Struktur des Wikis visuell zu erfassen
- Verwaiste Seiten (isolierte Knoten) zu identifizieren

Du schreibst das Wiki **nicht** selbst in Obsidian — das macht die KI. Obsidian ist der Viewer. (Quelle: clippings/llm-wiki.md)

## Nützliche Features

### Graphansicht
Die beste Methode, die Gestalt des Wikis zu sehen — welche Seiten miteinander verbunden sind, welche Seiten Knotenpunkte sind, welche verwaist sind.

**Graph-Test**: Eine verwaiste Datei (isolierter Knoten ohne Verbindungen) ist ein Warnsignal — dieser Inhalt gelangt nie in den Kontext von Claude. Monatliche Aufgabe: Graphen öffnen, Waisen identifizieren, verknüpfen oder archivieren. Der Graph dient als **Wartungswerkzeug**, nicht als Entscheidungs-Dashboard. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

### Obsidian Web Clipper
Eine Browser-Erweiterung, die Webartikel in Markdown umwandelt. Sehr nützlich, um schnell Quellen in die `clippings/`-Sammlung zu bringen. (Quelle: clippings/llm-wiki.md)

**Download**: https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjfabmlf

### Bilder lokal speichern
In den Obsidian-Einstellungen kann man einen festen Anhang-Ordner definieren (z.B. `raw/assets/`). Nach dem Clippen eines Artikels können alle Bilder mit einem Hotkey lokal heruntergeladen werden. Das erlaubt der KI, Bilder direkt zu referenzieren, statt sich auf URLs zu verlassen, die brechen können. (Quelle: clippings/llm-wiki.md)

### Marp (Plugin)
Ein Markdown-basiertes Slide-Deck-Format. Nützlich, um Präsentationen direkt aus Wiki-Inhalten zu generieren. Siehe [[marp]].

### Dataview (Plugin)
Führt Abfragen über [[yaml-frontmatter|YAML-Frontmatter]] von Seiten aus. Wenn die KI Frontmatter (Tags, Daten, Quellenzähler) zu Wiki-Seiten hinzufügt, kann [[dataview|Dataview]] dynamische Tabellen und Listen generieren. (Quelle: clippings/llm-wiki.md)

## Versionierung

Das Wiki ist letztlich ein **Git-Repository aus Markdown-Dateien**. Man bekommt Versionshistorie, Branching und Zusammenarbeit kostenlos dazu. (Quelle: clippings/llm-wiki.md)

## Verwandte Seiten

- [[llm-wiki-muster]]
- [[drei-ebenen-architektur]]
- [[llm-wiki-karpathy]]
- [[llm-wiki-tutorial-mit-mario]]
- [[obsidian-claude-code-pillitteri]]
- [[obsidian-web-clipper]]
- [[claude-code]]
- [[mece-prinzip]]
- [[modellunabhaengigkeit]]
- [[yaml-frontmatter]]
- [[dataview]]
- [[marp]]

---

[Zurück zum Index](../index.md)
