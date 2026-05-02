---
date: 2026-05-02
type: quelle
tags: [quelle, community, kritik]
status: active
---

# The LLM Wiki: How Karpathy's AI Memory Idea Sparked a Movement

**Zusammenfassung**: TecAdRise.ai beschreibt Karpathys LLM-Wiki-Muster als Bewegung: das Problem der KI-Amnesie, warum RAG nicht genug ist, die kompilierende Metapher, drei Kritikpunkte der Community und die Antwort: Constrained Pipelines. Bonus: drei Community-Projekte.
**Quellen**: clippings/The LLM Wiki How Karpathy's AI Memory Idea Sparked a Movement.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Kernaussage

KI hat Amnesie — jede Session startet bei null. RAG versucht das zu lösen, indem es Dokumente bei jeder Abfrage neu durchsucht, ohne je wirklich zu lernen. Karpathys LLM-Wiki-Muster baut stattdessen ein **komponentierendes Gehirn**: Wissen wird einmal kompiliert, verlinkt und akkumuliert — ein lebendes System, kein statischer Speicher. (Quelle: clippings/The LLM Wiki How Karpathy's AI Memory Idea Sparked a Movement.md)

## Die RAG-Kritik

> "RAG rereads the same books for every exam, never actually learning the material."

RAG kann riesige Dokumentenmengen durchsuchen — aber jedes Mal neu. Kein aufbauendes Wissen, keine Querverweise, kein Synthese-Speicher. Mehr: [rag-vs-wiki](../konzepte/rag-vs-wiki.md)

## Drei Ebenen des LLM-Wikis

1. **Raw Sources** — unveränderliche Quellen (Artikel, PDFs, Notizen)
2. **The Wiki** — strukturierte Markdown-Seiten unter LLM-Kontrolle
3. **The Schema** — das Regelwerk (CLAUDE.md), das den LLM-„Bibliothekar" diszipliniert

## Drei Kritikpunkte der Community

Nach Veröffentlichung von Karpathys Gist meldete sich die Developer-Community mit drei Hauptkritiken:

1. **Halluzinationsrisiko** — Fehler beim Ingest werden persistent und können sich über Querverweise ausbreiten. Mehr: [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md)
2. **Konsistenz-Problem** — Kann ein probabilistisches System eine kohärente Wissensbasis pflegen? Tiefe Frage über das Wesen des Schreibens und der Bedeutung.
3. **Architektonische Grenzen** — Wissen ist ein Netz, kein Baum. Einfache Links und Textdateien könnten strukturell unzureichend sein.

## Die Antwort: Constrained Pipelines

Kritik tötete die Idee nicht — sie verbesserte sie. Die Community verschob das Ziel: Kein perfektes KI-System, sondern **bessere Systeme um imperfekte KIs herum**. Das Ergebnis: Constrained Pipelines mit vier Mechanismen:

- **Strenge Schemas** — erzwingen Formatierungs- und Organisationsregeln
- **Confidence Scoring** — KI flaggt unsichere Outputs statt zu raten
- **Human-in-the-Loop** — Bei unlösbaren Widersprüchen: To-do-Liste für Menschen statt schlechte Entscheidungen
- **Source Linking** — Wiki zeigt immer auf Originaldokumente zurück

(Quelle: clippings/The LLM Wiki How Karpathy's AI Memory Idea Sparked a Movement.md)

## Community-Projekte

| Projekt | Ansatz | Besonderheit |
|---|---|---|
| Swarm Vault | Lokale App mit Graph-Viewer | 16 KI-Agenten, vollständig privat |
| BrainDB | Team aus 6 spezialisierten KIs | Automatisches Self-Healing mit Live-Web-Faktencheck |
| Menmo Vault | Gratis, Open Source, lokal | Läuft auf Open-Source-Modellen, keine API-Kosten |

Mehr: [community-projekte](../konzepte/community-projekte.md)

## Business-Winkel

Für kleine Unternehmen: Das LLM-Wiki löst das Problem von **institutionellem Wissen, das mit Mitarbeitern geht**. Ein KI-Assistent, der jede Kundenkonversation, jede Prozessentscheidung, jede Verhandlung kennt und in eine wachsende Wissensbasis organisiert.

## Verwandte Seiten

- [llm-wiki-karpathy](llm-wiki-karpathy.md) — Das Grundmuster
- [rag-vs-wiki](../konzepte/rag-vs-wiki.md) — RAG vs. Wiki-Ansatz
- [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md) — Halluzinationsrisiko und Mitigationen
- [community-projekte](../konzepte/community-projekte.md) — Swarm Vault, BrainDB, Menmo Vault
- [drei-ebenen-architektur](../konzepte/drei-ebenen-architektur.md)

---

[Wiki-Index](../index.md)
