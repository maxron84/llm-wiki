---
date: 2026-04-25
type: vorlage
tags: [vorlage, schema]
status: active
---

# CLAUDE.md-Vorlage: YouTube-Verlauf-Wiki

**Zusammenfassung**: Eine CLAUDE.md-Vorlage für ein persönliches Wissenswiki aus dem eigenen YouTube-Verlauf — extrahiert Konzepte aus Transkripten, verknüpft Sprecher und Themen, macht gesehene Videos dauerhaft abfragbar. Enthält Klassifikations-System (Zeitlos/Gemischt/Zeitgenössisch) und lebende Output-Dokumente (`betrachter.md`, `zitate.md`, `offene-fragen.md`).
**Quellen**: Abgeleitet aus [[llm-wiki-muster]], [[drei-ebenen-architektur]], [[ingest-workflow]], [[query-templates]], [[kompilierungs-metapher]] und [[claude-md-design]]; weiterentwickelt durch `raw/CLAUDE-2026-04-25_youtube-video-verlauf.md`
**Zuletzt aktualisiert**: 2026-04-25

---

## Zweck

Diese Vorlage implementiert die dritte Ebene der [[drei-ebenen-architektur]] — das **Schema** — für ein spezifisches Quellmaterial: YouTube-Videos aus dem eigenen Verlauf.

Das Grundproblem: Man schaut ein interessantes Video, nimmt etwas mit — und eine Woche später ist es vergessen. Das Wiki löst das durch das [[kompilierungs-metapher|Kompilierungsmuster]]: Statt jedes Video neu abzurufen, wenn man eine Frage hat, wird es einmal kompiliert und bleibt dauerhaft abfragbar.

## Benutzung

1. Kopiere **nur den Inhalt** des Vorlagenblocks unten (ohne die ` ```` ` Fence-Markierungen) als `CLAUDE.md` ins Wurzelverzeichnis deines YouTube-Wiki-Projekts. Der 4-Backtick-Fence dient hier nur als Darstellung — in deinem Projekt muss der Inhalt als direkter Top-Level-Markdown stehen, damit Claude ihn als operative Anweisung liest (siehe [[claude-md-design|Designprinzip 1]]).
2. Ersetze die `{{PLATZHALTER}}`
3. Lege Transkripte oder Notizen in `raw/` ab
4. Sage Claude: „Es gibt neue Quellen — bitte verarbeite sie"

## Besonderheiten gegenüber dem allgemeinen Wiki-Muster

| Aspekt | Allgemeines Wiki | YouTube-Verlauf-Wiki |
|---|---|---|
| **Quellformat** | Artikel, PDFs, Repos | Transkripte, Beschreibungen, Notizen |
| **Haupteinheit** | Quelle | Video |
| **Zusätzliche Entitäten** | — | Kanal, Serie/Playlist, Sprecher |
| **Metadaten** | Datum, Typ | + Kanal, Dauer, Gesehen-Datum, Zeitstempel |
| **Extraktionsstrategie** | Einheitlich | Variiert nach Kanal-Stil (Tutorial vs. Vortrag vs. Essay) |
| **Zeitstempel** | Nicht relevant | Verlinkbare Momente innerhalb eines Videos |
| **Klassifikation** | Optional | Pflichtfeld: Zeitlos / Gemischt / Zeitgenössisch |
| **Output-Dokumente** | — | `betrachter.md`, `zitate.md`, `offene-fragen.md` |

## Ordnerstruktur

```
raw/                      -- Unveränderliche Quellen (NIEMALS bearbeiten)
  transkripte/            -- Vollständige Videotranskripte (.txt oder .md)
  beschreibungen/         -- Video-Beschreibungen, Show Notes
  notizen/                -- Eigene Notizen während/nach dem Schauen
  unverarbeitet/          -- Inbox: noch nicht kategorisierte Quellen

clippings/                -- Web-Clipper-Exporte (NIEMALS bearbeiten)

wiki/                     -- Von Claude gepflegtes Wiki
  index.md
  log.md
  videos/                 -- Eine Seite pro Video
  konzepte/               -- Themenübergreifende Erkenntnisse
  kanaele/                -- Kanal-Profile
  personen/               -- Sprecher, Gäste, Autoren
  serien/                 -- Playlists und thematische Reihen
  output/                 -- Synthetisierte, lebende Ausgaben
    betrachter.md         -- Fortlaufende Betrachter-Analyse (lebendes Dokument)
    zitate.md             -- Prägnante, erinnerungswürdige Zitate
    offene-fragen.md      -- Fragen, die Videos aufwerfen, aber nicht beantworten
```

