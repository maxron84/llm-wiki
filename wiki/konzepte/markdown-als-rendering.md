---
date: 2026-08-11
type: konzept
tags: [konzept, schema, automatisierung]
status: active
---

# Markdown als Rendering

**Zusammenfassung**: Im SQL-Betrieb verliert der Markdown-Vault seinen Status als Original und wird zur generierten Ansicht. Der Export ist deshalb kein Nebenprodukt, sondern Architekturbestandteil — er stellt Obsidian, lesbare Git-Diffs und die Modellunabhängigkeit wieder her, die sonst verloren gingen.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Die Umkehrung der Schreibrichtung

In [Variante B der SQL-Wiki-Architektur](sql-wiki-architektur.md) hält die Datenbank den Inhalt. Der Vault entsteht daraus per Export.

> „Der Markdown-Vault verliert seinen Status als Original und wird zum **Rendering**. Für Obsidian, für die Graphansicht, für Lesbarkeit ohne Werkzeuge, für die Modellunabhängigkeit — alles bleibt erhalten, nur die Schreibrichtung dreht sich um." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Ohne diesen Export wäre der Umbau ein Rückschritt, weil er die Eigenschaft aufgäbe, die das Muster überhaupt robust macht.

## Drei Eigenschaften, die der Export rettet

1. **Obsidian** — Graphansicht, Backlinks, [Dataview](../werkzeuge/dataview.md), Lesen ohne Werkzeugkette. → [obsidian](../werkzeuge/obsidian.md)

2. **Git** — lesbare Diffs. Eine SQLite-Datei ist ein Binärblob; ein Commit über 40 geänderte Markdown-Dateien ist nachvollziehbar. Praktikabel: `wiki.db` in `.gitignore`, der exportierte Vault ist das, was versioniert wird. Zusätzlich ein `.sql`-Dump als Textdatei, falls die Datenbank selbst rekonstruierbar bleiben soll. → [zusammenarbeit-git](../anleitungen/zusammenarbeit-git.md)

3. **Modellunabhängigkeit** — die Eigenschaft, dass das Wiki jeden Agenten überlebt. Sie hängt daran, dass am Ende lesbares Markdown steht, nicht an der Speicherform dazwischen. → [modellunabhaengigkeit](modellunabhaengigkeit.md)

## Der Export ist deterministisch

| Was | Woraus |
|---|---|
| Ordner | `pages.type` |
| Dateiname | `pages.slug` |
| Frontmatter | Spalten von `pages` und `page_tags` |
| Seiteninhalt | `sections` nach `ord` konkateniert |
| „Verwandte Seiten" | `links` |
| `index.md` | `pages.summary` |
| `log.md` | `log`, `ORDER BY at DESC` |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md) → [wiki-datenbankschema](wiki-datenbankschema.md)

Es gibt keinen Entscheidungsspielraum und damit nichts, was das Modell falsch machen könnte. Genau deshalb entfallen die [Lint-Prüfungen](lint-pruefung.md) `TYPE / FOLDER`, `PAGES NOT IN INDEX` und `LOG ORDER` ersatzlos: Der Ordner *ist* der Typ, der Index *ist* die Summenspalte, die Log-Reihenfolge *ist* die Sortierung.

## Was bewusst fehlt: der Rückweg

Der Import zurück in die Datenbank ist ausdrücklich **nicht** vorgesehen.

Wer im Export-Vault eine Datei in Obsidian ändert, verliert die Änderung beim nächsten Export. Ein Rückimport wäre technisch möglich (Datei parsen, Sektionen vergleichen, Konflikte melden), aber Konfliktauflösung ist der aufwendigste Teil jedes Sync-Systems. Vorerst gilt: **Der Export-Vault ist schreibgeschützt zu behandeln.** (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Das hat eine Folge für den Arbeitsablauf, die man kennen muss: Der Mensch kuratiert weiterhin Quellen und stellt Fragen, aber er korrigiert nicht mehr direkt im Markdown. Korrekturen laufen über dieselbe [Werkzeugschicht](werkzeugschicht.md) wie alles andere — oder über ein Skript gegen die Datenbank.

Ebenfalls betroffen: **mehrere Geräte.** SQLite über Cloud-Ordner zu synchronisieren ist ein bekannter Weg zu beschädigten Datenbanken. Bei Bedarf synchronisiert der exportierte Vault, während die Datenbank auf einem Rechner bleibt — oder es wird doch [PostgreSQL](../werkzeuge/postgresql.md) mit Netzwerkzugriff.

## Verwandte Seiten

- [sql-wiki-architektur](sql-wiki-architektur.md) — Warum Variante B ohne Export ein Rückschritt wäre
- [wiki-datenbankschema](wiki-datenbankschema.md) — Die Spalten, aus denen der Export entsteht
- [modellunabhaengigkeit](modellunabhaengigkeit.md) — Die Eigenschaft, die der Export sichert
- [lint-pruefung](lint-pruefung.md) — Prüfungen, die durch die Generierung entfallen
- [werkzeugschicht](werkzeugschicht.md) — Der einzig verbleibende Schreibweg
- [obsidian](../werkzeuge/obsidian.md) — Der Hauptabnehmer des Renderings
- [zusammenarbeit-git](../anleitungen/zusammenarbeit-git.md) — Was versioniert wird
- [kompilierungs-metapher](kompilierungs-metapher.md) — Rohquellen = Quellcode, Wiki = Binary; hier kommt eine dritte Stufe dazu
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
