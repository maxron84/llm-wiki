---
date: 2026-05-15
type: quelle
tags: [quelle, lokale-modelle, setup]
status: active
---

# Roo Code Offizielle Dokumentation: Lokale Modelle

**Zusammenfassung**: Die offizielle Roo Code Dokumentation beschreibt Einrichtung und Betrieb mit lokalen Modellen via Ollama und LM Studio — inklusive Kontextfenster-Konfiguration, Troubleshooting und Hardware-Empfehlungen.
**Quellen**: `Using Local Models Roo Code Documentation.md`, `Using Ollama With Roo Code Roo Code Documentation.md`, `Using LM Studio With Roo Code.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Zusammenfassung der Quellen

Drei offizielle Seiten aus der Roo Code Dokumentation (roocodeinc.github.io), zusammengefasst.

### Unterstützte lokale Anbieter

Roo Code unterstützt zwei lokale Modell-Anbieter:
- **Ollama** — CLI-basiert, breite Modellauswahl, native API
- **LM Studio** — GUI-basiert, GGUF-Modelle, OpenAI-kompatibel

---

## Ollama: Einrichtung

```bash
# 1. Ollama starten
ollama serve

# 2. Modell herunterladen
ollama pull qwen2.5-coder:32b

# 3. Kontextfenster konfigurieren (Modelfile empfohlen)
ollama create mein-modell -f Modelfile
```

**Modelfile-Beispiel:**

```dockerfile
FROM qwen2.5-coder:32b
PARAMETER num_ctx 32768
PARAMETER temperature 0.7
PARAMETER repeat_penalty 1.1
```

**Wichtig**: Roo Code liest `num_ctx` automatisch aus dem Ollama-Modell und nutzt diesen Wert ohne weitere Konfiguration in den Roo Code Einstellungen.

**Roo Code konfigurieren:**
- API Provider: `ollama`
- Model: Name des gespeicherten Modells (z.B. `mein-modell`)
- Base URL: `http://localhost:11434` (Standard)

---

## Ollama: Troubleshooting

### Out-of-Memory beim ersten Request

Beim Kaltstart lädt Ollama das Modell und weist möglicherweise einen größeren Kontext zu als VRAM vorhanden.

**Fixes:**
1. Modell vor Roo-Anfrage manuell laden: `ollama run <modell>`
2. `num_ctx` im Modelfile fixieren — verhindert dynamische Zuweisung
3. Kleinere Quantisierung nutzen (Q4 statt Q8)
4. Kleineres Modell (7B/13B statt 32B)

```bash
# Laufende Modelle prüfen
ollama ps
# Modell stoppen nach OOM
ollama stop <modell-name>
```

---

## LM Studio: Einrichtung

1. Modell im **Discover**-Tab herunterladen (GGUF-Format)
2. **Local Server**-Tab → Modell auswählen → **Start Server**
3. Server läuft unter `http://localhost:1234`

**Roo Code konfigurieren:**
- API Provider: `LM Studio`
- Model ID: Dateiname des geladenen Modells (z.B. `codellama-7b.Q4_0.gguf`)
- Base URL: `http://localhost:1234` (Standard)

**Besonderheiten:**
- Token-Tracking wird unterstützt
- Reasoning-Tags (`<think>`) werden geparst
- Kontextfenster in der LM Studio UI einstellen (vor Serverstart)

---

## Vor- und Nachteile lokaler Modelle (laut offizieller Doku)

**Vorteile:**
- Datenschutz: Code verlässt nicht den Rechner
- Offline-Betrieb möglich
- Keine API-Kosten
- Experimentierfreiheit mit verschiedenen Modellen

**Nachteile:**
- Ressourcenintensiv (leistungsstarker Rechner mit GPU empfohlen)
- Aufwändigere Einrichtung
- Qualität variiert — lokale Modelle erreichen nicht immer die Leistung großer Cloud-Modelle
- Erweiterte Features (Prompt Caching, Computer Use) oft nicht verfügbar

---

## Verwandte Wiki-Seiten

- [roo-code](../werkzeuge/roo-code.md) — Roo Code im Detail
- [lm-studio](../werkzeuge/lm-studio.md) — LM Studio Werkzeugseite
- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — Das 2K-Problem und Lösungen
- [roocode-lokale-community-reddit](roocode-lokale-community-reddit.md) — Praxiserfahrungen aus der Community

---

[Wiki-Index](../index.md)
