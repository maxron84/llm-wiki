---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, hardware, gpu, lokale-modelle]
status: active
---

# AMD Radeon AI Pro R9700

**Zusammenfassung**: Professionelle KI-Grafikkarte mit 32 GB GDDR6 — doppelt so viel VRAM wie die RTX 5080, aber mit Blower-Kühler und geringerer Speicherbandbreite. Interessant für LLM-Inferenz mit 33B-Modellen, aber nicht uneingeschränkt für Desktop-Einsatz empfohlen.
**Quellen**: Geizhals-Produktseite, Gespräch mit Claude (2026-05-15)
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
| Kühlung | Axial (leise) | **Blower (laut)** |
| CUDA / ROCm | CUDA (ausgereift) | ROCm (in Entwicklung) |
| LLM ≤ 14B bei 40k | ✅ optimal | ✅ |
| LLM 33B bei 40k | ❌ passt nicht | ✅ (~28 GB) |

## Eignung für lokale LLM-Inferenz

**Vorteil gegenüber RTX 5080**: 32 GB ermöglichen Modelle wie Qwen3-32B oder Nemotron 33B vollständig auf GPU — bei 40k Kontext (~10 GB KV-Cache) bleibt noch Puffer. Das ist der entscheidende qualitative Sprung gegenüber der 5080.

**Nachteil**: Die Speicherbandbreite (645 vs. ~960 GB/s) ist spürbar geringer. Für Modelle die auf der 5080 passen (≤ 14B), wäre die R9700 beim Token-Durchsatz langsamer.

## Kühlung — kritisch für Desktop-Einsatz

Der Blower-Lüfter (Radial, 80 mm) ist ein Designmerkmal von Workstation- und Server-Karten: Er bläst die heiße Luft gezielt nach hinten aus dem Gehäuse — ideal im Rack oder in engen Tower-Servern, aber **laut unter Last**.

> **Für einen PC direkt am Schreibtisch ist das ein ernstes Problem.** Blower-Karten erzeugen deutlich mehr Lärm als Axial-Kühler mit 2–3 großen Lüftern. Unter Inferenz-Last (GPU dauerhaft aktiv) ist das nicht angenehm im Wohnraum.

Alternativen falls die Karte in Zukunft in einer Nicht-Blower-Variante erscheint: dann neu bewerten.

## Software-Ökosystem

- **Linux / Ubuntu**: ROCm-Support in Ollama funktioniert, aber weniger ausgereift als CUDA. Für diese neue Karte (Mai 2025) sind frühe Treiberprobleme möglich.
- **Windows**: AMD ROCm auf Windows ist experimentell — für produktiven LLM-Einsatz unter Windows derzeit nicht empfohlen.
- **Empfehlung**: Nur wenn Ubuntu das primäre Inference-OS ist und Zeit für Treiber-Debugging eingeplant ist.

## Verfügbarkeit

Derzeit (Mai 2026) keine Angebote in Österreich (Geizhals). Nur als Founder Edition bekannt — keine Drittanbieter-Versionen mit alternativer Kühlung in Sicht.

## Fazit

Technisch interessant für den Schritt von 14B zu 33B-Modellen. Die zwei entscheidenden Einschränkungen für diesen Einsatzfall:

1. **Blower-Kühler** — nicht geeignet für einen PC direkt am Schreibtisch
2. **ROCm-Reife** — unter Windows noch zu riskant, unter Ubuntu vertretbar

Empfehlung: Beobachten. Falls eine Axial-gekühlte Variante erscheint und ROCm unter Linux stabil läuft — dann ernsthaft in Betracht ziehen.

## Verwandte Seiten

- [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md) — RTX 5080 Setup, Modellvergleich, Qwen3-Stack
- [lokale-modelle](../anleitungen/lokale-modelle.md) — Einstieg in lokale Modelle mit Ollama

---

[Wiki-Index](../index.md)
