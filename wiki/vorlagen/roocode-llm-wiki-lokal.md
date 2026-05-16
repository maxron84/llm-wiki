---
date: 2026-05-16
type: vorlage
tags: [vorlage, lokale-modelle, setup, roo-code]
status: active
---

# Vorlage: LLM-Wiki mit Roo Code + lokalem Modell (qwen3:14b, RTX 5080)

**Zusammenfassung**: System-Prompt-Vorlage für den Betrieb eines LLM-Wikis mit Roo Code und einem lokalen 14B-Modell via Ollama. Auf die Session-Limits des 40K-Kontextfensters abgestimmt.
**Quellen**: Abgeleitet aus [llm-wiki-muster](../konzepte/llm-wiki-muster.md), [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md), [ingest-workflow](../konzepte/ingest-workflow.md), [roo-code](../werkzeuge/roo-code.md)
**Zuletzt aktualisiert**: 2026-05-16

---

## Zweck

Diese Vorlage ermöglicht einen abgespeckten LLM-Wiki-Betrieb mit Roo Code und einem lokalen Modell. Sie ist nicht die volle Erfahrung — sie ist das, was mit 40K Kontext realistisch funktioniert.

**Unterschied zur Cloud-Variante** (Claude Code + CLAUDE.md):

| | Cloud (Claude Code) | Lokal (Roo Code + qwen3:14b) |
|---|---|---|
| Kontextfenster | 200K | 40K (effektiv ~20–25K) |
| Ingest pro Session | Mehrere Quellen möglich | **Eine Quelle max.** |
| Neue Seiten pro Session | 10–15 | **3–5** |
| Lint-Läufe | ✅ | ❌ |
| Kosten | API-Gebühren | **Kostenlos (lokal)** |
| Datenschutz | Cloud | **Vollständig lokal** |

**Wann diese Vorlage sinnvoll ist**: Wenn Datenschutz oder Offline-Betrieb wichtiger sind als Vollständigkeit. Für einfache Queries und kompakte Einzelquellenaufnahmen gut geeignet. Für intensive Wiki-Pflege (Lint, viele Quellen, große Clippings) weiterhin Claude Code / Cloud empfohlen.

---

## Einrichtung in Roo Code

### Voraussetzungen

- Ollama läuft als Server (`http://<server>:11434`)
- Modell geladen: `qwen3:14b-40k` (Modelfile mit `PARAMETER num_ctx 40960`)
- Roo Code 3.54.0+

### Provider-Konfiguration in Roo Code

```
Provider:  OpenAI Compatible
Base URL:  http://<server>:11434/v1
Model ID:  qwen3:14b-40k
```

Nicht den nativen `ollama`-Provider verwenden — er liefert ein inkompatibles Tool-Calling-Format. → [roo-code](../werkzeuge/roo-code.md), [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md)

### Template einbinden

Die Vorlage unten als Projektdatei ablegen und in Roo Code als Custom Instructions einbinden:

1. Inhalt des Vorlagenblocks als `llm-wiki-rules.md` ins Projektverzeichnis kopieren
2. In Roo Code: Settings → Custom Instructions → Datei referenzieren  
   oder: Inhalt direkt in das Roo-Code-System-Prompt-Feld einfügen

**Wichtig**: Der Vorlageninhalt zählt zum Systemkontext und kostet bereits ~800–1.000 Tokens. Das reduziert das effektive Budget für Clippings und Gesprächsverlauf entsprechend.

---

## Token-Budget im Überblick

| Komponente | Tokens (ca.) |
|---|---|
| Roo Code eigener System-Prompt | 8–13k |
| Diese Vorlage | ~1k |
| Clipping (kleines Dokument) | 3–8k |
| Gesprächsverlauf (3–5 Runden) | 2–5k |
| **Verbleibendes Budget** | **~13–26k** |
| Praktische Nutzungsgrenze | ~20–25k gesamt |

→ [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) für die Herleitung dieser Werte.

---

## Benutzung

