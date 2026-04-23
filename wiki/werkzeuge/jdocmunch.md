---
date: 2026-04-23
type: werkzeug
tags: [werkzeug]
status: active
---

# jDocMunch

**Zusammenfassung**: jDocMunch ist ein MCP-Server, der Dokumente in Abschnitte mit Byte-Offsets parst und sektionsbasierte Abfragen ermöglicht — ohne das gesamte Dokument laden zu müssen. Benchmark: ~95% Token-Reduktion vs. vollständiges Wiki laden.
**Quellen**: clippings/J. Gravelle — DEV Community Profile.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Was ist jDocMunch?

jDocMunch löst das [[skalierungsgrenzen|Skalierungsproblem]] des LLM-Wikis. Wenn das Wiki über ~50–100K Token wächst, wird das Laden des gesamten Wikis (oder auch nur der index.md) prohibitiv teuer. jDocMunch implementiert einen anderen Zugriffsmodus:

1. Dokument in **Abschnitte** parsen (mit Byte-Offsets für effizienten Direktzugriff)
2. Index **auf der Festplatte** durchsuchen statt im Prompt
3. **Exakten Abschnitt** abrufen statt gesamte Datei

Die zwei Kernoperationen:
- `search_sections` — Semantische oder Keyword-Suche über den Abschnittsindex
- `get_section` — Exakten Abschnitt per Byte-Offset abrufen

## Benchmarks

**Setup**: 7 Wiki-Seiten, 7.449 Token Gesamtkorpus, 5 Abfragen

| Vergleich | Basis | jDocMunch | Einsparung | Verhältnis |
|---|---|---|---|---|
| Vollständiges Wiki | 37.245 | 1.874 | **95,0%** | 19,9× |
| Einzelne Datei | 5.347 | 1.874 | **65,0%** | 2,9× |

(Quelle: clippings/J. Gravelle — DEV Community Profile.md)

Selbst im "fairen" Vergleich (nur die relevante Datei vs. jDocMunch) gewinnt jDocMunch mit 65% Einsparung.

## Das Prinzip

> "You're not compressing data. You're avoiding loading irrelevant data."

Das ist der gesamte Vorteil. jDocMunch ändert nicht die Datenqualität — es ändert den Zugriffsmodus: von "Dokument laden" zu "Datenbank abfragen".

## Installation

```bash
pip install jdocmunch-mcp
```

MCP-Konfiguration:
```json
{
  "mcpServers": {
    "jdocmunch": {
      "command": "uvx",
      "args": ["jdocmunch-mcp"]
    }
  }
}
```

## Workflow-Änderung

**Bisheriges Muster** (nicht skalierbar):
```
"Lies index.md" → navigate im Prompt → Seite laden
```

**Neues Muster** (skaliert mit Antwort-Komplexität):
```
search_sections → get_section
```

Keine andere Verhaltensänderung nötig.

## Wann einsetzen

jDocMunch wird relevant, wenn:
- index.md mehr als ~500 Einträge hat
- Abfragen langsamer werden oder Antworten nachlassen
- Das Wiki über ~50K Token wächst

Für kleine Wikis (< 50 Seiten) ist direktes Laden einfacher und ausreichend.

## Verwandte Seiten

- [[skalierungsgrenzen]]
- [[qmd]]
- [[fortgeschrittene-architektur]]
- [[query-workflow]]
- [[llm-wiki-jgravelle]]

---

[Wiki-Index](../index.md)
