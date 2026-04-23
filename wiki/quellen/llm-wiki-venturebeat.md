---
date: 2026-04-23
type: quelle
tags: [quelle]
status: active
---

# Karpathy teilt LLM Knowledge Base Architektur (VentureBeat)

**Zusammenfassung**: Carl Franzens VentureBeat-Artikel berichtet über Karpathys Ankündigung, analysiert Community-Reaktionen (Steph Ango, Lex Fridman, Enterprise-Stimmen) und diskutiert Implikationen für Unternehmen, Feintuning-Potenzial und Multi-Agent-Erweiterungen.
**Quellen**: clippings/Karpathy shares 'LLM Knowledge Base' architecture that bypasses RAG with an evolving markdown library maintained by AI.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Über die Quelle

- **Autor**: Carl Franzen (VentureBeat)
- **Erschienen**: 2026-04-04
- **Art**: Nachrichtenartikel mit Community-Reaktionen

## Kernaussagen

### Karpathys Kernproblem

Der Artikel trifft den Kern präzise: Durch das Sitzungsende oder Kontextlimit-Reset muss man wertvolle Token damit verbringen, den KI-Agenten wieder einzuführen. Das LLM-Wiki löst das Statelessness-Problem systemisch.

### Community-Reaktionen

**Steph Ango ([@kepano](https://stephango.com/), Obsidian-Mitgründer)**: Schlug [[vault-trennung]] vor — persönliches sauberes Vault + separates "unordentliches Vault" für KI-generierte Inhalte. Verhindert Kontaminierung; der Mensch weiß immer, welches Wissen menschlich kuratiert vs. KI-kompiliert ist.

**Lex Fridman**: Bestätigte ähnliches Setup. Nutzt dynamisches HTML mit JavaScript für interaktive Visualisierungen. Erwähnte auch "ephemere Mini-Wissensbasis" — ein temporäres, aufgabenspezifisches Wiki, das nach dem Abschlussbericht gelöscht wird.

**Unternehmer Vamshi Reddy**: "Every business has a raw/ directory. Nobody's ever compiled it. That's the product." — Karpathy stimmte zu.

**Elvis Saravia (DAIR.AI)**: Bestätigte das Muster aus eigener Erfahrung: "LLMs are excellent at curating and searching once data is stored properly."

### Enterprise-Implikationen

Karpathy's System könnte für Unternehmen bedeuten: Von einem "Rohdaten-See" zu einem "kompilierten Wissens-Asset". Ein KI-Layer, der nicht nur Dokumente durchsucht, sondern aktiv eine "Company Bible" pflegt, die sich in Echtzeit aktualisiert.

Eugen Alpeza (Edra): Der Sprung von persönlichem Wiki zu Enterprise-Betrieb ist brutaler — tausende Mitarbeiter, Millionen Datensätze, widersprüchliches Stammwissen über Teams hinweg. Es gibt Raum für neue Produkte.

### Feintuning-Potenzial

VentureBeat hebt einen Aspekt hervor, der in anderen Quellen fehlt: Wenn das Wiki durch kontinuierliches LLM-Linting "sauber" wird, wird es zum perfekten Trainingsdatensatz. Man kann dann ein kleineres, effizienteres Modell auf dem Wiki feintunen — das Modell würde das persönliche Wissen in seinen Gewichten "kennen". Mehr: [[fortgeschrittene-architektur]]

### Multi-Agent-Erweiterung

Der @jumperz-"Swarm Knowledge Base"-Ansatz: 10-Agenten-System über OpenClaw, bei dem jede Agenten-Halluzination durch einen "Quality Gate" (Hermes-Modell als unabhängiger Prüfer) abgefangen wird, bevor sie ins Live-Wiki promoted wird.

### Librarian vs. Suchmaschine

> "Karpathy's 'Markdown Wiki' is like a curated library with a head librarian who is constantly writing new books to explain the old ones."

## Bezug zum bestehenden Wiki

Primär wertvolle Quelle für [[vault-trennung]] (Steph Ango), [[kontaminierungsrisiko]] und [[fortgeschrittene-architektur]] (Feintuning). Wichtige Community-Perspektive.

## Verwandte Seiten

- [[vault-trennung]]
- [[kontaminierungsrisiko]]
- [[fortgeschrittene-architektur]]
- [[steph-ango]]
- [[statelessness]]

---

[Wiki-Index](../index.md)
