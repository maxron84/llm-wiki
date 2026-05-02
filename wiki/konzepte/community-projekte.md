---
date: 2026-05-02
type: konzept
tags: [konzept, community]
status: active
---

# Community-Projekte rund um das LLM-Wiki-Muster

**Zusammenfassung**: Nach Karpathys Gist entstanden weltweit Implementierungen des LLM-Wiki-Musters. Drei herausragende Projekte: Swarm Vault (16 Agenten, Graph-Viewer), BrainDB (6 spezialisierte KIs, Self-Healing) und Menmo Vault (Open Source, lokal, keine API-Kosten).
**Quellen**: clippings/The LLM Wiki How Karpathy's AI Memory Idea Sparked a Movement.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Überblick

Karpathys LLM-Wiki-Gist löste eine breite Community-Reaktion aus. Entwickler weltweit begannen, eigene Implementierungen zu bauen — mit unterschiedlichen Schwerpunkten: Datenschutz, Automatisierung, Skalierung, Kostenfreiheit. (Quelle: clippings/The LLM Wiki How Karpathy's AI Memory Idea Sparked a Movement.md)

## Drei Hauptprojekte

### Swarm Vault

| | |
|---|---|
| **Ansatz** | Vollständige lokale Anwendung mit Graph-Viewer |
| **Besonderheit** | 16 verschiedene KI-Agenten, vollständig privat |
| **Fokus** | Visualisierung und Multi-Agenten-Koordination |

Swarm Vault geht über das Einzel-LLM-Modell hinaus: 16 spezialisierte Agenten arbeiten parallel an verschiedenen Aspekten der Wissensbasis. Der integrierte Graph-Viewer macht die Verbindungsstruktur sichtbar — eine praktische Umsetzung von Obsidians Graphansicht-Gedanken ohne Obsidian-Abhängigkeit.

### BrainDB

| | |
|---|---|
| **Ansatz** | Team aus 6 spezialisierten KIs |
| **Besonderheit** | Automatisches Self-Healing mit Live-Web-Faktencheck |
| **Fokus** | Qualitätssicherung und Aktualität |

BrainDB adressiert direkt das [Kontaminierungsrisiko](kontaminierungsrisiko.md): Spezialisierte Agenten überprüfen Wiki-Einträge gegen Live-Webquellen und korrigieren veraltete oder fehlerhafte Informationen automatisch. Self-Healing in Echtzeit statt periodischer manueller Lint-Prüfung.

### Menmo Vault

| | |
|---|---|
| **Ansatz** | Gratis, Open Source, vollständig lokal |
| **Besonderheit** | Keine API-Kosten — läuft auf Open-Source-Modellen |
| **Fokus** | Zugänglichkeit und Datenschutz |

Menmo Vault demokratisiert das Muster: kein Cloud-Abo, keine Daten in Drittanbieter-Infrastruktur. Läuft auf lokalen Open-Source-Modellen (z.B. über [Ollama](../anleitungen/lokale-modelle.md)). Der Tradeoff: geringere Modellqualität gegenüber Cloud-Modellen bei rechenintensiven Ingest-Operationen.

## Weitere Ecosystemtrends

Neben den Flaggschiff-Projekten entwickelte die Gemeinschaft spezialisierte Varianten für:
- **Strikte Datenintegrität** — Fokus auf Quellenrückverfolgbarkeit
- **Mehrsprachige Unterstützung** — Wikis in nicht-englischen Sprachen
- **Verfeinerte Ingest-Pipelines** — Spezialisierung auf bestimmte Dokumenttypen

## Einordnung

Die Community-Projekte bestätigen das Kernmuster — zeigen aber auch, dass es in verschiedene Richtungen erweiterbar ist. Karpathys Gist war der Katalysator, aber die eigentliche Stärke liegt in der Anpassbarkeit des Musters auf unterschiedliche Anwendungskontexte.

Für die Weiterentwicklung des Musters (v2 mit typisierten Beziehungen, Enterprise-Skalierung) siehe: [llm-wiki-v2](llm-wiki-v2.md) und [enterprise-skalierung](enterprise-skalierung.md).

## Verwandte Seiten

- [llm-wiki-karpathy](../quellen/llm-wiki-karpathy.md) — Das Grundmuster
- [llm-wiki-tecadrise](../quellen/llm-wiki-tecadrise.md) — Quelle
- [llm-wiki-v2](llm-wiki-v2.md) — Weiterentwicklung des Musters
- [kontaminierungsrisiko](kontaminierungsrisiko.md) — BrainDB adressiert dieses Problem
- [fortgeschrittene-architektur](fortgeschrittene-architektur.md)

---

[Wiki-Index](../index.md)
