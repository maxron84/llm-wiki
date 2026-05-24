---
date: 2026-05-24
type: konzept
tags: [konzept, ux, desktop, ki-lehrer]
status: draft
---

# KI-Lehrer App

**Zusammenfassung**: Eine Tkinter-Desktop-Anwendung, die den KI-Lehrer-Ansatz für technisch unaffine Nutzer zugänglich macht — Kinder, Eltern, alle. GUI als Zugangslösung, nicht als Feature: die Komplexität dahinter (Git, Markdown, Heft-Muster) bleibt vollständig erhalten, wird aber unsichtbar.
**Quellen**: `raw/ki-lehrer_gui-entwurf_sehr-abstrakt.png`
**Zuletzt aktualisiert**: 2026-05-24

---

## Das Problem

Die bestehenden [KI-Lehrer-Vorlagen](../vorlagen/claude-md-lehrer.md) laufen in Claude Code — einem Terminal-Werkzeug. Wer die Vorlage nutzen will, muss:

- Ein Terminal öffnen und navigieren können
- Wissen was eine CLAUDE.md-Datei ist
- Markdown-Dateien bearbeiten
- Git-Commits verstehen (oder zumindest nicht fürchten)

Das ist für Entwickler kein Problem. Für ein Kind im Schulalter oder einen Elternteil ohne technischen Hintergrund ist das eine Wand. Das Konzept dahinter ist stark — der Zugang bisher nicht.

## Die Lösung: DAU-Prinzip als Designgrenze

**DAU-Prinzip** (Dümmster Anzunehmender User): Wenn jemand der gerade so ein Tablet bedienen kann die App starten und nutzen kann, dann kann es jeder. Das ist die Messlatte — keine Ausnahmen.

Konkret bedeutet das:
- Kein Terminal, keine Kommandozeilenbefehle sichtbar
- Keine Datei-Pfade, keine `.md`-Erweiterungen, keine Git-Begriffe
- Geführtes Setup beim ersten Start — eine Frage nach der anderen
- Klare, große Navigationspunkte statt technischer Menüs
- Fehlermeldungen in einfacher Sprache oder gar nicht sichtbar

Die GUI ist kein optionales Komfort-Layer. Sie ist die Bedingung dafür, dass das Konzept eine breitere Zielgruppe erreicht.

## GUI-Entwurf (Wireframe)

Der erste abstrakte Entwurf zeigt eine klassische Zweiteilung:

```
┌─────────────────────────────────────────────────────┐
│  KI Lehrer Programm                        [─][□][×] │
├──────────────┬──────────────────────────────────────┤
│ Navigation   │  Hauptbereich                         │
│              │                                       │
│ Lehrerpult   │  [Tab: Aufgaben] [Tab: Chat] [Tab: …] │
│ Aufgaben-    │                                       │
│  hefte       │  Inhalte je nach aktivem Tab          │
│ Fächer       │                                       │
│ Einstellungen│                                       │
│ …            │                                       │
└──────────────┴──────────────────────────────────────┘
```

**Linke Navigationsleiste**: Lehrerpult, Aufgabenhefte, Fächer, Einstellungen — erweiterbar.

**Hauptbereich mit Tabs**: „Aufgaben bearbeiten", „Mit Lehrer chatten", weitere nach Bedarf. Anzahl und Positionen der Tabs sind konfigurierbar — nicht statisch, modernes Layout.

**Ersteinrichtung**: Einmalig über geführtes Setup (wie in der Vorlage beschrieben), danach jederzeit über Einstellungen anpassbar.

## Was darunter läuft

Die GUI verbirgt Komplexität — sie entfernt sie nicht. Im Hintergrund laufen weiterhin:

- Das Interview-basierte Setup aus den [KI-Lehrer-Vorlagen](../vorlagen/claude-md-lehrer.md) — adaptiert das System an Fach, Alter und Vorkenntnisse
- Das [Heft-Muster](heft-muster.md) — Schülerarbeit in lebenden Markdown-Dateien, Git als implizite Lernhistorie
- Git-Commits nach jeder Session — automatisch im Hintergrund, unsichtbar für den Nutzer
- Die [drei-Ebenen-Architektur](drei-ebenen-architektur.md) — Rohquellen, Wiki, Schema

Der Nutzer sieht: „Aufgabe bearbeiten", „Mit Lehrer chatten". Was tatsächlich passiert: Markdown wird geschrieben, git committet, Kontext geladen.

## Zielgruppe

Primär: Kinder im Schulalter, technisch unerfahrene Erwachsene, Eltern die das System für ihre Kinder einrichten.

Die Zielgruppe bestimmt Designentscheidungen konkret:
- Schriftgröße und Kontrast: großzügig
- Sprache in der Oberfläche: einfach, kein Fachjargon
- Fehlerbehandlung: entweder unsichtbar oder in Klartext
- Konfigurierbarkeit: vorhanden, aber nicht im Weg

Technisch affine Nutzer können dieselbe App nutzen — sie verlieren nichts.

## Abgrenzung zu den bestehenden Vorlagen

Die [KI-Lehrer-Vorlagen](../vorlagen/claude-md-lehrer.md) sind CLAUDE.md-Dateien: sie definieren, *wie* Claude sich verhält. Sie setzen einen menschlichen Operator voraus, der Claude Code bedient.

Die KI-Lehrer App ist ein eigenständiges Programm: sie *ruft* die KI auf, verwaltet den Kontext, und zeigt Ergebnisse in einer GUI. Kein Claude Code im Terminal nötig.

| | KI-Lehrer Vorlage | KI-Lehrer App |
|---|---|---|
| Zugang | Claude Code CLI | Tkinter Desktop-App |
| Zielgruppe | Entwickler, Techniker | Alle, inkl. Kinder |
| Setup | CLAUDE.md kopieren | Geführtes Interview |
| Git | Manuell oder Claude | Automatisch im Hintergrund |
| Erweiterbarkeit | CLAUDE.md editieren | Einstellungsmenü |

## Projektstatus

**Konzeptphase.** Dieses Dokument hält die Idee und das Designmotiv fest.

Das Projekt wird als eigenständiges Projekt ausgelagert — mit einer eigenen Vorlage (kein CLAUDE.md-Format, ein neues Muster das noch definiert werden muss). Die Wissensbasis für das Projekt kommt aus diesem Wiki.

## Verwandte Seiten

- [claude-md-lehrer](../vorlagen/claude-md-lehrer.md) — Die inhaltliche Basis: Lehrstruktur, Interview, Phasen
- [claude-md-nachhilfe](../vorlagen/claude-md-nachhilfe.md) — Schwester-Vorlage: aufgabengetrieben, Kind sitzt selbst am Gerät
- [heft-muster](heft-muster.md) — Git-Commits als implizite Lernhistorie — läuft im Hintergrund der App
- [drei-ebenen-architektur](drei-ebenen-architektur.md) — Die Architektur die die App kapselt
- [multimodale-quellen](multimodale-quellen.md) — Bild als Quelle: dieser Entwurf stammt aus einem PNG

---

[Wiki-Index](../index.md)
