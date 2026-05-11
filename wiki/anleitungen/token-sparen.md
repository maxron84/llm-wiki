---
date: 2026-05-11
type: konzept
tags: [konzept, werkzeug]
status: active
---

# Token sparen mit Claude Code — ein Leitfaden

**Zusammenfassung**: Praktische Strategien zur Reduktion von Token-Verbrauch beim Betrieb eines LLM-Wikis mit Claude Code — von einfachen Sofortmaßnahmen bis zur fortgeschrittenen Architektur.
**Quellen**: wiki/konzepte/skalierungsgrenzen.md, wiki/konzepte/fortgeschrittene-architektur.md, wiki/werkzeuge/jdocmunch.md, wiki/werkzeuge/qmd.md, wiki/konzepte/ingest-workflow.md, wiki/konzepte/lernschleifen.md
**Zuletzt aktualisiert**: 2026-05-11

---

## Das Grundproblem

> "The LLM Wiki doesn't eliminate token cost. It moves it." — J. Gravelle

Das LLM-Wiki-Muster tauscht RAG-Kosten (pro Abfrage) gegen Sitzungskosten (Wiki in den Kontext laden). Dieser Tausch lohnt sich — *solange das Wiki ins Kontextfenster passt*. Wächst es darüber hinaus, entsteht dasselbe Retrieval-Problem, das man eigentlich vermeiden wollte.

Die Strategien in diesem Leitfaden sind nach Aufwand gestaffelt:

| Stufe | Aufwand | Einsparung |
|---|---|---|
| 1 — Sofortmaßnahmen | Null bis minimal | 20–50% |
| 2 — Werkzeuge einsetzen | Einmalige Einrichtung | 65–95% |
| 3 — Fortgeschrittene Architektur | Systemumbau | 90%+ |
| 4 — Systemebene | Laufender Prozess | akkumulierend |

---

## Stufe 1 — Sofortmaßnahmen (kein Aufwand)

### 1.1 Tiefe bewusst wählen beim Ingest

Beim Aufnehmen einer neuen Quelle bestimmt die Tiefenwahl, wie viele Wiki-Seiten entstehen — und damit, wie viele Token zukünftige Sitzungen kosten werden.

- **Minimal**: Nur eine Zusammenfassungsseite → wenige neue Token dauerhaft
- **Mittel** *(Standard)*: Zusammenfassung + Konzeptseiten für Hauptideen
- **Vollständig**: Alles inkl. einzelner Entitäten → kostet langfristig am meisten

Faustregel: Vollständig nur für Quellen, die man oft und gezielt abfragen wird. Für Hintergrundmaterial reicht Minimal.

### 1.2 Gezielte Fragen statt Wiki-Dump

Statt Claude am Anfang einer Sitzung die gesamte `index.md` lesen zu lassen, direkt eine Frage stellen. Claude navigiert dann nur die relevanten Seiten — nicht das gesamte Wiki.

**Teuer:**
```
"Lies index.md und gib mir eine Übersicht."
```

**Günstig:**
```
"Was sagt das Wiki über Skalierungsgrenzen?"
```

### 1.3 CLAUDE.md schlank halten

Die CLAUDE.md wird bei *jeder* Sitzung in den Kontext geladen. Eine aufgeblähte CLAUDE.md kostet bei jeder Interaktion Token. Empfehlung: operative Regeln als knappe Prosa, keine langen Beispielblöcke. Vorlagen-Inhalte gehören ins Wiki (`wiki/vorlagen/`), nicht in die CLAUDE.md.

Mehr: [claude-md-design](../konzepte/claude-md-design.md)

---

## Stufe 2 — Werkzeuge einsetzen (einmalige Einrichtung)

### 2.1 jDocMunch — 95% Token-Reduktion durch sektionsbasierten Zugriff

Der wichtigste Einzelschritt für wachsende Wikis. statt das Wiki als Dokument zu laden, wird es als Datenbank abgefragt.

**Benchmark** (7 Seiten, 5 Abfragen):

| Methode | Token | Einsparung |
|---|---|---|
| Vollständiges Wiki laden | 37.245 | — |
| Einzelne Datei laden | 5.347 | 65% |
| **jDocMunch** | **1.874** | **95%** |

Das Prinzip: jDocMunch parst Dokumente in Abschnitte mit Byte-Offsets. Claude ruft nur den exakten Abschnitt ab, nicht die ganze Datei.

```bash
pip install jdocmunch-mcp
```

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

Workflow-Änderung:
```
"Lies index.md" → ERSETZEN → search_sections → get_section
```

Mehr: [jdocmunch](../werkzeuge/jdocmunch.md)

**Wann einsetzen**: Ab ~50 Seiten oder wenn index.md > 50K Token.

### 2.2 qmd — Semantische Suche statt Volllade

qmd findet die relevanten Wiki-Seiten, ohne dass Claude die gesamte index.md lesen muss. Drei Suchmodi: BM25 (Keyword), Vektorsuche (semantisch), Hybrid mit LLM-Reranking.

```bash
npm install -g @tobilu/qmd
qmd collection add ./wiki --name mein-wiki
qmd mcp  # Als MCP-Server für Claude Code
```

**Kombination mit jDocMunch**: qmd findet die relevante Seite → jDocMunch lädt nur den relevanten Abschnitt. Zusammen adressieren sie das Skalierungsproblem vollständiger als jedes Tool allein.

Mehr: [qmd](../werkzeuge/qmd.md)

**Wann einsetzen**: Ab ~100 Seiten.

---

## Stufe 3 — Fortgeschrittene Architektur

