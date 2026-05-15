---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, lokale-modelle, setup]
status: active
---

# LM Studio

**Zusammenfassung**: LM Studio ist eine Desktop-App mit grafischer Oberfläche zum Herunterladen, Konfigurieren und Ausführen lokaler Sprachmodelle — einsteigerfreundlicher als Ollama, mit eingebautem OpenAI-kompatiblem Inferenz-Server.
**Quellen**: `Using LM Studio With Roo Code.md`, `Using Local Models Roo Code Documentation.md`, `Local model for coding.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Was ist LM Studio?

LM Studio ist eine Desktop-Anwendung (Windows, macOS, Linux) die das Arbeiten mit lokalen Modellen für Nicht-Terminal-Nutzer zugänglich macht. Es vereint:

- Modell-Browser mit direktem Download von Hugging Face
- Chat-Interface direkt in der App
- Eingebauten **lokalen Inferenz-Server** (OpenAI-API-kompatibel, Port 1234)

**Website:** [lmstudio.ai](https://lmstudio.ai)

---

## Modell-Format: GGUF

LM Studio arbeitet mit Modellen im **GGUF-Format** — dem Standardformat für CPU/GPU-Inferenz auf Consumer-Hardware (erzeugt von `llama.cpp`). GGUF-Modelle sind direkt von Hugging Face abrufbar und in verschiedenen Quantisierungsstufen verfügbar (Q4, Q5, Q8, F16).

---

## Einrichtung

1. LM Studio von [lmstudio.ai](https://lmstudio.ai) herunterladen und installieren
2. Im **Discover**-Tab ein Modell suchen und herunterladen (z.B. `qwen2.5-coder:32b`)
3. **Local Server**-Tab öffnen (Icon `<->`)
4. Heruntergeladenes Modell auswählen → **Start Server** klicken

Der Server läuft dann unter `http://localhost:1234`.

---

## Kontextfenster konfigurieren

Im Gegensatz zu Ollama muss das Kontextfenster in LM Studio **vor dem Start des Servers** über die UI eingestellt werden. Das ist gleichzeitig eine Stärke (GUI, kein Modelfile) und eine Schwäche (weniger reproduzierbar, kein Versionsmanagement).

> Tritt der Fehler „Please check the LM Studio developer logs to debug what went wrong" auf, liegt es oft an einem zu kleinen Kontextfenster. (Quelle: `Using LM Studio With Roo Code.md`)

---

## Einrichtung mit Roo Code

1. Roo Code Einstellungen öffnen (Zahnrad-Icon)
2. API Provider: **LM Studio**
3. Model ID: Dateiname des geladenen Modells (z.B. `codellama-7b.Q4_0.gguf`)
4. Base URL: standardmäßig `http://localhost:1234` — nur ändern, wenn LM Studio auf einem anderen Port läuft

Token-Tracking und Reasoning-Tag-Parsing (`<think>`-Tags) werden von Roo Code für LM-Studio-Modelle unterstützt.

---

## LM Studio vs. Ollama

| Eigenschaft | LM Studio | Ollama |
|---|---|---|
| Oberfläche | GUI | CLI |
| Modell-Format | GGUF | GGUF + andere |
| Kontextfenster | In UI konfigurierbar | Via Modelfile (versionierbar) |
| Reproduzierbarkeit | Manuell | Gut (Modelfile in Git) |
| Roo-Code-Empfehlung | Einsteiger | Fortgeschrittene |
| Stabilität mit Roo Code | ⚠️ Berichte über Probleme | ✅ Stabiler |

> Aus der Community: „LM Studios Interface für Roo ist kaputt — das Kontextfenster-Problem lässt sich schwer umgehen. Lieber Ollama nutzen." (Quelle: `Local model for coding.md`, Reddit)

---

## Verwandte Seiten

- [roo-code](roo-code.md) — Roo Code im Detail: Modi, Tool-Nutzung, Optimierungen
- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — Ollama's Kontextfenster-Problem und Modelfile-Lösung
- [lokale-modelle](../anleitungen/lokale-modelle.md) — Einrichtungsanleitung für lokale Modelle (LM Studio Anfänger-Pfad)

---

[Wiki-Index](../index.md)
