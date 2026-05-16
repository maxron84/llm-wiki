---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, hardware, gpu, lokale-modelle]
status: active
---

# AMD Radeon AI Pro R9700

**Zusammenfassung**: Professionelle KI-Grafikkarte mit 32 GB GDDR6 und Blower-Kühler. Vergleich zur RTX 5080 im Kontext lokaler LLM-Inferenz.
**Quellen**: AMD Produktseite R9700, AMD Radeon AI PRO Übersichtsseite, Geizhals Produktseiten (alle 2026-05-15)
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
| Kühlung | Aktiv (Blower) — alle Varianten |
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

## Verfügbare Varianten (Österreich, Mai 2026)

| Hersteller | Modell | Kühlung | Preis ab |
|---|---|---|---|
| ASRock | R9700 Creator 32GB | Radial 75mm | € 1.410,76 |
| PowerColor | R9700 | Radial 80mm | € 1.419,01 |
| GIGABYTE | R9700 AI TOP 32G | Radial 75mm | € 1.427,60 |
| Sapphire | R9700 | Radial 80mm | € 1.475,35 |
| ASUS Turbo | R9700 | Radial 75mm | € 1.486,81 |
| XFX | R9700 | Radial 80mm | € 1.575,59 |

Alle Varianten verwenden Blower-Kühler — das ist eine Klassen-Entscheidung für professionelle AI-Karten, keine Einschränkung einzelner Hersteller. Eine Axial-gekühlte Variante existiert nicht.

## Hypothetisches Setup: 32B-Modell + Roo Code auf R9700

Die 32 GB VRAM ermöglichen, was auf der RTX 5080 (16 GB) nicht geht: ein 32B-Modell vollständig auf der GPU mit nutzbarem Kontextfenster. Ob das für Roo Code tatsächlich funktioniert, hängt von drei Faktoren ab.

### VRAM-Kalkulation

| Modell | Quantisierung | Gewichte | KV-Cache 40K | Gesamt | Headroom |
|---|---|---|---|---|---|
| Qwen3 32B | Q4_K_M | ~18 GB | ~11 GB | ~29 GB | ~3 GB |
| Qwen3 32B | Q5_K_M | ~22 GB | ~11 GB | ~33 GB | ❌ |
| Qwen3 32B | Q4_K_M | ~18 GB | ~8,5 GB (32K) | ~26,5 GB | ~5,5 GB |
| Qwen3 32B | Q5_K_M | ~22 GB | ~8,5 GB (32K) | ~30,5 GB | ~1,5 GB |

**Fazit VRAM**: 32B + 40K passt nur in Q4_K_M, mit ~3 GB Headroom — riskant. Komfortabler: Q4_K_M oder Q5_K_M bei 32K Kontext.

### Qualität: Q4 vs. mehr Parameter

Q4_K_M ist die einzige Quantisierung, die 32B + 40K in 32 GB ermöglicht. Das ist ein Kompromiss — Q4 zeigt bei komplexen Coding-Aufgaben merkliche Qualitätseinbußen. Ob 32B@Q4 besser ist als 14B@Q8 (passt entspannt in die RTX 5080), ist aufgabenabhängig. Für Roo Code (Tool-Calling, Präzision wichtiger als Parameteranzahl): nicht selbstverständlich besser. → [quantisierung](../konzepte/quantisierung.md)

### ROCm: der unbekannte Faktor

Der R9700 läuft über ROCm, nicht CUDA. Für Ollama + Roo Code bedeutet das:

- Ollama unterstützt ROCm offiziell, aber weniger reif als CUDA
- RDNA 4 (Navi 48) ist eine neue Architektur — ROCm-Support für RDNA 4 noch im Aufbau (Stand: Mai 2026)
- Die bestätigte Roo-Code-Konfiguration (→ [roo-code](roo-code.md)) basiert auf CUDA; ROCm-Äquivalent ungetestet
- Auf Windows: ROCm-Support eingeschränkter als auf Linux

### Empfehlung für hypothetisches R9700-Setup

```
Modell:    qwen3:32b (Q4_K_M)
Kontext:   32K (komfortabler als 40K)
Anbieter:  OpenAI Compatible, http://<server>:11434/v1
System:    Linux (besserer ROCm-Support als Windows)
```

Gegenüber RTX 5080 + qwen3:14b-40k: größere Parameterzahl, aber Q4 statt Q8, und ROCm-Risiko. Kein klarer Gewinn — eher ein anderes Kompromiss-Profil.

---

## Hinweise

**Kühlung**: Der Blower-Lüfter (Radial) ist für Rack- und Servereinsatz ausgelegt. Unter Dauerlast (wie LLM-Inferenz) deutlich lauter als Axial-Kühler — relevant für Desktop-Einsatz direkt am Schreibtisch. Gilt für alle Hersteller-Varianten ohne Ausnahme.

**Backend**: ROCm wird offiziell für Windows 10/11 und Linux unterstützt. ECC-Speicher nur unter Linux verfügbar.

## Verwandte Seiten

- [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md) — RTX 5080 Setup, Modellvergleich, Qwen3-Stack
- [quantisierung](../konzepte/quantisierung.md) — VRAM-Kalkulation, Q4 vs. Q8 Qualitätsvergleich
- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — KV-Cache-Berechnung, Latenzdegradation
- [roo-code](roo-code.md) — Bestätigte Konfiguration (CUDA/RTX 5080)

---

[Wiki-Index](../index.md)
