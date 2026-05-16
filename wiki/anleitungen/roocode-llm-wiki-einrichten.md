---
date: 2026-05-16
type: anleitung
tags: [anleitung, setup, lokale-modelle, roo-code]
status: active
---

# LLM-Wiki mit Roo Code + lokalem Modell einrichten

**Zusammenfassung**: Schritt-für-Schritt-Anleitung für ein lokal betriebenes LLM-Wiki mit Roo Code, Ollama und qwen3:14b-40k auf einer RTX 5080. Bestätigte Konfiguration (Mai 2026).
**Zuletzt aktualisiert**: 2026-05-16

---

## Was du brauchst

| Komponente | Anforderung |
|---|---|
| GPU | RTX 5080 (16 GB VRAM) oder besser |
| Ollama | Läuft als Server (lokal oder Netzwerk) |
| VS Code oder VS Codium | Auf dem Arbeitsrechner |
| Roo Code Extension | Version 3.54.0+ |
| Betriebssystem | Linux oder Windows (Server-seitig) |

**Zwei mögliche Setups:**

- **Ein-Maschinen**: Ollama und VS Codium laufen auf demselben Rechner mit der GPU
- **Zwei-Maschinen** *(bestätigt)*: Ollama läuft auf dem GPU-Server, VS Codium + Roo Code auf einem Client-Rechner im selben Netzwerk

---

## Schritt 1: Ollama mit richtigem Modell einrichten

### 1a — Ollama installieren

**Linux (Server):**
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**Netzwerk-Zugriff freischalten** (für Zwei-Maschinen-Setup):
```bash
# In /etc/systemd/system/ollama.service oder als Umgebungsvariable:
OLLAMA_HOST=0.0.0.0
```

Danach Dienst neu starten:
```bash
sudo systemctl restart ollama
```

**Mit Docker** (alternativ — empfohlen für Zwei-Maschinen-Setup):
```bash
docker run -d --gpus=all --restart=always \
  -v ollama:/root/.ollama \
  -p 11434:11434 \
  --name ollama ollama/ollama
```

### 1b — Modell herunterladen

```bash
ollama pull qwen3:14b
```

Download: ca. 10 GB, dauert 5–30 Minuten je nach Verbindung.

### 1c — Modelfile mit 40K Kontext anlegen

Das ist der wichtigste Schritt. Ollamas Standardkontext (2K) ist viel zu klein für Roo Codes System-Prompt.

**Ohne Docker:**
```bash
printf 'FROM qwen3:14b\nPARAMETER num_ctx 40960\n' > /tmp/Modelfile
ollama create qwen3:14b-40k -f /tmp/Modelfile
```

**Mit Docker:**
```bash
printf 'FROM qwen3:14b\nPARAMETER num_ctx 40960\n' > /tmp/Modelfile
docker cp /tmp/Modelfile ollama:/tmp/Modelfile
docker exec ollama ollama create qwen3:14b-40k -f /tmp/Modelfile
```

### 1d — Prüfen

```bash
ollama ps
# → qwen3:14b-40k sollte mit CONTEXT=40960 erscheinen (nach erstem Request)

ollama list
# → qwen3:14b-40k sollte in der Liste stehen
```

Vom Client-Rechner aus testen (Zwei-Maschinen):
```bash
curl http://<server-ip>:11434/api/tags
# → JSON-Antwort mit dem Modell bestätigt Erreichbarkeit
```

> Wenn das Modell nicht in `ollama ps` erscheint: einmal manuell starten mit `ollama run qwen3:14b-40k`, dann `/bye` eingeben. So ist es geladen, wenn Roo Code die erste Anfrage schickt. → [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md)

---

## Schritt 2: VS Codium + Roo Code installieren

### 2a — VS Codium installieren

VS Codium ist die datenschutzfreundliche VS-Code-Variante ohne Microsoft-Telemetrie:

- **Linux**: [vscodium.com](https://vscodium.com) → `.deb` oder `.rpm` herunterladen
- **Windows**: `.exe`-Installer von vscodium.com
- **Alternativ**: VS Code von [code.visualstudio.com](https://code.visualstudio.com) — identische Funktionalität

### 2b — Roo Code Extension installieren

1. VS Codium öffnen
2. Extensions öffnen: `Strg+Shift+X`
3. Nach `Roo Code` suchen → **Installieren** (Extension von `RooVetGit`)
4. VS Codium neu laden wenn aufgefordert

---

## Schritt 3: Roo Code konfigurieren

### 3a — Provider einrichten

1. Roo Code öffnen (linke Leiste oder `Strg+Shift+P` → „Roo Code: Open")
2. Zahnrad-Icon → **Provider** → **Neuen Provider hinzufügen**
3. Einstellungen:

```
Provider-Typ:  OpenAI Compatible
Name:          Ollama lokal
Base URL:      http://<server-ip>:11434/v1
              (Ein-Maschinen: http://localhost:11434/v1)
API Key:       ollama          ← beliebiger String, wird ignoriert
Model ID:      qwen3:14b-40k
```

> **Nicht** den nativen `ollama`-Provider verwenden — er liefert ein anderes Tool-Calling-Format, das Roo Code 3.54.0 nicht korrekt verarbeitet. Der `/v1`-Endpoint ist zwingend. → [roo-code](../werkzeuge/roo-code.md)

### 3b — Verbindung testen

Im Roo Code Chat:
```
Hallo, kannst du dich kurz vorstellen?
```

Erwartete Antwort: Das Modell antwortet. In den Ollama-Logs sollte `qwen3:14b-40k` und `num_ctx=40960` erscheinen.

**Wenn keine Antwort kommt:**
- Ollama läuft? → `ollama ps` / `docker ps`
- Firewall? → Port 11434 auf dem Server freigeben
- URL korrekt? → `http://` nicht vergessen, kein abschließendes `/`

---

## Schritt 4: Projektordner und Vorlage einrichten

### 4a — Projektstruktur anlegen

```bash
mkdir mein-wiki
cd mein-wiki
mkdir -p clippings wiki/konzepte wiki/quellen wiki/werkzeuge wiki/personen
git init
```

### 4b — Vorlage einbinden

Den Inhalt aus dem `## Vorlage`-Block in [roocode-llm-wiki-lokal](../vorlagen/roocode-llm-wiki-lokal.md) kopieren (ohne die 4-Backtick-Fences).

**Option A — als Roo Code Custom Instructions** (empfohlen):

1. In Roo Code: Zahnrad → **Custom Instructions**
2. Vorlageninhalt einfügen
3. `{{PROJEKTNAME}}` ersetzen

**Option B — als Projektdatei**:

```bash
# Vorlageninhalt als .roo/rules/llm-wiki.md speichern
mkdir -p .roo/rules
# Inhalt einfügen, dann:
echo "wiki/" >> .gitignore   # optional: wiki lokal halten
```

### 4c — index.md und log.md anlegen

Roo Code kann das automatisch beim ersten Start — oder manuell voranlegen:

```bash
cat > wiki/index.md << 'EOF'
# Wiki-Index

**Zuletzt aktualisiert**: YYYY-MM-DD

---

*(Wird von Roo Code gepflegt)*

---
EOF

cat > wiki/log.md << 'EOF'
# Änderungsprotokoll

---
EOF
```

---

## Schritt 5: Erste Session starten

### 5a — Projektordner in VS Codium öffnen

**Datei → Ordner öffnen** → Projektordner wählen.

### 5b — Erste Quelle einlegen

Eine Quelldatei in `clippings/` kopieren. Unterstützte Formate: `.md`, `.txt`, `.pdf` (als Text).

### 5c — Roo Code anweisen

```
Nimm die Quelle in clippings/meine-quelle.md auf.
```

Roo Code liest die Datei, bespricht die Erkenntnisse und legt Wiki-Seiten an.

**Erwarteter Ablauf:**
1. Roo Code liest die Quelle
2. Kurze Besprechung der wichtigsten Punkte (1–2 Nachrichten)
3. Zusammenfassungsseite in `wiki/quellen/` wird angelegt
4. Konzeptseiten werden angelegt oder aktualisiert
5. `wiki/index.md` und `wiki/log.md` werden aktualisiert

---

## Laufender Betrieb

### Session-Regeln strikt einhalten

Die wichtigste Regel: **eine Quelle pro Session**.

| Situation | Was tun |
|---|---|
| Neue Quelle aufnehmen | Neue Session starten (`/new task` in Roo Code) |
| Gesprächsverlauf > 5 Runden | Neue Session starten |
| Wiki-Seite abfragen | Kurze Session, gezielt eine Seite lesen lassen |
| Mehrere Quellen heute | Eine pro Session, dazwischen neue Sessions |

Warum: Ab ~20k Tokens im Kontext steigt die Latenz quadratisch. Das passiert schneller als man denkt — System-Prompt + Quelle + Gespräch summieren sich auf 18–42k Tokens. → [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md)

### Typischer Tagesablauf

```
1. VS Codium öffnen, Projektordner öffnen
2. Roo Code starten, neue Session
3. Quelle in clippings/ legen
4. "Nimm clippings/X.md auf" → Roo Code arbeitet
5. Ergebnis in Obsidian prüfen
6. Nächste Quelle? → Neue Session starten
```

### Obsidian als Wiki-Viewer

Obsidian eignet sich gut zum Lesen und Navigieren des Wikis:

1. Obsidian öffnen → **Ordner als Vault öffnen**
2. Den `wiki/`-Unterordner wählen (nicht das gesamte Projektverzeichnis)
3. `Strg+G` → Graph-Ansicht zeigt Seitenvernetzung

→ [obsidian](../werkzeuge/obsidian.md)

---

## Was nicht geht (Grenzen)

| Aufgabe | Warum nicht | Alternative |
|---|---|---|
| Lint-Lauf | Zu viele Seiten gleichzeitig, Kontextlimit | Claude Code (Cloud) verwenden |
| Große Clippings (>8k Tokens) | Session-Budget erschöpft | In Abschnitte aufteilen oder Cloud |
| Mehrere Quellen in einer Session | Kontext läuft voll | Je eine neue Session pro Quelle |
| 32B-Modell bei 40K Kontext | Passt nicht in 16 GB VRAM | RTX 5090 / R9700 nötig → [quantisierung](../konzepte/quantisierung.md) |

→ [lokale-modelle](lokale-modelle.md) für die vollständige Gegenüberstellung Cloud vs. Lokal.

---

## Troubleshooting

**Roo Code nutzt keine Tools, gibt nur Text aus**
→ Provider ist auf nativem `ollama` statt `OpenAI Compatible`. Endpoint auf `/v1` prüfen. → [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md)

**Antworten werden extrem langsam (>30s)**
→ Kontextfenster fast voll. Neue Session starten (`/new task`).

**Modell läuft auf CPU statt GPU**
→ `ollama ps` prüfen — steht `CPU` statt `GPU`? Mögliche Ursachen: Docker ohne `--gpus=all` gestartet, CUDA nicht installiert, VRAM zu knapp.

**`ollama ps` zeigt `num_ctx=2048` statt `40960`**
→ Modelfile wurde nicht korrekt erstellt. `ollama list` prüfen ob `qwen3:14b-40k` existiert. Schritt 1c wiederholen.

**Verbindung vom Client zum Server schlägt fehl**
→ `OLLAMA_HOST=0.0.0.0` gesetzt? Port 11434 in der Firewall offen? URL mit `http://` (nicht `https://`)?

---

## Verwandte Seiten

- [roocode-llm-wiki-lokal](../vorlagen/roocode-llm-wiki-lokal.md) — Die System-Prompt-Vorlage für diese Konfiguration
- [roo-code](../werkzeuge/roo-code.md) — Roo Code: Funktionsweise, bestätigte Konfiguration
- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — Kontextlimits, Latenzdegradation, Modelfile-Details
- [lokale-modelle](lokale-modelle.md) — Allgemeine Einrichtung lokaler Modelle, Cloud-vs.-Lokal-Vergleich
- [quantisierung](../konzepte/quantisierung.md) — VRAM-Kalkulation, Modellgrößen, RTX 5080-Grenzen
- [erste-schritte](erste-schritte.md) — Einrichtung mit Claude Code (Cloud-Pfad)
- [obsidian](../werkzeuge/obsidian.md) — Wiki-Viewer einrichten

---

[Wiki-Index](../index.md)
