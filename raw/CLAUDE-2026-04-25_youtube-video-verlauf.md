# CLAUDE

## Zweck

Dieses Wiki macht meinen YouTube-Verlauf dauerhaft abfragbar. Claude kompiliert gesehene Videos zu einem vernetzten Wissensgraph — einmal verarbeitet, beliebig oft günstig abgefragt. Ich kuratiere die Quellen und stelle Fragen. Claude schreibt und pflegt das Wiki.

Das Grundproblem: Man schaut ein interessantes Video, nimmt etwas mit — und eine Woche später ist es vergessen. Statt jedes Video neu abzurufen, wenn man eine Frage hat, wird es einmal kompiliert und bleibt dauerhaft abfragbar.

**Thematischer Fokus**: Alles — kein thematischer Filter (Softwareentwicklung, KI, IT-Sicherheit, Gesellschaft & Politik, Psychologie)

## Ordnerstruktur

```
clippings/         -- Unveränderliche Quellen (Obsidian Web Clipper Exporte, NIEMALS bearbeiten)
raw/               -- Rohtexte und Primärquellen, die nicht per Web Clipper erfasst wurden
                      (z.B. Manifeste, Transkripte, kopierte Textdokumente)

wiki/
  index.md         -- Inhaltsverzeichnis
  log.md           -- Änderungsprotokoll
  ki/              -- KI und Sprachmodelle
  tech/            -- Softwareentwicklung und Karriere
  sicherheit/      -- IT-Sicherheit
  gesellschaft/    -- Gesellschaft, Politik, Wissenschaft
  psychologie/     -- Psychologie und Sexualität
  personen/        -- Sprecher, Gäste, Autoren
  output/          -- Synthetisierte Outputs (betrachter.md, ...)
```

## Quellen beschaffen

Quellen werden per **Obsidian Web Clipper** erzeugt und landen automatisch in `clippings/`. Der Clipper extrahiert Titel, URL, Beschreibung und — bei YouTube — das Transkript. Das erzeugte Frontmatter (`title`, `source`, `author`, `published`, `created`, `description`, `tags: [clippings]`) bleibt unverändert.

Alternativ: Transkript manuell via YouTube → Video → `...` → *Transkript anzeigen* kopieren und als `.md` ablegen.

Für Primärquellen, die nicht als YouTube-Clipping vorliegen (Manifeste, Originaltexte, kopierte Dokumente): als `.txt` oder `.md` in `raw/` ablegen. Zitierformat in Wiki-Seiten: `raw/dateiname.txt`.

## Aufnahme-Workflow

Wenn neue Quellen in `clippings/` hinzugefügt werden:

1. **Lies** das vollständige Dokument
2. **Identifiziere den Quelltyp**: Transkript / Beschreibung / Notizen / Playlist-Dump
3. **Übersetze** ins Deutsche, wenn die Quelle in einer anderen Sprache ist, ohne Informationen inhaltlich zu verändern
4. **Besprich** die wichtigsten Erkenntnisse und frage nach der gewünschten Tiefe:
   - **Minimal** — Nur eine Video-Zusammenfassungsseite
   - **Mittel** — Video-Seite + Konzeptseiten für die wichtigsten Ideen *(Standard)*
   - **Vollständig** — Alles oben + Personen- und Kanalseiten
5. **Erstelle die Video-Seite** im passenden thematischen Unterordner (`wiki/tech/`, `wiki/gesellschaft/` etc.)
6. **Erstelle oder aktualisiere Konzeptseiten** im gleichen thematischen Unterordner
7. **Erstelle oder aktualisiere Personenseite** in `wiki/personen/` (falls neu oder relevant)
8. **Verknüpfe** mit Markdown-Links `[text](pfad.md)` — verwandte Videos, Konzepte, Sprecher
9. **Aktualisiere** `wiki/index.md` und `wiki/log.md`
10. **Aktualisiere `wiki/output/betrachter.md`** — prüfe, ob sich Cluster-Tiefen, Wissenslandkarte, Gravitationszentrum oder wiederkehrende Motive verschoben haben. Prüfe zusätzlich, ob die neue Quelle Signale für das **Persönliche Profil** liefert (Beruf, Wohnort, Sprachen etc.) — nur eintragen, was sich aus Wiki-Content belastbar ableiten lässt; leer ist besser als geraten. Dieser Schritt ist verpflichtend, nicht optional.
11. **Prüfe weitere Output-Dokumente** — Bei prägnanten, erinnerungswürdigen Zitaten: in `wiki/output/zitate.md` eintragen. Bei Fragen, die das Video aufwirft, aber nicht beantwortet: in `wiki/output/offene-fragen.md` ergänzen.

