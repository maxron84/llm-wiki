---
date: 2026-05-02
type: quelle
tags: [quelle, produktion, v2]
status: active
---

# LLM Wiki v2: Extending Karpathy's Pattern with Production Lessons

**Zusammenfassung**: Tamiltech dokumentiert LLM Wiki v2 — die Community-Erweiterung von Karpathys Muster mit Produktionserfahrung: Lifecycle-Management, typisierte Beziehungen, Automatisierungshooks und Qualitätskontrolle.
**Quellen**: clippings/LLM Wiki v2 Extending Karpathy's Pattern with Production Lessons.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Kontext

Karpathys Gist: 16 Millionen Views in drei Wochen (April 2026). Zwei Wochen später erschien **LLM Wiki v2** — eine Community-Erweiterung auf Basis von Produktionserfahrung mit dem „agentmemory"-Projekt. Innerhalb von 24 Stunden: 985 Sterne, 143 Forks. (Quelle: clippings/LLM Wiki v2 Extending Karpathy's Pattern with Production Lessons.md)

Das zentrale Argument: RAG war ein **2023-Workaround für 8K-Kontextfenster**. Mit 1M-Token-Fenstern als Standard (DeepSeek V4, GPT-5.5, Qwen 3.6) schwächt sich die ökonomische Begründung für Retrieval gegenüber Inclusion jeden Quartal weiter ab.

## Vier Produktionsprobleme

### 1. Wikis verfaulen — schnell

Ohne Lifecycle-Management sammelt ein Wiki veraltete Einträge, die aktuellem Code widersprechen. Die KI, agreeable wie sie ist, folgt dem Wiki. Bugs entstehen.

**v2-Lösung**: Explizite Frischheits-Metadaten pro Eintrag — wann zuletzt verifiziert, von wem, gegen welche Code-Version.

### 2. Cross-Links werden Müll

Karpathys `[[doppelte Klammern]]` funktionieren bei 50 Einträgen. Bei 500 werden sie unwartbares Orphan-Link-Chaos.

**v2-Lösung**: **Typisierte Beziehungen** — aus dem Semantic Web und Ontologie-Engineering geborgt:
- `depends-on` — Abhängigkeit
- `supersedes` — ersetzt
- `owned-by` — Verantwortlichkeit
- `implements` — Implementierung

Das Modell kann dann Graphtraversal betreiben statt nur Keyword-Matching.

### 3. Menschen hören auf, es zu aktualisieren

Der wichtigste Grund für Wiki-Tod: niemand will es pflegen.

**v2-Lösung**: Automatisierungshooks — Git-Pre-Commit-Hooks, die fragen: „Du hast diese Klasse geändert — Wiki-Eintrag aktualisieren?" Mit LLM-generiertem Entwurf, den der Mensch nur bestätigen muss.

### 4. Qualität erodiert lautlos

Widersprüche sammeln sich ohne Bemerken.

**v2-Lösung**: Qualitätskontrolle durch periodische Audit-Prompts + **Trust Score** pro Eintrag, der über Zeit verfällt wenn keine Verifikation stattfindet.

## Die v2-Dateistruktur

```
llm-wiki/
├── _meta/
│   ├── conventions.md       # wie dieses Wiki organisiert ist
│   ├── ontology.md          # die typisierten Beziehungen
│   └── audit-log.md         # was wann geprüft wurde
├── entities/
│   ├── classes/
│   ├── modules/
│   ├── people/
│   └── decisions/
├── relationships.json       # die Graph-Schicht
└── README.md                # erster Einstiegspunkt
```

Die Schlüsselinnovation: `relationships.json` — ein flaches JSON-Array typisierter Kanten. Das Modell bekommt den strukturierten Graphen neben dem Markdown-Inhalt und kann über „was hängt wovon ab" nachdenken, ohne Textmuster zu erraten.

## Bekannte Grenzen von v2

v2 löst noch nicht:
- **Mehrsprachige Wikis** — aktuell English-only; polyglotte Codebasen brauchen Übersetzungsschicht
- **Nicht-Code-Wissen** — gut für Code; weniger klar für Organisationswissen (HR, Kundengeschichte)
- **Multi-Agent-Konflikte** — wenn 3 Agenten gleichzeitig das Wiki aktualisieren, fehlt Locking/Conflict Resolution
- **Kostenrechnung bei Scale** — 50 KB Wiki-Kontext bei 10M Aufrufen/Monat — keine Preisanalyse in v2

## Relevanz

v2 adressiert die wichtigsten Produktionsschwächen von Karpathys Originalkonzept. Besonders wertvoll für Teams, die das Muster in Produktionssystemen einsetzen statt als persönliches Wissenswerkzeug.

## Verwandte Seiten

- [llm-wiki-karpathy](llm-wiki-karpathy.md) — Das Grundmuster
- [llm-wiki-v2](../konzepte/llm-wiki-v2.md) — v2-Erweiterungen im Detail
- [fortgeschrittene-architektur](../konzepte/fortgeschrittene-architektur.md) — Hooks, Automation
- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md)
- [yaml-frontmatter](../konzepte/yaml-frontmatter.md) — Frischheits-Metadaten

---

[Wiki-Index](../index.md)
