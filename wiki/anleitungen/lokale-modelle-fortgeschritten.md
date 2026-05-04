---
date: 2026-05-03
type: anleitung
tags: [anleitung, setup, lokale-modelle, fortgeschritten]
status: active
---

# Lokale Modelle für Fortgeschrittene: Pygame-Projekt mit Qwen3

**Zusammenfassung**: Einrichtung eines Pygame-Projekts mit lokalem Qwen3-Modell (27B oder 35B) — Ollama als Backend, Continue in VS Codium als Arbeitsumgebung, halbautomatischer Wiki-Betrieb ohne Cloud.
**Zuletzt aktualisiert**: 2026-05-03

---

> Diese Anleitung setzt voraus: Vertrautheit mit dem Terminal, VS Code oder VS Codium bereits installiert, Erfahrung in der Projektverwaltung. Einstieg mit lokalen Modellen → [lokale-modelle](lokale-modelle.md).

---

## Voraussetzungen

### Hardware

Qwen3 27B und 35B sind rechenintensive Modelle. Die Mindestanforderungen im Überblick:

| Modell | RAM (CPU) | VRAM (GPU) | Anmerkung |
|---|---|---|---|
| Qwen3:27b | 32 GB | 24 GB | Q4_K_M-Quantisierung |
| Qwen3:30b-a3b (MoE) | 24 GB | 18 GB | Mixture-of-Experts — schneller als 27B bei ähnlicher Qualität |
| Qwen3:32b | 40 GB | 28 GB | Vollständiges Modell |
| Qwen3 235B-A22B (MoE) | 64 GB | — | Nur für sehr leistungsstarke Hardware |

> **Mac mit Apple Silicon (M2 Pro / M3 / M4)**: Unified Memory wird besonders effizient genutzt — mit 32 GB laufen 27B-MLX-Modelle flüssig, mit 24 GB das 30B-MoE-Modell.

**Wenig RAM?** Das Qwen3 30B-A3B MoE-Modell (`qwen3:30b-a3b`) ist ein guter Kompromiss: Es liefert die Qualität eines 30B-Modells, aktiviert aber nur 3B Parameter pro Token — deutlich schneller und speichersparender als ein vollständiges Modell gleicher Größe.

### Software