Eine einzelne Quelle kann 10–15 Wiki-Seiten betreffen. Das ist normal.

## Extraktionsstrategien nach Video-Typ

Verschiedene Video-Formate verlangen verschiedene Extraktionsschwerpunkte:

| Typ | Fokus beim Extrahieren |
|---|---|
| **Tutorial** | Schritte, Befehle, Voraussetzungen, häufige Fehler |
| **Vortrag / Talk** | Hauptthese, Argumente, Daten, Zitate |
| **Interview** | Meinungen des Gastes, Widersprüche zum Moderator, Empfehlungen |
| **Essay / Essay-Video** | Kernargument, Schlussfolgerung, kritisierte Alternativen |
| **Review / Vergleich** | Kriterien, Bewertungen, Empfehlung und für wen |
| **Dokumentation** | Fakten, Chronologie, Personen, Quellen |

## Seitenformate

### Video-Seite (`wiki/THEMA/kurzname.md`)

```markdown
# Videotitel

**Zusammenfassung**: Ein bis zwei Sätze, was dieses Video vermittelt.
**Quellen**: `clippings/dateiname.md`
**Zuletzt aktualisiert**: YYYY-MM-DD
**Klassifikation**: Zeitlos | Gemischt | Zeitgenössisch

---

## Rahmendaten

- **Format**: YouTube-Video / Podcast / Vortrag
- **Autor**: [Name](../personen/name.md)
- **Veröffentlicht**: YYYY-MM-DD
- **Länge**: ca. X Minuten

## [Inhaltliche Abschnitte]

## Verwandte Seiten

- [Konzept oder Video](pfad.md) — warum verwandt

---

[← Wiki-Index](../index.md)
```

### Konzept-Seite (`wiki/THEMA/konzeptname.md`)

```markdown
# Konzeptname

**Zusammenfassung**: Ein bis zwei Sätze.
**Quellen**: `clippings/dateiname.md`
**Zuletzt aktualisiert**: YYYY-MM-DD
**Klassifikation**: Zeitlos | Gemischt | Zeitgenössisch

---

Hauptinhalt: Was ist dieses Konzept? Wie erklären verschiedene Videos es?
Wo gibt es Widersprüche zwischen Videos?

## Verwandte Seiten

- [Anderes Konzept](anderes-konzept.md)
- [Video, das das erklärt](video.md)

---

[← Wiki-Index](../index.md)
```

### Personen-Seite (`wiki/personen/name.md`)

```markdown
# Name

**Zusammenfassung**: Wer ist das? Stil und Themenspektrum in einem Satz.
**Quellen**: `clippings/dateiname.md`
**Zuletzt aktualisiert**: YYYY-MM-DD
**Klassifikation**: Zeitlos | Gemischt | Zeitgenössisch

---

## Profil

Kurze Beschreibung: Hintergrund, Plattformen, warum relevant.

## Themenspektrum

- Thema 1
- Thema 2

## Videos im Wiki

| Datum | Titel | Thema |
|---|---|---|
| YYYY-MM-DD | [Videotitel](../thema/seite.md) | Kurzbeschreibung |

## Verwandte Seiten

- [Verwandtes Konzept](../thema/konzept.md)

---

[← Wiki-Index](../index.md)
```

## Klassifikation

Jede Seite trägt ein `**Klassifikation**:`-Feld direkt nach `**Zuletzt aktualisiert**`. Es hat genau einen der drei Werte:

| Wert | Bedeutung | Typische Seiten |
|---|---|---|
| **Zeitlos** | Informationswert verfällt nicht — Konzepte, Frameworks, historische Analysen, psychologische Modelle | JLP-Konzepte, evolutionspsychologische Seiten, klassische Software-Engineering-Prinzipien |
| **Gemischt** | Zeitloser Kern, aber zeitgebundene Daten oder Beispiele — braucht gelegentliche Überprüfung | Demographieseiten mit konkreten Zahlen, Vorfeld-Strategie, KI-Überblicksseiten |
| **Zeitgenössisch** | Wert an einen spezifischen Moment gebunden — veraltet mit Ereignissen, Produkten oder Akteuren | Reaktionsvideos, politische Personen, aktuelle Tech-Feature-Kritiken |

**Pflegeregeln:**
- Jede neu erstellte Seite bekommt sofort eine Klassifikation.
- Bei jeder Inhaltsaktualisierung kurz prüfen, ob die Klassifikation noch stimmt — und ggf. anpassen.
- `wiki/output/betrachter.md` ist eine Ausnahme: kein Klassifikationsfeld (lebendes Meta-Dokument).
- Im Lint-Check: Seiten ohne `**Klassifikation**:` melden.