### 3.1 Routing-Schritt beim Ingest

Naiver Ingest einer neuen Quelle würde theoretisch alle Wiki-Seiten berühren — prohibitiv teuer bei großen Wikis.

Mit einem Routing-Schritt:

1. Claude liest eine **Schema-Zusammenfassung** (1 Zeile pro Seite: `slug: titel — beschreibung`)
2. Claude gibt JSON-Array der relevanten Slugs zurück
3. Nur relevante Seiten werden synthetisiert

Das hält Ingest-Kosten linear, nicht quadratisch mit der Wiki-Größe.

### 3.2 Prompt Caching — 90% Kostenreduktion bei Wiederholungen

Wenn derselbe System-Prompt (CLAUDE.md + Routing-Instruktion + Index) über mehrere Operationen gleichartig aufgebaut wird, trifft er den Anthropic-Cache. Effekt: **~90% Kostenreduktion** bei Ingest und Abfrage-Sitzungen, die ähnliche Kontexte teilen.

Besonders wirksam bei:
- Mehrfachen Ingest-Operationen in einer Sitzung
- Regelmäßigen Lint-Läufen
- Workflows mit Standard-Schema

Mehr: [fortgeschrittene-architektur](../konzepte/fortgeschrittene-architektur.md)

### 3.3 Ephemere Mini-Wissensbases für Spezialaufgaben

Für eng begrenzte Aufgaben (z.B. "Schreib einen Artikel über X") eine temporäre, fokussierte Mini-Wissensbasis erstellen — und sie nach Abschluss wieder auflösen. Statt das gesamte Wiki zu laden, lädt Claude nur die 3–5 relevanten Seiten für diese Aufgabe.

Maximale Relevanz bei minimalem Token-Overhead. Idee von Lex Fridman.

---

## Stufe 4 — Systemebene (akkumulierender Effekt)

### 4.1 Lernschleifen und Graduation

Der teuerste Sitzungsanteil ist oft die Kontextrekonstruktion: Claude muss bei jeder Sitzung verstehen, wie das Projekt funktioniert. Diesen Overhead kann man dauerhaft reduzieren, indem bewährte Muster in CLAUDE.md-Regeln überführt werden.

Aaron Fulkersons Minimal-Implementierung:

1. **Täglich**: Eine kurze Beobachtung am Sitzungsende in `observations/YYYY-MM-DD.md`
2. **Wöchentlich**: Review-Prompt — „Welche Muster wiederholen sich? Was gehört in die CLAUDE.md?"
3. **Graduation**: Validierte Muster als knappe Regel in die CLAUDE.md

Ergebnis: Mit der Zeit braucht Claude weniger Token, um zu verstehen, was gemeint ist — weil die CLAUDE.md diese Konventionen direkt kodiert.

Mehr: [lernschleifen](../konzepte/lernschleifen.md)

### 4.2 Täglichen Workflow strukturieren

Ohne Struktur wird jede Sitzungseröffnung teuer: Man erklärt den Kontext neu, rekapituliert offene Aufgaben, erläutert den Stand. Ein strukturierter Workflow halbiert diese Kosten.

Zwei Rituale:

**Morgen** — Claude liest Tagesnotiz, gestrige offene Aufgaben, langfristige Ziele. In 20 Sekunden ist der Kontext hergestellt.

**Abend** — Claude stellt fünf kurze Fragen, schreibt die Antworten in die Tagesnotiz. Der nächste Morgen startet mit vollständigem Kontext, ohne Neuaufbau.

Mehr: [taeglicher-workflow](../konzepte/taeglicher-workflow.md)

---

## Schwellenwerte — wann welche Maßnahme?

| Wiki-Größe | Empfohlene Strategie |
|---|---|
| < 50 Seiten | Direktes Laden; CLAUDE.md schlank halten; Tiefe wählen |
| 50–100 Seiten | + jDocMunch einrichten |
| 100–500 Seiten | + qmd als MCP-Server; Routing-Schritt beim Ingest |
| > 500 Seiten | + RAG über kompilierte Wiki-Seiten; Prompt Caching optimieren |

(Quelle: [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md))

---

## Zusammenfassung

Token-Kosten beim LLM-Wiki entstehen an drei Stellen: beim Laden des Wikis, beim Ingest neuer Quellen, und beim Rekonstruieren von Sitzungskontext. Die wirksamsten Hebel:

1. **Sofort wirksam**: Tiefe beim Ingest bewusst wählen; gezielte Fragen statt Wiki-Dump; CLAUDE.md schlank halten
2. **95% Einsparung**: jDocMunch für sektionsbasierten Zugriff
3. **Navigation**: qmd für semantische Suche bei 100+ Seiten
4. **Sitzungskosten**: Strukturierter Tages-Workflow; Lernschleifen mit Graduation
5. **Ingest-Kosten**: Routing-Schritt; Prompt Caching

Der entscheidende Perspektivwechsel kommt von J. Gravelle:

> "Your wiki is not a document. It's a database. Stop loading it. Start querying it."

## Verwandte Seiten

- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md)
- [fortgeschrittene-architektur](../konzepte/fortgeschrittene-architektur.md)
- [jdocmunch](../werkzeuge/jdocmunch.md)
- [qmd](../werkzeuge/qmd.md)
- [lernschleifen](../konzepte/lernschleifen.md)
- [taeglicher-workflow](../konzepte/taeglicher-workflow.md)
- [claude-md-design](../konzepte/claude-md-design.md)
- [ingest-workflow](../konzepte/ingest-workflow.md)

---

[Wiki-Index](../index.md)
