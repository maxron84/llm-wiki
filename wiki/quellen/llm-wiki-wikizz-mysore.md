---
date: 2026-08-01
type: quelle
tags: [quelle, community, kontext, werkzeug]
status: active
---

# What Is Andrej Karpathy's LLM Wiki — And How Can You Extend It?

**Zusammenfassung**: Vishal Mysore erklärt Karpathys Muster und erweitert es um einen **5W1H-Kontextrahmen** (Who/What/When/Where/Why/How), den das Modell vor der ersten Frage autonom aus dem Dokument befüllt. Umgesetzt im Open-Source-Werkzeug **LLM WikiZZ** mit Vergleichsmodus und Bewerter-LLM.
**Quellen**: `clippings/What Is Andrej Karpathy’s LLM Wiki — And How Can You Extend It.md`
**Zuletzt aktualisiert**: 2026-08-01

---

## Einordnung

Medium-Artikel vom 2026-04-18, zwei Wochen nach Karpathys Gist. Der erste Teil referiert das Muster — deckungsgleich mit [llm-wiki-karpathy](llm-wiki-karpathy.md) und den übrigen Erklärstücken im Wiki. Der eigenständige Beitrag ist der zweite Teil: eine **konkrete Musterweiterung mit lauffähigem Code**, nicht bloß Kommentar.

Verbreitungszahlen zum Gist: über 5.000 Stars und 4.400 Forks innerhalb von zwei Wochen, dazu dutzende unabhängige Implementierungen auf GitHub. (Quelle: `clippings/What Is Andrej Karpathy’s LLM Wiki — And How Can You Extend It.md`)

> **Widerspruch zu bestehenden Seiten**: [community-projekte](../konzepte/community-projekte.md) nennt „dutzende Implementierungen", ohne Zahlen. Die Angabe 5.000 Stars / 4.400 Forks ist hier erstmals beziffert, stammt aber aus einer Sekundärquelle und ist ein Momentwert von Mitte April 2026 (überprüfungsbedürftig).

## Das Problem: Kontextschuld