- **Ollama** — [ollama.com](https://ollama.com) (Backend für alle lokalen Modelle)
- **VS Codium** — [vscodium.com](https://vscodium.com) (oder VS Code)
- **Continue Extension** — im Extension-Marketplace von VS Code/Codium
- **Obsidian** — [obsidian.md](https://obsidian.md) (zum Lesen und Durchsuchen des Wikis)
- **Git** — zur Versionierung des Projekts

---

## Schritt 1: Ollama einrichten

Falls noch nicht vorhanden:

```bash
# Linux
curl -fsSL https://ollama.com/install.sh | sh

# macOS: .dmg von ollama.com herunterladen und installieren
# Windows: Installer von ollama.com
```

Modell herunterladen — je nach verfügbarer Hardware:

```bash
# Empfehlung ab 32 GB RAM
ollama pull qwen3:27b

# Für 24 GB RAM oder GPU-knapp
ollama pull qwen3:30b-a3b

# Mac mit Apple Silicon (MLX — 20–50 % schneller als Standard)
ollama pull qwen3:27b-mlx
```

Der Download dauert je nach Modell 15–45 Minuten (ca. 15–20 GB). Anschließend kurz testen:

```bash
ollama list
ollama run qwen3:27b   # Direkttest im Terminal, mit /bye beenden
```

Nach der Installation läuft Ollama dauerhaft als lokaler Server auf `http://localhost:11434`.

---

## Schritt 2: VS Codium und Continue einrichten

### Continue installieren

1. VS Codium öffnen
2. Extension-Suche öffnen: `Ctrl+Shift+X`
3. Nach `Continue` suchen → Extension von `Continue.dev` installieren
4. Das Continue-Icon erscheint in der linken Leiste (alternativ: `Ctrl+Shift+L` / `Cmd+Shift+L`)

### Continue mit Ollama verbinden

Continue speichert seine Konfiguration in `~/.continue/config.json`. Öffnen: Continue-Panel → Zahnrad-Icon → `config.json`.

Grundkonfiguration für Qwen3:

```json
{
  "models": [
    {
      "title": "Qwen3 27B (lokal)",
      "provider": "ollama",
      "model": "qwen3:27b",
      "contextLength": 32768
    }
  ],
  "tabAutocompleteModel": {
    "title": "Qwen3 27B Autocomplete",
    "provider": "ollama",
    "model": "qwen3:27b"
  }
}
```

> **MoE-Variante**: `"model": "qwen3:30b-a3b"` eintragen. Mac MLX: `"model": "qwen3:27b-mlx"`.

Zum Testen im Continue-Chat `Hallo` eingeben — das Modell sollte innerhalb weniger Sekunden antworten.

---

## Schritt 3: Projektordner anlegen

```bash
mkdir pygame-projekt
cd pygame-projekt
git init
mkdir -p src wiki/sitzungen
```

Projektstruktur:

```
pygame-projekt/
  CLAUDE.md          ← Vorlage (im nächsten Schritt anlegen)
  src/               ← Pygame-Code
  wiki/
    sitzungen/       ← Sitzungsnotizen (manuell gepflegt)
```

---

## Schritt 4: Vorlage wählen und anpassen

Für ein Pygame-Projekt als erfahrener Programmierer bieten sich zwei Vorlagen an:

| Vorlage | Geeignet wenn... |
|---|---|
| [KI-Lehrer](../vorlagen/claude-md-lehrer.md) | Pygame systematisch gelernt werden soll — mit Lehrplan, Lektionen, Fortschrittstracking |
| [Software](../vorlagen/claude-md-software.md) | Das Ziel bereits feststeht — KI hilft beim Coden, dokumentiert und schlägt Architektur vor |

Als erfahrener Programmierer ist die **Software-Vorlage** meist die bessere Wahl. Programmierprinzipien sind bekannt — gefragt ist eine zügige Einarbeitung in die Pygame-API, kein Unterricht von Grund auf.

### Vorlage kopieren

1. [wiki/vorlagen/claude-md-software.md](../vorlagen/claude-md-software.md) öffnen
2. Den Inhalt unter `## Vorlage` kopieren (ohne die ` ```` `-Rahmenzeilen)
3. Als `CLAUDE.md` im Projektordner speichern

### Platzhalter füllen

Typische Anpassungen für ein Pygame-Projekt:

```markdown
## Projekt

**Name**: Pygame Side-Scroller
**Sprache**: Python / Pygame
**Ziel**: Ein lauffähiges 2D-Platformer-Spiel mit Spieler, Gegnern und Level-System
**Stand**: Greenfield — noch kein Code vorhanden

## Kontext

Erfahrener Python-Entwickler, vertraut mit OOP, neu in der Pygame-API.
Fokus: idiomatische Pygame-Patterns, keine Erklärungen zu Python-Grundlagen.
```

---

## Schritt 5: System-Prompt in Continue einrichten

Continue liest `CLAUDE.md` nicht automatisch — die Vorlage muss als Kontext übergeben werden.

**Option A — Global (gilt für alle Chats)**:

In `~/.continue/config.json` ein `systemMessage`-Feld ergänzen:

```json
{
  "models": [...],
  "systemMessage": "HIER DEN VOLLSTÄNDIGEN INHALT DER CLAUDE.md EINFÜGEN"
}
```

Nachteil: Bei mehreren Projekten muss die `config.json` jeweils angepasst werden.

**Option B — Pro Sitzung mit `@`-Referenz** (empfohlen):

Continue kann Dateien direkt als Kontext einbinden. Im Chat-Feld einfach eingeben:

```
@CLAUDE.md Lies meine Projektbeschreibung und starte dann mit der Analyse.
```

Continue lädt die `CLAUDE.md` aus dem aktuell geöffneten Workspace-Ordner — ohne jede Konfigurationsänderung, bei jeder Sitzung frisch.

> **Wichtig**: Continue liest `@`-Referenzen relativ zum geöffneten Ordner. VS Codium muss mit dem Projektordner gestartet sein: `codium pygame-projekt/` oder `Datei → Ordner öffnen`.

---

## Schritt 6: Erste Sitzung

### Sitzung starten

Im Continue-Chat:

```
@CLAUDE.md @src/

Wir fangen an. Ich möchte einen 2D-Platformer mit Pygame bauen.
Analysiere den aktuellen Projektstand und schlage eine Architektur vor.
```

Die `@src/`-Referenz bindet alle Dateien im `src/`-Ordner als Kontext ein — zu Beginn leer, später mit wachsendem Code.

### Typischer Sitzungsablauf

```
# Neue Datei anlegen
Erstelle src/main.py mit einem grundlegenden Pygame-Gameloop und einer leeren Spielerklasse.

# Code reviewen
@src/player.py Überprüfe die Kollisionserkennung — gibt es einen Pygame-idomatischeren Ansatz?

# Architektur besprechen
@src/ Ich möchte ein Level-System einbauen. Wie lässt sich das sauber strukturieren?
```

---

## Schritt 7: Wiki manuell führen

Lokale Modelle können keine Dateien selbständig schreiben — Ergebnisse werden manuell gespeichert.

### Sitzungsnotiz anlegen

Am Ende jeder Sitzung eine Zusammenfassung anfordern:

```
Fasse diese Sitzung als Markdown-Notiz zusammen:
- Was haben wir implementiert?
- Welche Entscheidungen wurden getroffen und warum?
- Offene Punkte für die nächste Sitzung?
```

Den generierten Text als `wiki/sitzungen/YYYY-MM-DD.md` speichern.

### Fortschrittsdatei führen

`wiki/fortschritt.md` manuell anlegen und nach jeder Sitzung aktualisieren:

```markdown
# Fortschritt

## Abgeschlossen
- [x] Pygame-Grundsetup, Gameloop
- [x] Spielerklasse mit Bewegung

## In Arbeit
- [ ] Kollisionserkennung mit Tilemaps

## Nächste Schritte
- Gegner-KI
- Soundsystem
```

Zu Beginn jeder neuen Sitzung:

```
@CLAUDE.md @wiki/fortschritt.md @src/

Weiter. Was steht als nächstes an?
```

Das Modell hat damit den vollständigen Kontext: Projektziele, aktueller Stand und vorhandener Code.

---

## Qwen3-spezifische Tipps

### Thinking Mode

Qwen3 27B und größer unterstützen einen expliziten Denkmodus für anspruchsvolle Fragen. Er lässt sich in Ollama durch einen vorangestellten Befehl aktivieren:

```
/think Analysiere diese Kollisionslogik und finde den Fehler:
@src/physics.py
```

Das Modell denkt vor der Antwort — sichtbar als `<think>...</think>`-Block. Bei Architektur- und Debugging-Fragen liefert dieser Modus spürbar bessere Ergebnisse als der Standard-Modus.

### Kontextfenster

Qwen3 27B hat ein 32K-Token-Kontextfenster. Bei wachsendem Projekt gezielt einzelne Dateien einbinden statt den gesamten Ordner:

```
@src/player.py @src/physics.py    ← besser: gezielte Auswahl
@src/                              ← bei großem Projekt zu viel Kontext auf einmal
```

### Temperatur

Für Code-Generierung empfiehlt sich ein niedriger Temperaturwert. In `config.json`:

```json
{
  "models": [
    {
      "title": "Qwen3 27B (lokal)",
      "provider": "ollama",
      "model": "qwen3:27b",
      "contextLength": 32768,
      "completionOptions": {
        "temperature": 0.2
      }
    }
  ]
}
```

Für kreative Aufgaben wie Spielideen oder Architektur-Brainstorming hingegen `temperature: 0.7` verwenden.

---

## Obsidian für das Wiki

Nach einigen Sitzungen den `wiki/`-Ordner in Obsidian als Vault einrichten:

1. Obsidian starten → „Ordner als Vault öffnen"
2. Den `wiki/`-Unterordner wählen (nicht den gesamten Projektordner)
3. Graph-Ansicht öffnen: `Ctrl+G` / `Cmd+G` — zeigt Verbindungen zwischen Sitzungsnotizen

Besonders nützlich ist die Volltextsuche über alle Sitzungsnotizen — so lassen sich frühere Architekturentscheidungen schnell wiederfinden.

---

## Häufige Probleme

**„Das Modell antwortet sehr langsam"**
→ CPU-Inferenz für ein 27B-Modell ist langsam (1–3 Token/s). Entweder auf das MoE-Modell wechseln (`qwen3:30b-a3b`) oder auf einem Rechner mit dedizierter GPU arbeiten.
→ Mac-Nutzer: MLX-Variante verwenden (`qwen3:27b-mlx`) — 3–5× schneller als die Standardversion.

**„Continue findet @CLAUDE.md nicht"**
→ Prüfen: Ist VS Codium mit dem richtigen Projektordner geöffnet? `Datei → Ordner öffnen` → Projektordner wählen.
→ `CLAUDE.md` muss im Stammverzeichnis des geöffneten Workspaces liegen.

**„Das Modell ignoriert die Projektbeschreibung"**
→ Bei langen Sitzungen kann die CLAUDE.md aus dem Kontext fallen. Einfach erneut einbinden: `@CLAUDE.md Erinnerung: Hier sind die Projektregeln. Fahre mit der aktuellen Aufgabe fort.`

**„Zu viel Kontext — das Modell wird langsam oder ungenauer"**
→ Sitzungen nicht zu lang werden lassen. Eine neue Unterhaltung im Continue-Chat starten und nur die relevanten Dateien neu einbinden, statt den gesamten Verlauf fortzuführen.

---

## Verwandte Seiten

- [beratungs-crm](../projekte/beratungs-crm.md) — Anwendungsbeispiel: lokales CRM mit Qwen3 für psychologische Beraterin
- [lokale-modelle](lokale-modelle.md) — Einstieg: LM Studio, Jan, Ollama-Grundlagen
- [vorlage-einrichten](vorlage-einrichten.md) — Vorlage einrichten Schritt für Schritt (Anfänger-Version)
- [claude-md-software](../vorlagen/claude-md-software.md) — Software-Vorlage im Detail
- [claude-md-lehrer](../vorlagen/claude-md-lehrer.md) — KI-Lehrer-Vorlage (Alternative für strukturiertes Lernen)
- [obsidian](../werkzeuge/obsidian.md) — Obsidian-Funktionen für den Wiki-Betrieb

---

[Wiki-Index](../index.md)
