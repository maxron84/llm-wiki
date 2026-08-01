---
date: 2026-08-01
type: konzept
tags: [konzept, kontext, schema, ingest]
status: active
---

# 5W1H-Kontextrahmen

**Zusammenfassung**: Sechs Felder — Wer, Was, Wann, Wo, Warum, Wie — die das Modell beim Ingest autonom aus einer Quelle befüllt. Sie halten den *Rahmen* eines Dokuments fest, nicht seinen Inhalt, und beugen so „Kontextschuld" vor.
**Quellen**: `clippings/What Is Andrej Karpathy’s LLM Wiki — And How Can You Extend It.md`
**Zuletzt aktualisiert**: 2026-08-01

---

## Das Problem: Kontextschuld

Ein Wiki hält fest, **was** in einer Quelle steht. Es hält selten fest, **für wen sie geschrieben wurde, wozu und in welcher Lage**. Vishal Mysore nennt die Lücke *Context Debt* — Kontextschuld: Das Modell kennt den Inhalt, aber nicht den Rahmen, und rekonstruiert ihn bei jeder Abfrage aufs Neue oder rät ihn falsch. (Quelle: `clippings/What Is Andrej Karpathy’s LLM Wiki — And How Can You Extend It.md`)

Das ist eine Restschuld, die das LLM-Wiki-Muster nicht automatisch tilgt. [kompilierungs-metapher](kompilierungs-metapher.md) sagt: Rohquelle = Quellcode, Wiki = kompiliertes Binary. Der 5W1H-Rahmen ergänzt: Beim Kompilieren gehen die **Kompilierungsbedingungen** verloren, wenn man sie nicht ausdrücklich mitschreibt.

## Die sechs Felder

| Feld | Erfasst | Beispiel (Karpathys Gist) |
|---|---|---|
| **Wer** | Zielgruppe / Persona | KI-Entwickler mit Agenten-Erfahrung |
| **Was** | Kernauftrag der Quelle | Ein Muster vorschlagen, kein Werkzeug liefern |
| **Wann** | Zeitlicher Kontext, Dringlichkeit | April 2026, offene Skizze, kein Endstand |
| **Wo** | Situativer Kontext | GitHub Gist — Diskussionsanstoß, kein Produkt |
| **Warum** | Zugrundeliegende Motivation | RAG-Unzufriedenheit; Wissen soll sich verzinsen |
| **Wie** | Struktur- und Formatanspruch | Knapp, prinzipiengeleitet, bewusst unfertig |

Entscheidend ist die Autonomie: **Das Modell füllt die Felder selbst**, aus dem Dokument, bevor die erste Frage gestellt wird. Der Mensch prüft — er diktiert nicht. Das entspricht der Rollenteilung, die dieses Wiki ohnehin fährt (siehe [llm-wiki-muster](llm-wiki-muster.md)): Der Mensch kuratiert Quellen und lenkt, das Modell strukturiert.

## Warum das die Abfrageseite verbessert

[query-templates](query-templates.md) beschreibt, *welche Art* Analyse man vom Wiki verlangt. Der 5W1H-Rahmen liefert das fehlende Gegenstück: *unter welchen Rahmenbedingungen* eine Quelle überhaupt gilt.

Mysores Beispiel: Ein Fachtext zur Erderwärmung. Ohne Rahmen liefert die Abfrage eine Standardliste von Umweltfolgen. Mit ausgefülltem „Warum" (technischer Leitfaden) und „Wer" (politische Entscheider) strukturiert das Modell dieselbe Quelle als geordnetes Verzeichnis chemischer Emissionen um — ohne dass jemand die Zusatztiefe angefordert hat.

Der praktische Nutzen im Wiki-Betrieb: Bei widersprüchlichen Quellen erklärt der Rahmen oft den Widerspruch. Zwei Seiten, die sich zu widersprechen scheinen, sprechen manchmal einfach zu verschiedenen „Wer" oder in verschiedenen „Wann".

## Bezug zur Seitenklassifikation

Das Feld **Wann** überschneidet sich mit der in [seitenklassifikation](seitenklassifikation.md) vorgeschlagenen Achse *zeitlos / gemischt / zeitgenössisch*. Beide fragen nach zeitlicher Geltung — die Seitenklassifikation als grobes Dreierraster über die fertige Wiki-Seite, das 5W1H-„Wann" feiner über die Quelle. Wer eines von beiden einführt, sollte das andere daran anschließen statt parallel zu pflegen.

## Möglicher Einsatz hier

Als **optionaler Block** auf Quellenseiten, unterhalb des Kopfes:

```markdown
**Rahmen (5W1H)**
- Wer: Zielgruppe der Quelle
- Was: Kernauftrag
- Wann: Zeitlicher Kontext und Haltbarkeit
- Wo: Situativer Kontext (Publikationsort, Anlass)
- Warum: Motivation des Autors
- Wie: Form und Anspruch
```

Bewusst **optional und nur für Quellenseiten**: Sechs Zeilen pro Seite sind bei 24 Quellenseiten vertretbar, bei allen 118 Seiten wären sie ein spürbarer Token-Aufschlag im Ingest — genau die Rechnung, die [skalierungsgrenzen](skalierungsgrenzen.md) und [claude-md-token-sparen](claude-md-token-sparen.md) aufmachen. Für Konzeptseiten ist der Rahmen ohnehin sinnlos: Sie haben keinen Autor mit Absicht.

**Status**: als Muster dokumentiert, in diesem Wiki noch nicht eingeführt. Eine Entscheidung darüber gehört mit der Entscheidung über [seitenklassifikation](seitenklassifikation.md) zusammen — beides sind Frontmatter-Erweiterungen mit ähnlichem Kosten-Nutzen-Profil.

## Grenzen

Der Rahmen ist **nicht gemessen**. Mysore beschreibt mit der „LLM-Jury" einen Messaufbau (ein Bewerter-Modell vergleicht Antworten mit und ohne Rahmen), veröffentlicht aber keine Ergebnisse — die Nutzenbehauptung bleibt plausibel und illustriert, aber unbelegt (überprüfungsbedürftig). Zudem ist der Autor Urheber des zugehörigen Werkzeugs, was ihn zur interessierten Partei macht.

Zweite Grenze: Sechs Felder, autonom vom Modell gefüllt, sind sechs weitere Stellen, an denen sich eine Halluzination als Fakt festsetzen kann — siehe [kontaminierungsrisiko](kontaminierungsrisiko.md). Anders als Inhaltsaussagen sind Rahmenfelder schlecht gegen die Quelle prüfbar, weil sie Interpretation sind.

## Verwandte Seiten

- [llm-wiki-wikizz-mysore](../quellen/llm-wiki-wikizz-mysore.md) — Die Quelle
- [seitenklassifikation](seitenklassifikation.md) — Verwandte Frontmatter-Erweiterung, gleiche Entscheidung
- [query-templates](query-templates.md) — Abfrageseite: welche Analyse
- [kompilierungs-metapher](kompilierungs-metapher.md) — Was beim Kompilieren verloren geht
- [llm-wiki-v2](llm-wiki-v2.md) — Andere Musterweiterung: typisierte Beziehungen
- [kontaminierungsrisiko](kontaminierungsrisiko.md) — Risiko autonom gefüllter Felder

---

[Wiki-Index](../index.md)
