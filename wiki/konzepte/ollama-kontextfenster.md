---
date: 2026-05-15
type: konzept
tags: [konzept, lokale-modelle, setup, hardware]
status: active
---

# Ollama-Kontextfenster: Das 2K-Standardproblem

**Zusammenfassung**: Ollama setzt standardmäßig ein Kontextfenster von nur 2.048 Token — viel zu klein für Roo Codes System-Prompt (~33–51 KB). Das führt dazu, dass lokale Modelle keine Werkzeuge nutzen und nur Text im Chat ausgeben. Lösung: Custom Modelfile mit `num_ctx`-Parameter.
**Quellen**: `Using Ollama With Roo Code Roo Code Documentation.md`, `Roo Code not using tools properly in offline setup.md`, `Local model for coding.md`, `olilanzRooCode-Local-Evaluation.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Das Problem

Ollama's globaler Standard-Kontextwert beträgt **2.048 Token** (~8 KB). Roo Codes System-Prompt allein ist jedoch:
- Mit MCP-Servern aktiviert: ~51 KB
- Ohne MCP: ~33 KB
- Minimiert (Foot Gun): ~28 KB

Das bedeutet: Das Modell sieht nicht einmal den vollständigen System-Prompt, geschweige denn die eigentliche Aufgabe oder Dateiinhalte.

**Symptome:**
- Modell gibt Antworten als reinen Text im Chat aus, anstatt Werkzeuge zu nutzen
- Fehlermeldung: `[ERROR] You did not use a tool in your previous response! Please retry with a tool use.`
- Kontext zwischen Nachrichten geht verloren — jede Folgefrage startet „neu"
- Roo Code kreist in Schleifen ohne Fortschritt

(Quelle: `Roo Code not using tools properly in offline setup.md`)

---

## Lösung: Custom Modelfile

Die zuverlässigste Lösung ist ein **Modelfile** mit explizit gesetztem `num_ctx`-Parameter.

### Option A: Interaktiv (einfach, aber nicht versionierbar)

```bash
ollama run qwen2.5-coder:32b
/set parameter num_ctx 32768
/save mein-modell
```

### Option B: Modelfile (empfohlen)

```dockerfile
FROM qwen2.5-coder:32b
PARAMETER num_ctx 32768
PARAMETER temperature 0.7
PARAMETER repeat_penalty 1.1
```

```bash
ollama create qwen-32k -f Modelfile
```

Roo Code liest den `num_ctx`-Wert automatisch aus dem Ollama-Modell und nutzt ihn ohne weitere Konfiguration. (Quelle: `Using Ollama With Roo Code Roo Code Documentation.md`)

---

## Warum funktioniert `ollama set parameter` oft nicht?

Mehrere Reddit-Nutzer berichten, dass `ollama set parameter num_ctx 32768` oder Umgebungsvariablen ignoriert werden — ein möglicher Bug in Ollama auf Linux und macOS. Die einzige zuverlässige Methode ist das Überschreiben über das **Modelfile**. (Quelle: `Local model for coding.md`, Reddit)

---

## Kontextgröße richtig wählen

Mehr Kontext ist nicht automatisch besser. Zu beachten:

### 1. Trainierter Kontext (n_ctx_train)

Das ist die Kontextgröße, mit der das Modell trainiert wurde — sichtbar in den Ollama-Logs:

```
llm_load_print_meta: n_ctx_train = 16384
```

Über diesen Wert hinausgehen ist riskant. Faustregel: Faktor 1,5–2,0 maximal.

### 2. RoPE-Skalierung

Manche Modelle unterstützen via **Rotary Position Embeddings (RoPE)** erweiterte Kontextfenster — aber mit abnehmender Präzision im oberen Bereich:

```
llama.rope.scaling.type    linear
llama.rope.scaling.factor  4.0   → 16K × 4 = 64K möglich
```

### 3. VRAM-Bedarf

Größerer Kontext = mehr VRAM. Orientierungswerte:

| num_ctx | VRAM-Zusatzbedarf (ca.) |
|---|---|
| 16.384 | ~8 GB |
| 32.768 | ~16 GB |
| 65.536 | ~24 GB+ |

### 4. num_predict: Ausgabelänge begrenzen

Ein oft übersehener Parameter: Ollama's Standard-`num_predict` kann Code-Ausgaben mitten im File abschneiden. Für Coding-Aufgaben empfehlen sich Werte ≥ 8.000:

```dockerfile
PARAMETER num_predict 12000
```

(Quelle: `olilanzRooCode-Local-Evaluation.md`)

---

## Out-of-Memory beim ersten Request (OOM)

Ollama lädt das Modell bei der ersten Anfrage von Roo Code — inklusive eines möglicherweise sehr großen Kontextfensters. Fixes:

1. **Modell vorher manuell starten**: `ollama run <modell>` ausführen bevor Roo Code genutzt wird
2. **num_ctx pinnen**: Verhindert dynamische Größenzuweisung beim Kaltstart
3. **Kleinere Quantisierung**: Q4 statt Q8 wenn VRAM knapp ist

---

## Modelle mit bereits eingebautem Kontext

Manche Modelle auf Ollama haben den großen Kontext bereits im Modelfile eingebaut — kein eigenes Custom-Modelfile nötig. Erkennbar am Namen:

| Modell | Kontext (num_ctx) | Anmerkung |
|---|---|---|
| `qwen3:14b-40k` | 40.960 | Bestätigt funktionsfähig mit Roo Code 3.54.0 |
| `qwen3:30b-a3b-40k` | 40.960 | Größeres Modell, gleicher Trick |

Beim Verwenden solcher Modelle prüfen, ob der große Kontext wirklich aktiv ist:
```bash
ollama ps  # CONTEXT-Spalte zeigt den tatsächlichen Wert
```

---

## Checkliste für lokales Ollama + Roo Code

- [ ] `num_ctx` im Modelfile gesetzt (mindestens 16.384, besser 32.768+) — oder Modell mit eingebautem Kontext nutzen
- [ ] `num_predict` gesetzt (≥ 8.000 für Coding)
- [ ] Modell vor Roo-Code-Nutzung manuell gestartet (`ollama run`)
- [ ] `ollama ps` zeigt Modell vollständig im GPU-Speicher, CONTEXT-Spalte stimmt
- [ ] n_ctx_train aus den Logs geprüft (nicht mehr als 2× überschreiten)
- [ ] In Roo Code: Provider `openai-compatible` + `/v1`-Endpoint (nicht nativer `ollama`-Provider)

---

## Verwandte Seiten

- [roo-code](../werkzeuge/roo-code.md) — Roo Code: Funktionsweise und Optimierungen
- [tool-use-lokale-modelle](tool-use-lokale-modelle.md) — Warum lokale Modelle mit Werkzeugaufrufen kämpfen
- [quantisierung](quantisierung.md) — VRAM-Kalkulation und Quantisierungsstufen
- [roocode-system-prompt-optimierung](roocode-system-prompt-optimierung.md) — System-Prompt verkleinern für lokale Setups
- [lm-studio](../werkzeuge/lm-studio.md) — Alternative mit GUI-basierter Kontextfenster-Konfiguration

---

[Wiki-Index](../index.md)
