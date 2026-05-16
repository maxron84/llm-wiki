---
date: 2026-05-15
type: konzept
tags: [konzept, lokale-modelle, schema]
status: active
---

# Tool-Nutzung mit lokalen Modellen

**Zusammenfassung**: Lokale Modelle scheitern bei Roo Code häufig daran, Werkzeugaufrufe korrekt auszuführen — entweder wegen zu kleinem Kontextfenster oder weil das Modell das erwartete Format nicht zuverlässig produziert. Ab Roo Code 3.54.0 ist natives Function Calling Pflicht; der XML-Fallback wurde entfernt.
**Quellen**: `Local model for coding.md`, `Roo Code not using tools properly in offline setup.md`, `olilanzRooCode-Local-Evaluation.md`, `mychen76GLM-4-32B-cline-roocode.md`, `mychen76openhands_32b-cline-roocode.md`, `Vibe Coding 'Locally' with ClineRooCode and Ollama.md`
**Zuletzt aktualisiert**: 2026-05-16

---

## Wie Roo Code Werkzeuge nutzt

### Ab Version 3.54.0: Natives Function Calling (aktuell)

Roo Code nutzt den nativen `tool_calls`-Mechanismus der OpenAI-API. Das Modell gibt keine XML-Tags im Text aus — stattdessen liefert es strukturierte Daten über den API-Response:

```json
"tool_calls": [{
  "id": "call_abc123",
  "type": "function",
  "function": {
    "name": "write_file",
    "arguments": "{\"path\": \"src/main.py\", \"content\": \"print('Hello')\"}"
  }
}]
```

Roo Code parst diese `tool_calls`-Struktur, führt die Aktion aus, und schickt das Ergebnis im nächsten Request zurück.

**Voraussetzung für Ollama**: Den `/v1`-Endpoint (OpenAI-kompatibel) nutzen, nicht den nativen `/api/chat`. Nur `/v1` liefert das `tool_calls`-Format — der native Endpoint gibt vereinfachtes JSON zurück, das Roo Code nicht parsen kann.

### Vor Version 3.54.0: XML-Fallback (veraltet)

In älteren Versionen konnte Roo Code auch XML-Tags im Modellantwort parsen:

```xml
<write_file>
  <path>src/main.py</path>
  <content>print("Hello World")</content>
</write_file>
```

Dieser Fallback wurde in 3.54.0 entfernt. Fine-tuned Modelle, die speziell auf dieses XML-Format trainiert wurden (z.B. `tom_himanen/deepseek-r1-roo-cline-tools`), funktionieren daher mit aktuellen Roo-Code-Versionen möglicherweise nicht mehr zuverlässig.

Gibt das Modell kein korrektes `tool_calls` zurück — oder antwortet es mit normalem Text —, erscheint:

```
Model Response Incomplete: The model failed to use any tools in its response.
```

---

## Warum lokale Modelle scheitern

### 1. Kontextfenster zu klein

Wenn das Kontextfenster kleiner als der System-Prompt ist (~33–51 KB), sieht das Modell die Werkzeugdefinitionen nicht vollständig — und kann sie folglich auch nicht korrekt nutzen.

→ Lösung: [ollama-kontextfenster](ollama-kontextfenster.md)

### 2. Kein Training auf Roo Code / Cline-Prompts

Roo Code und Cline nutzen sehr spezifische, lange System-Prompts. Allgemeine Coding-Modelle wurden nicht auf dieses Format trainiert. Symptom: Das Modell „versteht" die Struktur nicht und gibt zufällige oder unbrauchbare Ausgaben:

> „Ich sage 'Hallo', und es generiert einfach random Outputs." (Quelle: `Local model for coding.md`, Reddit)

### 3. Modell zu schwach für Aufgabenkomplexität

Auch wenn Tool-Use prinzipiell funktioniert, kann das Modell bei komplexeren Aufgaben inkonsistente Werkzeugaufrufe produzieren — falsche Dateipfade, fehlende Parameter, abgebrochene Diffs.

---

## Modelle die funktionieren (Roo Code 3.54.0+)

### Standard-Modelle mit nativem Function Calling

Modelle mit eingebautem Function-Calling-Support in Ollama funktionieren ohne spezielle Fine-Tuning:

| Modell | Ollama-Name | VRAM | Bestätigt |
|---|---|---|---|
| Qwen3 14B (40K Kontext) | `qwen3:14b-40k` | ~16 GB | ✅ Bestätigt 2026-05-16 |
| Qwen3 8B | `qwen3:8b` | ~6 GB | Erwartet ja (natives Function Calling) |
| Llama 3.1 8B | `llama3.1:8b` | ~5 GB | Natives Function Calling bekannt |

