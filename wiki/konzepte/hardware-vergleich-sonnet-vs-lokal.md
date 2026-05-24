---
date: 2026-05-16
type: konzept
tags: [konzept, hardware, lokale-modelle, vergleich]
status: active
---

# Hardware-Vergleich: Sonnet 4.6 vs. lokale Modelle für LLM-Wiki

**Zusammenfassung**: Welche Hardware braucht man, um mit lokalen Modellen eine mit Claude Sonnet 4.6 vergleichbare Qualität bei der LLM-Wiki-Pflege zu erreichen — und was "vergleichbar" in der Praxis bedeutet.
**Quellen**: Synthese aus Wiki-internen Seiten (quantisierung.md, tool-use-lokale-modelle.md, radeon-ai-pro-r9700.md, roocode-llm-wiki-einrichten.md)
**Zuletzt aktualisiert**: 2026-05-16

---

## Die ehrliche Einschätzung

"Vergleichbar mit Claude Sonnet 4.6" ist eine sehr hohe Latte. Sonnet 4.6 bietet:

- 200K Kontext — kann den gesamten Vault in einem Schritt lesen
- Nahezu perfektes Instruction Following (CLAUDE.md-Regeln, YAML-Format, Verlinkungsregeln)
- Zuverlässiges Multi-File-Tool-Use ohne Fehler bei Dateipfaden oder Parametern
- Ausgezeichnetes Deutsch auf Muttersprachler-Niveau

Kein aktuelles lokales Modell unter ~70B erreicht das für komplexe Wiki-Aufgaben. Für **einfachere Wiki-Aufgaben** (einzelne Quelle aufnehmen, Einzelseite aktualisieren) kommt man mit der richtigen Hardware jedoch nah heran.

---

## Hardware-Tiers

### Tier 1 — Minimal (RTX 5080, 16 GB VRAM)

Bestes Modell: `qwen3:14b-40k` (Q8, ~9,3 GB Gewichte + ~6,7 GB KV-Cache = ~16 GB)

| Aufgabe | Ergebnis |
|---|---|
| Kurze Quelle aufnehmen (<5K Tokens) | ✅ |
| Seitenformatierung und Verlinkung | ✅ |
| Quellen >8K Tokens | ⚠️ — Kontext wird knapp |
| Mehrere Seiten gleichzeitig bearbeiten | ❌ |
| Lint-Lauf über den gesamten Vault | ❌ |

Für ~60–70% der alltäglichen Wiki-Aufgaben ausreichend. Sonnet 4.6 ist bei allem Komplexen merklich besser.

---

### Tier 2 — Deutlicher Sprung (RTX 4090 / RTX 3090, 24 GB VRAM)

Preis: ca. €2.100–2.500 gebraucht (eBay.de, Mai 2026) — RTX 3090 günstiger, ca. €400–700

| Modell | Quantisierung | VRAM | Qualität |
|---|---|---|---|
| Qwen3 14B | Q8 | ~22 GB | Maximal mögliche Präzision für 14B |
| Qwen3 32B | Q4_K_M | ~24 GB | Mehr Parameter, aber Q4-Einbußen |

Was sich verbessert: 14B@Q8 mit 32K Kontext läuft entspannt ohne VRAM-Stress. Kein Headroom-Problem wie auf RTX 5080. Für einfache bis mittlere Wiki-Aufgaben die empfohlene Qualitätsstufe.

Wichtig: 32B@Q4 ist bei Tool-Use und Präzision **nicht automatisch besser** als 14B@Q8. Mehr Parameter kompensieren nicht den Q4-Qualitätsverlust — die Community-Erfahrung zeigt das deutlich. → [quantisierung](quantisierung.md)

---

### Tier 3 — Nächster Qualitätssprung (RTX 5090 / R9700, 32 GB VRAM)

Preis: ca. €1.400–5.000

- `qwen3:32b` mit Q4_K_M + 32K Kontext läuft vollständig auf der GPU
- ~4–5× mehr Parameter als 14B → besseres Reasoning, kohärentere lange Ausgaben
- RTX 5090: ~2,8× höhere Speicherbandbreite als R9700 → mehr Token/s; aber massiv überhöhte Marktpreise (Mai 2026)
- R9700: ROCm statt CUDA — Ollama-Support weniger ausgereift, RDNA 4 noch im Aufbau

