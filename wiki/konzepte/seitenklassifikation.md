---
date: 2026-04-25
type: konzept
tags: [konzept, schema, entwurf]
status: draft
---

# Seitenklassifikation (Zeitlos / Gemischt / Zeitgenössisch)

**Zusammenfassung**: *(Konzeptskizze)* Ein Feld, das bewertet, wie schnell der Informationswert einer Seite verfällt — Pflichtfeld in der YouTube-Verlauf-Vorlage, in diesem Wiki bewusst **nicht** eingeführt.
**Quellen**: `raw/CLAUDE-2026-04-25_youtube-video-verlauf.md`
**Zuletzt aktualisiert**: 2026-08-01

---

> **Geltungsbereich**: Diese Seite beschreibt ein Feld der Vorlage
> [claude-md-youtube-verlauf](../vorlagen/claude-md-youtube-verlauf.md), nicht eine Regel dieses Wikis.
> **In diesem Wiki ist das Feld nicht in Gebrauch** (Stand 2026-08-01: 0 von 118 Seiten), und die
> [CLAUDE.md](../../CLAUDE.md) fordert es nicht. Wo unten „Pflichtfeld" steht, ist die Pflicht
> *innerhalb der Vorlage* gemeint. Begründung der Entscheidung: Abschnitt
> [Warum hier nicht eingeführt](#warum-hier-nicht-eingeführt).

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

*In Vaults, die das Feld führen:* Alle Seiten ohne `**Klassifikation**:`-Feld melden. Das Feld ist dort Pflicht auf Video-, Konzept-, Personen- und Kanal-Seiten.

`wiki_lint.py` prüft diese Regel **nicht** — bewusst, weil das Feld hier nicht gilt. Wer die Klassifikation einführt, muss die Prüfung zusammen mit den Seiten nachrüsten.

## Warum hier nicht eingeführt

Die Idee entstand am 2026-04-25 aus der YouTube-Verlauf-Vorlage und wurde als Konzept übernommen — aber nie auf dieses Wiki angewandt. Beim Wiki-Audit am 2026-08-01 fiel der Widerspruch auf: eine Seite, die ein Pflichtfeld beschreibt, das auf keiner einzigen Seite existiert. Die Entscheidung fiel gegen die Einführung, aus drei Gründen:

1. **Der Nutzen ist hier klein.** Die Klassifikation zahlt sich aus, wo viele schnell verfallende Seiten viele langlebige überdecken — YouTube-Verläufe sind genau dieser Fall. Dieses Wiki besteht überwiegend aus Konzept- und Vorlagenseiten, die fast alle „Zeitlos" oder „Gemischt" wären. Ein Feld, dessen Wert man vorhersagen kann, trägt keine Information.
2. **Die Fälle, wo es zählte, sind bereits anders gelöst.** Bei Hardware-Preisen und Werkzeug-Versionen steht das Datum ohnehin im Text (siehe [radeon-ai-pro-r9700](../werkzeuge/radeon-ai-pro-r9700.md) oder die Preiskorrekturen im Log), und veraltete Seiten tragen ⚠️-Marken plus `status: archived` — etwa [roo-code](../werkzeuge/roo-code.md). Das ist gröber, aber es wird gepflegt.
3. **Nachrüsten kostet 118 Entscheidungen.** Ein Pflichtfeld halb einzuführen ist schlechter als es zu lassen: Ein Feld, das auf 30 Seiten steht, sagt über die anderen 88 nichts aus, und der Lint kann es nicht durchsetzen, ohne 88 Fehlalarme zu erzeugen.

Der Gedanke dahinter bleibt richtig und ist deshalb als Skizze erhalten. Falls das Wiki später viele zeitgebundene Seiten aufnimmt, ist das der Moment, es erneut zu prüfen — dann bitte zusammen mit [kontextrahmen-5w1h](kontextrahmen-5w1h.md), dessen Feld „Wann" dieselbe Achse feiner erfasst.

## In welchen Vorlagen

- [claude-md-youtube-verlauf](../vorlagen/claude-md-youtube-verlauf.md) — Pflichtfeld auf allen Inhaltsseiten; Ursprung dieses Konzepts
- [claude-md-legacy-forensik](../vorlagen/claude-md-legacy-forensik.md) — Optional, als zweite Dimension neben dem Konfidenzmodell (Zeitlos: Architekturprinzipien; Zeitgenössisch: spezifische Versionsnummern)
- [claude-md-software](../vorlagen/claude-md-software.md) — Optional im Dokumentiert-Modus für ADRs und Modulseiten

## Verwandte Seiten

- [claude-md-youtube-verlauf](../vorlagen/claude-md-youtube-verlauf.md) — Ursprungsvorlage dieses Konzepts, dort Pflichtfeld
- [kontextrahmen-5w1h](kontextrahmen-5w1h.md) — Verwandte Frontmatter-Erweiterung; das Feld „Wann" erfasst dieselbe Achse feiner
- [lint-pruefung](lint-pruefung.md) — Wo das Feld gilt, ist der Klassifikations-Check Teil des Lint-Workflows
- [yaml-frontmatter](yaml-frontmatter.md) — Alternativ kann die Klassifikation ins Frontmatter wandern

---

[Wiki-Index](../index.md)
