---
date: 2026-04-24
type: vorlage
tags: [vorlage, schema]
status: active
---

# CLAUDE.md-Vorlage: Rezepte- und Ernährungs-Wiki

**Zusammenfassung**: Eine CLAUDE.md-Vorlage für ein persönliches Wissenswiki über Kochen und Ernährung — vernetzt Rezepte, Zutaten, Techniken, Küchen und Ernährungskonzepte zu einer dauerhaft abfragbaren Wissensbasis.
**Quellen**: Abgeleitet aus [[llm-wiki-muster]], [[drei-ebenen-architektur]], [[ingest-workflow]], [[query-templates]], [[kompilierungs-metapher]] und [[claude-md-design]]
**Zuletzt aktualisiert**: 2026-04-24

---

## Zweck

Diese Vorlage implementiert die dritte Ebene der [[drei-ebenen-architektur]] — das **Schema** — für ein spezifisches Quellmaterial: Rezepte, Kochbücher, Food-Science-Artikel und eigene Küchenexperimente.

Das Grundproblem: Rezepte sammeln sich in Lesezeichen, Apps und Kochbüchern — aber das Wissen bleibt isoliert. Warum funktioniert diese Technik? Was haben diese drei Rezepte gemeinsam? Welche Zutaten kann man womit ersetzen? Das Wiki beantwortet diese Fragen, weil es Wissen **beim Ingest vernetzt** statt bei jeder Frage neu zu suchen.

## Benutzung

1. Kopiere **nur den Inhalt** des Vorlagenblocks unten (ohne die ```````` Fence-Markierungen) als `CLAUDE.md` ins Wurzelverzeichnis deines Projekts. Der 4-Backtick-Fence dient hier nur als Darstellung — in deinem Projekt muss der Inhalt als direkter Top-Level-Markdown stehen (siehe [[claude-md-design|Designprinzip 1]]).
2. Ersetze die `{{PLATZHALTER}}`
3. Lege geclippte Rezepte, Kochbuch-Notizen oder eigene Aufzeichnungen in `raw/` bzw. `clippings/` ab
4. Sage Claude: „Es gibt neue Quellen — bitte verarbeite sie"

## Besonderheiten gegenüber dem allgemeinen Wiki-Muster

| Aspekt | Allgemeines Wiki | Rezepte- und Ernährungs-Wiki |
|---|---|---|
| **Haupteinheit** | Quelle/Konzept | Rezept |
| **Zusätzliche Entitäten** | — | Zutat, Technik, Küche, Saison |
| **Metadaten** | Datum, Typ | + Küche, Diät-Flags, Schwierigkeit, Zeit, Saison |
| **Strukturierte Daten** | Fließtext | Zutaten als Liste, Schritte nummeriert |
| **Persönliche Ebene** | Quellenbasiert | + Eigene Anpassungen, Experimente, Bewertungen |
| **Querverweise** | Konzept ↔ Konzept | Rezept ↔ Technik ↔ Zutat ↔ Küche |

## Modell-Kompatibilität

| Funktion | 7B | 30B | Cloud (Sonnet/Opus) |
|---|---|---|---|
| Rezeptseiten (Zutaten, Schritte) | ✅ | ✅ | ✅ |
| Technikseiten | ❌ | ✅ | ✅ |
| Zutaten-Profile & Substitutions-Tabelle | ❌ | ✅ | ✅ |
| „Warum es funktioniert" (Food Science) | ❌ | ⚠️ | ✅ |
| Küchenübersichten, Vernetzung | ❌ | ⚠️ mit qmd | ✅ |
| Eigene Anpassungen verfolgen | ⚠️ einfach | ✅ | ✅ |

**7B** (z.B. Llama 3.2 8B, Mistral 7B): Nur Rezeptseiten — strukturierte Extraktion von Zutaten, Mengen und Schritten liegt im Stärkebereich kleiner Modelle. Technik- und Konzeptseiten weglassen. Eigene Anpassungen nur als einfache Liste, keine vergleichende Analyse.

**30B** (z.B. Qwen3.6:27b, Qwen3.6:35b): Vollständiger Funktionsumfang bis auf Food-Science-Synthese. Moderne 30B-Modelle wie Qwen3.6 haben 256K Kontextfenster — [[qmd]] erst bei sehr großen Sammlungen nötig.

**Cloud** (Claude Sonnet/Opus, GPT-4o): Voller Funktionsumfang inklusive Food-Science-Erklärungen und Substitutions-Analyse über die gesamte Sammlung.