Ehrliche Einschätzung: Der Qualitätsgewinn gegenüber 24 GB (Tier 2) ist real, aber kleiner als erwartet — weil man immer noch bei Q4 bleibt.

---

### Tier 4 — Nächster Qualitätssprung (48+ GB VRAM oder Apple Silicon)

**Option A: Dual-GPU (2× RTX 3090, 48 GB VRAM total)** — Preis: ca. €1.600–2.000

**Option B: Apple Mac Studio M3/M4 Ultra (192 GB Unified Memory)** — Preis: ca. €5.000–8.000

| Modell | Quantisierung | VRAM | Qualität |
|---|---|---|---|
| Qwen3 70B | Q4_K_M | ~40 GB | Annähernd Cloud-Niveau für einfache Tasks |
| Llama 3.3 70B | Q5_K_M | ~48 GB | Besser, braucht 48 GB |

70B-Modelle sind wo die Qualitätslücke zu Cloud-Modellen wirklich kleiner wird. Für Wiki-Arbeit (deutschen Text, Markdown, YAML-Frontmatter, Multi-File-Tool-Use) sind 70B@Q4 ernsthafte Kandidaten.

Der **Mac Studio** hat dabei praktische Vorteile:
- Kein ROCm-Risiko (Ollama läuft nativ auf Apple Silicon)
- Deutlich leiser als jede GPU-Workstation unter Dauerlast
- Unified Memory = kein Engpass zwischen GPU/CPU-Speicher

---

## Aufgaben-Qualitätsvergleich

| Aufgabe | Sonnet 4.6 | 14B@Q8 | 32B@Q4 | 70B@Q4 |
|---|---|---|---|---|
| Kurze Quelle aufnehmen (<5K) | ✅✅ | ✅ | ✅ | ✅✅ |
| Lange Quelle (>10K) | ✅✅ | ⚠️ | ✅ | ✅✅ |
| Mehrere Seiten verlinken | ✅✅ | ⚠️ | ✅ | ✅✅ |
| CLAUDE.md-Regeln strikt einhalten | ✅✅ | ⚠️ | ✅ | ✅ |
| Lint-Lauf (viele Seiten gleichzeitig) | ✅✅ | ❌ | ⚠️ | ✅ |
| Deutsch, klarer Stil | ✅✅ | ✅ | ✅ | ✅✅ |

---

## Empfehlung

**Ziel: 80% der Wiki-Aufgaben, bestes Preis-Qualitäts-Verhältnis**
→ RTX 3090 (24 GB) mit `qwen3:14b@Q8` oder `qwen3:32b@Q4`
→ Preis: ca. €400–700 gebraucht (Mai 2026)
→ Alternative: R9700 (32 GB, neu ~€1.400) — mehr VRAM, aber ROCm/RDNA 4 noch reifend
→ RTX 4090 gebraucht: €2.100–2.500 — kein gutes Preis-Leistungs-Verhältnis mehr (Stand Mai 2026, Quelle: eBay.de)

**Ziel: Wirklich vergleichbar mit Sonnet 4.6**
→ Mac Studio M4 Ultra (192 GB) oder 2× RTX 3090 für 70B-Modelle
→ Preis: ca. €1.600–8.000

**Aktuelles Setup (RTX 5080, 16 GB)**: Sehr gut für einfache Aufgaben. Der nächste sinnvolle Upgrade-Schritt wäre erst 70B — wegen des Q4-Problems macht 32 GB (Tier 3) nur einen kleinen Qualitätsunterschied.

---

## Verwandte Seiten

- [quantisierung](quantisierung.md) — VRAM-Kalkulation, Q4 vs. Q8 Qualitätsvergleich
- [tool-use-lokale-modelle](tool-use-lokale-modelle.md) — Welche Modelle mit Roo Code funktionieren
- [radeon-ai-pro-r9700](../werkzeuge/radeon-ai-pro-r9700.md) — R9700: 32 GB, ROCm, Blower-Kühler
- [ollama-kontextfenster](ollama-kontextfenster.md) — Kontextlimits und KV-Cache-Berechnung
- [roocode-llm-wiki-einrichten](../anleitungen/roocode-llm-wiki-einrichten.md) — Bestätigte Konfiguration RTX 5080 + Roo Code

---

[Wiki-Index](../index.md)
