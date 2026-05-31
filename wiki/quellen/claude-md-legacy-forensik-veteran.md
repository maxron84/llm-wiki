---
date: 2026-05-31
type: quelle
tags: [quelle, vorlage, forensik, claude-md]
status: active
---

# CLAUDE.md-Vorlage: Legacy-Forensik (Battle-tested Veteran)

**Zusammenfassung**: Die im echten Projekt erprobte Weiterentwicklung der Legacy-Forensik-Vorlage — mit Windows/PowerShell-Umgebungsregeln, DSGVO-Anforderungen, Session-Start-Protokoll, Token-Sparregeln mit konkreten Quoten und einem vollständigen Scripting-Ökosystem.
**Quellen**: `raw/CLAUDE-MD-Legacy-Forensik-Battletested-Veteran.md`
**Zuletzt aktualisiert**: 2026-05-31

---

## Kontext

Diese Datei ist das CLAUDE.md des AP20-Schnittstellenprojekts — einer rund 9 Jahre alten PHP/PL/SQL-Schnittstelle zwischen JobRouter und Oracle EBS. Die Vorlage ist durch echten Projekteinsatz entstanden und zeigt, wie die [claude-md-legacy-forensik](../vorlagen/claude-md-legacy-forensik.md)-Basisvorlage im Betrieb weiterentwickelt wird.

Das Projekt ist forensischer Natur: kein Code wird verändert, nur verstanden, kartiert und dokumentiert — als Grundlage für eine P2P-Modernisierung und BANF-Ablösung.

## Was diese Version gegenüber der Basisvorlage hinzufügt

### Windows/PowerShell-Umgebungsregeln

Explizite Shell-Deklaration als eigenständiger CLAUDE.md-Abschnitt. Alle Befehle in PowerShell-Syntax, keine Unix-Utilities (`sed`, `grep`, `awk`, `cat`), Semikolon statt `&&` als Befehlsverkettung, Skripte als `.ps1` in `scripts/`.

Hintergrund: Fehlende Shell-Deklaration führt dazu, dass Claude automatisch bash-Syntax erzeugt, die in Windows-Projekten fehlschlägt — und der Fehler erst beim Ausführen auffällt.

### DSGVO-Regeln

Dedizierter Abschnitt mit harten Verboten:
- Keine personenbezogenen Daten in Wiki-Seiten (Namen, E-Mails, Telefonnummern)
- Keine unternehmensinternen Daten (interne Hostnamen, IP-Adressen, Domains)
- Keine Hinweise auf KI-Nutzung im gesamten Projekt (eigenes Prüfskript: `wiki-ki-scan.ps1`)

### Session-Start-Protokoll

Definiertes Startverhalten: ein einziger paralleler Tool-Aufruf mit genau drei Dateien — `wiki/index.md`, `wiki/log.md` (offset=1, limit=30), `wiki/arbeit/journal.md`. Danach maximal 10 Zeilen Lagemeldung und eine einzige Frage. Keine weiteren Dateien spekulativ mitlesen.

Verhindert das Token-intensive Vorab-Lesen beim Sitzungsstart, das ohne Protokoll leicht entsteht.

### Token-Sparregeln mit konkreten Quoten

Formalisierte In-CLAUDE.md-Regeln für Token-Sparsamkeit im laufenden Betrieb:
- Standardmäßig maximal 20 Zeilen pro Antwort
- `offset` + `limit` bei großen Dateien — nie vollständig laden
- `search_files` statt Index-Lesen zur Navigation
- Jede Datei höchstens einmal pro Sitzung lesen
- Log-Einträge nur bei inhaltlich bedeutsamen Änderungen
- `apply_diff` statt `write_to_file` bei bestehenden Dateien
- Eine obligatorische Rückfrage am Antwortende statt langer Zusammenfassungen

Das unterscheidet sich von architektonischen Token-Strategien (jDocMunch, qmd): diese Regeln wirken im laufenden Gesprächsbetrieb, nicht auf Datei-Ladeebene.

→ Eigene Konzeptseite: [claude-md-token-sparen](../konzepte/claude-md-token-sparen.md)

### Scripting-Ökosystem

Formalisiertes Vorgehen für wiederkehrende Aufgaben als PowerShell-Skripte in `scripts/` (in `.gitignore`). Enthält eine gepflegte Liste benannter bekannter Skripte:
- `wiki-audit.ps1` — Pflichtfelder, Index-Konsistenz, leere Dateien
- `wiki-link-check.ps1` — Tote Verweise (read-only)
- `wiki-fix.ps1` — BOM-Entfernung, Stilzeichen-Korrekturen
- `wiki-umlaut-fix.ps1` — ASCII-Umlaute ersetzen, Code-Blöcke schützen
- `wiki-ki-scan.ps1` — Hinweise auf KI-Nutzung suchen (read-only, manuell bewerten)
- `wiki-bulk-rename.ps1` — Datei-Umbenennungen plus Link-Updates

Konventionen: DryRun-Modus, UTF-8-Erzwingung, Schutz von `raw/` und `clippings/`, keine Emojis in Konsolen-Ausgaben (`[OK]`, `[WARN]`, `[FIX]`).

### KI-Marker-Vermeidungsregel

Explizites Verbot, Hinweise auf KI-Nutzung im Projekt zu hinterlassen — auch kontextabhängige Treffer, nicht nur direkte Begriffe. Eigenes Prüfskript für manuelle Bewertung.

### Wirkbereich-Deklaration (Scope)

Explizite Formulierung, was außerhalb des Claude-Scopes liegt: laufendes JR-System, Oracle EBS, DocuWare, Testumgebungen. Claude kann analysieren, dokumentieren und How-Tos schreiben — nicht deployen oder ausführen. Fix-Anleitungen sind Vorlagen für den menschlichen Umsetzer.

### "Außerordentliche Anfrage"-Muster

Ein benanntes Ausnahme-Muster: Prompts mit diesem Schlüsselwort schalten Dateiänderungen, Logging und Projektregeln aus. Für Situationen, in denen nur eine Antwort ohne Seiteneffekte gewünscht ist.

### Arbeit-Unterordner mit Journal

Neben dem generischen `wiki/` gibt es einen `wiki/arbeit/`-Zweig mit thematischen Unterordnern (`ap20-betrieb/`, `banf-abloesung/`, `kostenstelle/`) und einem zentralen `journal.md`. Trennt laufende Arbeit von dauerhaften Befundseiten.

## Verhältnis zur Basisvorlage

Die Basisvorlage [claude-md-legacy-forensik](../vorlagen/claude-md-legacy-forensik.md) enthält bereits: Konfidenzmodell, Zitierregeln, Spezialberichte, forensische Workflows. Diese Battle-tested-Version ergänzt den Betriebsteil: Umgebung, Datenschutz, Sitzungsdisziplin, Scripting.

## Verwandte Seiten

- [claude-md-legacy-forensik](../vorlagen/claude-md-legacy-forensik.md) — Die Basisvorlage, die diese Quelle weiterentwickelt
- [claude-md-token-sparen](../konzepte/claude-md-token-sparen.md) — In-CLAUDE.md-Token-Sparregeln als eigenständiges Konzept
- [claude-md-design](../konzepte/claude-md-design.md) — Designprinzipien für CLAUDE.md-Dateien
- [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md) — Warum Spot-Checking forensischer Befunde Pflicht ist

---

[Wiki-Index](../index.md)
