# CLAUDE

## LLM Wiki

Eine persönliche Wissensdatenbank, gepflegt von Claude.
Basierend auf Andrej Karpathys LLM-Wiki-Muster.

## Zweck

Dieses Wiki ist eine strukturierte, vernetzte Wissensdatenbank für das LLM-Wiki-Konzept.
Claude pflegt das Wiki. Der Mensch kuratiert Quellen, stellt Fragen und leitet die Analyse.

## Ordnerstruktur

```
raw/                  -- Quelldokumente (unveränderlich — niemals bearbeiten)
clippings/            -- Quelldokumente (unveränderlich — niemals bearbeiten)
wiki/                 -- Von Claude gepflegte Markdown-Seiten
wiki/index.md         -- Inhaltsverzeichnis
wiki/log.md           -- Chronologisches Änderungsprotokoll
wiki/konzepte/        -- Konzept- und Ideenseiten
wiki/quellen/         -- Zusammenfassungen der Quelldokumente
wiki/personen/        -- Personen und Autoren
wiki/werkzeuge/       -- Tools, Apps, Technologien
wiki/vorlagen/        -- CLAUDE.md-Vorlagen für andere Projekttypen
```

Neue Unterordner anlegen, wenn ein Thema drei oder mehr Seiten umfasst.

## Aufnahme-Workflow

Wenn der Benutzer eine neue Quelle zu **raw/** oder **clippings/** hinzufügt und dich bittet, sie aufzunehmen:

1. Lies das vollständige Quelldokument
2. Übersetze ins Deutsche (ohne Informationen inhaltlich zu verändern), wenn nötig
3. Bespreche die wichtigsten Erkenntnisse und frage nach der gewünschten Tiefe:
   - **Minimal** — Nur eine Zusammenfassungsseite
   - **Mittel** — Zusammenfassung + Konzeptseiten für die wichtigsten Ideen *(Standard)*
   - **Vollständig** — Alles inkl. einzelner Entitäten, Personen, Werkzeuge
4. Erstelle eine Zusammenfassungsseite in `wiki/quellen/`, benannt nach der Quelle
5. Erstelle oder aktualisiere Konzeptseiten für jede wichtige Idee oder Entität
6. Füge Standard-Markdown-Links `[seitenname](../kategorie/seitenname.md)` hinzu, um verwandte Seiten zu verknüpfen
7. Gruppiere Wiki-Seiten thematisch in Unterordner
8. Aktualisiere `wiki/index.md` mit neuen Seiten und einzeiligen Beschreibungen
9. Hänge einen Eintrag an `wiki/log.md` an (Format siehe unten)
10. Committe die neuen Clippings explizit mit `git add raw/ clippings/` — neue Dateien dort sind untracked und werden sonst nicht erfasst

Eine einzelne Quelle kann 10–15 Wiki-Seiten betreffen. Das ist normal.

## Seitenformat

Jede Wiki-Seite folgt dieser Struktur:

```markdown
---
date: YYYY-MM-DD
type: konzept | quelle | person | werkzeug
tags: [tag1, tag2]
status: active | draft | archived
---

# Seitentitel

**Zusammenfassung**: Ein bis zwei Sätze, die diese Seite beschreiben.
**Quellen**: Liste der Rohdateien, aus denen diese Seite schöpft.
**Zuletzt aktualisiert**: YYYY-MM-DD

---

Hauptinhalt mit klaren Überschriften und kurzen Absätzen.

Verlinke verwandte Konzepte mit Standard-Markdown `[seitenname](../kategorie/seitenname.md)` im Text.

## Verwandte Seiten

- [verwandtes-konzept-1](../kategorie/verwandtes-konzept-1.md)
- [verwandtes-konzept-2](../kategorie/verwandtes-konzept-2.md)

---

[Wiki-Index](../index.md)
```

Tags folgen einem Zweistufen-System:

- **Erster Tag = Seitentyp** (muss mit `type:` übereinstimmen):
  `konzept`, `quelle`, `person`, `werkzeug`, `vergleich`, `vorlage`, `anleitung`, `projekt`
- **Weitere Tags = Themen** (beliebig viele, aus diesem Vokabular wählen oder erweitern):
  `produktion`, `enterprise`, `skalierung`, `automatisierung`, `metrik`, `kosten`,
  `community`, `lokale-modelle`, `hardware`, `setup`, `schema`

Neue Themen-Tags anlegen, wenn kein bestehender Begriff passt — Konsistenz über den gesamten Vault wichtiger als Vollständigkeit dieser Liste.

## Log-Format

Einträge in `wiki/log.md` beginnen mit einem konsistenten Präfix:

```
## [YYYY-MM-DD HH:MM] ingest | Quellenname
## [YYYY-MM-DD HH:MM] query | Fragentitel
## [YYYY-MM-DD HH:MM] lint | Prüfung
```

So sind Einträge mit einfachen Unix-Werkzeugen parsierbar (z.B. `grep "^## \[" log.md | tail -5`).

## Zitierregeln

- Jede faktische Behauptung sollte ihre Quelldatei referenzieren
- Format: `(Quelle: dateiname.md)` nach der Behauptung
- Wenn zwei Quellen sich widersprechen, vermerke den Widerspruch ausdrücklich
- Wenn eine Behauptung keine Quelle hat, markiere sie als `(überprüfungsbedürftig)`

## Fragenbeantwortung

Wenn der Benutzer eine Frage stellt:

1. Lies `wiki/index.md`, um relevante Seiten zu finden
2. Lies diese Seiten und fasse eine Antwort zusammen
3. Zitiere spezifische Wiki-Seiten in der Antwort
4. Wenn die Antwort nicht im Wiki steht, sage das klar
5. Wenn die Antwort wertvoll ist, speichere sie als neue Wiki-Seite — gute Antworten sollen zurück ins Wiki fließen

## Prüfung

Wenn der Benutzer das Wiki prüfen lässt:

- Widersprüche zwischen Seiten finden
- Verwaiste Seiten finden (keine eingehenden Links — sichtbar als isolierte Knoten in Obsidians Graphansicht)
- Konzepte identifizieren, die erwähnt werden, aber keine eigene Seite haben
- Behauptungen markieren, die aufgrund neuerer Quellen veraltet sein könnten
- Prüfen, ob alle Seiten dem Seitenformat folgen (inkl. YAML-Frontmatter)
- Ergebnisse als nummerierte Liste mit Korrekturvorschlägen berichten

Empfehlung: Prüfung nach jeweils 5–10 neuen Quellen oder einmal im Monat.

## Regeln

- Verändere **niemals** etwas in `raw/` oder `clippings/`
- Aktualisiere immer `wiki/index.md` und `wiki/log.md` nach Änderungen
- Erstelle in der Fußzeile jeder Wiki-Seite einen Link zum Index
- Halte Seitennamen in Kleinbuchstaben mit Bindestrichen (z.B. `maschinelles-lernen.md`)
- Schreibe in klarer, einfacher Sprache — der Ton ist locker, freundlich und kollegial
- Wenn du dir unsicher bist, wie etwas kategorisiert werden soll, frage den Benutzer
- Diese Datei erklärt nur, wo was zu finden ist — Inhalt gehört ins Wiki, nicht hierhin
