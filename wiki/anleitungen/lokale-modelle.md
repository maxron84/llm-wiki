---
date: 2026-05-02
type: anleitung
tags: [anleitung, setup, lokale-modelle]
status: active
---

# Lokale Modelle: Einrichtung und Betrieb

**Zusammenfassung**: Einrichtung und Betrieb der Wiki-Vorlagen mit lokal laufenden KI-Modellen — ohne Cloud, ohne Abo, ohne Datenweitergabe.
**Zuletzt aktualisiert**: 2026-05-02

---

## Was lokale Modelle können — und was nicht

Lokale Modelle laufen auf deinem eigenen Computer. Kein Internet, keine Kosten pro Anfrage, keine Datenweitergabe. Der Unterschied zu Claude Code ist aber wichtig zu verstehen:

| Fähigkeit | Claude Code | Lokales Modell |
|---|---|---|
| CLAUDE.md automatisch lesen | ✅ | ❌ — manuell als System-Prompt einfügen |
| Wiki-Seiten automatisch schreiben | ✅ | ❌ — manuell speichern |
| Fragen beantworten und erklären | ✅ | ✅ |
| Fotos und Scans lesen (Vision) | ✅ | ⚠️ — nur manche Modelle |
| Offline arbeiten | ❌ | ✅ |
| Datenschutz (nichts verlässt den Rechner) | ❌ | ✅ |

**Fazit**: Lokale Modelle eignen sich gut für den **Gesprächsbetrieb** — Nachhilfe, Erklärungen, Fragen beantworten. Das automatische Wiki-Wachstum aus dem LLM-Wiki-Muster ist ohne Dateizugriff nicht möglich. Wiki-Seiten können manuell angelegt werden, wenn die KI Inhalte produziert.

---

## Hardware-Voraussetzungen

Lokale Modelle brauchen Arbeitsspeicher. Zu wenig RAM bedeutet: das Modell läuft nicht, oder es ist so langsam, dass es unbrauchbar ist.

| Modellgröße | RAM (ohne GPU) | VRAM (mit GPU) | Geeignet für |
|---|---|---|---|
| 7–8B | 8 GB | 6 GB | Nachhilfe Klasse 1–7, einfache Fragen |
| 14B | 16 GB | 10 GB | Nachhilfe Klasse 8–10, Naturwissenschaften |
| 27–35B | 32 GB | 20+ GB | Komplexe Aufgaben, Software-Projekte |

> **Mac mit Apple Silicon (M1/M2/M3/M4)**: Unified Memory ist besonders effizient — ein Mac mit 16 GB läuft 14B-Modelle flüssig, mit 32 GB auch 27B-Modelle. Apple MLX-Versionen der Modelle sind zusätzlich 20–50 % schneller.

**Unsicher, was dein Rechner hat?**
- Windows: `Strg + Shift + Esc` → Reiter „Leistung" → „Arbeitsspeicher"
- macOS: Apfel-Menü → „Über diesen Mac" → Arbeitsspeicher / Unified Memory
- Linux: Terminal → `free -h`

---

---

# Anfänger-Pfad

> Kein Terminal, keine Konfigurationsdateien, kein Vorwissen nötig.
> Empfohlenes Tool: **LM Studio** — die einfachste Möglichkeit, lokale Modelle zu nutzen.

---

## Schritt 1: LM Studio installieren

LM Studio ist eine Desktop-App mit grafischer Oberfläche — Modelle herunterladen, starten und chatten, alles an einem Ort.

