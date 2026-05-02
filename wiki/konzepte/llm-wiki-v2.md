---
date: 2026-05-02
type: konzept
tags: [konzept, produktion, v2]
status: active
---

# LLM Wiki v2

**Zusammenfassung**: LLM Wiki v2 ist eine Community-Erweiterung von Karpathys Grundmuster auf Basis von Produktionserfahrung. Vier Kernergänzungen: Lifecycle-Management mit Frischheits-Metadaten, typisierte Beziehungen (relationships.json), Automatisierungshooks und Trust-Score-basierte Qualitätskontrolle.
**Quellen**: clippings/LLM Wiki v2 Extending Karpathy's Pattern with Production Lessons.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Entstehungskontext

Zwei Wochen nach Karpathys Gist (April 2026) veröffentlichte die Community **LLM Wiki v2** — mit 985 Sternen und 143 Forks in 24 Stunden. Basis: Erfahrungen mit dem „agentmemory"-Projekt, einem persistenten Memory-Engine für KI-Coding-Agenten.

Das übergeordnete Argument: RAG war ein **Workaround für 8K-Kontextfenster**. Mit 1M-Token-Fenstern als neuer Norm schwächt sich die ökonomische Begründung für Retrieval gegenüber direkter Inclusion jeden Quartal. (Quelle: clippings/LLM Wiki v2 Extending Karpathy's Pattern with Production Lessons.md)

## Die vier Produktionserweiterungen

### 1. Lifecycle-Management: Frischheits-Metadaten

**Das Problem**: Ohne Verfallsmanagement sammelt ein Wiki veraltete Einträge, die aktuellem Code widersprechen. Die KI folgt dem Wiki — und produziert Bugs.

**Die Lösung**: Explizite Metadaten pro Eintrag:
- `last_verified` — wann zuletzt geprüft
- `verified_by` — von wem
- `against_version` — gegen welche Code-Version

Diese Felder erweitern das Standard-[YAML-Frontmatter](yaml-frontmatter.md) um eine zeitliche Dimension.

### 2. Typisierte Beziehungen: relationships.json

**Das Problem**: `[[doppelte Klammern]]` funktionieren bei 50 Einträgen. Bei 500 werden sie unwartbares Orphan-Link-Chaos.

**Die Lösung**: Typisierte Beziehungen aus dem Semantic Web:

| Typ | Bedeutung |
|---|---|
| `depends-on` | A braucht B zum Funktionieren |
| `supersedes` | A ersetzt B |
| `owned-by` | A gehört Verantwortlichem C |
| `implements` | A ist die Implementierung von Konzept B |

Die Kanten leben in `relationships.json` als flaches Array. Das Modell bekommt den strukturierten Graphen neben dem Markdown — und kann Graphtraversal betreiben statt nur Keyword-Matching.

```json
[
  {"from": "auth-service", "to": "user-entity", "type": "depends-on"},
  {"from": "oauth-v2", "to": "oauth-v1", "type": "supersedes"}
]
```

### 3. Automatisierungshooks

**Das Problem**: Niemand will das Wiki manuell pflegen — der häufigste Todesgrund für Wikis.

**Die Lösung**: Git-Pre-Commit-Hooks, die automatisch erkennen, welche Wiki-Einträge von einer Code-Änderung betroffen sind:

```bash
# Beispiel-Hook-Logik (vereinfacht)
changed_files=$(git diff --cached --name-only)
# LLM prüft: "Du hast auth-service.py geändert — Wiki-Eintrag aktualisieren?"
# LLM generiert Entwurf → Mensch bestätigt in Sekunden
```

Das reduziert die Wartungsaufwand auf reine Qualitätskontrolle statt Erstell-Arbeit. Verwandt: [lernschleifen](../quellen/llm-wiki-fulkerson-exo.md) (Fulkerson), [taeglicher-workflow](taeglicher-workflow.md)

### 4. Trust Score und Qualitätskontrolle

**Das Problem**: Qualitätserosion läuft lautlos — kein Fehler, kein Crash.

**Die Lösung**:
- **Periodische Audit-Prompts** — LLM findet Widersprüche im Wiki selbst
- **Trust Score pro Eintrag** — verfällt über Zeit ohne Verifikation; erzwingt regelmäßige Überprüfung
- Ähnlich zu Karpathys Lint-Konzept ([lint-pruefung](lint-pruefung.md)), aber automatisiert und quantifiziert

## v2-Dateistruktur

```
llm-wiki/
├── _meta/
│   ├── conventions.md       # Organisationsprinzipien
│   ├── ontology.md          # Typisierte Beziehungen
│   └── audit-log.md         # Prüfhistorie
├── entities/
│   ├── classes/
│   ├── modules/
│   ├── people/
│   └── decisions/
├── relationships.json       # Der Graph-Layer
└── README.md
```

## Was v2 noch nicht löst

- Mehrsprachige Wikis (aktuell englisch-only)
- Nicht-Code-Wissen (HR, Kundengeschichte)
- Multi-Agent-Schreib-Konflikte (kein Locking)
- Kostenanalyse bei 10M+ API-Aufrufen/Monat

## Abgrenzung zu Karpathys Original

| Aspekt | Karpathy v1 | v2-Ergänzung |
|---|---|---|
| Verlinkung | `[[freie Links]]` | Typisierte `relationships.json` |
| Aktualität | Manuelles Lint nach Bedarf | Trust Score + automatisches Decay |
| Wartung | Mensch kuratiert | Git-Hooks + LLM-Entwürfe |
| Metadaten | date, type, tags | + last_verified, against_version |

## Verwandte Seiten

- [llm-wiki-v2-tamiltech](../quellen/llm-wiki-v2-tamiltech.md) — Quellartikel
- [llm-wiki-karpathy](../quellen/llm-wiki-karpathy.md) — Grundmuster
- [yaml-frontmatter](yaml-frontmatter.md) — Metadatenstruktur
- [lint-pruefung](lint-pruefung.md) — Qualitätsprüfung
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md) — Hooks
- [enterprise-skalierung](enterprise-skalierung.md) — Nächste Skalierungsstufe

---

[Wiki-Index](../index.md)
