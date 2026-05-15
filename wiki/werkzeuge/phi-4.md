---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, lokale-modelle]
status: active
---

# Microsoft Phi-4

**Zusammenfassung**: Kleine, leistungsstarke Modellfamilie von Microsoft — besonders stark bei Mathematik, Code und strukturiertem Denken. Trotz 14B Parameter oft auf Augenhöhe mit größeren Modellen.
**Zuletzt aktualisiert**: 2026-05-15

---

## Die Phi-4-Familie

Microsoft veröffentlichte Phi-4 im Dezember 2024 unter der MIT-Lizenz. Die Familie ist seither gewachsen:

| Modell | Parameter | Kontext | Stärke | Veröffentlicht |
|---|---|---|---|---|
| Phi-4 | 14B | 16K | Allround: Math, Code, Reasoning | Dez. 2024 |
| Phi-4-mini | 3.8B | 128K | Sehr schnell, schwache Hardware | Feb. 2025 |
| Phi-4-reasoning | 14B | 32K | Chain-of-Thought, von o3-mini destilliert | Mai 2025 |
| Phi-4-mini-reasoning | 3.8B | 64K | Miniatur-Reasoning, Edge-Geräte | Mai 2025 |
| Phi-4-multimodal | 5.6B | 128K | Text + Bild + Audio | Feb. 2025 |

Alle Varianten sind über Ollama, LM Studio und Hugging Face verfügbar.

## Hardware-Anforderungen

Phi-4 (14B) belegt im Q4-Format ca. 8–9 GB VRAM — etwas weniger als Qwen3 14B (~10 GB). Auf einer [[radeon-ai-pro-r9700]] mit 32 GB GDDR6 bleibt viel Puffer für einen großen KV-Cache.

| Modell | VRAM (Q4) | R9700 (32 GB) | RTX 5080 (16 GB) |
|---|---|---|---|
| Phi-4 14B | ~9 GB | ✅ viel Puffer | ✅ |
| Phi-4-reasoning 14B | ~9 GB | ✅ | ✅ |
| Phi-4-mini 3.8B | ~2 GB | ✅ | ✅ |

## Vergleich mit Qwen3 14B

Für den deutschen Wiki-Betrieb ist der direkte Vergleich mit Qwen3 14B relevant — beide belegen ähnlich VRAM und laufen vollständig auf GPU.

| | Phi-4 14B | Qwen3 14B |
|---|---|---|
| Kontext (nativ) | **16K** | 40K |
| Deutsch | ⚠️ okay | ✅ stark |
| Mathematik | ✅ sehr stark | ✅ gut |
| Code | ✅ sehr stark | ✅ gut |
| Lizenz | MIT | Apache 2.0 |
| Ollama-Befehl | `ollama pull phi4` | `ollama pull qwen3:14b` |

**Fazit für Wiki-Betrieb**: Qwen3 14B ist für deutschsprachige, kontextintensive Aufgaben die bessere Wahl. Phi-4 überzeugt bei englischen Mathematik- und Code-Aufgaben — besonders `phi4-reasoning` für strukturierte Analysen.

## Das Kontextfenster-Problem

Phi-4 (14B) hat nativ nur 16K Token Kontext. Für aktiven Wiki-Betrieb ist das knapp:

- CLAUDE.md + `wiki/index.md` + 2–3 Seiten ≈ 6–10K Token
- Bei langen Sitzungen oder vielen eingebundenen Dateien wird das Limit rasch erreicht

`phi4-mini` (128K) und `phi4-reasoning` (32K) haben deutlich größere Fenster, sind aber für andere Zwecke optimiert.

## Empfohlene Einsatzszenarien

**Gut geeignet:**
- Mathematische Probleme, Textaufgaben, strukturierte Analysen
- Code-Aufgaben (bevorzugt englisch)
- Phi-4-mini als schnelles Autocomplete-Modell in Continue
- Phi-4-reasoning als spezialisiertes Reasoning-Modell neben einem Haupt-Allrounder

**Weniger geeignet:**
- Deutschsprachige Wiki-Pflege (Qwen3 bevorzugen)
- Lange Sitzungen mit vielen eingebundenen Wiki-Seiten (Kontext-Limit bei 14B)

## Einrichtung mit Ollama

```bash
ollama pull phi4              # Phi-4 14B (~5,5 GB Download)
ollama pull phi4-mini         # Phi-4-mini 3.8B (~2,5 GB Download)
ollama pull phi4-reasoning    # Phi-4-reasoning 14B (~5,5 GB Download)
```

Eintrag in `~/.continue/config.json` für Continue:

```json
{
  "models": [
    {
      "title": "Phi-4 (lokal)",
      "provider": "ollama",
      "model": "phi4",
      "contextLength": 16384
    },
    {
      "title": "Phi-4 Reasoning (lokal)",
      "provider": "ollama",
      "model": "phi4-reasoning",
      "contextLength": 32768
    }
  ]
}
```

## Verwandte Seiten

- [lokale-modelle](../anleitungen/lokale-modelle.md) — Einstieg und Modellübersicht
- [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md) — Qwen3-Stack im Detail
- [radeon-ai-pro-r9700](radeon-ai-pro-r9700.md) — Hardware für lokale Modelle

---

[Wiki-Index](../index.md)
