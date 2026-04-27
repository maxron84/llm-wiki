---
date: 2026-04-27
type: konzept
tags: [konzept]
status: draft
---

# Multimodale Quellen im LLM-Wiki

**Zusammenfassung**: Eine Konzeptskizze zur Erweiterung des LLM-Wiki-Musters auf nicht-textuelle Eingaben — Audio, Bilder und Video. Noch nicht in der Praxis erprobt; beschreibt mögliche Pipelines, offene Fragen und Implikationen für künftige Vorlagen.
**Quellen**: (überprüfungsbedürftig — theoretische Ableitung aus [[llm-wiki-muster]] und [[drei-ebenen-architektur]])
**Zuletzt aktualisiert**: 2026-04-27

> **Konzeptskizze**: Diese Seite beschreibt eine theoretische Erweiterung, keine erprobte Praxis. Sie hält fest, was konzeptionell durchdacht wurde — als Ausgangspunkt für spätere Vorlagen oder Experimente, nicht als fertige Anleitung.

---

## Kerngedanke

Das [[llm-wiki-muster]] ist in seiner Grundform textbasiert: Quellen kommen als Markdown oder Plaintext herein, das Wiki ist Markdown. Die [[kompilierungs-metapher]] gilt trotzdem für jedes Medium — Audio, Bilder und Video lassen sich ebenfalls *einmal kompilieren* und dann billig abfragen. Der Unterschied: Vor dem eigentlichen Ingest braucht es einen zusätzlichen Konvertierungsschritt.

```
Text    →  Ingest  →  Wiki
Audio   →  Transkription  →  Ingest  →  Wiki
Bild    →  Multimodale Analyse  →  Ingest  →  Wiki
Video   →  Transkription + Frames  →  Ingest  →  Wiki
```

Der Output des Wikis bleibt immer Text-Markdown. Die Erweiterung betrifft ausschließlich die Eingabeseite.

---

## Die drei Eingabeklassen

### Audio

Podcasts, Vorlesungen, Voice Memos, Interviews, Telefonnotizen.

**Pipeline**: Transkription via Whisper (lokal, kostenlos) oder vergleichbare Modelle → `.md`-Datei in `raw/` → Standard-Ingest.

**Stärke**: Gut erprobt. Whisper liefert auf sauberem Audio zuverlässige Transkripte. Das Ergebnis ist normaler Text — der restliche Workflow ändert sich nicht.

**Verlust**: Ton, Betonung, Pausen. Für Wissensextraktion meist irrelevant; für Sentiment-Analyse relevant.

**Konkreter Anwendungsfall**: Voice Memos beim Autofahren als Eingangskanal für persönliche Wissensbasen.

### Bilder

Screenshots, Fotos, Whiteboards, Buchseiten, Diagramme, Slides, handschriftliche Notizen.

**Pipeline**: Direkte multimodale Analyse durch Claude (ohne Vorverarbeitung) → Beschreibung + extrahiertes Wissen → Wiki-Seite.

**Stärke**: Claude analysiert Bilder nativ. Für das [[claude-md-rezepte-ernaehrung|Rezepte-Wiki]] wären abfotografierte Rezeptkarten oder Bilder von Gerichten sofort verwertbar. Whiteboards aus Meetings könnten direkt in Architekturseiten landen.

**Verlust**: Visuelle Präzision. Eine Beschreibung eines Diagramms ist nicht das Diagramm.

**Offene Frage**: Soll das Originalbild referenziert werden? Ein Link auf eine externe Bilddatei würde die Nachvollziehbarkeit verbessern, aber die Portabilität des Wikis verringern.

### Video (Rohdatei)

**Pipeline**: Für Sprache → Transkript (wie beim YouTube-Verlauf, bereits gelöst). Für visuelle Inhalte → Frame-Extraktion an relevanten Stellen (Diagramme, Code, Demos) → multimodale Analyse einzelner Frames.

