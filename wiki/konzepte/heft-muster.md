---
date: 2026-05-18
type: konzept
tags: [konzept, schema, setup, automatisierung]
status: active
---

# Heft-Muster

**Zusammenfassung**: Schüler schreiben ihre Lösungen in lebende `.md`-Dateien (`heft/`), eine pro Konzept. Git-Commits nach jeder Review werden zur impliziten Lernhistorie — ohne dass Schüler oder Lehrer aktiv etwas tracken.
**Quellen**: Entwickelt aus der Praxis der KI-Lehrer-Vorlagen; kein einzelnes Quelldokument.
**Zuletzt aktualisiert**: 2026-05-18

---

## Das Problem

Hausaufgaben verschwinden. Sie werden abgegeben, korrigiert, weggeworfen. Was übrig bleibt ist eine Note — kein Bild davon, wie jemand gedacht hat, wie oft er an einem Konzept gerungen hat, wann er es wirklich verstanden hat.

Explizite Tracking-Systeme (Fortschrittsbalken, Lernprotokolle, Wiederholungsplaner) lösen das Problem nicht — sie erzeugen Overhead, werden vergessen oder geschönt.

## Die Idee

Statt im Chat zu antworten, schreibt der Schüler seine Lösungen in eine Markdown-Datei: `heft/bruchrechnen.md`. Eine Datei pro Konzept, über die gesamte Projektlaufzeit. Neue Einträge kommen unten dran. Der Schüler denkt dabei nicht ans Tracking — er schreibt einfach.

Nach jeder Session liest Claude die Datei, reviewt im Chat, und committet:

```
git add heft/bruchrechnen.md
git commit -m "heft: bruchrechnen — Review 2026-05-18"
```

Das ist alles. Die Git-Historie ist danach die Lerngeschichte.

## Was die Git-Historie erzählt

```
git log --format="%ci %s" -- heft/bruchrechnen.md
```

Das Ergebnis liest sich wie eine Landkarte:

- **Viele Commits kurz hintereinander** — das Konzept war schwierig, der Schüler kam immer wieder zurück
- **Lücke von drei Wochen** — das Thema wurde liegen gelassen (aus welchem Grund auch immer)
- **Zwei Commits kurz vor einer Prüfung** — Pauk-Session sichtbar ohne dass jemand das berichtet hat
- **Danach nichts mehr für Monate** — das Konzept sitzt

Kein einziges dieser Signale musste explizit erfasst werden.

## Was git diff zeigt

```
git diff HEAD~3 HEAD -- heft/bruchrechnen.md
```

Der erste Eintrag ist kurz, tastend, vielleicht noch fehlerhaft. Der letzte ist präzise, benutzt die richtigen Begriffe, erklärt den Gedankengang in eigenen Worten. Der Abstand zwischen diesen beiden Zuständen ist sichtbar — nicht als Note, sondern als echtes Denken das sich verändert hat.

## Abwesenheit als Signal

Dateien die lange nicht committed wurden sind genauso informativ wie aktive Dateien. Ein `heft/gleichungen.md` das seit acht Wochen keinen Commit gesehen hat ist ein klarer Hinweis: dieses Konzept braucht Wiederholung.

Deshalb prüft Claude zu Beginn jeder Session:

```
git log --format="%ci %s" -- heft/*.md
```

Konzepte die länger als 14 Tage nicht reviewed wurden werden als Wiederholungskandidaten gemeldet. Kein manueller Planer, kein Aufwand — der Scan läuft automatisch.

## Ehrliche Zeitstempel

Git-Timestamps lassen sich nicht sinnvoll fälschen. Wenn jemand drei Wochen nichts getan hat und dann kurz vor einem Review alles nachholt — der zeitliche Abstand zwischen den Commits bleibt sichtbar. Das System ist ehrlicher als jede selbstberichtete Lernzeit.

Das ist kein Kontrollmechanismus. Es ist ein Spiegel — nützlich für den Schüler selbst, um Muster im eigenen Lernen zu erkennen.

## Obsidian-Integration

Die `heft/`-Dateien sind normale Markdown und erscheinen im Obsidian-Graph. Jede Heft-Seite verlinkt auf ihre zugehörige Konzeptseite in `wiki/konzepte/` — und die Konzeptseite verlinkt zurück. So verbinden sich Schülerarbeit und Wissensbasis bidirektional.

## Abgrenzung zu ähnlichen Konzepten

| | Heft-Muster | Spaced Repetition (Anki) | Fortschrittsbalken |
|---|---|---|---|
| Overhead für Schüler | Keiner — normales Schreiben | Karteikarten pflegen | Nichts tun (automatisch) |
| Datenbasis | Git-Commits | Anki-Datenbank | System-Logs |
| Was gemessen wird | Wann und wie oft gearbeitet | Wie gut geantwortet | Ob etwas angesehen wurde |
| Ehrlichkeit | Hoch (Timestamps unmanipulierbar) | Mittel (muss genutzt werden) | Niedrig (Anwesenheit ≠ Lernen) |
| Inhalt sichtbar | Ja — Diff zeigt Denken | Nein | Nein |

Das Heft-Muster ist kein Ersatz für Spaced Repetition — es ist eine einfachere Alternative für den KI-Lehrer-Kontext, die ohne zusätzliche Tools auskommt.

## Verwandte Seiten

- [claude-md-lehrer](../vorlagen/claude-md-lehrer.md) — Vorlage die heft/ implementiert (Phase 2: Schüler arbeitet)
- [claude-md-nachhilfe](../vorlagen/claude-md-nachhilfe.md) — Vorlage die heft/ implementiert (Aufgaben-Workflow)
- [lernschleifen](lernschleifen.md) — Drei formale Schleifen: täglich erfassen, wöchentlich reviewen, graduieren
- [kompilierungs-metapher](kompilierungs-metapher.md) — heft/ = Rohquellen des Schülers; wiki/konzepte/ = kompiliertes Verständnis
- [taeglicher-workflow](taeglicher-workflow.md) — Session-Öffnung und -Abschluss als Protokoll

---

[Wiki-Index](../index.md)