## Ordnerstruktur

```
raw/                      -- Unveränderliche Quellen (NIEMALS bearbeiten)
  buecher/                -- Kochbuch-Notizen, eingescannte Seiten, PDFs
  notizen/                -- Eigene Rezeptexperimente, Anpassungen, Bewertungen
  unverarbeitet/          -- Inbox: noch nicht kategorisierte Quellen

clippings/                -- Web-Clipper-Exporte (Rezeptseiten, Food-Blogs — NIEMALS bearbeiten)

wiki/                     -- Von Claude gepflegtes Wiki
  index.md
  log.md
  rezepte/                -- Eine Seite pro Rezept
  zutaten/                -- Zutaten-Profile (Eigenschaften, Saison, Substitutionen)
  techniken/              -- Kochtechniken und -methoden
  konzepte/               -- Food Science, Ernährungsprinzipien, Geschmackschemie
  kuechen/                -- Nationale und regionale Küchen
  personen/               -- Köche, Autoren, Quellen-Personen
```

## Designhinweise

Beim Einsatz dieser Vorlage die [[claude-md-design|CLAUDE.md-Designprinzipien]] beachten:

1. **Fence entfernen**: Beim Kopieren in ein neues Projekt den 4-Backtick-Wrapper entfernen — nur den Inhalt als direkte Prosa ins Projekt-Root kopieren.
2. **Diät-Flags konsistent halten**: Eine geschlossene Liste erlaubter Werte für `diät`-Tags definieren und nie davon abweichen — sonst funktionieren Dataview-Abfragen nicht.
3. **Frontmatter-Entscheidung**: Die Vorlage nutzt [[yaml-frontmatter]] für Rezeptseiten. Wenn Dataview nicht genutzt wird, kann Frontmatter vereinfacht werden — aber konsistent bleiben.
4. **Keine toten Links**: Alle `{{PLATZHALTER}}` müssen ausgefüllt oder entfernt werden.
5. **Regelmäßig reviewen**: Nach ~30 aufgenommenen Rezepten die CLAUDE.md auf neue Muster prüfen (z.B. neue Quelltypen oder Küchen, die eigene Extraktionsstrategien brauchen).

## Vorlage

````markdown
# CLAUDE.md — Rezepte- und Ernährungs-Wiki

> **Anleitung**: Kopiere diese Datei als `CLAUDE.md` ins Wurzelverzeichnis deines Projekts.
> Ersetze alle `{{PLATZHALTER}}`. Entferne diesen Block vor der produktiven Nutzung.

---

# CLAUDE

## Zweck

Dieses Wiki macht mein Kochwissen dauerhaft abfragbar und vernetzt. Claude verarbeitet Rezepte, Kochbücher und Ernährungsartikel zu einem vernetzten Wissensgraph — Rezepte verlinken auf Techniken, Techniken auf Zutaten, Zutaten auf Küchen. Ich kuratiere die Quellen, Claude schreibt und pflegt das Wiki.

**Thematischer Fokus**: {{z.B. "mediterrane Küche, Fermentation, Brotbacken" oder "vegane Ernährung" — oder "alles, was ich koche"}}

## Ordnerstruktur

```
raw/
  buecher/           -- Kochbuch-Notizen und PDFs
  notizen/           -- Eigene Experimente, Anpassungen, Bewertungen
  unverarbeitet/     -- Inbox: noch nicht kategorisiert

clippings/           -- Web-Clipper-Exporte (Rezeptseiten, Blogs)

wiki/
  index.md           -- Inhaltsverzeichnis
  log.md             -- Änderungsprotokoll
  rezepte/           -- Eine Seite pro Rezept
  zutaten/           -- Zutaten-Profile
  techniken/         -- Kochtechniken
  konzepte/          -- Food Science, Ernährungsprinzipien
  kuechen/           -- Nationale/regionale Küchen
  personen/          -- Köche, Autoren
```

## Aufnahme-Workflow

Wenn neue Quellen in `raw/` oder `clippings/` hinzugefügt werden:

1. **Lies** das vollständige Dokument
2. **Identifiziere den Quelltyp**: Rezept / Kochbuch-Abschnitt / Food-Science-Artikel / Eigenexperiment
3. **Besprich** die wichtigsten Erkenntnisse und frage nach der gewünschten Tiefe:
   - **Minimal** — Nur die Rezeptseite
   - **Mittel** — Rezeptseite + Konzept-/Technikseiten für neue Ideen *(Standard)*
   - **Vollständig** — Alles oben + Zutaten-Profile, Küchenübersicht, Personenseiten
