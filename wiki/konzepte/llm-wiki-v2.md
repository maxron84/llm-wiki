---
date: 2026-05-02
type: konzept
tags: [konzept, produktion, v2]
status: active
---

# LLM Wiki v2

**Zusammenfassung**: LLM Wiki v2 ist eine Community-Erweiterung von Karpathys Grundmuster auf Basis von Produktionserfahrung. Vier Kernergänzungen: Lifecycle-Management mit Frischheits-Metadaten, typisierte Beziehungen (relationships.json), Automatisierungshooks und Trust-Score-basierte Qualitätskontrolle.
**Quellen**: clippings/LLM Wiki v2 Extending Karpathy's Pattern with Production Lessons.md, raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

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

## Was eine Datenbank aus v2 machen würde

Drei der vier v2-Ergänzungen sind im Kern Datenbankkonzepte, die als Dateien nachgebaut wurden. Der [SQL-Entwurf](wiki-datenbankschema.md) benennt das ausdrücklich: (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

| v2-Ergänzung | Als SQL |
|---|---|
| `relationships.json` (typisierte Beziehungen) | Eine Kantentabelle `links (from_page, to_page, rel)` — „nur als das, was sie ohnehin ist" |
| Frischheits-Metadaten (Lifecycle) | `last_verified`, `against_version` — aber **pro Behauptung**, nicht pro Seite |
| Trust Score mit Verfallslogik | `confidence` als CHECK-Spalte plus eine Query auf `last_verified` |
| Automatisierungshooks | Trigger und Transaktions-Nebeneffekte |

Der Unterschied zur JSON-Datei ist dabei nicht kosmetisch: `ON DELETE RESTRICT` auf `to_page` bedeutet, dass eine verlinkte Seite nicht gelöscht werden kann. Tote Links werden nicht gefunden, sie entstehen nicht. Und die Kante `contradicts` kommt als fünfter Beziehungstyp dazu — Widersprüche werden erfasste Daten statt eines Absatzes im Fließtext.

Die Präzisierung bei den Frischheits-Metadaten ist die interessanteste: Auf Seitenebene sind sie zu grob, um nützlich zu sein — der Abschnitt „VRAM-Bedarf" veraltet, während „Die Grundidee" auf derselben Seite gültig bleibt. → [sektion-als-atom](sektion-als-atom.md), [kontaminierungsrisiko](kontaminierungsrisiko.md)

## Verwandte Seiten

- [llm-wiki-muster](llm-wiki-muster.md) — Das Grundmuster, das v2 erweitert
- [wiki-datenbankschema](wiki-datenbankschema.md) — v2-Konzepte als Tabellen
- [sql-wiki-architektur](sql-wiki-architektur.md) — Der Rahmen
- [sektion-als-atom](sektion-als-atom.md) — Frischheit auf der richtigen Granularität
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Quelle des SQL-Vergleichs
- [llm-wiki-v2-tamiltech](../quellen/llm-wiki-v2-tamiltech.md) — Quellartikel
- [kontextrahmen-5w1h](kontextrahmen-5w1h.md) — Konkurrierende Musterweiterung: Graph über gemeinsame Rahmenfelder statt typisierter Kanten
- [llm-wiki-karpathy](../quellen/llm-wiki-karpathy.md) — Grundmuster
- [yaml-frontmatter](yaml-frontmatter.md) — Metadatenstruktur
- [lint-pruefung](lint-pruefung.md) — Qualitätsprüfung
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md) — Hooks
- [enterprise-skalierung](enterprise-skalierung.md) — Nächste Skalierungsstufe

---

[Wiki-Index](../index.md)