## Transkripte beschaffen

Möglichkeiten, Transkripte in `raw/transkripte/` zu bekommen:

```bash
# yt-dlp (empfohlen): Auto-Untertitel als .vtt herunterladen
yt-dlp --write-auto-subs --skip-download "https://youtube.com/watch?v=VIDEO_ID"

# Oder: YouTube → Video öffnen → ... → Transkript anzeigen → kopieren

# Oder: Python-Bibliothek für automatische Extraktion
pip install youtube-transcript-api
python -c "from youtube_transcript_api import YouTubeTranscriptApi; ..."
```

Alternativ genügen Video-Beschreibung + eigene Notizen für einen Minimal-Ingest.

## Designhinweise

Beim Einsatz dieser Vorlage die [[claude-md-design|CLAUDE.md-Designprinzipien]] beachten — die Prinzipien stammen aus der praktischen Erfahrung mit genau dieser Vorlage:

1. **Fence entfernen**: Der Vorlageninhalt unten steht in einem 4-Backtick-Fence. Beim Kopieren in ein YouTube-Wiki-Projekt **den Fence-Wrapper entfernen** — Claude liest Inhalte in Code-Fences als Illustration, nicht als operative Anweisungen.
2. **Link-Stil anpassen**: Die Vorlage nutzt `[[wiki-links]]`. Falls das Projekt Standard-Markdown verwenden soll, durchgehend anpassen.
3. **Frontmatter-Entscheidung**: Die Vorlage enthält [[yaml-frontmatter]] im Video-Seitenformat. Wenn man darauf verzichten will, das Format entsprechend anpassen — aber konsistent bleiben.
4. **Keine toten Links**: Die `{{PLATZHALTER}}` müssen ausgefüllt oder entfernt werden.
5. **Regelmäßig reviewen**: Nach ~20 aufgenommenen Videos die CLAUDE.md auf neue Muster prüfen (z.B. neuer Kanal-Stil, der eine eigene Extraktionsstrategie braucht).
6. **`betrachter.md` ist ein Sonderfall**: Lebendes Meta-Dokument — kein `**Klassifikation**:`-Feld, kein fixes Format. Es wächst mit dem Wiki.

## Vorlage

````markdown
# CLAUDE.md — YouTube-Verlauf-Wiki

> **Anleitung**: Kopiere diese Datei als `CLAUDE.md` ins Wurzelverzeichnis deines Projekts.
> Ersetze alle `{{PLATZHALTER}}`. Entferne diesen Block vor der produktiven Nutzung.

---

# CLAUDE

## Zweck

Dieses Wiki macht meinen YouTube-Verlauf dauerhaft abfragbar. Claude kompiliert gesehene Videos zu einem vernetzten Wissensgraph — einmal verarbeitet, beliebig oft günstig abgefragt. Ich kuratiere die Quellen und stelle Fragen. Claude schreibt und pflegt das Wiki.

**Thematischer Fokus**: {{z.B. "Softwareentwicklung, KI, Produktivität" oder "Kochen, Fermentation, Food Science" — oder "alles"}}

## Ordnerstruktur

