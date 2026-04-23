---
date: 2026-04-23
type: konzept
tags: [konzept, vergleich]
status: active
---

# Kompilierungs-Metapher

**Zusammenfassung**: Die prägnanteste Erklärung des LLM-Wiki-Musters: Rohquellen verhalten sich wie Quellcode, das Wiki ist das kompilierte Binärformat — einmal kompilieren, beliebig oft günstig ausführen.
**Quellen**: clippings/Andrej Karpathy's LLM Wiki Create your own knowledge base.md, clippings/Beyond RAG..., clippings/Karpathy's LLM Knowledge Bases The Post-Code AI Workflow.md
**Zuletzt aktualisiert**: 2026-04-23

---

## Die Analogie

```
SOFTWARE ENGINEERING
Quellcode ──[ einmal kompilieren ]──► Binärformat
(lesbar)                               (läuft schnell bei jedem Aufruf)

LLM WIKI
Rohquellen ──[ LLM kompiliert ]──► Wiki
(PDFs, Artikel,                    (vorsynthetisiert, verlinkt,
 Notizen)                           jederzeit bereit)
```

> "You don't execute source code every time you want to run a program. You compile it once into a binary and run that." — Urvil Joshi (Quelle: clippings/Andrej Karpathy's LLM Wiki Create your own knowledge base.md)

## Was die Metapher bedeutet

**Traditionelles RAG** = Quellcode bei jeder Abfrage neu ausführen. Das System liest dieselben Dokumente, chunked sie, synthethisiert die Antwort — und vergisst alles wieder.

**LLM-Wiki** = Einmalig kompilieren, günstig abfragen. Die Synthese-Arbeit geschieht beim Ingest. Jede Abfrage arbeitet mit dem bereits verarbeiteten Artefakt.

Die Kompilierung ist teuer — aber sie zahlt sich bei jeder nachfolgenden Nutzung aus.

## Die vollständige Analogie (Antigravity.codes)

| Software | LLM-Wiki |
|---|---|
| Quellcode (`raw/`) | Rohquellen |
| Compiler | LLM |
| Ausführbares Programm | Wiki |
| Test-Suite | Health Checks (Lint) |
| Laufzeitumgebung | Abfragen |

> "Karpathy is building GCC for knowledge." — Antigravity.codes (Quelle: clippings/Karpathy's LLM Knowledge Bases The Post-Code AI Workflow.md)

## Was beim "Kompilieren" passiert

Wenn eine neue Quelle aufgenommen wird:

1. LLM liest die Quelle vollständig
2. Extrahiert Schlüsselinformationen
3. Aktualisiert bestehende Seiten (Synthese erweitern, nicht überschreiben)
4. Erstellt neue Seiten wo nötig
5. Setzt Querverweise
6. Markiert Widersprüche

Das Wiki ist das **persistierende, wachsende Artefakt** dieser Kompilierung.

## Grenzen der Metapher

Die Metapher hat Grenzen: Binärcode wird für Menschen unlesbar — das Wiki bleibt vollständig menschenlesbar. Außerdem ist das Wiki nicht "einmal fertig" wie ein Binary, sondern wächst inkrementell weiter.

Eine treffendere Erweiterung: Es ist eher wie ein **Lehrbuch, das bei jeder neuen Quelle neu geschrieben wird** — aber immer kohärenter und vernetzter wird.

## Verwandte Seiten

- [[rag-vs-wiki]]
- [[llm-wiki-muster]]
- [[ingest-workflow]]
- [[drei-ebenen-architektur]]

---

[Wiki-Index](../index.md)
