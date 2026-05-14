---
date: 2026-05-03
type: anleitung
tags: [anleitung, setup, lokale-modelle, fortgeschritten]
status: active
---

# Lokale Modelle für Fortgeschrittene: Pygame-Projekt mit Qwen3

**Zusammenfassung**: Einrichtung eines Pygame-Projekts mit lokalem Qwen3-Modell — Ollama als Backend, Continue in VS Codium als Arbeitsumgebung, halbautomatischer Wiki-Betrieb ohne Cloud.
**Zuletzt aktualisiert**: 2026-05-13

---

> Diese Anleitung setzt voraus: Vertrautheit mit dem Terminal, VS Code oder VS Codium bereits installiert, Erfahrung in der Projektverwaltung. Einstieg mit lokalen Modellen → [lokale-modelle](lokale-modelle.md).

---

## Voraussetzungen

### Hardware

Offizielle Qwen3-Modelle auf Ollama (Stand Mai 2026) und ihre VRAM-Anforderungen:

| Modell | Größe | Kontext | VRAM | Anmerkung |
|---|---|---|---|---|
| qwen3:8b | 5,2 GB | 40K | ~6 GB | Einstieg, sehr schnell |
| qwen3:14b | 9,3 GB | 40K | ~10 GB | **Empfehlung für 16 GB VRAM** |
| qwen3:30b | 19 GB | 256K | ~20 GB | Übersteigt 16 GB → CPU-Offload |
| qwen3:32b | 20 GB | 40K | ~21 GB | Für 24+ GB VRAM |

> **RTX 5080 (16 GB VRAM)**: `qwen3:14b` ist die saubere Wahl — passt vollständig ins VRAM, 40K Kontext, kein CPU-Anteil. Für den KI-Lehrer-Betrieb empfohlen.

> **Mac mit Apple Silicon (M2 Pro / M3 / M4)**: Unified Memory wird besonders effizient genutzt — mit 32 GB läuft `qwen3:30b` flüssig, mit 16 GB `qwen3:14b`.

**Community-Modelle (Praxistest RTX 5080)**: `yolo0perris/Qwen3.5-27B-Claude-4.6-Opus-Reasoning-Distilled-GGUF_Q3_K_M` belegt 18 GB und läuft mit 78% GPU / 22% CPU — die Geschwindigkeit ist akzeptabel. **Kritische Einschränkung**: Ollama begrenzt den Kontext automatisch auf 4096 Token, weil kein VRAM-Puffer für einen größeren KV-Cache bleibt. Für den KI-Lehrer (CLAUDE.md + Lehrplan + Fortschritt + Gesprächsverlauf = 6–11K Token) reicht das nicht. Empfehlung erst ab 32 GB VRAM (RTX 5090).

| | 27B Distillat (RTX 5080) | qwen3:14b (RTX 5080) |
|---|---|---|
| Modellgröße | 18 GB | 9,3 GB |
| CPU-Anteil | 22% | 0% |
| Verfügbarer Kontext | **4K** | **40K** |
| KI-Lehrer geeignet | ❌ Kontext zu klein | ✅ |

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
# 16 GB VRAM (z.B. RTX 5080) — Empfehlung
ollama pull qwen3:14b

# 24+ GB VRAM (z.B. RTX 4090, RTX 5090)
ollama pull qwen3:32b

# Mac mit Apple Silicon, 32 GB Unified Memory
ollama pull qwen3:30b
```

Der Download dauert je nach Modell 10–30 Minuten. Anschließend kurz testen:

```bash
ollama list
ollama run qwen3:14b   # Direkttest im Terminal, mit /bye beenden
```

Nach der Installation läuft Ollama dauerhaft als lokaler Server auf `http://localhost:11434`.

> **Wichtig für Docker-Nutzer**: Ollama muss zwingend mit `--gpus=all` gestartet werden, sonst läuft Inferenz auf der CPU — auch wenn eine GPU vorhanden ist. Für Autostart bei Systemboot `--restart=always` ergänzen:
> ```bash
> docker stop ollama && docker rm ollama
> docker run -d --gpus=all --restart=always \
>   -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
> ```
> GPU-Nutzung prüfen: `docker exec ollama nvidia-smi`

---

## Schritt 2: VS Codium und Continue einrichten

### Continue installieren

1. VS Codium öffnen
2. Extension-Suche öffnen: `Ctrl+Shift+X`
3. Nach `Continue` suchen → Extension von `Continue.dev` installieren
4. Das Continue-Icon erscheint in der linken Leiste (alternativ: `Ctrl+Shift+L` / `Cmd+Shift+L`)

### Continue mit Ollama verbinden

Continue speichert seine Konfiguration in `~/.continue/config.json`. Öffnen: Continue-Panel → Zahnrad-Icon → `config.json`.

Grundkonfiguration für Qwen3 (Beispiel RTX 5080):

