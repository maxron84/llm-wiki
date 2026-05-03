---
date: 2026-05-03
type: anleitung
tags: [anleitung, setup, lokale-modelle, fortgeschritten]
status: active
---

# Lokale Modelle für Fortgeschrittene: Pygame-Projekt mit Qwen3

**Zusammenfassung**: Vollständige Einrichtung eines Pygame-Lernprojekts mit lokalem Qwen3-Modell (27B oder 35B) — Ollama als Backend, Continue in VS Codium als Frontend, halbautomatischer Wiki-Betrieb ohne Cloud.
**Zuletzt aktualisiert**: 2026-05-03

---

> Diese Anleitung setzt voraus: Terminal-Kenntnisse, VS Code oder VS Codium installiert, Erfahrung mit Projektverwaltung. Für den Einstieg mit lokalen Modellen → [lokale-modelle](lokale-modelle.md).

---

## Voraussetzungen

### Hardware

Qwen3 27B und 35B sind große Modelle. Die Mindestanforderungen:

| Modell | RAM (CPU-only) | VRAM (GPU) | Anmerkung |
|---|---|---|---|
| Qwen3:27b | 32 GB | 24 GB | Q4_K_M-Quantisierung |
| Qwen3:30b-a3b (MoE) | 24 GB | 18 GB | Mixture-of-Experts — schneller als 27B |
| Qwen3:32b | 40 GB | 28 GB | Vollständiges Modell |
| Qwen3 235B-A22B (MoE) | 64 GB | — | Nur für sehr starke Hardware |

> **Mac mit Apple Silicon (M2 Pro / M3 / M4)**: Unified Memory ist effizient — 32 GB reichen für 27B MLX-Versionen flüssig. 24 GB laufen das 30B MoE-Modell gut.

**Knapp an RAM?** Das Qwen3 30B-A22B MoE-Modell (`qwen3:30b-a3b`) ist ein guter Kompromiss: es verhält sich qualitativ wie ein 30B-Modell, aktiviert aber nur 3B Parameter pro Token — deutlich schneller und speicherschonender.

### Software

