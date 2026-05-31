---
date: 2026-05-31
type: konzept
tags: [konzept, claude-md, token, kosten]
status: active
---

# Token-Sparregeln in der CLAUDE.md

**Zusammenfassung**: Formalisierte Verhaltensregeln direkt in der CLAUDE.md, die Claudes Token-Verbrauch im laufenden Gesprächsbetrieb begrenzen — unabhängig von architektonischen Maßnahmen wie jDocMunch oder qmd.
**Quellen**: `raw/CLAUDE-MD-Legacy-Forensik-Battletested-Veteran.md` (Token-Sparregeln-Abschnitt)
**Zuletzt aktualisiert**: 2026-05-31

---

## Zwei Ebenen der Token-Sparsamkeit

Das [Token-Sparen mit Claude Code](../anleitungen/token-sparen.md) behandelt vor allem **architektonische Maßnahmen**: jDocMunch für sektionsbasierten Zugriff, qmd für semantische Navigation, Routing-Schritt beim Ingest. Diese Maßnahmen greifen auf Datei-Ladeebene.

Die Token-Sparregeln in der CLAUDE.md wirken auf einer anderen Ebene: im **laufenden Gesprächsbetrieb**. Sie steuern, wie Claude Antworten formuliert, wie es navigiert und wann es Dateien liest — auch wenn keine separaten MCP-Tools vorhanden sind.

Beide Ebenen ergänzen sich. Die CLAUDE.md-Regeln sind die Untergrenze: Sie helfen auch dann, wenn qmd und jDocMunch noch nicht eingerichtet sind.

## Die Regelkategorien

### Datei-Lesen

Verhindert, dass Claude Dateien spekulativ oder redundant lädt:

- Dateien **nur lesen wenn explizit benötigt**, kein Vorab-Lesen
- `offset` + `limit` bei großen Dateien verwenden — nie vollständig laden
- Jede Datei **höchstens einmal pro Sitzung** lesen; danach gilt der gelesene Stand als bekannt
- `search_files` statt ganze Dateien einlesen, wenn nach einem Begriff gesucht wird
- Nur die relevante Wiki-Seite lesen, nicht den gesamten Index zur Navigation
- `wiki/log.md` beim Session-Start: nur 30 Zeilen (`offset=1, limit=30`), nicht die gesamte Datei

Die kritische Situation, die diese Regeln adressieren: Claude liest `index.md` am Sitzungsstart spekulativ, dann drei weitere Seiten "um Kontext aufzubauen", bevor die erste Frage beantwortet wird. Bei einem 60-Seiten-Wiki sind das leicht 20.000 Token, bevor die eigentliche Arbeit beginnt.

### Antworten

Steuert Länge und Struktur von Antworten:

- Standardmäßig **maximal 20 Zeilen** — keine Wall of Text
- Keine vorausschauenden Erklärungen, keine Zusammenfassungen des eigenen Tuns, keine spekulativen Nebenstränge
- **Eine Rückfrage am Antwortende**: "Soll ich das ausführlicher erläutern?" statt automatisch langer Antworten
- Ausführliche Darstellungen **nur auf explizite Anfrage** ("ausführlich", "im Detail", "lang", "mit Begründung")
- Keine Wiederholung des Benutzer-Prompts in der Antwort

### Datei-Schreiben

Reduziert Token-Overhead beim Schreiben:

- `apply_diff` statt `write_to_file` bei bestehenden Dateien (sendet nur den Diff)
- Mehrere Änderungen an derselben Datei in **einem einzigen** `apply_diff`-Aufruf zusammenfassen
- Vor dem Erstellen mehrerer neuer Seiten **einmal fragen**, nicht nach jeder einzelnen

### Logging

Verhindert aufgeblähte Log-Dateien, die bei jedem Sitzungsstart teuer geladen werden:

- `wiki/log.md` nur bei **inhaltlich bedeutsamen** Analyseänderungen aktualisieren
- Keine Log-Einträge für: Tippfehler-Korrekturen, reine Formatanpassungen, fehlgeschlagene Versuche
- Log-Einträge kurz halten: **maximal 5 Zeilen** pro Eintrag

### Index

Hält `index.md` navigierbar ohne Token-Overhead:

- `wiki/index.md` nur aktualisieren wenn **neue Seiten erstellt oder gelöscht** werden
- Keine Aktualisierung bei inhaltlichen Updates bestehender Seiten

## Session-Start-Protokoll als Sonderfall

Eine besonders wirksame Anwendung der Datei-Leseregeln ist das Session-Start-Protokoll: statt beim ersten Prompt spekulativ viele Dateien zu lesen, wird ein **einziger paralleler Tool-Aufruf** mit genau drei Dateien definiert — Index, Log (30 Zeilen), Arbeits-Journal. Danach folgt eine kompakte Lagemeldung (maximal 10 Zeilen) und eine einzige Frage.

Diese Struktur schneidet den teuersten Token-Treiber bei Sitzungseröffnungen ab: den rekonstruktiven Kontext-Aufbau.

## Wo diese Regeln hingehören

Die Regeln wirken nur, wenn sie explizit in der CLAUDE.md stehen — nicht als allgemeines Wissen über Token-Kosten, sondern als operative Anweisungen. Die Formulierung als klare Verbote ("nie vollständig laden", "höchstens einmal") ist dabei wichtiger als erklärende Begründungen.

## Abgrenzung

Diese Regeln sind **kein Ersatz** für jDocMunch, qmd oder Prompt Caching. Bei einem Wiki mit 100+ Seiten greifen diese Maßnahmen früher und tiefer. Die CLAUDE.md-Regeln ergänzen sie — und helfen besonders in kleinen Projekten, wo architektonische MCP-Werkzeuge noch überdimensioniert sind.

## Verwandte Seiten

- [token-sparen](../anleitungen/token-sparen.md) — Die architektonische Ebene: jDocMunch, qmd, Routing
- [claude-md-design](claude-md-design.md) — Designprinzipien für effektive CLAUDE.md-Dateien
- [claude-md-legacy-forensik-veteran](../quellen/claude-md-legacy-forensik-veteran.md) — Die Quelle, aus der diese Regeln stammen
- [skalierungsgrenzen](skalierungsgrenzen.md) — Wann Token-Sparsamkeit kritisch wird

---

[Wiki-Index](../index.md)
