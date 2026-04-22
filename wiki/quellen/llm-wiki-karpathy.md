---
date: 2026-04-22
type: quelle
tags: [quelle]
status: active
---

# LLM Wiki — Andrej Karpathy

**Zusammenfassung**: Karpathys originales Konzeptdokument, das das LLM-Wiki-Muster beschreibt — eine Methode zum Aufbau persönlicher Wissensbasen mithilfe von LLMs.
**Quellen**: clippings/llm-wiki.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Überblick

[[andrej-karpathy|Andrej Karpathy]] — Mitgründer von OpenAI und ehemaliger KI-Direktor bei Tesla — hat dieses Konzeptdokument als GitHub Gist veröffentlicht. Es beschreibt ein Muster, bei dem LLMs nicht nur Dokumente durchsuchen (wie bei [[rag-vs-wiki|RAG]]), sondern aktiv ein **persistentes, wachsendes Wiki** aus Markdown-Dateien aufbauen und pflegen.

Das Dokument ist bewusst abstrakt gehalten. Es beschreibt die Idee, nicht eine spezifische Implementierung. Verzeichnisstruktur, Schema-Konventionen, Seitenformate und Werkzeuge hängen von der jeweiligen Domäne, den persönlichen Vorlieben und dem gewählten LLM ab. (Quelle: clippings/llm-wiki.md)

## Kernkonzept

Das [[llm-wiki-muster]] dreht das übliche Muster um: Statt bei jeder Frage die Rohdokumente von Grund auf zu durchsuchen, lässt die KI deine Quellen **einmal lesen** und baut ein strukturiertes Wiki daraus. Neues Wissen wird integriert, Widersprüche markiert, Querverweise gepflegt. Das Wiki ist ein **persistentes, kumulatives Artefakt**. (Quelle: clippings/llm-wiki.md)

## Architektur

Die [[drei-ebenen-architektur]] besteht aus:

1. **Rohquellen** — Unveränderliche Quelldokumente (Artikel, Papers, Bilder, Daten)
2. **Das Wiki** — Von der KI erstellte und gepflegte Markdown-Dateien
3. **Das Schema** — Ein Regelwerk (z.B. `CLAUDE.md`), das der KI sagt, wie sie arbeiten soll

## Operationen

Drei Kernoperationen definieren den Workflow:

- **[[ingest-workflow|Ingest]]** — Neue Quelle aufnehmen, besprechen, ins Wiki integrieren
- **[[query-workflow|Query]]** — Fragen gegen das Wiki stellen, Antworten zurückspeichern
- **[[lint-pruefung|Lint]]** — Regelmäßige Gesundheitsprüfung des Wikis

## Schlüsselmetapher

> „Obsidian ist die IDE; die KI ist der Programmierer; das Wiki ist die Codebase."

Du schreibst das Wiki nicht selbst — die KI macht das. Du bestimmst, was reinkommt und welche Fragen du stellst. Die KI erledigt die Routinearbeit: Zusammenfassen, Querverweisen, Ablegen, Buchführung. (Quelle: clippings/llm-wiki.md)

## Indexierung und Protokollierung

Zwei spezielle Dateien helfen bei der Navigation:

- **index.md** — Inhaltsorientiert. Ein Katalog aller Wiki-Seiten mit Links und Kurzbeschreibungen. Die KI liest den Index zuerst, wenn sie eine Frage beantwortet.
- **log.md** — Chronologisch. Ein append-only-Protokoll aller Vorgänge. Nützlich für die Nachvollziehbarkeit.

## Anwendungsbereiche

Karpathy nennt zahlreiche Einsatzmöglichkeiten:

- **Persönlich** — Ziele, Gesundheit, Selbstverbesserung
- **Forschung** — Wochen- oder monatelange Vertiefung in ein Thema
- **Bücher lesen** — Kapitelweise ein Begleit-Wiki aufbauen (wie Fan-Wikis à la Tolkien Gateway)
- **Business/Team** — Internes Wiki, gespeist aus Slack, Meeting-Protokollen, Projektdokumenten
- **Weitere** — Wettbewerbsanalyse, Due Diligence, Reiseplanung, Kursnotizen, Hobby-Vertiefungen

## Historischer Bezug

Das Konzept ist verwandt mit [[memex|Vannevar Bushs Memex]] (1945) — einem persönlichen Wissensspeicher mit assoziativen Pfaden zwischen Dokumenten. Was Bush fehlte, war jemand, der die Pflege übernimmt. Die KI löst dieses Problem. (Quelle: clippings/llm-wiki.md)

## Warum es funktioniert

Der mühsame Teil einer Wissensbasis ist nicht das Lesen oder Denken — es ist die **Buchführung**: Querverweise aktualisieren, Zusammenfassungen aktuell halten, Widersprüche erkennen. Menschen geben Wikis auf, weil der Pflegeaufwand schneller wächst als der Nutzen. LLMs werden nicht müde, vergessen keine Querverweise und können 15 Dateien in einem Durchgang bearbeiten. (Quelle: clippings/llm-wiki.md)

## Verwandte Seiten

- [[andrej-karpathy]] — Autor dieses Dokuments
- [[llm-wiki-muster]]
- [[drei-ebenen-architektur]]
- [[ingest-workflow]]
- [[query-workflow]]
- [[lint-pruefung]]
- [[rag-vs-wiki]]
- [[memex]]
- [[obsidian]]

---

[Zurück zum Index](../index.md)
