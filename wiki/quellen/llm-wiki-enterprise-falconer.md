---
date: 2026-05-02
type: quelle
tags: [quelle, enterprise, skalierung]
status: active
---

# The Enterprise LLM Wiki: Scaling Karpathy's Pattern to Your Org

**Zusammenfassung**: Falconer analysiert, warum Karpathys persönliches LLM-Wiki-Muster nicht direkt auf Unternehmensebene skaliert, und beschreibt vier notwendige Anpassungen: kontinuierliche Aufnahme aus Tools, cross-tool Entity Resolution, automatische Drift-Erkennung und eigentumsbasiertes Routing.
**Quellen**: clippings/The enterprise LLM wiki scaling Karpathy's pattern to your org (April 2026).md
**Zuletzt aktualisiert**: 2026-05-02

---

## Kernaussage

Karpathys Muster funktioniert hervorragend — für **eine motivierte Person**. Bei Unternehmen scheitern dieselben vier Eigenschaften, die das persönliche Wiki stark machen, an den strukturellen Unterschieden der Organisation. (Quelle: clippings/The enterprise LLM wiki scaling Karpathy's pattern to your org (April 2026).md)

## Die vier Eigenschaften — persönlich vs. Enterprise

| Eigenschaft | Karpathys Wiki (persönlich) | Enterprise LLM Wiki |
|---|---|---|
| **Capture** | Manuell kuratierter `raw/`-Ordner | Kontinuierliche Aufnahme aus GitHub, Slack, Linear, Granola, Google Drive, Notion |
| **Link** | Obsidian bidirektionale Links | Cross-tool Entity Resolution in Wissensgraph |
| **Compound** | LLM kompiliert neue Quellen in bestehende Seiten | System erkennt Drift, entwirft Updates, routet zu Verantwortlichen |
| **Stay Current** | Karpathy führt periodische Health Checks durch | Kontinuierliche Drift-Erkennung im Hintergrund |
| **Schema** | CLAUDE.md-Datei im Vault | SSOT-Designierungen und Ownership-Metadaten, systemseitig erzwungen |

## Warum es bricht

### Capture: Der `raw/`-Ordner existiert nicht

Ein Unternehmen hat keinen äquivalenten Ordner — strukturell, nicht als Disziplin-Fehler. Organisationswissen liegt verteilt auf GitHub-PRs, Slack-Threads, Linear-Tickets, Granola-Meeting-Notizen, Google-Drive-Docs, Incident-Retrospektiven, Zendesk-Kundenkonversationen. Die Quellen **sind** die Arbeit.

### Link: Bidirektionale Verbindungen brauchen cross-tool-Semantik

Obsidians Backlinks funktionieren innerhalb eines Vaults. Ein Enterprise-Wissensgraph muss tool-übergreifend verlinken: Slack-Entscheidung → PR der Implementierung → Linear-Ticket → Granola-Transkript → Notion-Dokumentation. Diese Links existieren nicht standardmäßig und überleben Tool-Wechsel nicht.

### Compound: Veralteter Kontext akkumuliert nicht — er fault

StackOverflow Developer Survey 2024 (65.000 Entwickler): Mehr als 60% verbringen täglich 30+ Minuten mit der Suche nach Lösungen; 68% stoßen mindestens einmal pro Woche auf ein Knowledge Silo. Jedes neue Dokument stapelt sich auf den Haufen statt in den Graphen — weil niemand die Integrationsarbeit macht.

### Stay Current: Health Checks müssen automatisch laufen

Karpathys manuelle Health Checks skalieren nicht. Der Autor des Runbooks ist auf einem anderen Projekt; der Senior Engineer, der den Kontext kannte, ist weg; das System wurde zweimal neu geschrieben. Veraltete Runbooks werden von Coding Agents gelesen und produzieren veraltete Ergebnisse.

> [Anthropic's engineering team describes context as the scarcest resource for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

## Warum Retrieval-Tools nicht ausreichen

Glean, Notion AI, Confluence Search sind **Retrieval-Tools** — sie erleichtern das Finden von bereits Erfasstem. Besseres Retrieval über veraltete Wissensbasis liefert confident-formulierte falsche Antworten, schneller als schlechtes Retrieval.

Was Karpathys Muster einzigartig macht: **die Wartungsschleife**, nicht die Suchschicht. Der Graph bleibt akkurat, weil der LLM kontinuierlich lint, entwirft und reconciliert.

## YC Spring 2026 RFS

Y Combinator Spring 2026 Requests for Startups benennt das fehlende Primitive direkt:

> "If we want every company to run on AI automation, we need a new primitive: a company brain. A system that pulls knowledge out of all these fragmented sources, structures it, keeps it current, and turns it into an executable skills file for AI."

## Verwandte Seiten

- [enterprise-skalierung](../konzepte/enterprise-skalierung.md) — Konzeptseite zum Enterprise-Ansatz
- [llm-wiki-karpathy](llm-wiki-karpathy.md) — Das Grundmuster
- [skalierungsgrenzen](../konzepte/skalierungsgrenzen.md) — Technische Skalierungsgrenzen
- [fortgeschrittene-architektur](../konzepte/fortgeschrittene-architektur.md) — MCP-Integration
- [lint-pruefung](../konzepte/lint-pruefung.md)

---

[Wiki-Index](../index.md)