**Wichtig**: Auch bei diesen Modellen muss `num_ctx` groß genug sein (≥ 16K), damit der System-Prompt vollständig gelesen wird. `qwen3:14b-40k` hat den Kontext bereits eingebaut.

### Fine-tuned Modelle (für ältere Roo Code-Versionen / XML-Format)

Diese Modelle wurden speziell auf das XML-Format von Cline/Roo Code (vor 3.54.0) trainiert. **Kompatibilität mit Roo Code 3.54.0+ unklar** — sie könnten nach wie vor funktionieren, wenn Ollama ihre Ausgabe korrekt in `tool_calls` übersetzt:

| Modell | Ollama-Name | Größe | Anmerkung |
|---|---|---|---|
| Qwen2.5-Coder-Tools | `hhao/qwen2.5-coder-tools:32b` | 32B | Breiter Community-Support |
| Qwen2.5-Coder-Cline | `maryasov/qwen2.5-coder-cline:32b` | 32B | Alternative |
| GLM-4-32B (Cline/RooCode) | `mychen76/GLM-4-32B-cline-roocode:Q4` | 32B, Q4 = 20 GB | 32K Kontext |
| OpenHands-32B (Cline/RooCode) | `mychen76/openhands_32b-cline-roocode:Q4` | 32B, Q4 = 20 GB | 32K Kontext |

(Quellen: `mychen76GLM-4-32B-cline-roocode.md`, `mychen76openhands_32b-cline-roocode.md`, `Local model for coding.md`)

### Modelle die nicht funktionieren

| Modell | Problem |
|---|---|
| `deepseek-r1` (alle Varianten) | `<think>`-Tags wrappen die Ausgabe; Roo Code kann Tool-Calls nicht extrahieren |
| `devstral` | Für OpenHands-Format trainiert, inkompatibel mit Roo Code |
| `qwen2.5-coder:14b` (Standard) | Gibt JSON als Text aus statt echte `tool_calls` |
| Jedes Modell via `ollama`-Provider | Falsches API-Format (kein `tool_calls`) |

> **Achtung bei Reasoning-Modellen** (z.B. DeepSeek R1): Die internen Denkprozesse (`<think>`-Blöcke) füllen den Kontext schnell auf — und Roo Code 3.54.0 kann keine Tool-Calls aus `<think>`-gewrappten Antworten extrahieren.

---

## Praxistipp: Aufgaben klein halten

Auch mit gut konfiguriertem Modell gilt: **Je kleiner und präziser die Aufgabe, desto zuverlässiger die Tool-Nutzung.**

- Eine Datei oder eine Funktion pro Aufgabe
- Explizit angeben, welche Datei bearbeitet werden soll (`@dateiname.py`)
- Bei großen Refactorings: Schritt für Schritt vorgehen

Mit zunehmendem Kontext (mehr Iterationen) steigt die Wahrscheinlichkeit, dass Tool-Calls fehlerhaft werden — auch bei eigentlich gut funktionierenden Modellen. (Quelle: `olilanzRooCode-Local-Evaluation.md`)

---

## Ehrliche Einschätzung (Community-Konsens, Stand 2025/26)

- **Einfache, abgegrenzte Aufgaben** (einzelne Funktion, eine Datei): Funktioniert oft gut mit 32B-Modellen
- **Mittlere Aufgaben** (Refactoring einer Klasse, neue Funktion mit Abhängigkeiten): Mit viel Aufwand und Geduld möglich
- **Komplexe Projekte** (Mehrere Dateien, externe Libraries, Enterprise-Code): Suboptimal — lokale Modelle unter 70B sind noch nicht dort

> „Wenn jemand auf YouTube sagt 'CODE WITH LOCAL MODELS NOW, IT'S GREAT' und nur winzige Übungsaufgaben zeigt — das spiegelt nicht die Realität bei echten Projekten wider." (Quelle: `Local model for coding.md`, Reddit)

---

## Verwandte Seiten

- [ollama-kontextfenster](ollama-kontextfenster.md) — Kontextfenster korrekt konfigurieren
- [quantisierung](quantisierung.md) — VRAM-Kalkulation und Quantisierungsstufen
- [roo-code](../werkzeuge/roo-code.md) — Roo Code: interne Funktionsweise und Modi
- [roocode-system-prompt-optimierung](roocode-system-prompt-optimierung.md) — System-Prompt verkleinern
- [roocode-lokale-mychen76](../quellen/roocode-lokale-mychen76.md) — mychen76's fine-tuned Modelle im Detail

---

[Wiki-Index](../index.md)
