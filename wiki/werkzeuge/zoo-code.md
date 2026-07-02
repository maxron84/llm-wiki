---
date: 2026-07-02
type: werkzeug
tags: [werkzeug, lokale-modelle, setup, automatisierung]
status: active
---

# Zoo Code

**Zusammenfassung**: Zoo Code ist ein Community-Fork von Roo Code — eine Open-Source-VS-Code-Extension für autonomes Coding mit lokalen oder Cloud-Modellen. Das Projekt entstand, nachdem das ursprüngliche Roo-Team die aktive Entwicklung eingestellt und sich auf das neue Produkt „Roomote" konzentriert hat.
**Quellen**: [Zoo-Code-Org/Zoo-Code](https://github.com/Zoo-Code-Org/Zoo-Code), [Zoo-Code-Org/Zoo-Code-Docs](https://github.com/Zoo-Code-Org/Zoo-Code-Docs), [docs.zoocode.dev](https://docs.zoocode.dev)
**Zuletzt aktualisiert**: 2026-07-02

---

## Hintergrund: Warum Zoo Code?

[Roo Code](roo-code.md) war bis Anfang 2026 die führende OSS-Alternative zu GitHub Copilot für autonomes Coding. Als das Roo-Team die Entwicklung einstellte und zu „Roomote" wechselte, entstand Zoo Code als Community-Fork unter der Apache-2.0-Lizenz — mit dem Ziel, Roo Codes Featureset weiterzupflegen und auszubauen.

Zoo Code ist ein direkter Drop-in-Ersatz: bestehende Roo-Code-Konfigurationen lassen sich in zwei Schritten importieren.

---

## Was ist Zoo Code?

Zoo Code ist ein **autonomer Coding-Agent als VS-Code-Extension**. Das Modell formuliert eigenständig einen Plan, liest Dateien, führt Code aus und schreibt Änderungen — bis die gestellte Aufgabe erledigt ist. Zoo Code nutzt dabei dieselbe Architektur wie sein Vorgänger Roo Code.

Gegenüber einfachen Autocomplete-Assistenten (GitHub Copilot, Cursor) ist Zoo Code ein **Auto-Coder**: er schreibt keine einzelnen Zeilen vor, sondern bearbeitet eigenständig vollständige Aufgaben.

---

## Modi

Zoo Code erbt alle Roo-Code-Modi und baut sie aus:

| Modus | Zweck |
|---|---|
| **Code** | Schreibt und editiert Code |
| **Architect** | Strukturierung und Systemdesign |
| **Debug** | Fehlersuche und Bugfixing |
| **Ask** | Verstehen und Erklären ohne Codeänderungen |
| **Orchestrator** | Koordiniert Unteraufgaben über mehrere Agenten |
| **Custom** | Eigene, frei definierbare Modi |

Der **Orchestrator-Modus** ist neu gegenüber Roo Code und ermöglicht komplexere Multi-Step-Aufgaben mit Sub-Agenten.

---

## Neue Features gegenüber Roo Code

| Feature | Beschreibung |
|---|---|
| **Rules Management UI** | Grafische Verwaltung von Custom Rules — kein manuelles Datei-Editieren mehr |
| **Completion Change Review** | Änderungen vor dem Übernehmen inspizieren und selektiv rückgängig machen |
| **Orchestrator-Modus** | Koordination komplexer Projekte über mehrere Unteragenten |
| **Codebase-Indexierung** | Eingebaute semantische Indexierung des Projekts für bessere Trefferquote |

---

## Unterstützte Anbieter und Modelle

Zoo Code ist kompatibel mit „Dutzenden von Anbietern und Hunderten von Modellen" (Zitat Docs). Die Architektur entspricht Roo Code:

| Anbieter | Endpoint | Zoo-Code-Provider |
|---|---|---|
| Ollama (empfohlen) | `http://host:11434/v1` | `openai-compatible` |
| LM Studio | `http://localhost:1234/v1` | `lmstudio` |
| Anthropic (Claude) | API-Schlüssel | `anthropic` |
| OpenAI | API-Schlüssel | `openai` |
| Open WebUI + Ollama | variabel | `openai-compatible` |

> Für lokale Modelle via Ollama gilt weiterhin: Den nativen `ollama`-Provider **nicht** verwenden — er liefert ein vereinfachtes JSON-Format. Den `/v1`-Endpoint mit Provider `openai-compatible` verwenden.

Alle bestätigten Roo-Code-Konfigurationen (z.B. `qwen3:14b-40k` auf RTX 5080) funktionieren unverändert in Zoo Code.

---

## Migration von Roo Code

Zoo Code bietet eine dedizierte Migrationshilfe:

1. **Export aus Roo Code**: Roo-Code-Einstellungen öffnen → „Export" klicken → Datei speichern
2. **Import in Zoo Code**: Zoo Code öffnen → „Import Settings" → exportierte Datei auswählen

Die Einstellungen werden mit der aktuellen Zoo-Code-Konfiguration zusammengeführt.

> **Sicherheitshinweis**: Die exportierte Datei kann API-Schlüssel enthalten. Nicht teilen oder in ein Versionskontrollsystem einchecken.

---

## Aktueller Status (Stand: 2026-07-02)

| Merkmal | Wert |
|---|---|
| Version | 3.64.0 (Juni 2026) |
| Lizenz | Apache 2.0 |
| Sprache | TypeScript (99%) |
| GitHub Stars | ~1.200 |
| Forks | ~167 |
| Commits | ~7.350 |

---

## Stärken und Grenzen (lokale Modelle)

**Stärken:**
- Drop-in-Ersatz für Roo Code — keine Neukonfiguration nötig
- Aktive Community-Entwicklung
- Rules Management UI erleichtert das Anpassen ohne Datei-Editierung
- Completion Change Review reduziert das Risiko unerwünschter Änderungen

**Grenzen (wie beim Vorgänger):**
- Lokale Modelle unter 70B für komplexe Aufgaben oft nicht stark genug
- Tool-Calling erfordert korrekt konfiguriertes Kontextfenster
- Modelle müssen echte `tool_calls` liefern (kein JSON-als-Text-Fallback)

---

## Verwandte Seiten

- [roo-code](roo-code.md) — Vorgänger, nicht mehr aktiv gepflegt
- [tool-use-lokale-modelle](../konzepte/tool-use-lokale-modelle.md) — Warum lokale Modelle mit Tool-Nutzung kämpfen
- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — Kontextlimits und Modelfile-Setup
- [quantisierung](../konzepte/quantisierung.md) — VRAM-Tradeoffs bei lokalen Modellen
- [roocode-system-prompt-optimierung](../konzepte/roocode-system-prompt-optimierung.md) — Kontext-Reduktion (gilt auch für Zoo Code)
- [lm-studio](lm-studio.md) — Alternative zu Ollama mit GUI
- [zoocode-llm-wiki-lokal](../vorlagen/zoocode-llm-wiki-lokal.md) — System-Prompt-Vorlage für LLM-Wiki mit Zoo Code

---

[Wiki-Index](../index.md)