1. Vorlageninhalt unten in Roo Code einbinden (ohne den 4-Backtick-Fence)
2. `{{PROJEKTNAME}}` ersetzen
3. Projektstruktur (`wiki/`, `clippings/`) anlegen
4. Roo Code mit qwen3:14b-40k starten
5. Eine Quelle in `clippings/` legen → Roo Code anweisen, sie aufzunehmen
6. Bei längerer Session oder nächster Quelle: neue Session starten

---

## Vorlage

````markdown
# LLM-Wiki — {{PROJEKTNAME}}

## Modell und Grenzen

Dieses Projekt läuft mit qwen3:14b-40k via Ollama (40K Kontextfenster, ~20K effektiv nutzbar).

**Sessionsregeln — strikt einhalten:**
- Maximal eine Quelle pro Session
- Maximal 3–5 neue Wiki-Seiten pro Session
- Keine Lint-Läufe (Kontextlimit)
- Bei Gesprächsverlauf > 5 Runden: neue Session starten

## Ordnerstruktur

```
clippings/        -- Quelldokumente (niemals bearbeiten)
wiki/             -- Von Claude gepflegte Markdown-Seiten
wiki/index.md     -- Inhaltsverzeichnis
wiki/log.md       -- Änderungsprotokoll
wiki/konzepte/    -- Konzept- und Ideenseiten
wiki/quellen/     -- Quelldokumentationen
wiki/werkzeuge/   -- Tools und Technologien
wiki/personen/    -- Personen und Autoren
```

## Aufnahme-Workflow (eine Quelle pro Session)

1. Quelle vollständig lesen
2. Wichtigste Erkenntnisse kurz besprechen (1–2 Runden)
3. Zusammenfassungsseite in `wiki/quellen/` anlegen
4. Konzeptseiten erstellen oder aktualisieren — **maximal 3–5 Seiten** wegen Kontextlimit
5. `wiki/index.md` und `wiki/log.md` aktualisieren
6. Session beenden — nächste Quelle in neuer Session

## Seitenformat

```markdown
---
date: YYYY-MM-DD
type: konzept | quelle | person | werkzeug
tags: [type-tag, thema-tag]
status: active
---

# Titel

**Zusammenfassung**: Ein bis zwei Sätze.
**Quellen**: Quelldatei(en)
**Zuletzt aktualisiert**: YYYY-MM-DD

---

Inhalt mit klaren Überschriften.

## Verwandte Seiten

- [seitenname](../kategorie/seitenname.md)

---

[Wiki-Index](../index.md)
```

## Fragenbeantwortung

1. `wiki/index.md` lesen um relevante Seiten zu finden
2. **Nur eine oder zwei Seiten** lesen — nicht mehrere gleichzeitig (Kontextlimit)
3. Antwort mit Quellenangabe: `(Quelle: wiki/kategorie/seite.md)`
4. Wenn Antwort nicht im Wiki: klar sagen — nicht spekulieren

## Regeln

- `clippings/` niemals bearbeiten
- Immer `wiki/index.md` und `wiki/log.md` nach Änderungen aktualisieren
- Seitennamen: Kleinbuchstaben mit Bindestrichen (`beispiel-seite.md`)
- Standard-Markdown-Links `[text](../kategorie/seite.md)` — kein `[[wiki-link]]`
- Faktische Behauptungen mit Quelle belegen: `(Quelle: dateiname.md)`
- Bei Unsicherheit über Kategorisierung: fragen statt raten
````

---

## Verwandte Seiten

- [roo-code](../werkzeuge/roo-code.md) — Roo Code: bestätigte Konfiguration mit RTX 5080
- [ollama-kontextfenster](../konzepte/ollama-kontextfenster.md) — Kontextlimits, Latenzdegradation, Modelfile-Setup
- [ingest-workflow](../konzepte/ingest-workflow.md) — Vollständiger Ingest-Workflow (Cloud-Variante)
- [lokale-modelle](../anleitungen/lokale-modelle.md) — Einrichtung und Betrieb lokaler Modelle
- [claude-md-software](claude-md-software.md) — Schwester-Vorlage für Software-Projekte mit Claude Code
- [llm-wiki-muster](../konzepte/llm-wiki-muster.md) — Das übergeordnete Konzept

---

[Wiki-Index](../index.md)
