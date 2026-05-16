---
date: 2026-05-02
type: anleitung
tags: [anleitung, setup]
status: active
---

# Erste Schritte: LLM-Wiki einrichten

**Zusammenfassung**: Schritt-für-Schritt-Einrichtung eines LLM-Wiki-Projekts — von der Installation bis zur ersten Sitzung mit Claude.
**Zuletzt aktualisiert**: 2026-05-11

---

> Diese Anleitung folgt dem **einfachen Pfad**: Claude Code Desktop App (Windows/macOS) + Claude.ai-Abo.
> Linux-Nutzer und alle, die VS Code, einen API-Key oder Git-Backup wollen → [Fortgeschrittener Pfad](#fortgeschrittener-pfad-optional) am Ende dieser Seite.

---

## Was du brauchst

Bevor es losgeht:

- **Ein Claude.ai-Konto** mit aktivem Abo (Pro oder Team) — [claude.ai](https://claude.ai)
- **Windows oder macOS** — für den einfachen Pfad mit der Desktop App
- **Internetverbindung** für die Installation
- **~20 Minuten** für das erste Setup

---

## Schritt 1: Claude Code Desktop installieren

Claude Code Desktop ist die einfachste Art, mit Claude zu arbeiten — keine Terminal-Kenntnisse nötig.

1. Öffne [claude.ai](https://claude.ai) im Browser und melde dich an
2. Suche im Menü nach **Claude Code** oder **Download** — lade die Desktop App für dein Betriebssystem herunter
3. Installieren:
   - **Windows**: `.exe`-Datei herunterladen → Doppelklick → Installationsassistenten folgen
   - **macOS**: `.dmg`-Datei herunterladen → Öffnen → App-Symbol in den Programme-Ordner ziehen
4. App starten und mit deinem Claude.ai-Konto anmelden

> **Linux**: Die Desktop App gibt es nur für Windows und macOS. Für Linux → [VS Code-Pfad weiter unten](#vs-code-oder-vs-codium).

---

## Schritt 2: Obsidian installieren

Obsidian ist ein kostenloser Markdown-Editor — damit liest und durchsuchst du dein Wiki bequem. Du brauchst ihn erst **nach der ersten Claude-Sitzung** (das Wiki existiert vorher noch nicht).

1. Öffne [obsidian.md](https://obsidian.md) im Browser
2. Lade die Version für dein Betriebssystem herunter:
   - **Windows**: `.exe`-Installer → ausführen
   - **macOS**: `.dmg`-Datei → App in Programme ziehen
   - **Linux**: `.AppImage` oder `.deb`-Paket
3. Obsidian starten — noch nichts öffnen, erst nach Schritt 5

---

## Schritt 3: Projektordner anlegen

Lege einen Ordner für dein Projekt an. Das ist der Ort, an dem dein Wiki und alle Quellen gespeichert werden.

**Regeln für den Ordnernamen:**

- Nur Kleinbuchstaben
- Bindestriche statt Leerzeichen (kein `Mathe Klasse 5`, sondern `mathe`)
- Keine Sonderzeichen, keine Umlaute im Ordnernamen

**Beispiele:**

| Vorlage | Guter Ordnername |
|---|---|
| Nachhilfe Mathe | `mathe` |
| Kochrezepte-Wiki | `mein-kochbuch` |
| Software-Projekt | `mein-projekt` |
| Lernprojekt Pygame | `pygame-lernen` |

Den Ordner kannst du legen, wo du möchtest — z.B. unter `Dokumente` oder auf dem Desktop.

> **Nachhilfe in mehreren Fächern?** Lege zuerst einen Elternordner an (z.B. `max-schule/`) und darin je einen Unterordner pro Fach. Mehr dazu im Abschnitt [## Elternordner-Setup](../vorlagen/claude-md-nachhilfe.md) der Nachhilfe-Vorlage.

---

## Schritt 4: Vorlage auswählen und CLAUDE.md anlegen

Jedes Projekt braucht eine Datei namens `CLAUDE.md` im Projektordner. Diese Datei erklärt Claude, was er tun soll.

### 4a — Passende Vorlage wählen

| Vorlage | Wann passend |
|---|---|
| [Nachhilfe](../vorlagen/claude-md-nachhilfe.md) | Schulfächer, Aufgaben aus Buch/Foto — Kind sitzt selbst am Gerät |
| [Laienlehrer](../vorlagen/claude-md-laienlehrer.md) | Elternteil oder Laie unterrichtet — KI coacht den Erwachsenen, nicht das Kind |
| [KI-Lehrer](../vorlagen/claude-md-lehrer.md) | Lernprojekte mit eigenem Kurs und Lehrplan |
| [Software](../vorlagen/claude-md-software.md) | Software-Projekte, KI schreibt und dokumentiert Code |
| [Software-Begleiter](../vorlagen/claude-md-software-begleiter.md) | Mensch schreibt Code, KI dokumentiert und reviewt |
| [Team-Wiki](../vorlagen/claude-md-team.md) | Gemeinsames Wiki für 2–8 Personen mit Ownership und Provenienz-Tags |
| [Rezepte & Ernährung](../vorlagen/claude-md-rezepte-ernaehrung.md) | Kochbücher, Rezepte, eigene Experimente |
| [YouTube-Verlauf](../vorlagen/claude-md-youtube-verlauf.md) | YouTube-Videos und Transkripte als Wissensquelle |
| [Legacy-Forensik](../vorlagen/claude-md-legacy-forensik.md) | Unbekannte oder gewachsene Codebase analysieren |

### 4b — Vorlage kopieren

1. Öffne die gewählte Vorlagenseite (Links in der Tabelle oben)
2. Scrolle zum Abschnitt **`## Vorlage`**
3. Dort steht der Inhalt zwischen ` ```` `-Markierungen — **nur den Inhalt dazwischen kopieren**, nicht die ` ```` `-Zeilen selbst
4. Lege im Projektordner eine neue Datei namens `CLAUDE.md` an (Dateiname exakt so, Großbuchstaben)
5. Kopierten Inhalt einfügen

### 4c — Platzhalter ersetzen

Ersetze alle `{{...}}`-Felder mit deinen Angaben. Beispiel aus der Nachhilfe-Vorlage:

```
{{Schulfach, z.B. "Mathematik"}}  →  Mathematik
{{Name des Schülers}}             →  Max
{{Klasse}}                        →  5
```

---

## Schritt 5: Erste Sitzung starten

1. Öffne **Claude Code Desktop**
2. Klicke auf **„Ordner öffnen"** und wähle deinen Projektordner
3. Schreibe in das Chat-Feld:

   ```
   Wir fangen an
   ```

4. Claude liest die `CLAUDE.md` und startet entsprechend der gewählten Vorlage

**Was jetzt passiert, hängt von der Vorlage ab:**

| Vorlage | Erster Start |
|---|---|
| KI-Lehrer | Aufnahmegespräch → Claude generiert `wiki/lehrplan.md` |
| Nachhilfe | Kurze Begrüßung → Warten auf erste Aufgabe oder Foto |
| Laienlehrer | Kurze Vorstellung → Warten auf Stundenauftrag oder Korrekturaufgabe |
| Software | Claude liest bestehenden Code und legt Wiki-Seiten an |
| Software-Begleiter | Claude liest Code, richtet Dokumentationsstruktur ein |
| Team-Wiki | Claude stellt Onboarding-Fragen → legt `wiki/team.md` an |
| Rezepte | Warten auf erste Quelle in `raw/` oder `clippings/` |
| YouTube-Verlauf | Warten auf erste Video-URL oder Transkript in `clippings/` |
| Legacy-Forensik | Claude liest Codebase, erstellt erste Übersichtsseite |

> Claude legt den `wiki/`-Ordner und alle Unterordner **automatisch beim ersten Schreiben** an — du musst nichts manuell erstellen.

---

## Schritt 6: Obsidian öffnen

Jetzt, nach der ersten Sitzung, existiert der `wiki/`-Ordner.

1. Starte Obsidian
2. Klicke auf **„Ordner als Vault öffnen"**
3. Wähle den **`wiki/`-Unterordner** deines Projekts — nicht den gesamten Projektordner
4. Obsidian zeigt alle Wiki-Seiten in der linken Seitenleiste

**Nützliche Ansichten:**

| Ansicht | Öffnen mit | Wozu |
|---|---|---|
| Datei-Explorer | Linke Leiste | Alle Seiten nach Ordner |
| Graph-Ansicht | `Strg+G` / `Cmd+G` | Vernetzung aller Seiten visualisieren |
| Volltextsuche | `Strg+F` / `Cmd+F` | Im gesamten Wiki suchen |

Für erweiterte Obsidian-Funktionen (Dataview-Plugin, Präsentationen) → [obsidian](../werkzeuge/obsidian.md)

---

## Fertig

Das war's für das Grundsetup. Von hier aus wächst das Wiki automatisch: Jedes Mal wenn du Claude eine Aufgabe gibst oder eine Frage stellst, schreibt er neue Seiten oder aktualisiert bestehende.

**Wie es weitergeht:**

- Lies den Abschnitt **`## Benutzung`** in der Seite deiner gewählten Vorlage — dort stehen vorlage-spezifische Hinweise für die laufende Nutzung
- Lege bei Bedarf einen `raw/`-Ordner für Quellmaterial an (Fotos, Scans, Texte) — je nach Vorlage
- Nach ein paar Sitzungen: `wiki/index.md` in Obsidian öffnen — dort siehst du alle bisher angelegten Seiten

---

## Was kostet das?

Kurze Antwort: **weniger als du wahrscheinlich denkst.**

Karpathys eigener Use Case — 100 Wiki-Seiten, ~70 Quellen, 3 Monate, Sonnet 4.6 — kostet bei API-Nutzung **ca. $51 gesamt**. Das ist weniger als ein Monatsabo vieler Notiz-Tools.

| Nutzung | Modell | Kosten (Richtwert) |
|---|---|---|
| 100 Seiten, 3 Monate | Sonnet 4.6 | ~$51 gesamt |
| 100 Seiten, 1 Jahr | Haiku 4.5 | ~$7 gesamt |
| 500 Seiten, 6 Monate | Sonnet 4.6 | ~$660 gesamt |

(Quelle: [usd-pro-wiki-seite](../konzepte/usd-pro-wiki-seite.md))

**Claude.ai Pro-Abo vs. API-Key**: Das Pro-Abo ist eine Monats-Flatrate (~$20/Monat) — rechnet sich bei täglicher Nutzung. Der API-Key ist verbrauchsbasiert — günstiger bei gelegentlicher Nutzung oder wenn du die Kosten genau kontrollieren willst.

Wenn das Wiki wächst und die Kosten steigen, gibt es klare Strategien dagegen — von einfach bis fortgeschritten: [token-sparen](token-sparen.md)

---

## Fortgeschrittener Pfad (optional)

Dieser Abschnitt richtet sich an Linux-Nutzer und alle, die mehr Kontrolle wollen: eigene Entwicklungsumgebung, API-Key statt Abo, oder Git-Backup.

---

### VS Code oder VS Codium

VS Code ist ein kostenloser Code-Editor von Microsoft. VS Codium ist die datenschutzfreundliche Variante ohne Telemetrie — funktional identisch.

**Installation:**

- VS Code: [code.visualstudio.com](https://code.visualstudio.com) → Download für dein Betriebssystem
- VS Codium: [vscodium.com](https://vscodium.com) → Download für dein Betriebssystem

**Claude Code Extension installieren:**

1. VS Code / VS Codium starten
2. Erweiterungen öffnen: `Strg+Shift+X` (Windows/Linux) oder `Cmd+Shift+X` (macOS)
3. Nach **„Claude Code"** suchen → **Installieren**
4. Projektordner öffnen: **Datei → Ordner öffnen** → deinen Projektordner wählen
5. Claude Code in der Seitenleiste öffnen und API-Key oder Claude.ai-Konto verbinden

> **Linux-Nutzer**: Das ist euer Hauptpfad — Desktop App gibt es nicht für Linux.

---

### API-Key einrichten

Ein API-Key ermöglicht die direkte Nutzung der Anthropic-API, unabhängig von einem Claude.ai-Abo. Abrechnung erfolgt verbrauchsbasiert (Token).

1. Öffne [console.anthropic.com](https://console.anthropic.com) und melde dich an
2. Navigiere zu **API Keys** → **Create Key**
3. Key kopieren und **sicher speichern** — er wird nur einmal angezeigt
4. In Claude Code Desktop: Einstellungen → API Key eintragen
5. In VS Code Extension: Claude Code Einstellungen → API Key eintragen

> **Abo vs. API-Key**: Claude.ai Pro ist eine Monats-Flatrate — günstig bei regelmäßiger, intensiver Nutzung. Der API-Key ist verbrauchsbasiert — günstiger bei seltener Nutzung. Für Kinder-Nachhilfe mit täglicher Nutzung ist Pro meist wirtschaftlicher.

---

### Git für Backup und Versionsverlauf

Git speichert jede Version deines Wikis — du kannst jederzeit zu einem früheren Stand zurück. Nützlich wenn Claude versehentlich etwas überschreibt.

**Git installieren:**

- **Windows**: [git-scm.com](https://git-scm.com) → „Git for Windows" herunterladen und installieren
- **macOS**: Terminal öffnen → `git --version` eingeben. Wenn nicht installiert: Xcode Command Line Tools werden automatisch angeboten
- **Linux**: Terminal → `sudo apt install git` (Debian/Ubuntu) oder `sudo dnf install git` (Fedora)

**Projekt als Git-Repository einrichten:**

Terminal öffnen, in den Projektordner navigieren, dann:

```bash
git init
git add .
git commit -m "Erstes Commit"
```

**Für Cloud-Backup** (optional): Kostenloses Repository auf [github.com](https://github.com) oder [gitlab.com](https://gitlab.com) anlegen und mit dem lokalen Ordner verbinden.

Wenn mehrere Personen gemeinsam am Wiki arbeiten → [zusammenarbeit-git](zusammenarbeit-git.md)

---

## Verwandte Seiten

- [claude-code](../werkzeuge/claude-code.md) — Claude Code im Detail
- [obsidian](../werkzeuge/obsidian.md) — Obsidian-Funktionen und Plugins
- [llm-wiki-muster](../konzepte/llm-wiki-muster.md) — Das Konzept hinter diesem Wiki-Ansatz
- [claude-md-nachhilfe](../vorlagen/claude-md-nachhilfe.md) — Nachhilfe-Vorlage inkl. Elternordner-Setup
- [claude-md-laienlehrer](../vorlagen/claude-md-laienlehrer.md) — Laienlehrer-Vorlage: KI coacht den Erwachsenen
- [claude-md-lehrer](../vorlagen/claude-md-lehrer.md) — KI-Lehrer-Vorlage
- [claude-md-software](../vorlagen/claude-md-software.md) — Software-Vorlage
- [claude-md-team](../vorlagen/claude-md-team.md) — Team-Wiki-Vorlage
- [vorlage-einrichten](vorlage-einrichten.md) — Nächster Schritt: wie du eine Vorlage in ein echtes Projekt verwandelst
- [token-sparen](token-sparen.md) — Strategien zur Kostensenkung, wenn das Wiki wächst
- [usd-pro-wiki-seite](../konzepte/usd-pro-wiki-seite.md) — Detaillierte Kostenkalkulation: $/WP-Metrik, drei Szenarien

---

[Wiki-Index](../index.md)
