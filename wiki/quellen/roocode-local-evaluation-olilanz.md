---
date: 2026-05-15
type: quelle
tags: [quelle, lokale-modelle, hardware, metrik]
status: active
---

# Roo Code Local Evaluation (olilanz)

**Zusammenfassung**: Detaillierte technische Evaluation von Roo Code mit lokal gehosteten LLMs auf Consumer-Hardware — ein GitHub-Repository mit Erfahrungsbericht, Modelfile-Konfigurationen und System-Prompt-Optimierungen.
**Quellen**: `olilanzRooCode-Local-Evaluation Evaluation of Roo Code and locally hosted LLMs.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Quelle

GitHub-Repository von olilanz: [RooCode-Local-Evaluation](https://github.com/olilanz/RooCode-Local-Evaluation)
Zeitraum: Februar 2025

---

## Hardware-Setup des Autors

- AMD Ryzen 7, 128 GB DDR4 RAM, 2 TB NVMe
- **RTX 3090 Ti** (24 GB VRAM) + **RTX 3060** (12 GB VRAM) = **36 GB VRAM gesamt**
- Dual-GPU via PCIe (4x + 16x)
- Betriebssystem: Unraid OS mit Container-Setup (Docker)
- Roo Code auf Mac, Remote-Entwicklung auf Unraid-Server

---

## Kernerkenntnisse

### Wie Roo Code intern funktioniert

Roo Code schickt bei jeder Iteration den **vollständigen Kontext** an das Modell:
- System-Prompt (Verhalten + Tool-Definitionen)
- User-Prompt (Aufgabe)
- Alle referenzierten Dateiinhalte
- Vollständige Gesprächshistorie aller vorherigen Iterationen

Selbst einfache Anfragen produzieren HTTP-Requests von 200 KB+. Der Kontext wächst mit jeder Iteration.

### Wann lokale Modelle scheitern

Zwei kritische Punkte bei komplexeren Aufgaben:

1. **Frühe Fehlentscheidungen amplifizieren**: Falsche Annahmen in frühen Iterationen werden als Fakten im wachsenden Kontext zementiert
2. **Kontextüberlauf**: Bei langen Aufgaben überschreitet der Kontext die Modell-Kapazität → Modell halluziniert oder bricht ab

### Quantisierung und Parametergröße

Auf 36 GB VRAM bestens funktionierendes Setup:
```dockerfile
# Beste Stabilität mit diesem Setup
FROM qwen2.5-coder:14b-instruct-q8_0
PARAMETER num_ctx 49125
PARAMETER num_predict 12000
```

Alternative mit Phi-4:
```dockerfile
FROM hf.co/lmstudio-community/phi-4-GGUF:Q8_0
PARAMETER num_ctx 32768
PARAMETER num_predict 12000
```

**Wichtig**: `num_predict` auf 12.000 setzen — Ollamas Standard schneidet sonst große Code-Dateien mitten im Inhalt ab.

### Trained Context Size vs. configured Context Size

Die trainierte Kontextgröße (`n_ctx_train`) ist im Ollama-Log sichtbar:
```
llm_load_print_meta: n_ctx_train = 16384
```

Diese nicht um mehr als Faktor 1,5–2,0 überschreiten. Manche Modelle nutzen RoPE-Skalierung:
```
llama.rope.scaling.factor = 4.0  → theoretisch 64K, aber mit Qualitätsverlust
```

---

## System-Prompt-Optimierung

### MCP deaktivieren
Reduziert System-Prompt von ~51 KB auf ~33 KB. Keine Nachteile wenn MCP nicht genutzt wird.

### Foot Gun System Prompt (Roo Code 3.7.8+)
Weitere Reduktion auf ~28 KB möglich durch Entfernen der Moduswechsel-Logik.

### Eigene Ergänzungen für Low-Memory
```
Als du in einer Low-Memory-Konfiguration läufst, ist es EXTREM wichtig,
dass du keine großen Refactoring-Schritte auf einmal machst. Deine Ausgabe
ist auf 12.000 Token begrenzt. Bei großen Dateien: apply_diff statt write_file.
```

---

## Modelfile-Management-Skript

Der Autor empfiehlt Modelfiles in Git zu versionieren — eines pro getestetes Modell. Für das Batch-Rebuild:

```bash
#!/usr/bin/bash
for modelfile in *.modelfile; do
    base_name="$(basename "${modelfile%.*}")"
    model="${base_name%%-*}"
    tag="${base_name#*-}"
    ollama rm "$model:$tag"
    ollama create "$model:$tag" -f "$modelfile"
done
```

Namenskonvention: `[modellname]-[tag].modelfile`

---

## Testergebnis: Refactoring-Aufgaben

**Aufgabe A (einfach, abgegrenzt)**: „Vereinfache den Code in einer einzelnen Datei, ohne Funktionalität zu ändern."
→ **Erfolgreich** nach 2 Iterationen mit Qwen 2.5 Coder 14B (Q8) und Phi-4

**Aufgabe B (komplex, dateienübergreifend)**: „Faktoriere diese Klasse aus, verschiebe Argparse in eine neue Datei, dokumentiere alle Funktionen."
→ **Instabil** — Tool-Calls fehlerhaft, Roo Code kreist in Schleifen

**Beobachtetes Muster**: Mit steigender Aufgabenkomplexität verschlechtert sich nicht nur die Code-Qualität, sondern auch die Stabilität der Tool-Integration.

---

## Fazit des Autors

> „Für mich ist der Zeitpunkt noch nicht gekommen. Ich überlege ob ein GPU-Upgrade oder Rental-GPUs bei runpod.io die bessere Option ist. Aber egal was kommt — Roo Code wird Teil meines Weges bleiben."

**Hybride Alternative**: Ollama auf gemieteten GPUs (z.B. runpod.io) — volle Kontrolle über Modell und Setup, aber mit leistungsstarker Hardware zu fairen Stundenpreisen.

---

## Verwandte Wiki-Seiten

- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — num_ctx korrekt konfigurieren
- [quantisierung](../konzepte/quantisierung.md) — VRAM-Kalkulation und Stufen
- [roocode-system-prompt-optimierung](../konzepte/roocode-system-prompt-optimierung.md) — Foot Gun und MCP-Deaktivierung
- [roo-code](../werkzeuge/roo-code.md) — Roo Code: Funktionsweise
- [phi-4](../werkzeuge/phi-4.md) — Phi-4 14B im Detail

---

[Wiki-Index](../index.md)