Mysores Diagnose geht einen Schritt weiter als die übliche RAG-Kritik im Wiki. Nicht nur, dass RAG das Dokument bei jeder Abfrage neu entdeckt — es entsteht **Kontextschuld** („Context Debt"): Das Modell versteht den *Rahmen* der Daten nie, nur ihren Inhalt. Es weiß, was im Dokument steht, aber nicht, für wen es geschrieben wurde, wozu, in welcher Lage. (Quelle: `clippings/What Is Andrej Karpathy’s LLM Wiki — And How Can You Extend It.md`)

Mehr zur Grundkritik: [rag-vs-wiki](../konzepte/rag-vs-wiki.md)

## Die Erweiterung: 5W1H-Rahmen

Sechs Felder, die das Modell vor der ersten Frage autonom aus dem Dokument füllt — Details und Wiki-Anwendung auf der Konzeptseite [kontextrahmen-5w1h](../konzepte/kontextrahmen-5w1h.md).

Die Rollenverschiebung, die Mysore daraus ableitet: In klassischen Abläufen ist der Mensch der **Sachbearbeiter**, der den Kontext bei jeder Frage neu mitliefert. Im 5W1H-Rahmen wird das Modell zum **Architekten**, der sein eigenes Gerüst baut, bevor es antwortet.

> „LLM WikiZZ proves that the most valuable thing an LLM can do isn't answering the question — it's **understanding the request**."

Das ist dieselbe Rollenverschiebung, die [llm-wiki-muster](../konzepte/llm-wiki-muster.md) für den Ingest beschreibt — hier auf die **Abfrageseite** angewandt.

## Drei Mechanismen von LLM WikiZZ

**1. Autonomes Gerüst** — Ein Klick auf „Generate Wiki" lässt das Modell das gesamte Dokument analysieren und den 5W1H-Rahmen selbst befüllen. Ergebnis ist ein geteiltes mentales Modell zwischen Mensch und Maschine, das die Sitzung überdauert.

**2. Kontrast-Maschine** — Zwei Antworten nebeneinander: *Plain Mode* (kontextloses RAG) gegen *WikiZZ Mode* (Frage durch den 5W1H-Rahmen verfeinert). Macht den Mehrwert des Kontexts sichtbar, statt ihn zu behaupten.

**3. LLM-Jury** — Ein separates, starkes Bewerter-Modell analysiert semantisch die Differenz zwischen beiden Antworten und benennt, *was* besser wurde: situative Relevanz, Knappheit oder fachliche Tiefe.

Mechanismus 2 und 3 zusammen sind ein **Messaufbau für Kontextnutzen** — im Wiki bislang unbelegtes Terrain. Die nächstliegende verwandte Idee ist der Trust Score aus [llm-wiki-v2](../konzepte/llm-wiki-v2.md), der aber Seiten*qualität* bewertet, nicht Antwort*qualität*.

## Technische Architektur

| Eigenschaft | Umsetzung |
|---|---|
| Betriebsmodell | Zero-Server, statisch — läuft vollständig im Browser |
| Datenschutz | Dokumente werden lokal per `FileReader` geparst, nie gespeichert |
| API-Zugang | Cloudflare Worker als CORS-Proxy zu NVIDIA NIM, Anthropic, Gemini |
| Kontext-Persistenz | Der einmal erzeugte Rahmen gilt für die gesamte Sitzung |

Live-Demo: <https://vishalmysore.github.io/lllmwikiZZ/> — Code quelloffen.

Der Ansatz „lokal parsen, nur Anfragen nach außen" deckt sich mit der Datenschutz-Motivation hinter [lokale-modelle](../anleitungen/lokale-modelle.md), löst sie aber nur halb: Das Modell selbst läuft weiterhin in der Cloud.

## Der lebende Wissensgraph

Mysores Abschlussthese: Wenn jedes Dokument durch denselben 5W1H-Rahmen läuft, verbindet sich das „Who" des einen mit dem „Who" des anderen, das „Why" einer medizinischen Studie mit dem „Why" eines Positionspapiers. Aus getrennten Silos wird ein **strukturiertes semantisches Netz**, in dem Chirurg, Patient und Politiker verbundene Knoten mit unterschiedlichen Blickwinkeln auf dasselbe Wissen sind.

Das ist derselbe Gedanke wie die **typisierten Beziehungen** in [llm-wiki-v2](../konzepte/llm-wiki-v2.md) — dort über explizite Kanten zwischen Seiten, hier über gemeinsame Rahmenfelder. Beide Wege führen zu einem Graphen; der 5W1H-Weg erzeugt ihn als Nebenprodukt des Ingests, ohne dass jemand Beziehungstypen pflegen muss.

## Bewertung für dieses Wiki

**Übernehmenswert**: Der 5W1H-Rahmen als *optionaler* Frontmatter-Block auf Quellenseiten — siehe [kontextrahmen-5w1h](../konzepte/kontextrahmen-5w1h.md).

**Nicht übernehmenswert**: Das Werkzeug selbst. LLM WikiZZ ist eine Browser-App für Einzeldokument-Abfragen, kein Wiki-Betriebssystem. Dieses Wiki nutzt Claude Code auf einem Dateibaum — die Architekturen überschneiden sich kaum.

**Vorsicht**: Der Artikel ist zugleich Werbung für das eigene Werkzeug des Autors. Die Behauptungen zum Nutzen des Rahmens sind plausibel und mit einem Beispiel illustriert (Klimadokument → Politikleitfaden statt Impact-Liste), aber **nicht gemessen** — die „LLM-Jury" ist als Mechanismus beschrieben, ohne veröffentlichte Ergebnisse (überprüfungsbedürftig).

## Verwandte Seiten

- [kontextrahmen-5w1h](../konzepte/kontextrahmen-5w1h.md) — Die Musterweiterung im Detail
- [llm-wiki-karpathy](llm-wiki-karpathy.md) — Das Grundmuster
- [llm-wiki-v2](../konzepte/llm-wiki-v2.md) — Andere Musterweiterung: typisierte Beziehungen, Trust Score
- [rag-vs-wiki](../konzepte/rag-vs-wiki.md) — Grundkritik am RAG-Ansatz
- [query-templates](../konzepte/query-templates.md) — Abfrageseite des Wikis
- [community-projekte](../konzepte/community-projekte.md) — Weitere unabhängige Implementierungen

---

[Wiki-Index](../index.md)