## Zitierregeln

- Jede faktische Behauptung sollte ihre Quelldatei referenzieren
- Verwende das Format `(Quelle: dateiname.md)` nach der Behauptung
- Wenn zwei Quellen sich widersprechen, vermerke den Widerspruch ausdrücklich
- Wenn eine Behauptung keine Quelle hat, markiere sie als überprüfungsbedürftig
- **Kontaminierungsrisiko beachten**: Zusammenfassungen immer gegen das Transkript/die Notizen prüfbar halten — jede Kernaussage muss ihre Quelle haben

## Fragenbeantwortung

Wenn ich eine Frage stelle:

1. Lies `wiki/index.md`, um relevante Seiten zu finden
2. Lies diese Seiten und fasse eine Antwort zusammen
3. Zitiere die spezifischen Videos oder Konzeptseiten
4. Wenn die Antwort nicht im Wiki steht, sage das klar
5. Wenn die Antwort wertvoll ist, speichere sie als neue Konzept-Seite

### Benannte Abfragetypen

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
- Video-Seiten ohne Personenseite für den Autor
- Behauptungen ohne Quellenangabe markieren
- Prüfe, ob alle Seiten dem obigen Seitenformat folgen
- Seiten ohne `**Klassifikation**:`-Feld melden
- Berichte die Ergebnisse als nummerierte Liste mit Korrekturvorschlägen

## Vernetzung & Konsistenzpflege

Das Wiki ist nicht nur ein Archiv einzelner Videos — es ist die Datengrundlage für die fortlaufende Analyse des Betrachters (`wiki/output/betrachter.md`). Diese Analyse ist nur so belastbar wie die Vernetzung der Artikel untereinander. Fehlende oder falsch gesetzte Verbindungen erzeugen falsche Rückschlüsse — über Interessen, Positionen und Muster, die vielleicht gar nicht vorhanden sind.

**Bidirektionale Links sind Standard**: Wenn Seite A auf Seite B verweist, verweist Seite B in aller Regel zurück. Ausnahmen sind erlaubt, müssen aber thematisch plausibel begründbar sein — keine erzwungenen Links.

**Neue Seiten rückwärts integrieren**: Nach jeder Aufnahme explizit prüfen, welche bestehenden Seiten einen Rückverweis auf die neue Seite verdient hätten. Nicht nur vorwärts verlinken.

**Das Betrachter-Profil mitpflegen**: Nach jeder Aufnahme kurz prüfen, ob sich Themengewichtungen, Grundpositionen oder wiederkehrende Motive verschoben haben. Wenn ja, `wiki/output/betrachter.md` entsprechend anpassen — Cluster, Lücken, Gravitationszentrum. Das **Persönliche Profil** am Dokumentanfang wird ebenfalls mitgeprüft: Felder werden ausschließlich aus Wiki-Content und Clippings abgeleitet — kein Raten, kein Verwenden externer Quellen (E-Mail, Git-Metadaten o.ä.). Ein leeres Feld ist besser als eine Fehlannahme.

**Regelmäßige Audits**: `python3 ~/.claude/scripts/wiki_lint.py` nach größeren Aufnahmephasen ausführen. Tote Links und Waisen sind Zeichen unvollständiger Integration und werden behoben, nicht ignoriert.

## Regeln

- Verändere **NIEMALS** etwas in `clippings/` — Originalquellen bleiben unveränderlich
- Aktualisiere immer `wiki/index.md` und `wiki/log.md` nach Änderungen
- Erstelle in der Fußzeile jeder Wiki-Seite einen Link zur Wiki-Index-Datei: `[← Wiki-Index](../index.md)`
- Dateinamen: Kleinbuchstaben mit Bindestrichen (`konzept-name.md`)
- Thematische Zuordnung: Verwende den passenden Unterordner (`ki/`, `tech/`, `gesellschaft/`, `psychologie/`, `sicherheit/`); Personen immer in `personen/`
- Schreibe in klarer, einfacher Sprache. Der Ton ist locker, freundlich und kollegial
- Wenn du dir beim Kategorisieren unsicher bist, frage nach
- Gute Antworten auf meine Fragen sollen als Konzept-Seiten ins Wiki zurückfließen

## Skalierung

Wenn das Wiki über ~100 Video-Seiten wächst und `index.md` unhandlich wird:
- **qmd** (`npm install -g @tobilu/qmd`): Semantische Suche über alle Seiten
- **jDocMunch** (`pip install jdocmunch-mcp`): Nur den relevanten Abschnitt laden statt ganze Seite
- Beide als MCP-Server in Claude Code integrierbar