```json
{
  "models": [
    {
      "title": "Qwen3 14B (lokal)",
      "provider": "ollama",
      "model": "qwen3:14b",
      "contextLength": 40960
    }
  ],
  "tabAutocompleteModel": {
    "title": "Qwen3 14B Autocomplete",
    "provider": "ollama",
    "model": "qwen3:14b"
  }
}
```

> **Größeres VRAM**: `"model": "qwen3:32b"`, `"contextLength": 40960`. Mac mit 32 GB: `"model": "qwen3:30b"`, `"contextLength": 262144`.

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
    code-stand.md    ← Kompakte Codebasis-Übersicht (für 32k-Kontext, s. Schritt 7)
    sitzungen/       ← Sitzungsnotizen (manuell gepflegt)
```

---

## Schritt 4: Vorlage wählen und anpassen

Für ein Pygame-Projekt als erfahrener Programmierer bieten sich zwei Vorlagen an:

| Vorlage | Geeignet wenn... |
|---|---|
| [KI-Lehrer](../vorlagen/claude-md-lehrer.md) | Pygame systematisch gelernt werden soll — mit Lehrplan, Lektionen, Fortschrittstracking |
| [Software](../vorlagen/claude-md-software.md) | Das Ziel bereits feststeht — KI hilft beim Coden, dokumentiert und schlägt Architektur vor |

**Wahl nach Ziel:**

- **KI-Lehrer** — wenn Pygame strukturiert gelernt werden soll: Lehrplan, Lektionen, adaptives Tempo. Für 32k-Modelle den Code-Digest aktivieren (s. Schritt 7).
- **Software** — wenn das Ziel bereits feststeht und nur Coding-Hilfe gefragt ist. Kein Lehrplan, weniger Kontext-Overhead.

### Vorlage kopieren

1. Gewählte Vorlage öffnen: [KI-Lehrer](../vorlagen/claude-md-lehrer.md) oder [Software](../vorlagen/claude-md-software.md)
2. Den Inhalt unter `## Vorlage` kopieren (ohne die ` ```` `-Rahmenzeilen)
3. Als `CLAUDE.md` im Projektordner speichern

### Platzhalter füllen (Beispiel KI-Lehrer)

```markdown
## Lernprojekt

**Projekttitel**: Mein erstes 2D-Spiel mit Pygame
**Schüler**: Max, 41 Jahre
**Vorkenntnisse**: Erfahrener Software-Entwickler, Python bekannt, Pygame neu
**Zeitbudget**: 60 Minuten pro Sitzung
**Sprache des Unterrichts**: Deutsch
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

### Code-Digest (für 32k-Modelle empfohlen)

Ab ~500 Zeilen Code wird `@src/` zu groß für ein 32k-Kontextfenster. Der Code-Digest ersetzt es durch eine kompakte Übersicht.

`wiki/code-stand.md` nach jeder Sitzung aktualisieren lassen:

```
Aktualisiere wiki/code-stand.md: alle Module mit 2–3 Zeilen Beschreibung,
wichtige Klassen und Funktionen mit Signatur, aktuelle offene Punkte.
```

Ab dann zu Sitzungsbeginn statt `@src/` nur noch:

```
@CLAUDE.md @wiki/fortschritt.md @wiki/code-stand.md @src/main.py

Weiter.
```

Kontext bleibt dauerhaft unter ~8k Token — unabhängig davon wie groß das Projekt wächst.

---

## Qwen3-spezifische Tipps

### Thinking Mode

Qwen3 14B und größer unterstützen einen expliziten Denkmodus für anspruchsvolle Fragen. Er lässt sich in Ollama durch einen vorangestellten Befehl aktivieren:

```
/think Analysiere diese Kollisionslogik und finde den Fehler:
@src/physics.py
```

Das Modell denkt vor der Antwort — sichtbar als `<think>...</think>`-Block. Bei Architektur- und Debugging-Fragen liefert dieser Modus spürbar bessere Ergebnisse als der Standard-Modus.

### Kontextfenster

Qwen3 14B hat ein 40K-Token-Kontextfenster — ausreichend für die meisten Pygame-Projekte. Bei sehr großem Projekt (2000+ Zeilen Code) trotzdem Code-Digest aktivieren (s. Schritt 7):

```
@wiki/code-stand.md @src/aktuelle-datei.py    ← mit Code-Digest: konstant ~4–6k Token
@src/                                          ← ohne Digest: wächst unkontrolliert
```

### Temperatur

Für Code-Generierung empfiehlt sich ein niedriger Temperaturwert. In `config.json`:

```json
{
  "models": [
    {
      "title": "Qwen3 14B (lokal)",
      "provider": "ollama",
      "model": "qwen3:14b",
      "contextLength": 40960,
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
→ CPU-Inferenz ist langsam (1–3 Token/s) — eine dedizierte GPU ist für flüssiges Arbeiten nötig.
→ Bei GPU-Offload (Modell zu groß für VRAM): auf das kleinere Modell wechseln. RTX 5080-Nutzer: `qwen3:14b` statt `qwen3:30b`.

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
