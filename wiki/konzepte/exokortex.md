---
date: 2026-05-02
type: konzept
tags: [konzept, produktion]
status: active
---

# Exokortex

**Zusammenfassung**: Ein Exokortex ist eine externe kognitive Erweiterungsschicht — ein System, das wie ein zweites Gehirn außerhalb des eigenen Kopfes arbeitet. Aaron Fulkersons „Exo" ist die bisher ausgefeilteste Implementierung des LLM-Wiki-Musters als vollwertiger Exokortex.
**Quellen**: clippings/Karpathy's Pattern for an "LLM Wiki" in Production.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Begriff

Der Begriff „Exokortex" (von lat. *cortex* = Rinde, Großhirnrinde) bezeichnet eine externe Erweiterung des eigenen Denkens und Gedächtnisses. Vannevar Bushs Memex (1945) war die ursprüngliche Vision — ein mechanisches System zur Gedankenverlängerung. (Mehr: [memex](memex.md))

Aaron Fulkerson benannte sein System „Exo", nachdem er das LLM in einer spätnächtlichen Session fragte, was es gerade werde: „The name came from the system itself." (Quelle: clippings/Karpathy's Pattern for an "LLM Wiki" in Production.md)

## Was ein Exokortex vom LLM-Wiki unterscheidet

Das [llm-wiki-muster](llm-wiki-muster.md) ist ein Werkzeug: Man bringt Quellen, stellt Fragen, bekommt Antworten. Ein Exokortex ist ein **Betriebssystem für das eigene Denken und Arbeiten**:

| | LLM-Wiki | Exokortex |
|---|---|---|
| **Quellen** | Manuell kuratierte Dokumente | Live-Datenfeeds (Kalender, E-Mail, CRM, Biometrie) |
| **Operationen** | Ingest, Query, Lint (manuell) | 26+ Skills mit natürlichsprachlichen Triggern |
| **Wartung** | Periodisch, explizit | Auto-Enrichment als Seiteneffekt jedes Workflows |
| **Lernmechanismus** | Wissensbasis wächst | Wissensbasis + Verhaltensregeln wachsen (Graduation) |
| **Durchsetzung** | CLAUDE.md-Anweisungen | Hook-Skripte (deterministisch) |

## Fulkersons Exo-System

Nach zwei Monaten Produktionsbetrieb (ab Februar 2026):
- **26 Skills** — kodierte Workflows mit natürlichsprachlichen Triggern
- **14 MCP-Server** — davon 7 selbst gebaut; Gmail, Slack, HubSpot, Jira, WHOOP-Biometrie, iMessage, Granola, Google Drive, Browser-Automation
- **8 Hooks** — E-Mail-Safety-Gate, TIL-Capture bei jedem Commit, MCP-Audit-Log, mobile Genehmigungen
- **Obsidian Vault** — hunderte Dateien: People Files, Account Files, Entscheidungslog, Competitive Intel, Gesundheitsdaten

## Das Dual-Identity-Prinzip

Fulkerson trennt persönliche und Arbeits-Layer bewusst:
- **Persönlich** (läuft lokal, privat): Gesundheits-Tracking, iMessage-Beziehungsanalyse, psychologisches Profiling eigener Kommunikationsmuster
- **Arbeit** (paketiert, verteilbar): Meeting-Prep, Memos, PM-Methodik, CRM-Analytics — an Teammitglieder als eigenständiges Paket verteilbar

## Der Kompilierungseffekt im Exokortex

> „Knowledge that compounds is a different kind of advantage. It's patient. It's quiet. And it gets wider every day." — Aaron Fulkerson

Der Exokortex realisiert die [kompilierungs-metapher](kompilierungs-metapher.md) vollständig: Kein separater Pflegeaufwand — Wissen wächst als Nebeneffekt des normalen Arbeitens. Meeting-Brief liest People File → Debrief aktualisiert People File. Das System lernt aus jeder Aktivität.

## Verwandte Seiten

- [llm-wiki-fulkerson-exo](../quellen/llm-wiki-fulkerson-exo.md) — Quellartikel
- [lernschleifen](lernschleifen.md) — Die drei formalen Lernschleifen
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md) — Hooks, MCP, technische Details
- [memex](memex.md) — Geistiger Vorläufer
- [taeglicher-workflow](taeglicher-workflow.md) — Einstiegspunkt für eigene Exokortex-Praxis
- [aaron-fulkerson](../personen/aaron-fulkerson.md)

---

[Wiki-Index](../index.md)