- **Ollama** — [ollama.com](https://ollama.com) (Backend für alle lokalen Modelle)
- **VS Codium** — [vscodium.com](https://vscodium.com) (oder VS Code)
- **Continue Extension** — im Extension-Marketplace von VS Code/Codium
- **Obsidian** — [obsidian.md](https://obsidian.md) (zum Lesen und Durchsuchen des Wikis)
- **Git** — für Versionierung des Projekts

---

## Schritt 1: Ollama einrichten

Falls noch nicht geschehen:

```bash
# Linux
curl -fsSL https://ollama.com/install.sh | sh

# macOS: .dmg von ollama.com herunterladen und installieren
# Windows: Installer von ollama.com
```

Modell herunterladen — je nach Hardware:

```bash
# Empfehlung für 32 GB RAM
ollama pull qwen3:27b

# Für 24 GB RAM oder GPU-knapp
ollama pull qwen3:30b-a3b

# Auf Mac mit Apple Silicon (MLX — 20-50% schneller)
ollama pull qwen3:27b-mlx
```

Der Download dauert je nach Modell 15–45 Minuten (15–20 GB). Anschließend prüfen:

```bash
ollama list
ollama run qwen3:27b   # Kurzer Test im Terminal, mit /bye beenden
```

Ollama läuft nach der Installation dauerhaft als lokaler Server auf `http://localhost:11434`.

---

## Schritt 2: VS Codium + Continue einrichten

### Continue installieren

1. VS Codium öffnen
2. Extensions öffnen: `Ctrl+Shift+X`
3. Nach `Continue` suchen → Extension von `Continue.dev` installieren
4. Continue-Icon erscheint in der linken Leiste (oder `Ctrl+Shift+L` / `Cmd+Shift+L`)

### Continue mit Ollama verbinden

Continue speichert seine Konfiguration in `~/.continue/config.json`. Öffnen über: Continue-Panel → Zahnrad-Icon → `config.json`.

Minimalconfig für Qwen3:

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

> **Für MoE-Variante**: `"model": "qwen3:30b-a3b"` eintragen. Für Mac MLX: `"model": "qwen3:27b-mlx"`.

Verbindung testen: Im Continue-Chat `Hallo` eingeben — das Modell sollte innerhalb weniger Sekunden antworten.

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
  CLAUDE.md          ← Vorlage (gleich anlegen)
  src/               ← Pygame-Code
  wiki/
    sitzungen/       ← Sitzungsnotizen (manuell oder durch KI vorgeschlagen)
```

---

## Schritt 4: Vorlage wählen und anpassen

Für ein Pygame-Projekt als erfahrener Programmierer gibt es zwei sinnvolle Optionen:

| Vorlage | Wenn... |
|---|---|
| [KI-Lehrer](../vorlagen/claude-md-lehrer.md) | Du Pygame systematisch lernen willst — mit Lehrplan, Lektionen, Fortschrittstracking |
| [Software](../vorlagen/claude-md-software.md) | Du weißt was du bauen willst — KI hilft beim Coden, dokumentiert, schlägt Architektur vor |

Als erfahrener Programmierer ist die **Software-Vorlage** meist sinnvoller. Du kennst bereits Programmiermuster — du willst die Pygame-API schnell erschließen, nicht von Grund auf unterrichtet werden.

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
**Stand**: Greenfield — noch kein Code

## Kontext

Ich bin erfahrener Python-Entwickler, kenne OOP und kenne keine Pygame-API.
Fokus: idiomatische Pygame-Patterns, keine Anfänger-Erklärungen für Python-Grundlagen.
```

---

## Schritt 5: System-Prompt in Continue einrichten

Da Continue keinen automatischen CLAUDE.md-Zugriff hat, muss die Vorlage als System-Prompt eingebunden werden.

**Option A — Global (für alle Chats)**:

In `~/.continue/config.json` ein `systemMessage`-Feld ergänzen:

```json
{
  "models": [...],
  "systemMessage": "HIER DEN VOLLSTÄNDIGEN INHALT DEINER CLAUDE.md EINFÜGEN"
}
```

Nachteil: Bei mehreren Projekten müsste man `config.json` wechseln.

**Option B — Per Session mit `@`-Referenz** (empfohlen):

Continue kann Dateien direkt als Kontext einbinden. Im Chat-Feld:

```
@CLAUDE.md Lies meine Projektbeschreibung und starte dann mit der Analyse.
```

Continue lädt die `CLAUDE.md` aus dem aktuell geöffneten Workspace-Ordner. Das funktioniert für jede Sitzung neu ohne Konfigurationsänderung.

> **VS Codium Workspace**: Continue liest `@`-Referenzen relativ zum geöffneten Ordner. Stelle sicher, dass du VS Codium mit `codium pygame-projekt/` oder über `Datei → Ordner öffnen` gestartet hast.

---

## Schritt 6: Erste Sitzung

### Projekt eröffnen

Im Continue-Chat:

```
@CLAUDE.md @src/

Wir fangen an. Ich möchte einen 2D-Platformer mit Pygame bauen.
Analysiere den aktuellen Projektstand und schlage eine Architektur vor.
```

Die `@src/`-Referenz fügt alle Dateien im `src/`-Ordner als Kontext ein — zu Beginn leer, später mit Code.

### Typischer Sitzungsablauf

```
# Neue Datei anlegen lassen
Erstelle src/main.py mit dem grundlegenden Pygame-Gameloop und einer leeren Spielerklasse.

# Code reviewen
@src/player.py Reviewe die Kollisionserkennung — gibt es Pygame-idiomatischere Ansätze?

# Architektur besprechen
@src/ Ich möchte ein Level-System einbauen. Wie strukturiere ich das sauber?
```

---

## Schritt 7: Wiki manuell führen

Lokale Modelle können keine Dateien automatisch schreiben — du kopierst Ergebnisse manuell.

### Sitzungsnotiz anlegen

Nach jeder Sitzung: Im Chat eine Zusammenfassung anfordern:

```
Fasse diese Sitzung als Markdown-Notiz zusammen:
- Was haben wir implementiert?
- Welche Entscheidungen wurden getroffen und warum?
- Offene Punkte für die nächste Sitzung?
```

Den generierten Text als `wiki/sitzungen/YYYY-MM-DD.md` speichern.

### Fortschrittsdatei führen

Lege `wiki/fortschritt.md` manuell an und halte sie aktuell:

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

Am Anfang jeder neuen Sitzung:

```
@CLAUDE.md @wiki/fortschritt.md @src/

Weiter. Was steht als nächstes an?
```

Das Modell hat damit den vollen Kontext: Projektziele + aktueller Stand + Code.

---

## Qwen3-spezifische Tipps

### Thinking Mode

Qwen3 27B und 35B unterstützen einen expliziten „Thinking"-Modus für schwierige Probleme. In Ollama aktivierbar durch einen speziellen System-Prompt-Zusatz:

```
/think Analysiere diese Kollisionslogik und finde den Fehler:
@src/physics.py
```

Das Modell „denkt" vor der Antwort — sichtbar als `<think>...</think>`-Block. Bei Architektur- und Debugging-Fragen deutlich besser als der Standard-Modus.

### Kontextfenster

Qwen3 27B hat ein 32K-Token-Kontextfenster. Bei wachsendem Projekt relevante Dateien selektiv einbinden statt `@src/`:

```
@src/player.py @src/physics.py    ← gut
@src/                              ← bei großem Projekt zu viel Kontext
```

### Temperatur für Code

Für Code-Generierung ist eine niedrigere Temperatur besser. In `config.json`:

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

Für kreative Aufgaben (Spielideen, Architektur-Brainstorming) `temperature: 0.7` verwenden.

---

## Obsidian für das Wiki

Nach ein paar Sitzungen Obsidian öffnen und den `wiki/`-Ordner als Vault öffnen:

1. Obsidian starten → „Ordner als Vault öffnen"
2. Den `wiki/`-Unterordner wählen (nicht den gesamten Projektordner)
3. Graph-Ansicht: `Ctrl+G` / `Cmd+G` — zeigt Verbindungen zwischen Sitzungsnotizen

Nützlich: Volltextsuche über alle Sitzungsnotizen, um frühere Architekturentscheidungen wiederzufinden.

---

## Häufige Probleme

**„Das Modell antwortet sehr langsam"**
→ CPU-Inference für 27B ist langsam (1–3 Token/s). Entweder auf das 30B MoE-Modell wechseln (`qwen3:30b-a3b`) oder Hardware mit GPU nutzen.
→ Auf Mac: MLX-Variante nutzen (`qwen3:27b-mlx`) — 3–5× schneller als Standard.

**„Continue findet @CLAUDE.md nicht"**
→ Prüfen: VS Codium mit dem richtigen Projektordner geöffnet? `Datei → Ordner öffnen` → Projektordner wählen.
→ `CLAUDE.md` muss im Wurzelordner des geöffneten Workspaces liegen.

**„Das Modell ignoriert die Projektbeschreibung"**
→ Bei langen Sitzungen kann das Modell frühe Kontextinhalte „vergessen". Vorlage erneut einbinden: `@CLAUDE.md Erinnerung: Hier sind die Projektregeln. Fahre mit der aktuellen Aufgabe fort.`

**„Zu viel Kontext — das Modell wird langsam oder liefert schlechtere Antworten"**
→ Sitzungen nicht zu lang werden lassen. Neue Unterhaltung im Continue-Chat starten und relevante Dateien neu einbinden statt alles aufzusammeln.

---

## Verwandte Seiten

- [lokale-modelle](lokale-modelle.md) — Einstieg: LM Studio, Jan, Ollama-Grundlagen
- [vorlage-einrichten](vorlage-einrichten.md) — Vorlage Schritt für Schritt einrichten (Anfänger-Version)
- [claude-md-software](../vorlagen/claude-md-software.md) — Software-Vorlage im Detail
- [claude-md-lehrer](../vorlagen/claude-md-lehrer.md) — KI-Lehrer-Vorlage (Alternative für strukturiertes Lernen)
- [obsidian](../werkzeuge/obsidian.md) — Obsidian-Funktionen für den Wiki-Betrieb

---

[Wiki-Index](../index.md)
