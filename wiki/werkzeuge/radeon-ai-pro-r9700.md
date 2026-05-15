---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, hardware, gpu, lokale-modelle]
status: active
---

# AMD Radeon AI Pro R9700

**Zusammenfassung**: Professionelle KI-Grafikkarte mit 32 GB GDDR6 und Blower-Kühler. Vergleich zur RTX 5080 im Kontext lokaler LLM-Inferenz.
**Quellen**: AMD Produktseite R9700, AMD Radeon AI PRO Übersichtsseite (beide 2026-05-15)
**Zuletzt aktualisiert**: 2026-05-15

---

## Spezifikationen

| Eigenschaft | Wert |
|---|---|
| VRAM | 32 GB GDDR6 |
| Speicherbandbreite | 640 GB/s |
| Infinity Cache | 64 MB |
| Chip | Navi 48 XTW (RDNA 4) |
| Fertigung | TSMC N4P (5 nm) |
| Stream Processors | 4096 |
| FP32 Vector | 47,8 TFLOPS |
| FP16 Vector | 47,8 TFLOPS |
| FP16 Matrix | 191 TFLOPS (383 mit Structured Sparsity) |
| INT8 | 383 TOPS (766 mit Structured Sparsity) |
| TDP | 300 W |
| Empf. Netzteil | 750 W |
| Kühlung | Aktiv (Blower) |
| PCIe | 5.0 x16 |
| ECC | Ja (nur Linux) |
| OS-Support | Windows 10/11 (64-bit), Linux x86 64-bit |
| Erscheinung | Mai 2025 |

## Vergleich mit RTX 5080

| | RTX 5080 | Radeon AI Pro R9700 |
|---|---|---|
| VRAM | 16 GB GDDR7 | **32 GB GDDR6** |
| Speicherbandbreite | ~960 GB/s | 640 GB/s |
| FP16 Matrix | ~200 TFLOPS | 191 TFLOPS |
| TDP | ~350 W | 300 W |
| Kühlung | Axial | **Blower** |
| Backend | CUDA | ROCm |
| LLM ≤ 14B bei 40k | ✅ | ✅ |
| LLM 33B bei 40k | ❌ | ✅ (~28 GB) |

## AMD-Benchmarks: Große Modelle (Quelle: AMD)

Für Modelle die nicht in den VRAM der RTX 5080 passen und daher CPU-Offloading benötigen, zeigt AMD folgende Token/s-Verhältnisse (R9700 vs. RTX 5080):

| Modell | R9700 vs. RTX 5080 |
|---|---|
| Phi 3.5 MoE Q4 | 3,6× |
| Mistral Small 3.1 24B Q8 | 4,4× |
| DeepSeek R1 Distill Qwen 32B Q6 | 4,5× |
| Qwen3 32B Q6 | 4,5× |
| Qwen3 32B Q6 (3000+ Token Prompt) | 5,0× |

Die Gewinne entstehen weil die RTX 5080 diese Modelle nur mit CPU-Offloading ausführen kann — kein reiner GPU-gegen-GPU-Vergleich.

## Hinweise

**Kühlung**: Der Blower-Lüfter ist für Rack- und Servereinsatz ausgelegt. Unter Dauerlast (wie LLM-Inferenz) deutlich lauter als Axial-Kühler — relevant für Desktop-Einsatz direkt am Schreibtisch.

**Backend**: ROCm wird offiziell für Windows 10/11 und Linux unterstützt. ECC-Speicher nur unter Linux verfügbar.

## Verwandte Seiten

- [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md) — RTX 5080 Setup, Modellvergleich, Qwen3-Stack

---

[Wiki-Index](../index.md)