1. Öffne [lmstudio.ai](https://lmstudio.ai) im Browser
2. Lade die Version für dein Betriebssystem herunter:
   - **Windows**: `.exe`-Installer → ausführen
   - **macOS**: `.dmg`-Datei → App in Programme ziehen
   - **Linux**: `.AppImage`-Datei → ausführbar machen → starten
3. LM Studio starten — beim ersten Start führt ein Einrichtungsassistent durch die Grundkonfiguration

---

## Schritt 2: Modell herunterladen

LM Studio hat einen eingebauten Modell-Browser. Kein manuelles Herunterladen nötig.

1. In LM Studio auf **„Discover"** oder **„Models"** klicken (linke Leiste)
2. Nach einem Modell suchen — Empfehlungen nach Arbeitsspeicher:

| Dein RAM | Empfohlenes Modell | Suchbegriff in LM Studio |
|---|---|---|
| 8 GB | Llama 3.2 8B oder Phi-4-mini | `llama 3.2 8b` / `phi-4-mini` |
| 16 GB | Qwen3 14B | `qwen3 14b` |
| 32 GB | Qwen3 27B | `qwen3 27b` |
| Mac 16 GB (M-Chip) | Qwen3 14B (MLX) | `qwen3 14b mlx` |
| Mac 32 GB (M-Chip) | Qwen3 27B (MLX) | `qwen3 27b mlx` |

3. Auf das gewünschte Modell klicken → **„Download"**
4. Der Download kann je nach Modellgröße 5–30 Minuten dauern (4–20 GB)

> **Welche Variante wählen?** Wenn mehrere Varianten eines Modells angezeigt werden: `Q4_K_M` ist ein guter Kompromiss zwischen Qualität und Geschwindigkeit. MLX-Varianten nur auf Mac mit Apple Silicon.

---

## Schritt 3: Vorlage als System-Prompt einrichten

Da LM Studio keinen Dateizugriff hat, muss die `CLAUDE.md` einmalig als System-Prompt eingefügt werden. Das sagt dem Modell, wie es sich verhalten soll.

1. Öffne deine `CLAUDE.md` in einem Texteditor (z.B. Editor/Notepad unter Windows, TextEdit auf Mac)
2. Gesamten Inhalt kopieren
3. In LM Studio: Chat-Bereich öffnen → auf **„System Prompt"** oder das Zahnrad-Symbol klicken
4. Inhalt einfügen → bestätigen
5. Das Modell verhält sich jetzt entsprechend der gewählten Vorlage

> **Kein System-Prompt-Feld sichtbar?** Je nach LM Studio-Version unter „Advanced" oder „Settings" des Chat-Fensters suchen.

---

## Schritt 4: Erste Sitzung starten

1. Sicherstellen, dass das heruntergeladene Modell geladen ist (in LM Studio: Modell auswählen → „Load")
2. In das Chat-Feld schreiben:

   ```
   Wir fangen an
   ```

3. Das Modell reagiert entsprechend der eingefügten Vorlage

**Was jetzt passiert — je nach Vorlage:**

| Vorlage | Erster Start |
|---|---|
| Nachhilfe | Kurze Begrüßung → warten auf erste Aufgabe oder Foto |
| KI-Lehrer | Aufnahmegespräch → Lehrplan wird im Chat vorgeschlagen (nicht automatisch gespeichert) |
| Rezepte | Warten auf erste Quelle |

> **Wiki-Seiten manuell anlegen**: Wenn das Modell eine Zusammenfassung oder Wiki-Seite produziert, den Text kopieren und als `.md`-Datei im `wiki/`-Ordner speichern. Langfristig bequemer: für das automatische Wiki-Wachstum Claude Code nutzen, für den Gesprächsbetrieb das lokale Modell.

---

## Alternative: Jan.ai

Jan ist eine Alternative zu LM Studio — etwas schlichter, aber vollständig offline und open-source.

1. [jan.ai](https://jan.ai) → Download für dein Betriebssystem
2. Installation wie LM Studio
3. Unter „Hub" Modelle durchsuchen und herunterladen
4. System-Prompt genauso wie in Schritt 3 beschrieben einrichten

Jan eignet sich besonders wenn: du keiner proprietären App vertraust, oder LM Studio auf deinem System Probleme macht.

---

---

# Fortgeschrittener Pfad

> Terminal-Kenntnisse hilfreich, aber nicht zwingend. Mehr Kontrolle, mehr Möglichkeiten.

---

## Ollama — das Fundament

Ollama läuft im Hintergrund als lokaler Server und stellt Modelle über eine API bereit. Viele andere Tools (Open WebUI, Continue) bauen darauf auf.

**Installation:**

1. [ollama.com](https://ollama.com) → Download für dein Betriebssystem
   - Windows / macOS: Installer ausführen
   - Linux: Terminal → `curl -fsSL https://ollama.com/install.sh | sh`

2. Erstes Modell herunterladen — Terminal öffnen:

```bash
ollama pull qwen3:14b
```

Weitere nützliche Befehle:

```bash
ollama list              # Alle heruntergeladenen Modelle anzeigen
ollama run qwen3:14b     # Modell direkt im Terminal starten
ollama pull llama3.3     # Anderes Modell herunterladen
```

3. Ollama läuft nach der Installation automatisch im Hintergrund (Systray-Icon)

---

## Open WebUI — Browser-Oberfläche für Ollama

Open WebUI ist eine polierte Web-Oberfläche für Ollama — vergleichbar mit ChatGPT, aber lokal.

**Installation (einfachste Methode via Docker):**

```bash
docker run -d -p 3000:80 --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui ghcr.io/open-webui/open-webui:main
```

Danach im Browser öffnen: `http://localhost:3000`

> **Kein Docker?** Open WebUI kann auch ohne Docker installiert werden (Python-Paket), oder als Desktop-App direkt von [openwebui.com](https://openwebui.com).

**System-Prompt in Open WebUI einrichten:**

1. Oben rechts: Konto-Einstellungen → „System Prompt"
2. Oder: Pro Gespräch über das Gesprächs-Einstellungen-Menü

---

## Continue — lokale Modelle in VS Code

Continue ist die einzige Option mit echtem Dateizugriff — es kann Dateien aus dem Projektordner als Kontext einbinden. Damit ist ein halbautomatischer Wiki-Betrieb möglich.

**Voraussetzung:** VS Code oder VS Codium mit installierter Continue-Extension (→ [erste-schritte](erste-schritte.md), Fortgeschrittener Pfad)

**Continue mit Ollama verbinden:**

1. Continue-Extension in VS Code öffnen (linke Leiste)
2. Einstellungen öffnen → `config.json` bearbeiten
3. Modell eintragen:

```json
{
  "models": [
    {
      "title": "Qwen3 14B (lokal)",
      "provider": "ollama",
      "model": "qwen3:14b"
    }
  ]
}
```

**Dateizugriff für Wiki nutzen:**

Im Continue-Chat können Dateien direkt eingebunden werden:

- `@dateiname.md` — einzelne Datei als Kontext
- `@wiki/` — gesamten wiki-Ordner als Kontext (bei großen Wikis: lieber einzelne Seiten)
- `@CLAUDE.md` — Vorlage als Kontext mitgeben

So kann das lokale Modell die Wiki-Seiten lesen und Inhalte vorschlagen — manuell speichern muss man trotzdem noch selbst.

> **Empfehlung für Continue**: Dual-Model-Setup — schnelles 7B-Modell für Autocomplete, 14B+ für den Chat-Bereich. In der `config.json` beide Modelle eintragen.

---

## Modell-Empfehlungen 2026

| Modell | Stärke | Ideal für |
|---|---|---|
| **Qwen3:27b / 35b** | Bestes Allround-Modell, starkes Deutsch | Software, komplexe Aufgaben, Klasse 9–10 |
| **Qwen3:14b** | Gutes Gleichgewicht aus Qualität und Geschwindigkeit | Nachhilfe Klasse 7–10, allgemeine Nutzung |
| **Llama 3.3** | Stark bei Reasoning, gutes Englisch | Software-Projekte, englischsprachige Quellen |
| **Mistral Small 3** | Schnell, kompakt, gut für europäische Sprachen | Nachhilfe, Rezepte, YouTube-Vorlagen |
| **[Phi-4](../werkzeuge/phi-4.md) 14B** | Sehr stark bei Mathematik und Code | Englischsprachige Code-Aufgaben, strukturierte Analysen |
| **[Phi-4-mini](../werkzeuge/phi-4.md)** | Sehr klein (3.8B), 128K Kontext | Schnelles Autocomplete, 8-GB-Rechner |

> **Für Deutsch**: Qwen3-Modelle sind aktuell die stärksten lokal verfügbaren Modelle für deutschsprachige Aufgaben. Phi-4 (14B) ist eine gute Ergänzung für Mathematik und Code, hat aber nur 16K Kontext.

---

## Verwandte Seiten

- [lokale-modelle-fortgeschritten](lokale-modelle-fortgeschritten.md) — Pygame-Projekt mit Qwen3 27B/35B: Ollama + Continue in VS Codium, für erfahrene Programmierer
- [phi-4](../werkzeuge/phi-4.md) — Phi-4-Modellfamilie im Detail: Varianten, Vergleich, Einrichtung
- [erste-schritte](erste-schritte.md) — Einrichtung mit Claude Code Desktop (Cloud-Pfad)
- [claude-code](../werkzeuge/claude-code.md) — Claude Code im Detail
- [obsidian](../werkzeuge/obsidian.md) — Wiki-Oberfläche
- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md) — Wenn das Wiki mit lokalem Modell wächst
- [qmd](../werkzeuge/qmd.md) — Semantische Suche für große Wikis (funktioniert auch mit lokalen Modellen)

---

[Wiki-Index](../index.md)
