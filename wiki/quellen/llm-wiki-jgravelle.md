---
date: 2026-04-23
type: quelle
tags: [quelle]
status: active
---

# A Radical Diet for Karpathy's Token-Eating LLM Wiki (J. Gravelle)

**Zusammenfassung**: J. Gravelle beschreibt das Skalierungsproblem des LLM-Wikis (index.md als Flaschenhals bei ~50–100K Token) und stellt jDocMunch als Lösung vor — ein Tool für sektionsbasierte Dokumentenabfrage mit bis zu 95% Token-Einsparung.
**Quellen**: clippings/J. Gravelle — DEV Community Profile.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Über die Quelle

- **Autor**: J. Gravelle (DEV.to)
- **Erschienen**: 2026-04-12
- **Art**: Problem-Analyse + Tool-Vorstellung

## Kernaussagen

### Das Skalierungsproblem

Gravelle beschreibt präzise, was bei wachsendem Wiki passiert:

> "The LLM Wiki doesn't eliminate token cost. It moves it."

Der Trade: Per-Query-Retrieval-Kosten werden durch Per-Session-Kompilierungskosten ersetzt. Das ist ein guter Trade — bis das Wiki das Kontextfenster übersteigt.

Konkrete Zahlen:
- `~50K–100K Token` → index.md wird zum Flaschenhals
- `~200K–300K Token` → Qualitätsdegradierung beginnt (verpasste Links, schwächere Synthese)

Symptome: Die Abfragen werden langsamer, die Antworten weicher, das Modell übersieht Dinge, die definitiv im Wiki stehen. Ohne Fehlermeldung — es wird einfach schlechter.

### Die Lösung: jDocMunch

jDocMunch parst Dokumente in **Abschnitte** und speichert Byte-Offsets. Statt das gesamte Wiki zu laden, arbeitet es mit zwei Operationen:

1. `search_sections` — Index auf der Festplatte durchsuchen
2. `get_section` — exakten Abschnitt abrufen

**Benchmark** (7 Seiten, 7.449 Token Korpus, 5 Abfragen):

| Vergleich | Baseline | jDocMunch | Einsparung | Verhältnis |
|---|---|---|---|---|
| Vollständiges Wiki | 37.245 | 1.874 | 95,0% | 19,9× |
| Einzelne Datei | 5.347 | 1.874 | 65,0% | 2,9× |

Das Prinzip: Kein Komprimieren von Daten — sondern das Laden irrelevanter Daten vermeiden.

### Das mentale Modell

> "Your wiki is not a document. It's a database. Stop loading it. Start querying it."

Das bisherige Muster `"Lies index.md"` ersetzen durch: `search_sections` → `get_section`. Keine andere Verhaltensänderung nötig.

### Installation (3 Minuten)

```
pip install jdocmunch-mcp
```

MCP-Server läuft mit `uvx jdocmunch-mcp`.

## Bezug zum bestehenden Wiki

Diese Quelle führt das [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md)-Problem ein und liefert mit [jdocmunch](../werkzeuge/jdocmunch.md) die erste spezifische Lösung dafür. Ergänzt die Diskussion über [rag-vs-wiki](../konzepte/rag-vs-wiki.md) um eine praktische Einschränkung des Wiki-Ansatzes.

## Verwandte Seiten

- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md)
- [jdocmunch](../werkzeuge/jdocmunch.md)
- [rag-vs-wiki](../konzepte/rag-vs-wiki.md)
- [query-workflow](../konzepte/query-workflow.md)

---

[Wiki-Index](../index.md)
