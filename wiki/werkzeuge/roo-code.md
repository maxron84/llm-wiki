---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, lokale-modelle, setup, automatisierung]
status: active
---

# Roo Code

**Zusammenfassung**: Roo Code ist eine Open-Source-VS-Code-Extension für autonomes Coding — ein Auto-Coder, der LLMs nutzt um Aufgaben selbstständig abzuarbeiten, Dateien zu lesen und zu schreiben, und schrittweise komplexe Refactorings durchzuführen.
**Quellen**: `Using Local Models Roo Code Documentation.md`, `olilanzRooCode-Local-Evaluation Evaluation of Roo Code and locally hosted LLMs.md`, `No Cost Guide to Local AI Code Assistant Roo Code + Ollama.md`, `Vibe Coding 'Locally' with ClineRooCode and Ollama.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Was ist Roo Code?

Roo Code (früher Roo Cline) ist ein Fork von [Cline](https://github.com/cline/cline) — einem VS-Code-Plugin, das einen LLM als autonomen Coding-Agenten nutzt. Im Gegensatz zu Coding-Assistenten wie GitHub Copilot, die primär Autocomplete anbieten, ist Roo Code ein **Auto-Coder**: Es formuliert eigenständig einen Plan, liest Dateien, führt Code aus und schreibt Änderungen — bis die gestellte Aufgabe erledigt ist.

Roo Code ist Open Source und kann mit lokalen Modellen über [Ollama](ollama.md) oder LM Studio betrieben werden, aber auch mit Cloud-Modellen (Claude, GPT-4o, Gemini) über API.

---

## Modi (Specialized Personas)

Roo Code bringt mehrere eingebaute Modi mit, die das Verhalten des Assistenten spezialisieren:

| Modus | Zweck |
|---|---|
| **Code** | Schreibt und editiert Code |
| **Architect** | Strukturierung und Systemdesign |
| **Debug** | Fehlersuche und Bugfixing |
| **Ask** | Verstehen und Erklären ohne Codeänderungen |
| **Custom** | Eigene, frei definierbare Modi |

Benutzerdefinierte Modi ermöglichen z.B. einen Dokumentations-Generator, einen Security-Reviewer oder einen Tech-Writer. (Quelle: `olilanzRooCode-Local-Evaluation Evaluation of Roo Code and locally hosted LLMs.md`)

---

## Wie Roo Code intern funktioniert

LLMs sind zustandslos — sie haben keinen eigenen Speicher. Roo Code löst das, indem es bei **jeder Anfrage den vollständigen Kontext** mitschickt:

- System-Prompt: Verhaltensregeln + Tool-Definitionen (~33–51 KB)
- User-Prompt: die aktuelle Aufgabe
- Dateiinhalte: alle referenzierten Dateien
- Vollständige Gesprächshistorie aller vorherigen Iterationen

Eine einzelne Anfrage bei Roo Code kann daher schnell 200 KB+ groß werden — auch für scheinbar einfache Aufgaben. Mit jeder Iteration wächst der Kontext weiter an.

```
RooCode → LLM: Sende vollständigen Kontext
LLM → RooCode: Tool-Call oder Antwort
RooCode: Führe Tool aus (Datei lesen/schreiben, Terminal)
RooCode: Embed Ergebnis in Kontext
→ nächste Iteration
```

(Quelle: `olilanzRooCode-Local-Evaluation Evaluation of Roo Code and locally hosted LLMs.md`)

---

## Tool-Nutzung

Roo Code definiert im System-Prompt eine Reihe von XML-basierten Werkzeugen, die das Modell aufrufen kann:

- `read_file` — Dateiinhalt lesen
- `write_file` — Datei schreiben (gesamter Inhalt)
- `apply_diff` — Datei partiell bearbeiten (effizienter bei großen Dateien)
- Weitere: Ordner erstellen, Terminal-Befehle ausführen, Browser-Screenshots, u.a.

Das Modell muss diese XML-Struktur korrekt ausgeben — kleinere Modelle scheitern oft daran. Symptom: das Modell gibt Antworten nur als Text im Chat aus, anstatt Werkzeuge zu nutzen.

→ Mehr dazu: [tool-use-lokale-modelle](../konzepte/tool-use-lokale-modelle.md)

---

## Unterstützte lokale Anbieter

| Anbieter | Schnittstelle | Standard-Port | Roo-Code-Provider |
|---|---|---|---|
| [Ollama](ollama-kontextfenster.md) | Native API | 11434 | `ollama` |
| LM Studio | OpenAI-kompatibel | 1234 | `lmstudio` |
| Open WebUI + Ollama | OpenAI-kompatibel | variabel | `openai-compatible` |

---

## Stärken und Grenzen bei lokalen Modellen

**Stärken:**
- Datenschutz (Code verlässt nicht das Gerät)
- Offline-Betrieb
- Keine Kosten pro Token

**Grenzen:**
- Lokale Modelle unter 70B sind oft für komplexe Aufgaben nicht stark genug
- Kontextfenster ist begrenzter als bei Cloud-Modellen
- Tool-Nutzung erfordert gut konfiguriertes Kontextfenster
- Kleine Modelle können Roo Codes System-Prompt nicht zuverlässig verarbeiten

> Für echte Software-Entwicklungsprojekte mit größeren Codebases sind Cloud-Modelle (Claude 3.7 Sonnet, GPT-4o) derzeit noch deutlich überlegen. Lokale Modelle eignen sich gut für abgegrenzte, gut spezifizierte Einzelaufgaben. (Quelle: `Local model for coding.md`, Reddit-Community)

---

## Optimierungen für lokale Setups

1. **MCP deaktivieren** — reduziert System-Prompt von ~51 KB auf ~33 KB
2. **Foot Gun System Prompt** (ab Roo Code 3.7.8) — weitere Reduktion auf ~28 KB
3. **Aufgaben klein halten** — eine Datei oder Funktion pro Task
4. **`apply_diff` bevorzugen** — weniger VRAM-Verbrauch als `write_file`
5. **`num_predict` in Modelfile setzen** — verhindert abgeschnittene Code-Ausgaben

→ Details: [roocode-system-prompt-optimierung](../konzepte/roocode-system-prompt-optimierung.md)

---

## Fine-tuned Modelle für Roo Code / Cline

Standard-Coding-Modelle scheitern oft an Roo Codes XML-Werkzeug-Format. Diese Modelle wurden speziell für Tool-Use mit Cline/Roo Code feinabgestimmt:

| Modell | Ollama-Name | Größe |
|---|---|---|
| DeepSeek R1 (Roo/Cline-Tools) | `tom_himanen/deepseek-r1-roo-cline-tools` | 14B / 70B |
| Qwen2.5-Coder-Tools | `hhao/qwen2.5-coder-tools` | 32B |
| Qwen2.5-Coder-Cline | `maryasov/qwen2.5-coder-cline` | 32B |
| GLM-4-32B (Cline/RooCode) | `mychen76/GLM-4-32B-cline-roocode` | 32B |
| OpenHands-32B (Cline/RooCode) | `mychen76/openhands_32b-cline-roocode` | 32B |

→ Mehr: [tool-use-lokale-modelle](../konzepte/tool-use-lokale-modelle.md)

---

## Verwandte Seiten

- [tool-use-lokale-modelle](../konzepte/tool-use-lokale-modelle.md) — Warum lokale Modelle mit Tool-Nutzung kämpfen
- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — Das 2K-Standardproblem und Lösungen
- [quantisierung](../konzepte/quantisierung.md) — VRAM-Tradeoffs bei lokalen Modellen
- [roocode-system-prompt-optimierung](../konzepte/roocode-system-prompt-optimierung.md) — Kontext-Reduktion für lokale Setups
- [lm-studio](lm-studio.md) — Alternative zu Ollama mit GUI
- [phi-4](phi-4.md) — Phi-4-Modellfamilie: Evaluation für lokales Coding

---

[Wiki-Index](../index.md)