4. **Erstelle die Rezeptseite** in `wiki/rezepte/` (Format siehe unten)
5. **Erstelle oder aktualisiere** Technik-, Zutaten- und Konzeptseiten
6. **Erstelle oder aktualisiere Küchenübersicht** in `wiki/kuechen/` (falls neue Küche)
7. **Verknüpfe** mit `[[wiki-links]]` (Techniken, Zutaten, verwandte Rezepte)
8. **Aktualisiere** `wiki/index.md` und `wiki/log.md`

## Extraktionsstrategien nach Quelltyp

| Typ | Fokus beim Extrahieren |
|---|---|
| **Rezeptseite / Blog** | Zutaten, Schritte, Technik-Begründungen ("warum funktioniert das?"), Variationen |
| **Kochbuch-Abschnitt** | Rezept + übergeordnete Philosophie des Autors, Grundprinzipien der Küche |
| **Food-Science-Artikel** | Prinzip, praktische Anwendung in Rezepten, Auswirkung auf Geschmack/Textur |
| **Eigenexperiment** | Was wurde verändert, Ergebnis, Bewertung, Lehre für künftige Versuche |
| **Ernährungs-Artikel** | Nährstoffprofil, praktische Empfehlungen, Verbindung zu konkreten Zutaten |

## Seitenformate

### Rezept-Seite (`wiki/rezepte/rezeptname.md`)

```markdown
---
date: YYYY-MM-DD
type: rezept
tags: [tag1, tag2]
status: active
küche: "{{z.B. Italienisch, Japanisch, Levantinisch}}"
diät: [{{z.B. vegan, glutenfrei, milchfrei}}]
schwierigkeit: "{{einfach | mittel | anspruchsvoll}}"
zeit: "{{z.B. 30 min | 2 h | über Nacht}}"
saison: [{{z.B. Frühling, Sommer, Herbst, Winter, ganzjährig}}]
---

# Rezeptname

**Zusammenfassung**: Ein bis zwei Sätze — was ist das Besondere an diesem Rezept?
**Quelle**: [[kanalname]] / (Quelle: clippings/dateiname.md)
**Zuletzt aktualisiert**: YYYY-MM-DD

---

## Zutaten

- Menge Zutat — Anmerkung (z.B. "zimmerwarm", "grob gehackt")
- ...

## Zubereitung

1. Schritt 1
2. Schritt 2
...

## Warum es so funktioniert

Kurze Food-Science-Erklärung: Warum ergibt diese Kombination/Technik das Ergebnis?
(Quelle: ...)

## Eigene Anpassungen

- Änderung 1 — Ergebnis
- Verbesserungsidee

## Verwandte Techniken

- [[technik-name]] — wie sie in diesem Rezept vorkommt

## Verwandte Rezepte

- [[anderes-rezept]] — warum verwandt (gleiche Technik, ähnliche Zutaten, selbe Küche)
```

### Technik-Seite (`wiki/techniken/technikname.md`)

```markdown
---
date: YYYY-MM-DD
type: konzept
tags: [technik]
status: active
---

# Technikname

**Zusammenfassung**: Was ist diese Technik und was bewirkt sie?
**Quellen**: Liste der Rezepte/Quellen, aus denen diese Seite schöpft
**Zuletzt aktualisiert**: YYYY-MM-DD

---

## Was und warum

Erklärung der Technik und des zugrunde liegenden Prinzips (Food Science).

## Wann einsetzen

In welchen Situationen bringt diese Technik den größten Vorteil?

## Häufige Fehler

- Fehler 1 — Symptom und Ursache
- ...

## Rezepte, die diese Technik nutzen

- [[rezept-1]]
- [[rezept-2]]
```

### Zutaten-Seite (`wiki/zutaten/zutatname.md`)

```markdown
---
date: YYYY-MM-DD
type: konzept
tags: [zutat]
status: active
saison: [{{z.B. Sommer, ganzjährig}}]
---

# Zutatname

**Zusammenfassung**: Kurzprofil: Geschmack, Textur, typischer Einsatz.
**Zuletzt aktualisiert**: YYYY-MM-DD

---

## Eigenschaften

Geschmack, Textur, Nährstoffprofil (falls relevant).

## Saison und Lagerung

Wann verfügbar, wie lagern.

## Substitutionen

| Statt | Nimm | Anmerkung |
|---|---|---|
| Diese Zutat | Alternative | Wann passt das? |

## Rezepte mit dieser Zutat

- [[rezept-1]]
```

