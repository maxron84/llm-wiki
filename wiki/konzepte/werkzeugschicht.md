---
date: 2026-08-11
type: konzept
tags: [konzept, schema, lokale-modelle, automatisierung]
status: active
---

# Werkzeugschicht: Das Modell schreibt niemals SQL

**Zusammenfassung**: Die eine Regel, ohne die der SQL-Umbau in sich zusammenfällt. Statt freiem Datenbankzugriff bekommt das Modell sechs enge, flache, typisierte Werkzeuge. Alles Mechanische verantwortet die Vermittlungsschicht; das Modell trifft ausschließlich Bedeutungsentscheidungen.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Warum kein freier SQL-Zugriff

> „Ein Modell, das an Dateipfaden scheitert, schreibt kein korrektes SQL — und im Fehlerfall ist der Schaden nicht eine kaputte Datei, sondern ein `UPDATE` ohne `WHERE`." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Alles, was die [SQL-Wiki-Architektur](sql-wiki-architektur.md) gewinnt, hängt daran, dass ungültige Zustände unerreichbar sind. Freier SQL-Zugriff gibt genau die Freiheitsgrade zurück, die der Umbau entfernen sollte. Der Bezug zum [read-only-guard](read-only-guard.md) ist direkt: Beschränkung ist hier kein Misstrauen, sondern Konstruktionsprinzip.

## Die sechs Werkzeuge

| Werkzeug | Parameter | Rückgabe |
|---|---|---|
| `search` | `query`, `limit` | Sektions-IDs + Seitenzusammenfassungen |
| `get_section` | `section_id` | Überschrift + Text |
| `get_page_outline` | `page_slug` | Sektionsüberschriften + Nachbarseiten |
| `upsert_section` | `page_slug`, `heading`, `body`, `source_slug` | neue Sektions-ID |
| `add_link` | `from_slug`, `to_slug`, `rel` | ok |
| `add_claim` | `page_slug`, `subject`, `statement`, `source_slug` | Claim-ID |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Drei lesende, drei schreibende. Flach und typisiert — keine verschachtelten Objekte, keine optionalen Strukturen, nichts, wobei ein schwaches Modell die Form verfehlen kann. → [wiki-datenbankschema](wiki-datenbankschema.md)

## Was die Schicht übernimmt

Die Vermittlungsschicht verantwortet alles Mechanische:

- Transaktion
- `updated_at` setzen
- Tokenzählung
- Log-Eintrag schreiben
- Slug-Normalisierung
- Validierung gegen die CHECK-Werte
- Ablehnung mit verständlicher Fehlermeldung

Das Modell trifft ausschließlich Bedeutungsentscheidungen: Was gehört in diese Sektion? Ist das ein Widerspruch? Welche Seiten hängen zusammen?

## Vier Konsequenzen

1. **Der System-Prompt wird klein.** Sechs flache Werkzeuge statt eines Dateisystem-Agenten mit Lese-, Schreib-, Diff- und Suchwerkzeugen. Der dokumentierte System-Prompt-Umfang von 33–51 KB ist selbst ein Hauptgrund, warum lokale Modelle scheitern — er lässt sich auf einen Bruchteil drücken. → [roocode-system-prompt-optimierung](roocode-system-prompt-optimierung.md)

2. **Ungültige Zustände sind nicht erreichbar.** Das Modell *kann* keinen toten Link erzeugen, weil `add_link` gegen existierende Slugs prüft und der Fremdschlüssel das ohnehin erzwingt.

3. **Fehler sind lokal und rückgängig.** Eine misslungene Sektion ist eine Zeile in einer Transaktion, kein halb überschriebenes Dokument.

4. **Der Freiheitsgrad, an dem schwache Modelle scheitern, wird ihnen genommen.**

> „Das ist kein Kompromiss, sondern der Zweck der Übung." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Technische Voraussetzung

Natives Function Calling über den OpenAI-kompatiblen `/v1`-Endpoint. Mit dem nativen Ollama-Endpoint kommt kein `tool_calls`-Format zurück. Reasoning-Modelle mit `<think>`-Blöcken bleiben ungeeignet. → [tool-use-lokale-modelle](tool-use-lokale-modelle.md)

Das ist keine Empfehlung, sondern eine harte Bedingung: Ab Roo Code 3.54.0 ist der XML-Fallback entfernt, natives Function Calling also Pflicht. Ein Modell, das das Format nicht trifft, kann die Werkzeugschicht nicht bedienen — und ohne Werkzeugschicht gibt es keinen SQL-Betrieb.

## Verwandte Seiten

- [sql-wiki-architektur](sql-wiki-architektur.md) — Warum ohne diese Regel alles zusammenfällt
- [wiki-datenbankschema](wiki-datenbankschema.md) — Was die Werkzeuge kapseln
- [tool-use-lokale-modelle](tool-use-lokale-modelle.md) — Welche Modelle natives Function Calling können
- [roocode-system-prompt-optimierung](roocode-system-prompt-optimierung.md) — System-Prompt-Umfang als Scheiterungsgrund
- [read-only-guard](read-only-guard.md) — Technische Beschränkung von KI-Rollen als verwandtes Muster
- [ingest-fliessband](ingest-fliessband.md) — Die Werkzeuge im Ablauf
- [claude-md-design](claude-md-design.md) — Designprinzipien für Anweisungsdateien
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
