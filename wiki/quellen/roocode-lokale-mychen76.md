---
date: 2026-05-15
type: quelle
tags: [quelle, lokale-modelle, setup, schema]
status: active
---

# Roo Code Lokale Modelle: mychen76's Ressourcen

**Zusammenfassung**: Minyang Chen (mychen76) hat mehrere quantisierte Modelle speziell für Cline/Roo Code Tool-Nutzung auf Ollama veröffentlicht und einen Artikel über systematische Evaluation lokaler Modelle mit Roo Code geschrieben.
**Quellen**: `mychen76GLM-4-32B-cline-roocode.md`, `mychen76openhands_32b-cline-roocode.md`, `Vibe Coding 'Locally' with ClineRooCode and Ollama.md`, `No Cost Guide to Local AI Code Assistant Roo Code + Ollama.md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Veröffentlichte Modelle

Minyang Chen hat zwei 32B-Modelle quantisiert und für Cline/Roo Code optimiert auf Ollama.com veröffentlicht:

### GLM-4-32B-cline-roocode

Basis: `THUDM/GLM-4-32B-0414` — quantisierte Version, optimiert für Tool-Nutzung mit Cline/Roo Code

```bash
ollama run mychen76/GLM-4-32B-cline-roocode:Q4
```

| Variante | Größe | Kontext |
|---|---|---|
| Q4 | 20 GB | 32K |
| Q5 | 23 GB | 32K |
| Q6 | 27 GB | 32K |

Downloads (Stand 2025): ~1.773

### openhands_32b-cline-roocode

Basis: `Openhands-lm-32B-v01` — quantisierte Version, optimiert für komplexes Problemlösen mit Cline/Roo Code

```bash
ollama run mychen76/openhands_32b-cline-roocode:Q4
```

| Variante | Größe | Kontext |
|---|---|---|
| Q4 | 20 GB | 32K |
| Q5 | 23 GB | 32K |
| Q6 | 27 GB | 32K |

Downloads (Stand 2025): ~750

### Integration mit anderen Tools

Beide Modelle werden auch für andere KI-Coding-Tools unterstützt:

```bash
# Claude Code
ollama launch claude --model mychen76/GLM-4-32B-cline-roocode:Q4
# OpenCode
ollama launch opencode --model mychen76/GLM-4-32B-cline-roocode:Q4
```

---

## Artikel: „Vibe Coding 'Locally' with Cline/RooCode and Ollama" (April 2025)

Minyang Chen beschreibt eine systematische Evaluation mit dem Ziel, das beste lokale Modell für Roo Code / Cline zu finden. Der Artikel betont:

**Zentrale Herausforderung**: Autonome Coding-Agenten wie Cline und Roo Code verlassen sich darauf, dass das Modell interne Tools präzise aufruft (Dateien erstellen, lesen, editieren). Größere Cloud-Modelle (Gemini 2.5, Claude) sind darin zuverlässig. Kleinere lokale Modelle haben oft Probleme mit zuverlässiger Tool-Nutzung.

**Themen des Artikels** (aus Inhaltsverzeichnis):
- Small Local Models Tool Use Challenges
- Experiment Objective & Workflow
- Hardware für Tests
- Evaluation Methodologien
- Bewertung verschiedener Modelle
- Optimierung von Ollama-Modell-Parametern
- Liste Ollama-Modelle, optimiert für Tool-Nutzung
- Ollama Server-Optimierungen

*(Vollständiger Artikel-Inhalt über den Medium-Link erreichbar)*

---

## Artikel: „No Cost Guide to Local AI Code Assistant: Roo Code + Ollama" (Sahil Tah, Medium 2025)

Einstiegs-Tutorial für Entwickler, die Roo Code + Ollama kostenlos und privat einrichten möchten.

**Kontext**: Viele Unternehmen erlauben keine Cloud-KI-Tools aus Datenschutz- oder Compliance-Gründen. Lokale Open-Source-Tools umgehen diese Einschränkung.

**Einrichtungsschritte:**
1. Ollama installieren → `ollama serve`
2. Modell herunterladen → `ollama pull <modell>`
3. Roo Code Extension in VS Code installieren
4. API Provider auf Ollama setzen (automatische Modellerkennung)

**Monitoring:**
```bash
ollama ps  # Zeigt Modell, Speicher, GPU-Nutzung in Echtzeit
```

**Roo Code Modes (Specialized Personas):**  
Roo Code bietet Modi für verschiedene Entwicklungsphasen — Code, Architect, Debug, Ask — die das Verhalten des Assistenten für die jeweilige Rolle anpassen.

---

## Einordnung

mychen76's Modelle sind eine praktische Ressource für alle, die:
- Genau 32 GB VRAM haben (RTX 3090 + Reserve für Kontext)
- Tool-Use-Verlässlichkeit über rohe Coding-Qualität priorisieren
- Keine Zeit für eigenes Fine-Tuning haben

Für die meisten Setups empfiehlt sich zuerst `hhao/qwen2.5-coder-tools:32b` zu testen (breiter Community-Support), bevor man zu GLM-4 oder OpenHands wechselt.

---

## Verwandte Wiki-Seiten

- [tool-use-lokale-modelle](../konzepte/tool-use-lokale-modelle.md) — Vollständige Übersicht aller Fine-tuned Modelle
- [quantisierung](../konzepte/quantisierung.md) — VRAM-Kalkulation für 32B-Modelle
- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — Kontextfenster konfigurieren
- [roo-code](../werkzeuge/roo-code.md) — Roo Code im Detail

---

[Wiki-Index](../index.md)
