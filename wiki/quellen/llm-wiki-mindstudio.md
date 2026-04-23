---
date: 2026-04-23
type: quelle
tags: [quelle]
status: active
---

# Karpathy's LLM Wiki mit Claude Code (MindStudio)

**Zusammenfassung**: Praxisleitfaden des MindStudio-Teams: LLM-Wiki in 5 Minuten mit Obsidian aufbauen, Best Practices für abfragbare Wissensbases, und ein FAQ zu häufigen Fragen. Beinhaltet auch Überlegungen zur Teamskalierung.
**Quellen**: clippings/What Is Andrej Karpathy's LLM Wiki How to Build a Personal Knowledge Base With Claude Code.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Über die Quelle

- **Autor**: MindStudio Team
- **Erschienen**: 2026-04-06
- **Art**: Praxisleitfaden + FAQ

## Kernaussagen

### Warum Markdown die richtige Grundlage ist

Der Artikel liefert die prägnanteste Begründung für Markdown:

- Portabel und zukunftssicher — ein `.md`-File ist einfach eine Textdatei
- LLMs lesen Markdown nativ — trainiert auf GitHub-READMEs, Dokumentations-Sites, Foren
- Erzwingt Klarheit — Überschriften verlangen Benennung, Listen verlangen Trennung
- Kein Vendor-Lock-in — funktioniert mit Git, VS Code, Obsidian, Terminal

### 5 Best Practices für abfragbare Wikis

1. **Zusammenfassungen, nicht nur Inhalt** — Die ein-Zeilen-Zusammenfassung am Seitenende ist überraschend wichtig: Claude liest sie, um zu entscheiden, ob die vollständige Seite relevant ist. 10 Sekunden Aufwand, bedeutende Qualitätsverbesserung.

2. **Konsistente Terminologie** — Wenn "RAG" und "Retrieval Augmented Generation" in verschiedenen Notizen auftauchen, kann Claude die Verbindung herstellen, aber konsistente Terminologie liefert sauberere Ergebnisse. Alias-Zeile für Konzepte mit mehreren Namen.

3. **Wiki-Links nutzen** — `[[Wiki-Links]]` erstellen Verbindungen, denen Claude folgen kann. Ein gut verlinktes Wiki bietet einen reicheren Graphen als eine flache Sammlung isolierter Dateien.

4. **Seiten fokussiert halten** — Ein 10.000-Wort-Sammel-Dokument ist schwerer abzufragen als zehn fokussierte 1.000-Wort-Seiten. Je spezifischer jede Datei, desto präziser kann Claude sie lokalisieren.

5. **Inbox-Muster für schnelle Erfassung** — Rohe Notizen in `/inbox` ablegen, dann periodisch Claude bitten, sie aufzuräumen und zu verschieben: "Schau dir meinen Inbox-Ordner an und schlage vor, wohin jede Notiz gehört."

### Skalierung: Wann RAG wieder nötig wird

Der direkte Datei-Lese-Ansatz skaliert für Wikis bis einige hundert Notizen. Bei größerer Skala: semantische Suche hinzufügen, damit Claude Kandidaten eingrenzen kann, bevor vollständige Dateien gelesen werden. Empfehlung: RAG hinzufügen, wenn Claude Dinge nicht findet, von denen man weiß, dass sie im Wiki sind. (Ergänzend zu [[skalierungsgrenzen]])

### Team-Skalierung

Für Team-Wikis empfiehlt MindStudio eine no-code Plattform wie ihre eigene statt Claude Code direkt. Das ermöglicht: Webinterface, Quellenverbindungen (Google Drive, Notion), Query-Logging (was suchen Leute, was fehlt noch?), 200+ Modelle austauschbar.

## Bezug zum bestehenden Wiki

Ergänzt [[ingest-workflow]] um Qualitätspraktiken und [[obsidian]] um das Inbox-Muster. Die Begründung für Markdown ist die prägnanteste im Wiki und könnte in [[modellunabhaengigkeit]] einfließen.

## Verwandte Seiten

- [[ingest-workflow]]
- [[obsidian]]
- [[modellunabhaengigkeit]]
- [[skalierungsgrenzen]]
- [[query-workflow]]

---

[Wiki-Index](../index.md)
