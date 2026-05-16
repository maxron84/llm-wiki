---
date: 2026-05-15
type: konzept
tags: [konzept, lokale-modelle, hardware, kosten]
status: active
---

# Quantisierung lokaler Modelle

**Zusammenfassung**: Quantisierung reduziert die Speicherpräzision von Modellgewichten um den VRAM-Bedarf zu senken — auf Kosten von Modellqualität. Das richtige Gleichgewicht zwischen Parametergröße, Quantisierungsstufe und Kontextfenster ist der Schlüssel zu funktionierenden lokalen Setups.
**Quellen**: `olilanzRooCode-Local-Evaluation Evaluation of Roo Code and locally hosted LLMs.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Was ist Quantisierung?

Beim Training werden Modellgewichte (Parameter) in 32-Bit-Gleitkommazahlen (fp32) gespeichert. Für die lokale Inferenz ist das zu speicherintensiv. Quantisierung konvertiert diese Gewichte in niederpräzisere Datentypen:

| Stufe | Bits | Speicher/Parameter | Präzisionsverlust |
|---|---|---|---|
| fp32 | 32 Bit | ~4 Bytes | Keiner (Training) |
| fp16 / bf16 | 16 Bit | ~2 Bytes | Minimal |
| Q8 | 8 Bit | ~1 Byte | Gering |
| Q6 | 6 Bit | ~0,75 Bytes | Merklich |
| Q5 | 5 Bit | ~0,625 Bytes | Spürbar |
| Q4 | 4 Bit | ~0,5 Bytes | Deutlich |

---

## VRAM-Kalkulation

Faustformel für den VRAM-Bedarf eines Modells:

```
VRAM (GB) ≈ Parameter (B) × Bytes/Parameter × 1,1 (Overhead)
```

**Beispiele:**

| Modell | Quantisierung | VRAM (Modell) | + 32K Kontext | Gesamt ca. |
|---|---|---|---|---|
| 7B | Q8 | ~7 GB | ~4 GB | ~11 GB |
| 14B | Q8 | ~14 GB | ~8 GB | ~22 GB |
| 14B | Q4 | ~7 GB | ~8 GB | ~15 GB |
| 32B | Q4 | ~16 GB | ~8 GB | ~24 GB |
| 70B | Q4 | ~35 GB | ~8 GB | ~43 GB |

> VRAM-Bedarf für Kontext: ~0,5 MB pro 1.000 Token bei Q8, variiert nach Modellarchitektur.

---

## Empfehlung: Nicht unter Q8 gehen (wenn möglich)

Qualitativ macht der Schritt von Q8 auf Q4 bei Coding-Aufgaben einen merklichen Unterschied:

- **Q8**: Kaum sichtbarer Qualitätsverlust gegenüber fp16
- **Q6/Q5**: Kompromiss — oft ausreichend für Code-Aufgaben
- **Q4**: Spürbare Qualitätseinbußen, erhöhte Halluzinationsrate, Instabilität bei komplexen Aufgaben
- **Q3 und darunter**: Für ernsthafte Coding-Aufgaben in der Regel nicht empfohlen

> „Quantisierung unter Q8 zu gehen macht die Sache schwieriger. Mit 36 GB VRAM habe ich die besten Ergebnisse mit Q8 eines 14B-Modells erzielt." (Quelle: `olilanzRooCode-Local-Evaluation.md`)

---

## Parametergröße vs. Quantisierung

Grundsätzliche Faustregel: **Mehr Parameter bei niedrigerer Quantisierung** kann schlechter sein als **weniger Parameter bei höherer Quantisierung**.

| Option A | Option B |
|---|---|
| 32B @ Q4 (~16 GB) | 14B @ Q8 (~14 GB) |
| Mehr Parameter, aber ungenauer | Weniger Parameter, aber präziser |

Die Wahl hängt von der Aufgabe ab. Für Coding-Agenten (Roo Code) ist Präzision bei der Tool-Nutzung wichtiger als rohe Parameteranzahl. Praktische Empfehlung: Erst mit Q8 testen, dann Quantisierung verringern wenn VRAM nicht reicht.

---

## GPU-Nutzung optimieren

Für interaktives Arbeiten (nicht: Batch-Verarbeitung über Nacht) sollte das Modell **vollständig in den GPU-VRAM passen**:

```bash
ollama ps  # Zeigt ob Modell auf GPU oder CPU läuft
```

CPU-Inferenz ist 10–100× langsamer als GPU-Inferenz. Wenn das Modell nicht vollständig in den VRAM passt und Teile auf die CPU ausgelagert werden, ist die Antwortzeit für interaktives Arbeiten meist unbrauchbar.

---

## Speziell trainierte Quantisierungen (GGUF-Varianten)

Für Roo Code optimierte Modelle auf Ollama bieten oft mehrere Quantisierungsstufen an:

| Stufe | Dateigröße (32B) | VRAM (ca.) | Kontext |
|---|---|---|---|
| Q4 | ~20 GB | ~24–28 GB | 32K |
| Q5 | ~23 GB | ~28–32 GB | 32K |
| Q6 | ~27 GB | ~32–36 GB | 32K |

(Quelle: `mychen76GLM-4-32B-cline-roocode.md`, `mychen76openhands_32b-cline-roocode.md`)

---

## Kontextgröße und Quantisierung: gemeinsam planen

Modell-VRAM und Kontext-VRAM teilen sich den gleichen Speicher. Wer ein 14B-Modell bei Q8 mit 42K Kontext betreibt, verbraucht:
- ~14 GB für das Modell
- ~8–12 GB für den Kontext
- = ~22–26 GB gesamt

Bei 24 GB VRAM (eine RTX 3090) ist das knapp. Lösung: Kontext auf 32K reduzieren oder auf Q6 wechseln.

### Grenze: 32B-Modelle mit 40K Kontext auf RTX 5080 (16 GB)

Auf einer RTX 5080 (16 GB VRAM) ist 32B + 40K Kontext unter keiner praxistauglichen Quantisierung möglich:

| Quantisierung | Modell-VRAM | KV-Cache 40K (32B) | Gesamt | Passt in 16 GB? |
|---|---|---|---|---|
| Q4_K_M | ~18 GB | ~11 GB | ~29 GB | ❌ |
| Q3_K_M | ~12 GB | ~11 GB | ~23 GB | ❌ |
| Q2_K | ~8 GB | ~11 GB | ~19 GB | ❌ |

Der KV-Cache bei 40K Kontext ist für 32B-Modelle (~64 Layer, 8 KV-Heads) mit ~10–11 GB unabhängig von der Gewichts-Quantisierung — er liegt immer in fp16. Das bedeutet: selbst die aggressivste Quantisierung der Gewichte reicht nicht aus.

**Fazit für RTX 5080**: Das Optimum bleibt `qwen3:14b-40k` — ~9,3 GB Gewichte + ~6,7 GB KV-Cache = ~16 GB, alles auf der GPU. 32B mit reduziertem Kontext (≤ 8K) ist möglich, aber dann entfällt der 40K-Vorteil. → [ollama-kontextfenster](ollama-kontextfenster.md), [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md)

---

## Verwandte Seiten

- [ollama-kontextfenster](ollama-kontextfenster.md) — num_ctx korrekt setzen, VRAM-Auswirkungen
- [tool-use-lokale-modelle](tool-use-lokale-modelle.md) — Fine-tuned Modelle für Roo Code / Cline
- [roo-code](../werkzeuge/roo-code.md) — Roo Code und lokale Modelle
- [radeon-ai-pro-r9700](../werkzeuge/radeon-ai-pro-r9700.md) — 32 GB VRAM-Karte für lokales KI-Setup
- [phi-4](../werkzeuge/phi-4.md) — Phi-4: 14B mit 16K Kontext-Limit

---

[Wiki-Index](../index.md)