```
raw/
  transkripte/       -- Vollständige Videotranskripte
  beschreibungen/    -- Video-Beschreibungen, Show Notes
  notizen/           -- Eigene Notizen während/nach dem Schauen
  unverarbeitet/     -- Inbox: noch nicht kategorisiert

clippings/           -- Web-Clipper-Exporte (NIEMALS bearbeiten)

wiki/
  index.md           -- Inhaltsverzeichnis
  log.md             -- Änderungsprotokoll
  videos/            -- Eine Seite pro Video
  konzepte/          -- Themenübergreifende Erkenntnisse
  kanaele/           -- Kanal-Profile
  personen/          -- Sprecher, Gäste, Autoren
  serien/            -- Playlists, thematische Reihen
  output/            -- Synthetisierte, lebende Ausgaben
    betrachter.md    -- Fortlaufende Betrachter-Analyse
    zitate.md        -- Prägnante Zitate
    offene-fragen.md -- Fragen aus Videos, noch unbeantwortet
```

## Aufnahme-Workflow

Wenn neue Quellen in `raw/` oder `clippings/` hinzugefügt werden:

1. **Lies** das vollständige Dokument
2. **Identifiziere den Quelltyp**: Transkript / Beschreibung / Notizen / Playlist-Dump
3. **Übersetze** ins Deutsche, wenn die Quelle in einer anderen Sprache ist, ohne Informationen inhaltlich zu verändern
4. **Besprich** die wichtigsten Erkenntnisse und frage nach der gewünschten Tiefe:
   - **Minimal** — Nur eine Video-Zusammenfassungsseite
   - **Mittel** — Video-Seite + Konzeptseiten für die wichtigsten Ideen *(Standard)*
   - **Vollständig** — Alles oben + Personen- und Kanalseiten
5. **Erstelle die Video-Seite** in `wiki/videos/` (Format siehe unten)
6. **Erstelle oder aktualisiere Konzeptseiten** in `wiki/konzepte/`
7. **Erstelle oder aktualisiere Kanalseite** in `wiki/kanaele/` (falls neu)
8. **Verknüpfe** mit `[[wiki-links]]` (verwandte Videos, Konzepte, Sprecher)
9. **Aktualisiere** `wiki/index.md` und `wiki/log.md`
10. **Pflege `wiki/output/betrachter.md`** — Prüfe, ob sich Cluster-Tiefen, Wissenslandkarte, Gravitationszentrum oder wiederkehrende Motive verschoben haben. Dieser Schritt ist **verpflichtend**, nicht optional.
11. **Prüfe weitere Output-Dokumente** — Bei prägnanten Zitaten: in `wiki/output/zitate.md` eintragen. Bei offenen Fragen, die das Video aufwirft, aber nicht beantwortet: in `wiki/output/offene-fragen.md` ergänzen.

## Seitenformate

### Video-Seite (`wiki/videos/YYYY-MM-DD-kurzname.md`)

```markdown
---
date: YYYY-MM-DD          # Gesehen-Datum
type: video
tags: [tag1, tag2]
status: active
channel: "{{Kanalname}}"
published: YYYY-MM-DD     # Erscheinungsdatum des Videos
duration: "HH:MM"
---

# Videotitel

**Zusammenfassung**: Ein bis zwei Sätze, was dieses Video vermittelt.
**Kanal**: [[kanalname]]
**Sprecher**: [[person]] (falls relevant)
**Quellen**: raw/transkripte/dateiname.md
**Zuletzt aktualisiert**: YYYY-MM-DD
**Klassifikation**: Zeitlos | Gemischt | Zeitgenössisch

---

## Kernaussagen

- Wichtigste Erkenntnis 1 (Quelle: raw/transkripte/...)
- Wichtigste Erkenntnis 2

## Zitate

> "Wörtliches Zitat, das besonders prägnant ist."

## Verwandte Konzepte

- [[konzept-1]] — kurze Erklärung der Verbindung

## Verwandte Videos

- [[YYYY-MM-DD-anderes-video]] — warum verwandt
```

### Konzept-Seite (`wiki/konzepte/konzeptname.md`)

```markdown
---
date: YYYY-MM-DD
type: konzept
tags: [tag1, tag2]
status: active
---

# Konzeptname

**Zusammenfassung**: Ein bis zwei Sätze.
**Quellen**: Liste der Videos, aus denen dieses Konzept destilliert wurde
**Zuletzt aktualisiert**: YYYY-MM-DD
**Klassifikation**: Zeitlos | Gemischt | Zeitgenössisch

---

Hauptinhalt: Was ist dieses Konzept? Wie erklären verschiedene Videos es?
Wo gibt es Widersprüche zwischen Videos?

## Verwandte Seiten

- [[anderes-konzept]]
- [[YYYY-MM-DD-video-das-das-erklaert]]
```

