---
date: 2026-05-15
type: quelle
tags: [quelle, lokale-modelle, community]
status: active
---

# Roo Code + Lokale Modelle: Reddit-Community-Erfahrungen

**Zusammenfassung**: Zwei Reddit-Threads aus dem r/RooCode-Subreddit dokumentieren praktische Probleme und Lösungen beim Betrieb mit lokalen Modellen — insbesondere das Kontextfenster-Problem und die Herausforderungen mit Tool-Nutzung.
**Quellen**: `Local model for coding.md`, `Roo Code not using tools properly in offline setup (with Ollama models and Open AI Compatible API provider).md`
**Zuletzt aktualisiert**: 2026-05-15

---

## Thread 1: „Local model for coding" (r/RooCode, März 2025)

**Ausgangssituation**: Ein Mac-Nutzer (64 GB RAM) erfragt Erfahrungen mit lokalen Modellen in Roo Code.

### Wichtigste Erkenntnisse

**Ollama-Kontextfenster ist das #1-Problem:**
Ollamas Standard-Kontextfenster von 2K ist zu klein für Roo Code. Umgebungsvariablen oder `ollama set parameter` werden auf manchen Systemen ignoriert — nur das Überschreiben via Modelfile funktioniert zuverlässig.

**LM Studio hat Probleme mit Roo:**
> „LM Studios Interface für Roo ist kaputt — das Kontextfenster-Problem lässt sich schwer umgehen. Lieber Ollama verwenden. Ende der Geschichte."

**Standard-Coding-Modelle scheitern an Roo Codes Prompts:**
> „Ich sage 'Hallo' und sie generieren einfach random Outputs. Kleinere Modelle verlieren sich in den spezifischen Prompts von RooCode/Cline und verstehen nicht einmal was die Aufgabe ist."

**Empfohlene Fine-tuned Modelle:**
- `hhao/qwen2.5-coder-tools:32b` — bestes lokales Allround-Modell für Roo Code
- `tom_himanen/deepseek-r1-roo-cline-tools:14b` — funktioniert gut, auch wenn langsamer
- `maryasov/qwen2.5-coder-cline:32b` — Alternative, etwas langsamer

**Ehrliche Einschätzung zu 32B-Modellen:**
> „Die beste lokale 32B-Option ist mit sehr vorsichtigem Prompting, je einer Datei/Modul auf einmal, und gutem Debugging-Know-how einigermaßen nutzbar."

**Ernüchterndes Fazit zum Stand 2025:**
> „Lokale Modelle unter 70B sind noch nicht bereit für echte Software-Entwicklung auf größeren Projekten. Jeder auf YouTube, der das Gegenteil behauptet und nur winzige Übungsbeispiele zeigt, lügt dich an."

**Alternative: Kostenlose Cloud-Modelle:**
Gemini Flash 2.0 (kostenlos mit API-Schlüssel) wird als deutlich praktikablere Alternative zu lokalen Modellen eingeschätzt — schneller und qualitativ besser als 32B lokal.

---

## Thread 2: „Roo Code not using tools properly" (r/RooCode, Mai 2025) — GELÖST

**Ausgangssituation**: Ein Windows-Nutzer versucht Roo Code vollständig offline zu betreiben. Gemma 3 27B und Qwen 2.5 Coder 32B via Ollama + Open WebUI (OpenAI-Compatible API). Modelle antworten nur mit Text im Chat, schreiben keine Dateien.

**Fehlermeldung:**
```
[ERROR] You did not use a tool in your previous response! Please retry with a tool use.
```

**Lösung:**
> **Erstelle ein Custom Ollama-Modell mit größerem Kontextfenster via Modelfile.**

```bash
# Modelfile erstellen
cat > Modelfile << EOF
FROM gemma3:27b-it-q4_K_M
PARAMETER num_ctx 32768
EOF

# Neues Modell erstellen
ollama create mein-gemma-32k -f Modelfile
```

Nach dem Wechsel auf das Custom-Modell funktionierte die Tool-Nutzung sofort.

**Nachfrage zur Qualität:**
> Verglichen mit Claude 3.7 und Gemini 2.5 ist die Performance lokaler Modelle deutlich schlechter.

---

## Gemeinsame Muster aus beiden Threads

1. **Das Kontextfenster ist meistens der Schuldige** — Bevor man das Modell wechselt, zunächst `num_ctx` prüfen
2. **LM Studio ist instabiler als Ollama** für Roo Code
3. **Fine-tuned Tool-Use-Modelle** lösen das Problem zuverlässiger als allgemeine Coding-Modelle
4. **Offline-Setup** funktioniert grundsätzlich, erfordert aber sorgfältige Konfiguration
5. **Qualitätserwartung anpassen**: Lokale Modelle sind kein gleichwertiger Ersatz für Claude oder Gemini

---

## Verwandte Wiki-Seiten

- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — Das Problem und alle Lösungsoptionen
- [tool-use-lokale-modelle](../konzepte/tool-use-lokale-modelle.md) — Fine-tuned Modelle für Roo Code
- [roo-code](../werkzeuge/roo-code.md) — Roo Code im Detail
- [roocode-lokale-doku](roocode-lokale-doku.md) — Offizielle Einrichtungsanleitung
- [roocode-local-evaluation-olilanz](roocode-local-evaluation-olilanz.md) — Technische Tiefenanalyse

---

[Wiki-Index](../index.md)
