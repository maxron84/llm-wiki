---
date: 2026-08-11
type: konzept
tags: [konzept, hardware, lokale-modelle, metrik]
status: active
---

# Die KV-Cache-Rechnung

**Zusammenfassung**: Der KV-Cache wächst linear mit der Kontextlänge und hängt an Layer-Zahl und GQA-Konfiguration, nicht an der Parameterzahl. Für 16 GB VRAM folgt daraus: 24B mit 100–200k Kontext ist um Faktor 2–3 ausgeschlossen — und selbst die rechnerisch machbaren Zahlen sind wegen der quadratischen Attention Papierzahlen.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Die Formel

Der KV-Cache ist der Speicher, in dem die Attention-Zwischenergebnisse aller bisherigen Token liegen. Er wächst **linear mit der Kontextlänge**:

```
Bytes pro Token = 2 (K und V) × n_layers × n_kv_heads × head_dim × Bytes pro Element
```

Für ein typisches 24B-Modell der Mistral-Small-Klasse (40 Layer, 8 KV-Heads bei GQA, head_dim 128):

```
2 × 40 × 8 × 128 × 2 Byte (FP16) = 163.840 Byte = 160 KiB pro Token
```

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

**Der wichtigste Befund dabei**: `qwen3:14b` hat dieselbe Geometrie (40 Layer, 8 KV-Heads, head_dim 128) und damit **denselben KV-Bedarf pro Token** wie das 24B-Modell. Der KV-Cache skaliert mit der Layer-Zahl und der GQA-Konfiguration, nicht mit der Parameterzahl.

> „Ein kleineres Modell spart bei den Gewichten, nicht beim Kontext." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## KV-Cache nach Kontextlänge

| Kontext | FP16 | Q8-KV | Q4-KV |
|---|---|---|---|
| 40k | 6,4 GB | 3,2 GB | 1,6 GB |
| 100k | 16,0 GB | 8,0 GB | 4,0 GB |
| 200k | 32,0 GB | 16,0 GB | 8,0 GB |

## Gewichte (Richtwerte GGUF)

| Modell | Q3_K_M | Q4_K_M | Q5_K_M | Q8_0 |
|---|---|---|---|---|
| 14B | ~7,0 GB | ~9,0 GB | ~10,5 GB | ~15,7 GB |
| 24B | ~11,6 GB | ~14,3 GB | ~16,8 GB | ~25,0 GB |

→ [quantisierung](quantisierung.md)

## Das Ergebnis für 16 GB

Nutzbar sind auf einer 16-GB-Karte realistisch **14,5–15,0 GB** (Display, Treiberreserve, Fragmentierung).

| Konfiguration | Gewichte | Rest für KV | Max. Kontext |
|---|---|---|---|
| 24B Q4_K_M + Q8-KV | 14,3 GB | ~0,7 GB | **~9k Token** |
| 24B Q4_K_M + Q4-KV | 14,3 GB | ~0,7 GB | ~18k Token |
| 24B Q3_K_M + Q4-KV | 11,6 GB | ~3,4 GB | ~85k Token (starker Qualitätsverlust) |
| 14B Q4_K_M + Q8-KV | 9,0 GB | ~6,0 GB | **~75k Token** |
| 14B Q4_K_M + Q4-KV | 9,0 GB | ~6,0 GB | ~150k Token (theoretisch) |

> „24B mit 100–200k Kontext auf 16 GB ist ausgeschlossen. Es geht sich nicht knapp nicht aus, sondern um den Faktor 2–3." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Warum auch die machbaren Zahlen Papierzahlen sind

Die 75k des 14B-Modells sind rechnerisch korrekt und praktisch irrelevant. Transformer-Attention ist quadratisch: Jedes neue Token muss gegen alle gecachten berechnet werden. Die vorliegende Messreihe mit `qwen3:14b-40k` zeigt das Muster deutlich:

| Request | Prompt-Token | Dauer |
|---|---|---|
| 1 | ~13,9k | 7,5 s |
| 3 | ~19,6k | 23,9 s |

Ein Sprung um Faktor 3 bei 40 % mehr Kontext. (Quelle: raw/sqlwiki_lokalesmodell_architektur.md) → [ollama-kontextfenster](ollama-kontextfenster.md)

> „Die praktische Nutzungsgrenze liegt bei ~20–25k Token, unabhängig davon, wie viel VRAM man dem KV-Cache widmet. Wer 150k Kontext konfiguriert, konfiguriert eine Zahl, die er nie erreicht, weil er vorher aufgibt."

Das ist der Grund, warum mehr VRAM [Engpass B](engpass-groesse-vs-session.md) nicht auflöst: Die Grenze liegt vor dem Speicher.

## Praktische Konsequenzen für die Konfiguration

- **KV-Quantisierung erfordert Flash Attention.** Bei Ollama: `OLLAMA_FLASH_ATTENTION=1` und `OLLAMA_KV_CACHE_TYPE=q8_0`.
- **Q8-KV kostet praktisch keine Qualität und halbiert den Cache.** Q4-KV ist spürbar, besonders bei langen Kontexten und bei Zahlen/Code.
- **`num_predict` ≥ 8.000 bleibt nötig**, auch wenn im [SQL-Betrieb](sql-wiki-architektur.md) die Einzelausgaben klein sind — abgeschnittene Ausgaben sind der teuerste Fehler, weil sie stillschweigend passieren.

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Korrektur einer früheren Wiki-Angabe

Die Rechnung deckte einen Widerspruch im Bestand auf. Die frühere Angabe lautete:

> „`qwen3:14b-40k` (Q8, ~9,3 GB Gewichte + ~6,7 GB KV-Cache = ~16 GB)"

Beide Teile passen nicht zum Label „Q8":

- **6,7 GB KV-Cache** entsprechen exakt 40k × 160 KiB bei **FP16**. Bei Q8-KV wären es ~3,3 GB.
- **9,3 GB Gewichte** entsprechen bei 14B einer **Q4/Q5**-Quantisierung. Q8_0 wären ~15,7 GB — allein damit wäre die Karte voll.

Die gemessene Gesamtbelegung von 15,1 GiB ist plausibel und bleibt gültig; falsch war nur das Quantisierungslabel. Korrigiert am 2026-08-11 in [hardware-vergleich-sonnet-vs-lokal](hardware-vergleich-sonnet-vs-lokal.md), [quantisierung](quantisierung.md) und [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md). (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Verwandte Seiten

- [quantisierung](quantisierung.md) — Q4/Q5/Q8 bei Gewichten: Qualitätsverlust und VRAM
- [engpass-groesse-vs-session](engpass-groesse-vs-session.md) — Warum die Rechnung Engpass B nicht auflöst
- [ollama-kontextfenster](ollama-kontextfenster.md) — Kontextkonfiguration und Latenzdegradation
- [hardware-vergleich-sonnet-vs-lokal](hardware-vergleich-sonnet-vs-lokal.md) — Hardware-Tiers auf Basis dieser Rechnung
- [sql-wiki-architektur](sql-wiki-architektur.md) — Die Architektur, die die Anforderung verschwinden lässt
- [radeon-ai-pro-r9700](../werkzeuge/radeon-ai-pro-r9700.md) — 32-GB-Karte als Alternative zur Architekturlösung
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
