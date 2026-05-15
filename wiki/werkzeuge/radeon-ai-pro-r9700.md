---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, hardware, gpu, lokale-modelle]
status: active
---

# AMD Radeon AI Pro R9700

**Zusammenfassung**: Professionelle KI-Grafikkarte mit 32 GB GDDR6 und Blower-Kühler. Vergleich zur RTX 5080 im Kontext lokaler LLM-Inferenz.
**Quellen**: Geizhals-Produktseite (2026-05-15)
**Zuletzt aktualisiert**: 2026-05-15

---

## Spezifikationen

| Eigenschaft | Wert |
|---|---|
| VRAM | 32 GB GDDR6 |
| Speicherbandbreite | 645 GB/s |
| Chip | Navi 48 XTW (RDNA 4) |
| Fertigung | TSMC N4P (5 nm) |
| FP16 | 95 TFLOPS |
| INT8 | 383 TOPS |
| TDP | 300 W |
| Kühlung | 1× Radial-Lüfter (80 mm Blower) |
| PCIe | 5.0 x16 |
| Anschlüsse | 4× DisplayPort 2.1a |
| Erscheinung | Mai 2025 |

## Vergleich mit RTX 5080

| | RTX 5080 | Radeon AI Pro R9700 |
|---|---|---|
| VRAM | 16 GB GDDR7 | **32 GB GDDR6** |
| Speicherbandbreite | ~960 GB/s | 645 GB/s |
| FP16 | ~100 TFLOPS | 95 TFLOPS |
| TDP | ~350 W | 300 W |
| Kühlung | Axial | **Blower** |
| Backend | CUDA | ROCm |
| LLM ≤ 14B bei 40k | ✅ | ✅ |
| LLM 33B bei 40k | ❌ | ✅ (~28 GB) |

## Hinweise

**Kühlung**: Der Blower-Lüfter ist für Rack- und Servereinsatz ausgelegt. Unter Dauerlast (wie LLM-Inferenz) deutlich lauter als Axial-Kühler — relevant für Desktop-Einsatz direkt am Schreibtisch.

**Backend**: Die Karte verwendet ROCm statt CUDA. Unter Linux (Ollama) grundsätzlich unterstützt; unter Windows experimentell.

## Verwandte Seiten

- [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md) — RTX 5080 Setup, Modellvergleich, Qwen3-Stack

---

[Wiki-Index](../index.md)