### Kanal-Seite (`wiki/kanaele/kanalname.md`)

```markdown
---
date: YYYY-MM-DD
type: person
tags: [kanal]
status: active
---

# Kanalname

**Zusammenfassung**: Worum geht es auf diesem Kanal? Stil und Stärken.
**Zuletzt aktualisiert**: YYYY-MM-DD
**Klassifikation**: Zeitlos | Gemischt | Zeitgenössisch

---

## Stil und Extraktionsstrategie

{{z.B. "Lange Vorträge — Zeitstempel sind wertvoll; Kernthesen am Anfang und Ende"}}

## Gesehene Videos

- [[YYYY-MM-DD-video-1]]
- [[YYYY-MM-DD-video-2]]
```

## Klassifikation

Jede Seite (außer Output-Dokumente) trägt ein `**Klassifikation**:`-Feld direkt nach `**Zuletzt aktualisiert**`:

| Wert | Bedeutung | Typische Seiten |
|---|---|---|
| **Zeitlos** | Informationswert verfällt nicht | Konzepte, Frameworks, psychologische Modelle, Designprinzipien |
| **Gemischt** | Zeitloser Kern, aber zeitgebundene Details | KI-Überblicksseiten, Technologievergleiche mit konkreten Zahlen |
| **Zeitgenössisch** | Wert an einen spezifischen Moment gebunden | Reaktionsvideos, aktuelle Produktkritiken, politische Ereignisse |

**Pflegeregeln:**
- Jede neu erstellte Seite bekommt sofort eine Klassifikation.
- Bei Inhaltsaktualisierungen kurz prüfen, ob die Klassifikation noch stimmt — und ggf. anpassen.
- `wiki/output/betrachter.md` ist eine Ausnahme: kein Klassifikationsfeld (lebendes Meta-Dokument).
- Im Lint-Check: Seiten ohne `**Klassifikation**:` melden.

## Extraktionsstrategien nach Video-Typ

| Typ | Fokus beim Extrahieren |
|---|---|
| **Tutorial** | Schritte, Befehle, Voraussetzungen, häufige Fehler |
| **Vortrag / Talk** | Hauptthese, Argumente, Daten, Zitate |
| **Interview** | Meinungen des Gastes, Widersprüche zum Moderator, Empfehlungen |
| **Essay / Essay-Video** | Kernargument, Schlussfolgerung, kritisierte Alternativen |
| **Review / Vergleich** | Kriterien, Bewertungen, Empfehlung und für wen |
| **Dokumentation** | Fakten, Chronologie, Personen, Quellen |

## Vernetzung & Konsistenzpflege

Das Wiki ist nicht nur ein Archiv einzelner Videos — es ist die Datengrundlage für `wiki/output/betrachter.md`. Fehlende oder falsch gesetzte Verbindungen erzeugen falsche Rückschlüsse über Interessen, Positionen und Muster.

**Bidirektionale Links sind Standard**: Wenn Seite A auf Seite B verweist, verweist Seite B in aller Regel zurück. Keine erzwungenen Links — aber nach jeder Aufnahme explizit prüfen, welche bestehenden Seiten einen Rückverweis auf die neue Seite verdienen.

**Das Betrachter-Profil mitpflegen**: Nach jeder Aufnahme prüfen, ob sich Themengewichtungen, Grundpositionen oder wiederkehrende Motive verschoben haben. `wiki/output/betrachter.md` entsprechend anpassen.

**Regelmäßige Audits**: `python3 ~/.claude/scripts/wiki_lint.py` nach größeren Aufnahmephasen. Tote Links und Waisen werden behoben, nicht ignoriert.

## Fragenbeantwortung

Wenn ich eine Frage stelle:

1. Lies `wiki/index.md`, um relevante Seiten zu finden
2. Lies diese Seiten und fasse eine Antwort zusammen
3. Zitiere die spezifischen Videos oder Konzeptseiten
4. Wenn die Antwort nicht im Wiki steht, sage das klar
5. Wenn die Antwort wertvoll ist, speichere sie als neue Konzept-Seite

## Benannte Abfragetypen

| Typ | Beispiel-Abfrage |
|---|---|
| **Synthese** | „Die eine Erkenntnis, die sich durch alle Videos zu Thema X zieht?" |
| **Lückensuche** | „Welche Themen tauchen immer wieder auf, haben aber noch keine Konzeptseite?" |
| **Debatte** | „Welche Videos widersprechen sich zu Thema X? Beide Seiten als Steelman." |
| **Output** | „Erstelle eine Lernreihenfolge für Y aus allem, was ich gesehen habe" |
| **Gesundheit** | „Welche Video-Seiten haben keine Konzept-Links? Welche Konzepte sind verwaist?" |
| **Persönliche Anwendung** | „Was sollte ich als nächstes schauen, basierend auf meinen Lücken?" |

## Prüfung (Lint)

Wenn ich das Wiki prüfen lasse:

- Videos ohne Konzept-Verlinkungen finden (Waisen)
- Konzepte, die in Videos erwähnt werden, aber keine eigene Seite haben
- Widersprüche zwischen Konzeptseiten verschiedener Videos
- Video-Seiten ohne Kanalseite
- Behauptungen ohne Quellenangabe markieren
- Seiten ohne `**Klassifikation**:`-Feld melden

## Regeln

- Verändere **NIEMALS** etwas in `raw/` oder `clippings/` — Originalquellen bleiben unveränderlich
- Aktualisiere immer `wiki/index.md` und `wiki/log.md` nach Änderungen
- Dateinamen für Videos: `YYYY-MM-DD-kurzname.md` (Datum = Gesehen-Datum)
- Dateinamen für Konzepte: Kleinbuchstaben mit Bindestrichen
- Bidirektionale Links sind Standard — nach jeder Aufnahme Rückverweise prüfen
- Wenn du dir beim Kategorisieren unsicher bist, frage nach
- Gute Antworten auf meine Fragen sollen als Konzept-Seiten ins Wiki zurückfließen
- **Kontaminierungsrisiko beachten**: Zusammenfassungen immer gegen das Transkript prüfbar halten — jede Kernaussage muss ihre Quelle haben

## Skalierung

Wenn das Wiki über ~100 Video-Seiten wächst und `index.md` unhandlich wird:
- **qmd** (`npm install -g @tobilu/qmd`): Semantische Suche über alle Seiten
- **jDocMunch** (`pip install jdocmunch-mcp`): Nur den relevanten Abschnitt laden statt ganze Seite
- Beide als MCP-Server in Claude Code integrierbar
````

## Verwandte Seiten

- [[claude-md-design]] — Die 6 Designprinzipien — stammen aus der Praxis mit dieser Vorlage
- [[drei-ebenen-architektur]] — Die Vorlage implementiert Ebene 3 (Schema)
- [[kompilierungs-metapher]] — Videos = Quellcode; Wiki = kompiliertes Binary
- [[ingest-workflow]] — Basis des Aufnahme-Workflows
- [[query-templates]] — Die 6 Abfragetypen, hier auf YouTube-Inhalte angewendet
- [[kontaminierungsrisiko]] — Warum Quellenangaben in Video-Seiten Pflicht sind
- [[skalierungsgrenzen]] — Was tun wenn das Wiki mit dem Verlauf wächst
- [[seitenklassifikation]] — Das Zeitlos/Gemischt/Zeitgenössisch-System
- [[qmd]] — Skalierungslösung für große Video-Wikis
- [[jdocmunch]] — Sektionsbasierter Zugriff als Alternative zu vollem Laden
- [[claude-md-software]] — Schwester-Vorlage für Software-Projekte
- [[claude-md-legacy-forensik]] — Schwester-Vorlage für Legacy-Analyse
- [[claude-md-rezepte-ernaehrung]] — Schwester-Vorlage für Rezepte-Wikis

---

[Wiki-Index](../index.md)
