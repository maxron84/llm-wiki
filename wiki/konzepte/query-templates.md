---
date: 2026-04-23
type: konzept
tags: [konzept]
status: active
---

# Query-Templates (Abfragevorlagen)

**Zusammenfassung**: Benannte Abfragetypen verwandeln das LLM-Wiki vom Frage-Antwort-System zum Denkpartner — Synthese, Lückensuche, Debatte, Output-Generierung, Health-Check und persönliche Anwendung.
**Quellen**: clippings/Beyond RAG How Andrej Karpathy's LLM Wiki Pattern Builds Knowledge That Actually Compounds.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Überblick

Query-Templates sind vorgefertigte Abfragemuster, die spezifische Einsichtstypen aus dem Wiki extrahieren. Statt immer neue Fragen zu formulieren, wählt man einen Template-Typ — und das Wiki liefert strukturierte Analyse. (Quelle: clippings/Beyond RAG...)

## Die 6 Template-Typen

### 1. Synthese
Integriertes Verständnis erzeugen.

Beispiel: *"Lies alles und nenne mir die eine wichtigste Erkenntnis — die Idee, die alles andere zusammenhält."*

Nutzen: Findet den gemeinsamen Nenner über viele Quellen hinweg, der sich beim manuellen Lesen nicht zeigt.

### 2. Lückensuche
Schwachstellen im Wiki identifizieren.

Beispiel: *"Welche wichtigen Themen fehlen komplett? Worüber sollte ich mehr Quellen suchen?"*

Nutzen: Verwandelt das Wiki in einen Kurations-Assistenten, der aktiv auf seine eigenen Lücken hinweist.

### 3. Debatte
Spannungen und Widersprüche aufdecken.

Beispiel: *"Was ist der größte Widerspruch zwischen meinen Quellen? Erkläre beide Seiten als Steelman-Argumente."*

Nutzen: Verhindert, dass das Wiki zu einer Echo-Kammer wird. Ähnlich dem [Lint-Prozess](lint-pruefung.md), aber auf Inhaltsebene statt Struktur.

### 4. Output-Generierung
Artefakte aus dem Wiki kompilieren.

Beispiele:
- *"Erstelle einen Spickzettel der 10 wichtigsten Konzepte"*
- *"Schreibe einen Lernmodul-Entwurf für Einsteiger"*
- *"Erstelle eine Gliederung für einen Vortrag"*

Nutzen: Das Wiki als Quelldatei für Präsentationen, Docs, Lehrmaterialien.

### 5. Health-Check
Das Wiki selbst auditieren.

Beispiele: Duplikat-Seiten finden, Konsistenz-Audit, Integritätsbericht.

Nutzen: Ergänzt den formalen [Lint-Workflow](lint-pruefung.md) um inhaltliche Tiefenprüfung.

### 6. Persönliche Anwendung
Das Wiki auf die eigene Situation anwenden.

Beispiel: *"Welche Fehler mache ich gerade wahrscheinlich — auch wenn ich es nicht weiß?"*

Nutzen: Verwandelt abstraktes Wissen in konkretes Handeln. Das ist der Schritt von "interessant" zu "wertvoll".

## Das Reframing

Query-Templates illustrieren eine zentrale Stärke des LLM-Wiki-Musters:

> "You are not just retrieving facts — you are commissioning analysis across your entire compiled knowledge base." — Plaban Nayak (Quelle: clippings/Beyond RAG...)

Das Wiki ist kein besseres Google. Es ist ein Denkpartner, der das gesamte kompilierte Wissen auf eine spezifische Analyseart anwenden kann.

## Bezug zum täglichen Workflow

Query-Templates sind besonders wertvoll beim [täglichen Workflow](taeglicher-workflow.md) als strukturierte Einstiegspunkte statt leerer Seite. Eine wöchentliche Lückensuche + monatliche Synthese-Abfrage als Standard-Routine ergibt sich natürlich.

## Verwandte Seiten

- [query-workflow](query-workflow.md)
- [lint-pruefung](lint-pruefung.md)
- [taeglicher-workflow](taeglicher-workflow.md)
- [llm-wiki-muster](llm-wiki-muster.md)
- [llm-wiki-plaban-nayak](../quellen/llm-wiki-plaban-nayak.md) — Quelle der 6 Query-Template-Typen

---

[Wiki-Index](../index.md)
