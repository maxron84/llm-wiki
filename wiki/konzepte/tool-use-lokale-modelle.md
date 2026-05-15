---
date: 2026-05-15
type: konzept
tags: [konzept, lokale-modelle, schema]
status: active
---

# Tool-Nutzung mit lokalen Modellen

**Zusammenfassung**: Roo Code und Cline verlassen sich auf XML-basierte Werkzeugaufrufe, die das Modell präzise ausgeben muss. Kleinere lokale Modelle scheitern daran häufig — entweder wegen zu kleinem Kontextfenster oder mangels Training auf dieses spezifische Format. Spezielle Fine-Tuned-Modelle lösen dieses Problem.
**Quellen**: `Local model for coding.md`, `Roo Code not using tools properly in offline setup.md`, `olilanzRooCode-Local-Evaluation.md`, `mychen76GLM-4-32B-cline-roocode.md`, `mychen76openhands_32b-cline-roocode.md`, `Vibe Coding 'Locally' with ClineRooCode and Ollama.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Wie Roo Code Werkzeuge nutzt

Roo Code definiert im System-Prompt eine Reihe von XML-Werkzeugen. Das Modell muss, wenn es eine Aktion ausführen möchte, genau dieses XML-Format ausgeben:

```xml
<write_file>
  <path>src/main.py</path>
  <content>print("Hello World")</content>
</write_file>
```

Roo Code parst diese Antwort, führt die Aktion aus (Datei schreiben), und schickt das Ergebnis im nächsten Prompt zurück. Gibt das Modell das XML nicht korrekt aus — oder antwortet es stattdessen mit normalem Text — erscheint:

```
[ERROR] You did not use a tool in your previous response! Please retry with a tool use.
```

Roo Code schickt dann eine Korrektur-Aufforderung, was den Kontext weiter aufbläht und oft in einem Endloskreis endet.

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

## Lösung: Fine-tuned Tool-Use-Modelle

Einige Modelle wurden speziell auf Cline/Roo Code-Werkzeugnutzung feinabgestimmt. Sie verstehen das XML-Format zuverlässig:

| Modell | Ollama-Name | Größe | Besonderheit |
|---|---|---|---|
| DeepSeek R1 (Roo/Cline) | `tom_himanen/deepseek-r1-roo-cline-tools:14b` | 14B | Reasoning-Modell, gut für komplexe Aufgaben |
| DeepSeek R1 (Roo/Cline) | `tom_himanen/deepseek-r1-roo-cline-tools:70b` | 70B | Benötigt viel VRAM; Reasoning füllt Kontext |
| Qwen2.5-Coder-Tools | `hhao/qwen2.5-coder-tools:32b` | 32B | Bestes lokales Allround-Modell für Roo Code |
| Qwen2.5-Coder-Cline | `maryasov/qwen2.5-coder-cline:32b` | 32B | Alternative, etwas langsamer |
| GLM-4-32B (Cline/RooCode) | `mychen76/GLM-4-32B-cline-roocode:Q4` | 32B, Q4 = 20 GB | THUDM-Basis, optimiert für komplexes Problemlösen |
| OpenHands-32B (Cline/RooCode) | `mychen76/openhands_32b-cline-roocode:Q4` | 32B, Q4 = 20 GB | OpenHands-lm-Basis |

(Quellen: `mychen76GLM-4-32B-cline-roocode.md`, `mychen76openhands_32b-cline-roocode.md`, `Local model for coding.md`)

> **Achtung bei Reasoning-Modellen** (z.B. DeepSeek R1): Die internen Denkprozesse (`<thinking>`-Blöcke) füllen den Kontext schnell auf und können bei kleinem Kontextfenster die Ausgabe-Qualität verschlechtern.

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

---

[Wiki-Index](../index.md)