## Fragenbeantwortung

Wenn ich eine Frage stelle:

1. Lies `wiki/index.md`, um relevante Seiten zu finden
2. Lies diese Seiten und fasse eine Antwort zusammen
3. Zitiere die spezifischen Rezept- oder Konzeptseiten
4. Wenn die Antwort nicht im Wiki steht, sage das klar
5. Wenn die Antwort wertvoll ist (z.B. eine Technik-Erklärung, ein Vergleich), speichere sie als neue Konzeptseite

## Benannte Abfragetypen

| Typ | Beispiel-Abfrage |
|---|---|
| **Synthese** | „Welches Prinzip verbindet alle meine Brotrezepte?" |
| **Lückensuche** | „Welche Techniken tauchen in Rezepten auf, haben aber keine eigene Seite?" |
| **Debatte** | „Wo gibt es widersprüchliche Anweisungen zu Technik X?" |
| **Output** | „Erstelle einen Wochenplan aus Rezepten, die ich bereits beherrsche" |
| **Gesundheit** | „Welche Rezepte sind mit keiner Technikseite verknüpft?" |
| **Persönliche Anwendung** | „Was sollte ich als nächstes ausprobieren, basierend auf meinen Lücken?" |

## Prüfung (Lint)

Wenn ich das Wiki prüfen lasse:

- Rezepte ohne Technik- oder Zutaten-Verlinkungen finden
- Techniken, die in Rezepten vorkommen, aber keine eigene Seite haben
- Widersprüche zwischen Rezepten bei derselben Technik
- Zutaten ohne Saison-Angabe
- Behauptungen ohne Quellenangabe markieren
- Alle Seiten auf Frontmatter und korrekte Tags prüfen

## Regeln

- Verändere **NIEMALS** etwas in `raw/` oder `clippings/` — Originalquellen bleiben unveränderlich
- Aktualisiere immer `wiki/index.md` und `wiki/log.md` nach Änderungen
- Dateinamen: Kleinbuchstaben mit Bindestrichen (`bruschetta-al-pomodoro.md`)
- Diät-Tags: Nur aus der geschlossenen Liste: `vegan`, `vegetarisch`, `glutenfrei`, `milchfrei`, `nussfrei`
- Eigene Anpassungen immer klar als solche markieren — nie mit der Originalquelle vermischen
- Wenn du dir bei der Kategorisierung unsicher bist, frage nach
- **Kontaminierungsrisiko**: Ernährungsbehauptungen (z.B. Nährwertangaben, gesundheitliche Effekte) immer gegen die Originalquelle prüfbar halten — jede Behauptung braucht eine Quellenangabe

## Skalierung

Wenn das Wiki über ~100 Rezeptseiten wächst und `index.md` unhandlich wird:
- **qmd** (`npm install -g @tobilu/qmd`): Semantische Suche über alle Seiten
- **jDocMunch** (`pip install jdocmunch-mcp`): Nur den relevanten Abschnitt laden
- Beide als MCP-Server in Claude Code integrierbar
````

## Verwandte Seiten

- [[drei-ebenen-architektur]] — Die Vorlage implementiert Ebene 3 (Schema)
- [[claude-md-design]] — 6 Designprinzipien, die beim Einsatz dieser Vorlage gelten
- [[kompilierungs-metapher]] — Rezepte = Quellcode; Wiki = kompiliertes Binary
- [[ingest-workflow]] — Basis des Aufnahme-Workflows
- [[query-templates]] — Die 6 Abfragetypen, hier auf Kochthemen angewendet
- [[kontaminierungsrisiko]] — Warum Quellenangaben bei Ernährungsbehauptungen Pflicht sind
- [[skalierungsgrenzen]] — Was tun wenn das Wiki mit dem Rezeptarchiv wächst
- [[qmd]] — Skalierungslösung für große Rezepte-Wikis
- [[jdocmunch]] — Sektionsbasierter Zugriff als Alternative zu vollem Laden
- [[yaml-frontmatter]] — Basis für Diät-Flags und Dataview-Abfragen
- [[claude-md-software]] — Schwester-Vorlage für Software-Projekte
- [[claude-md-youtube-verlauf]] — Schwester-Vorlage für YouTube-Verlauf

---

[Wiki-Index](../index.md)
