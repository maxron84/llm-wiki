---
date: 2026-04-25
type: konzept
tags: [konzept]
status: active
---

# Seitenklassifikation (Zeitlos / Gemischt / Zeitgenössisch)

**Zusammenfassung**: Ein Pflichtfeld auf jeder Wiki-Seite, das bewertet, wie schnell der Informationswert einer Seite verfällt — und so gezielte Pflege und Priorisierung ermöglicht.
**Quellen**: raw/CLAUDE-2026-04-25_youtube-video-verlauf.md
**Zuletzt aktualisiert**: 2026-04-25

---

## Überblick

Nicht alle Wiki-Seiten altern gleich. Eine Seite über evolutionspsychologische Grundprinzipien hat in zehn Jahren denselben Wert wie heute. Eine Seite über die neuesten KI-Modelle ist in sechs Monaten überholt. Die Klassifikation macht diesen Unterschied explizit — beim Schreiben und beim Prüfen.

## Die drei Werte

| Wert | Bedeutung | Typische Seiten |
|---|---|---|
| **Zeitlos** | Informationswert verfällt nicht | Konzepte, Frameworks, wissenschaftliche Modelle, Designprinzipien, historische Analysen |
| **Gemischt** | Zeitloser Kern, aber zeitgebundene Details | Überblicksseiten mit konkreten Zahlen, Technologievergleiche, KI-Kapazitätsvergleiche |
| **Zeitgenössisch** | Wert an einen spezifischen Moment gebunden | Reaktionsvideos, aktuelle Produktkritiken, politische Personen, Feature-Reviews |

## Wie es eingesetzt wird

Das Feld steht auf jeder Inhaltsseite direkt nach `**Zuletzt aktualisiert**`:

```markdown
**Zuletzt aktualisiert**: 2026-04-25
**Klassifikation**: Zeitlos
```

Genau einer der drei Werte — kein Freitext, keine Kombinationen. Konsistenz ist wichtiger als Präzision.

## Warum es hilft

**Priorisierte Pflege**: Zeitgenössische Seiten brauchen häufigere Überprüfung; Zeitlose brauchen kaum Pflege.

**Lint-Integration**: Seiten ohne Klassifikation werden im Lint-Check gemeldet — ein Zeichen, dass eine Entscheidung aussteht.

**Ehrlichkeit**: Macht explizit, was sonst implizit bleibt — dass der Wert einer Seite zeitabhängig ist. Eine als Zeitgenössisch markierte Seite erwartet keine ewige Pflege.

## Ausnahmen

Output-Dokumente wie `betrachter.md` bekommen kein Klassifikationsfeld — sie sind lebende Dokumente ohne festes Verfallsdatum. Gleiches gilt für `index.md` und `log.md`.

## Lint-Regel

Alle Seiten ohne `**Klassifikation**:`-Feld melden. Das Feld ist Pflicht auf Video-, Konzept-, Personen- und Kanal-Seiten.

## In welchen Vorlagen

- [[claude-md-youtube-verlauf]] — Pflichtfeld auf allen Inhaltsseiten; Ursprung dieses Konzepts
- [[claude-md-legacy-forensik]] — Optional, als zweite Dimension neben dem Konfidenzmodell (Zeitlos: Architekturprinzipien; Zeitgenössisch: spezifische Versionsnummern)
- [[claude-md-software]] — Optional im Dokumentiert-Modus für ADRs und Modulseiten

## Verwandte Seiten

- [[lint-pruefung]] — Klassifikations-Check ist Teil des Lint-Workflows
- [[claude-md-youtube-verlauf]] — Ursprungsvorlage dieses Konzepts
- [[yaml-frontmatter]] — Alternativ kann die Klassifikation ins Frontmatter wandern

---

[Wiki-Index](../index.md)
