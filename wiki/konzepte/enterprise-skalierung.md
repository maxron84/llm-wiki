---
date: 2026-05-02
type: konzept
tags: [konzept, enterprise, skalierung]
status: active
---

# Enterprise-Skalierung des LLM-Wikis

**Zusammenfassung**: Karpathys persönliches LLM-Wiki-Muster skaliert nicht direkt auf Unternehmensebene. Die vier tragenden Eigenschaften (Capture, Link, Compound, Stay Current) bleiben dieselben — aber jede braucht eine andere Implementierung, weil die Wartungsstruktur fundamental verschieden ist.
**Quellen**: clippings/The enterprise LLM wiki scaling Karpathy's pattern to your org (April 2026).md
**Zuletzt aktualisiert**: 2026-05-02

---

## Warum das persönliche Muster bricht

Das persönliche LLM-Wiki funktioniert, weil **eine motivierte Person** den Vault kuratiert und Wartungsschleifen manuell auslöst. Unternehmen haben keine äquivalente Einzelperson — der Versuch, eine zu bestimmen, reproduziert das Wiki-Problem, das gelöst werden soll. (Quelle: clippings/The enterprise LLM wiki scaling Karpathy's pattern to your org (April 2026).md)

Confluence-Friedhöfe, Notion-Deployments die niemand aktualisiert, vierzehn Slack-Threads aus Q3 die eine Frage lösten die alle weiter stellen — das ist die Enterprise-Realität.

## Die vier Eigenschaften und ihre Enterprise-Implementierung

### Capture: Kontinuierliche Tool-Integration statt `raw/`-Ordner

Persönlich: Karpathy befüllt `raw/` manuell mit kuratierten Quellen.

Enterprise: Die Quellen **sind** die Arbeit, verteit auf:
- GitHub (PRs, Code-Änderungen)
- Slack (Entscheidungsthreads)
- Linear (Tickets, Projektkontext)
- Granola / meeting notes
- Google Drive (Design Docs, RFCs)
- Confluence / Notion (bestehende Dokumentation)
- Zendesk / CRM (Kundenkonversationen)

Ein Enterprise LLM Wiki muss **kontinuierlich** aus diesen Tools ingesten — nicht auf dem Kurations-Takt eines Einzelnen.

### Link: Cross-Tool Entity Resolution statt Vault-Backlinks

Persönlich: Obsidians bidirektionale Links vernetzen Dateien im Vault.

Enterprise: Eine Entscheidung aus Slack muss verknüpft sein mit dem PR der Implementierung, dem Linear-Ticket, dem Granola-Transkript wo sie diskutiert wurde und dem Notion-Dokument das das System beschreibt. Das sind vier Tools, vier Namensräume.

Gefragt ist ein **Wissensgraph mit Cross-Tool-Semantik**: „payments-service" in einem Design Doc ist dieselbe Entität wie `payments-service` im GitHub-Repo und `@payments-team` in Slack.

### Compound: Drift-Erkennung statt manuellem Ingest

Persönlich: LLM integriert neue Quellen in bestehende Seiten; Karpathy beobachtet und steuert.

Enterprise: Wenn ein neuer PR dem bestehenden Runbook widerspricht, muss das System:
1. Den Widerspruch erkennen
2. Einen Update-Entwurf erstellen
3. Zum **Verantwortlichen** des Dokuments routen — nicht zu Karpathy, der für alle Dokumente verantwortlich ist

Das erfordert Ownership-Metadaten und Routing-Logik, die im persönlichen Muster nicht existiert.

### Stay Current: Automatische Hintergrund-Drift-Erkennung

Persönlich: Karpathy löst Health Checks manuell aus, sieht die Lint-Ausgabe und handelt.

Enterprise: Dasselbe über Tausende Dokumente und Millionen Code-Zeilen lässt sich nicht personell triggern. Notwendig:
- Kontinuierliche Drift-Erkennung im Hintergrund
- Wöchentliche Review-Oberfläche (kein Quartals-Audit)
- Automatisches Flagging, nicht manuelle Beobachtung

## Warum Retrieval-Tools das nicht lösen

Glean, Notion AI, Confluence-Suche sind Abruf-Werkzeuge — sie machen das Finden von Erfasstem leichter. Besseres Retrieval über veraltete Wissensbasis liefert **schnellere falsche Antworten**.

Der entscheidende Teil von Karpathys Muster ist nicht die Suchschicht — es ist die **Wartungsschleife**. Der Graph bleibt akkurat, weil der LLM laufend lint, entwirft und reconciliert. Ohne diese Schleife ist smarte Suche über schlechten Kontext schlimmer als langsame Suche über schlechten Kontext.

## YC Spring 2026: Das fehlende Primitive

Y Combinator Spring 2026 Requests for Startups benennt den Bedarf direkt:

> "If we want every company to run on AI automation, we need a new primitive: a company brain. A system that pulls knowledge out of all these fragmented sources, structures it, keeps it current, and turns it into an executable skills file for AI."

Das ist präzise das, was Karpathys persönliches Muster auf Unternehmensebene leisten müsste.

## Kostenbenchmark: Enterprise mit US$/WP

Die [US$/WP-Metrik](usd-pro-wiki-seite.md) macht den Enterprise-Case konkret. Szenario C aus der Ralph-Analyse:

**5.000 Quellen, 3.000 Wiki-Seiten, 1 Jahr, Opus 4.7, Human-in-the-Loop:**

| Posten | Kosten |
|---|---:|
| Ingest (5.000 × $1,61) | $8.050 |
| Lint × 52 Wochen (~$35/Woche) | $1.820 |
| Queries (~100/Tag × 250 Werktage) | $10.000 |
| **Jahresgesamt** | **$19.870** |

**~$6,60/WP** über ein Jahr. Verhältnis zu einem menschlichen Knowledge-Manager (≥$80.000/Jahr fully loaded): ca. **1:4**. Selbst wenn die Qualität nicht gleich ist, bleibt das Verhältnis ökonomisch eindeutig. (Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

Voraussetzung für diese Kosten: eine [Ralph-Schleife](ralph-schleife.md) oder ein Äquivalent für automatisierten Ingest — ohne Automatisierung kommen menschliche Arbeitsstunden als versteckte Kosten hinzu.

## Abgrenzung zu technischen Skalierungsgrenzen

Die [technischen Skalierungsgrenzen](skalierungsgrenzen.md) des LLM-Wikis (index.md als Token-Flaschenhals, ~50–300K Token Degradierung) sind ein anderes Problem: Sie entstehen bei jedem Wiki ab einer bestimmten Größe, unabhängig von Nutzerzahl. Die Enterprise-Skalierungsprobleme entstehen schon bei kleinen Wikis, sobald die Kurations-Verantwortung auf mehrere Schultern verteilt werden muss.

## Verwandte Seiten

- [llm-wiki-enterprise-falconer](../quellen/llm-wiki-enterprise-falconer.md) — Quellartikel
- [skalierungsgrenzen](skalierungsgrenzen.md) — Technische Grenzen
- [kompilierungs-metapher](kompilierungs-metapher.md) — Warum Wartungsschleifen wichtiger sind als Retrieval
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md) — MCP-Integration
- [lint-pruefung](lint-pruefung.md) — Grundlage der Drift-Erkennung
- [llm-wiki-v2](llm-wiki-v2.md) — Schritt zwischen persönlich und Enterprise

---

[Wiki-Index](../index.md)