**Einschränkung**: Rohdateien sind groß. Die Frame-Extraktion erfordert ein zusätzliches Werkzeug (z.B. `ffmpeg`). Für die meisten Anwendungsfälle ist das Transkript ausreichend — Frame-Analyse lohnt sich nur bei visuell dichten Inhalten (technische Vorträge mit vielen Slides).

**Praktisch**: Dieser Kanal ist derzeit am wenigsten ausgereift.

---

## Das Speicherproblem

Text-Quellen passen in `raw/` und damit in Git — eine 50-KB-Transkriptdatei ist kein Problem. Mediadateien nicht:

| Dateityp | Typische Größe | Git-tauglich? |
|---|---|---|
| Transkript (.md) | 20–100 KB | ✅ ja |
| Bild (.png/.jpg) | 200 KB – 5 MB | ⚠️ klein: ja; groß: nein |
| Audio (.mp3) | 30–150 MB | ❌ nein |
| Video (.mp4) | 500 MB – 5 GB | ❌ nein |

**Mögliche Lösungen**:

- Mediadateien extern lagern (lokaler Ordner außerhalb des Repos, Cloud-Speicher) und im Wiki nur den Pfad referenzieren
- `.gitignore` für Mediendateien; nur extrahierte Transkripte und Bildbeschreibungen committen
- Die *unveränderliche Quelle* ist dann das Transkript, nicht die Originaldatei

Der zweite Ansatz ist konservativer und näher am bestehenden Muster: `raw/` enthält nur das, was tatsächlich ins Wiki floss.

---

## Verlusttoleranz

Jede Konvertierung ist verlustbehaftet. Das ist kein Fehler — es ist die Prämisse des Musters. Das Wiki ist eine Abstraktion, keine Archivkopie. Die Frage ist nicht „Geht dabei etwas verloren?" (ja, immer), sondern „Ist das, was bleibt, für spätere Abfragen ausreichend?"

Für die meisten Wissenszwecke: ja.

---

## Implikationen für künftige Vorlagen

Welche Wiki-Typen würden am meisten profitieren:

| Wiki-Typ | Relevanter Medienkanal |
|---|---|
| YouTube-Verlauf | Audio-Fallback wenn kein Transkript verfügbar |
| Rezepte & Ernährung | Bilder: Rezeptkarten, Fotos, Kochbuchseiten |
| Persönliche Notizen / Journal | Voice Memos als Haupteingabekanal |
| Konferenzen & Meetings | Audio-Aufnahmen + Whiteboard-Fotos |
| Lernwiki (Kurse, Vorlesungen) | Video-Frames für Slides und Diagramme |

Eine neue Vorlage in diesem Bereich würde einen **Medien-Aufnahme-Workflow** als eigene Sektion brauchen — vor dem eigentlichen Ingest-Workflow, der dann textbasiert wie gewohnt weiterläuft.

---

## Offene Fragen

- Wie wird die Herkunft eines Bildes zitierbar gemacht, wenn die Originaldatei nicht im Repo liegt?
- Welche Qualitätsschwelle rechtfertigt Frame-Extraktion vs. reinem Transkript?
- Lohnt es sich, einen standardisierten Vorverarbeitungsschritt (Whisper-Wrapper, ffmpeg-Skript) in `~/.claude/scripts/` abzulegen?

---

## Verwandte Seiten

- [[llm-wiki-muster]] — Das Basismuster, das hier erweitert wird
- [[kompilierungs-metapher]] — Gilt auch für multimodale Quellen
- [[ingest-workflow]] — Bestehender Workflow; multimodaler Kanal würde vorgelagert eingehängt
- [[drei-ebenen-architektur]] — Ebene 1 (Rohquellen) erweitert sich auf nicht-textuelle Medien
- [[kontaminierungsrisiko]] — Bei Bildbeschreibungen erhöhtes Risiko: Modell interpretiert, nicht nur extrahiert
- [[skalierungsgrenzen]] — Mediadateien verschärfen das Speicherproblem erheblich
- [[claude-md-youtube-verlauf]] — Bereits gelöste Variante: Audio via Transkript
- [[claude-md-rezepte-ernaehrung]] — Kandidat für Bild-Input

---

[Wiki-Index](../index.md)
