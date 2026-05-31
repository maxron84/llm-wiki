---
date: 2026-05-31
type: quelle
tags: [quelle, vorlage, software, claude-md, ki-lehrer]
status: active
---

# CLAUDE.md KI-Lehrer App (Battle-tested Rookie)

**Zusammenfassung**: Das echte CLAUDE.md der KI-Lehrer Desktop-App in V1.9 — mit FELDTEST-SPERRE, ralph.sh-Budget-Regeln, stufenweiser Build-Reihenfolge, detaillierten Regressions-Checklisten und Umgebungshinweisen für Flatpak-Limitierungen.
**Quellen**: `raw/CLAUDE-MD-Softwareprojekt-Battetested-Rookie.md`
**Zuletzt aktualisiert**: 2026-05-31

---

## Kontext

Diese Datei ist das echte CLAUDE.md des [KI-Lehrer-Projekts](../konzepte/ki-lehrer-app.md) — einer Tkinter-Desktop-App, die den KI-Lehrer-Ansatz für technisch unaffine Nutzer zugänglich macht. Die App hat Stufen 1 bis 78 durchlaufen und ist am 2026-05-31 in den ersten Feldtest mit einem Kind gegangen.

## Muster und Konzepte in dieser Datei

### FELDTEST-SPERRE

Ein vollständiger Änderungsstopp während eines echten Nutzertests. Definiert durch:
- Kein neues Feature, kein Refactoring, kein Bugfix auf Verdacht
- Kein Ralph-Lauf, keine Kaskaden-Fortsetzung
- Kein Major-Release
- Aufhebung nur durch explizite Nachricht mit "Feldtest abgeschlossen" und Änderungswunschliste

Motiviert durch das Prinzip: Während echter Feldtests soll das System stabil und beobachtbar sein. Änderungen würden Beobachtungen kontaminieren.

→ Eigene Konzeptseite: [feldtest-sperre](../konzepte/feldtest-sperre.md)

### Stufenweise Build-Reihenfolge

8 Stufen, jede eigenständig lauffähig (Stufe 1: Fenster und Navigation, Stufe 8: Fächer-Screen). Verhindert, dass eine unfertige Implementierung das gesamte Projekt unbenutzbar macht. Die aktuelle Stufe wird vor dem Start bestimmt, eine Stufe wird vollständig abgeschlossen, bevor die nächste beginnt.

Konkret im Projekt: Stufen 1 bis 78 committet entspricht V1.9.

### Regressions-Checkliste im CLAUDE.md

Explizit dokumentierte Regressions-Checks pro Feature — direkt in der CLAUDE.md, nicht in einem separaten Testdokument. Beispiele:
- Fach-Fokus (Stufe 28): "Erklär mir den 30-jährigen Krieg im Mathe-Chat" → KI soll freundlich ablehnen
- Foto-Tag-Override (Stufe 39): manueller Override darf von KI nicht überschrieben werden, wenn `status_quelle == "mensch"`
- Pult-Chat Stream-Persistenz (Stufe 33): Stream starten, Screen wechseln, zurück — Antwort komplett, kein Crash
- PDF-Export (Stufe 70): gespeicherte PDF enthält korrekte Wochendaten

Verhindert, dass neue Stufen alte Features brechen — ohne eigenständige Testinfrastruktur.

### Ralph.sh-Konfiguration

Konkrete Konfiguration des ralph.sh-Loops:
- Modell: `${RALPH_MODEL:-sonnet}` — Opus überspringen, 5× teurer ohne erkennbaren Qualitätsvorteil
- Permission-Mode: `bypassPermissions` — headless `-p`-Modus hängt sich im Default-Mode auf
- Smoke-Test: `py_compile` und `ast.parse` statt `import tkinter` — Flatpak-tauglich
- Budget: `RALPH_BUDGET_USD=N ./ralph.sh` — Empfehlung 3 USD pro Stufe als Obergrenze
- Promise-Match: `<promise>STUFE_N_COMPLETE</promise>` mit XML-Tags, nicht nur Konstantenstring

Bei Hängern: zuerst `pgrep -af "claude -p"` und letzten Log-Eintrag prüfen, bevor abgebrochen wird.

### Umgebungshinweise (Flatpak)

Tkinter ist in Flatpak-VSCodium nicht verfügbar — ein bekanntes, nicht offensichtliches Problem. `python3 -c "import tkinter"` schlägt in der Sandbox fehl. Lösung: App ausschließlich im nativen Terminal testen.

### Architektur-Regeln als CLAUDE.md-Inhalt

Vier Prinzipien direkt in der CLAUDE.md:
- `AppState` als zentrales State-Objekt, keine globalen Variablen
- `core/` kennt kein Tkinter
- Streaming in eigenem Thread, GUI-Updates via `root.after()`
- DAU-Prinzip als Designgrenze

### Spezifikations-Disziplin

Alle Designentscheidungen liegen in `docs/spec/`. Regel: Vor jeder Implementierung relevante Spec-Seite lesen. Die Spec ist die Wahrheit, nicht Annahmen. Wenn eine Designfrage nicht in der Spec steht: fragen, nicht raten.

### Aktueller-Stand-Abschnitt

Ein expliziter "Aktueller Stand"-Abschnitt in der CLAUDE.md beschreibt Versionsnummer, abgeschlossene Kaskaden und nächste geplante Themen. Mit jeder Kaskade wird der Abschnitt fortgeschrieben. Claude kann so beim Session-Start sofort den Projektzustand einschätzen, ohne Commit-Logs lesen zu müssen.

## Verhältnis zur generischen Software-Vorlage

Die allgemeine [claude-md-software](../vorlagen/claude-md-software.md)-Vorlage ist schlank gehalten. Diese Battle-tested-Version zeigt, was nach 78 Iterationen dazukommt: FELDTEST-SPERRE, Regressions-Checklisten, ralph.sh-Feinsteuerung, Umgebungs-Eigenheiten.

## Verwandte Seiten

- [ki-lehrer-app](../konzepte/ki-lehrer-app.md) — Das übergeordnete Konzept der App
- [feldtest-sperre](../konzepte/feldtest-sperre.md) — Das Freeze-Muster während echter Nutzertests
- [ralph-schleife](../konzepte/ralph-schleife.md) — Das Grundkonzept der Ralph-Schleife
- [claude-md-softwareprojekt-rookie](../vorlagen/claude-md-softwareprojekt-rookie.md) — Generalisierte Vorlage aus diesen Mustern
- [claude-md-software](../vorlagen/claude-md-software.md) — Die schlanke Basisvorlage

---

[Wiki-Index](../index.md)
