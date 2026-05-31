# CLAUDE — KI-Lehrer App

Eine Tkinter-Desktop-Anwendung, die den KI-Lehrer-Ansatz für technisch unaffine Nutzer zugänglich macht.

---

## 🛑 FELDTEST-SPERRE (seit 2026-05-31)

**Status:** V1.9.0 ist in den ersten Feldtest mit dem Kind gegangen. Bis zum
expliziten Abschluss dieses Feldtests gilt ein **vollständiger Änderungs-Stopp**:

- **Keine neuen Features**, keine Refactorings, keine Bugfixes „auf Verdacht".
- **Kein Ralph-Lauf**: `.ralph-state` bleibt auf `79`. Kaskaden 11, 12, 13
  (`plans/ralph-kaskade-11.md`, `plans/ralph-kaskade-12.md`,
  `plans/ralph-kaskade-13.md`) bleiben **eingefroren**.
- **Kein neuer Major-Release** (kein V2.0) vor Feldtest-Abschluss.
- **Erlaubt** bleibt: Status-Inspektion, Lesen, Dokumentation des Feldtest-Verlaufs.

**Aufhebung der Sperre** erfolgt nur durch eine explizite Nutzer-Nachricht mit:
1. „Feldtest abgeschlossen" (oder Äquivalent), und
2. einer neuen Feature- bzw. Änderungswunschliste auf Basis der Feldtest-Erkenntnisse.

Erst dann darf die Roadmap (Ralph-Kaskaden, README-Ausblick, Plan-Dateien) wieder
fortgeschrieben werden.

---

## Spezifikation

Alle Designentscheidungen, Wireframes, Datenmodelle und Screen-Beschreibungen liegen in:

```
docs/spec/
├── ki-lehrer-app.md          — DAU-Prinzip, Cloud/Lokal-Entscheidung, Wireframe
├── ki-lehrer-prototyp.md     — Tech-Stack, Projektstruktur, Build-Reihenfolge (Stufen 1–8)
├── ki-lehrer-screens.md      — Die vier Screens im Detail
├── ki-lehrer-setup-wizard.md — Alle Setup-Wizard-Schritte (s0–s8)
├── ki-lehrer-datenmodell.md  — Ordnerstruktur, JSON-Schemas, Kontext-Ladestrategie
├── ki-lehrer-lehrplan.md     — Lehrplan-Modus (optional, ab Kaskade 8): Wochen/Themen, Fortschritts-Heuristik
└── ki-lehrer-verbrauch.md    — Hybrid-Modelle, Token-Erfassung, Preis-Tabelle, Override (Kaskade 10)
```

Vor jeder Implementierung: relevante Spec-Seite lesen. Die Spec ist die Wahrheit — nicht Annahmen.

## Tech-Stack

| Schicht      | Technologie                     |
|--------------|---------------------------------|
| GUI          | Tkinter (Python-Stdlib)         |
| KI Cloud     | anthropic Python SDK            |
| KI Lokal     | openai SDK (OpenAI-kompatibel)  |
| Git          | gitpython                       |
| Datenhaltung | JSON + Markdown (Stdlib)        |
| Pfade        | pathlib (Stdlib)                |
| PDF-Export   | reportlab ≥ 4.0 (Runtime)      |
| Build        | pyinstaller ≥ 6.0 (Dev-Tool)   |
| Test-PDF     | pypdf ≥ 4.0 (Dev-Tool)         |

**Python-Mindestversion**: 3.10

## Projektstruktur

```
ki-lehrer/
├── main.py                  # Entry-Point (inkl. --selbsttest + Crash-Wrapper)
├── app.py                   # Hauptfenster, Screen-Switching, AppState, Update-Check
├── requirements.txt
├── requirements-build.txt   # pyinstaller>=6.0 (nur Build)
├── ki-lehrer.spec           # PyInstaller-Spec
├── build/
│   ├── build-linux.sh       # Linux-Build-Skript
│   ├── build-windows.bat    # Windows-Build-Skript
│   └── build-macos.sh       # macOS-Build-Skript
├── .github/workflows/
│   └── release.yml          # CI-Pipeline: Build + Release bei Tag-Push
├── gui/
│   ├── nav.py               # Linke Navigationsleiste
│   ├── chat/                # (Stufe 32) Extrahierter Chat-Tab
│   │   ├── __init__.py
│   │   └── chat_view.py     # ChatView(tk.Frame) — Chat, Streaming, Session-Lifecycle
│   ├── heft_theme.py        # (Stufe 35) Tagebuch-Theme-Konstanten für den Heft-Tab
│   ├── cockpit_charts.py    # (Stufe 69) Tk-Canvas-Diagramme: stapel_balken
│   └── screens/
│   │   ├── lehrerpult.py    # Chat lebt hier seit Stufe 33; Update-Banner seit Stufe 56
│   │   ├── aufgabenhefte.py # „Mappe" (Heft + Sitzungen + Fotos), kein Chat mehr
│   │   ├── faecher.py
│   │   ├── einstellungen.py
│   │   └── cockpit.py       # (Stufe 66) Eltern-Cockpit: Wochenbericht + Heft-Diff + Foto-Übersicht + PDF
│   └── wizard/
│       ├── wizard.py        # Wizard-Controller
│       └── steps/           # s0_willkommen.py … s8_fertig.py
├── core/
│   ├── data.py              # JSON/Markdown lesen+schreiben
│   ├── git_manager.py       # git init + commit
│   ├── api.py               # Anthropic + OpenAI-compat.
│   ├── context.py           # Kontext-Ladestrategie (inkl. vollkontext-Parameter)
│   ├── log_setup.py         # Zentraler Logger, JSON-Lines
│   ├── runtime.py           # (Stufe 51) is_frozen(), bundle_dir()
│   ├── crash_dialog.py      # (Stufe 55) Plattform-Crash-Dialog + Klartext-Fehler
│   ├── update_check.py      # (Stufe 56) Versions-Check gegen konfigurierbaren Endpoint
│   ├── foto_meta.py         # (Stufe 36) Sidecar-Schicht für Foto-Tags und -Status
│   ├── foto_tags.py         # (Stufe 37) Parser für KI-TAGS- und FOTO_STATUS-Blöcke
│   ├── sitzungen_suche.py   # (Stufe 34) Volltext-Suche über Sitzungs-Protokolle
│   ├── eltern_pin.py        # (Stufe 40) PIN-Hash, Prüfung, Validierung
│   ├── lehrplan.py          # (Stufe 59) Lehrplan-Datenschicht: IO, Validierung, aktuelle_woche
│   ├── fortschritt.py       # (Stufe 60) Fortschritts-Heuristik: Themen-Matching + Cache
│   ├── cockpit.py           # (Stufe 67) Aggregation: FachWoche, woche_aggregieren, heft_diff
│   ├── pdf_export.py        # (Stufe 70) reportlab-basierter Wochenbericht als PDF
│   ├── verbrauch.py         # (Stufe 74) Token-Erfassung: Append-Log + Aggregation
│   └── preise.py            # (Stufe 75) Kosten-Berechnung aus preise.json + Override
├── core/
│   └── preise.json          # (Stufe 75) Hardcoded Preis-Tabelle (USD/Mio. Token pro Modell)
├── docs/
│   ├── spec/                # Spezifikation (nicht bearbeiten)
│   └── beispiele/           # (Kaskade 8) Beispiel-Lehrpläne als JSON
└── plans/                   # Kaskaden-Plan-Dokumente
```

## Build-Reihenfolge

Wir bauen in acht Stufen — jede ist eigenständig lauffähig:

| Stufe | Was | Ziel |
|-------|-----|------|
| 1 | Grundgerüst: Fenster + Navigation | App startet, 4 Screens schaltbar |
| 2 | Setup-Wizard (UI, kein Schreiben) | Alle Schritte durchklickbar |
| 3 | Datenmodell + Git | Wizard legt echte Dateien an |
| 4 | Einstellungen-Screen | Config les- und schreibbar |
| 5 | Chat-Tab (Kern) | Echtes KI-Gespräch, Heft wird geschrieben |
| 6 | Aufgabenhefte-Screen | Heft und Sessions nachlesen |
| 7 | Lehrerpult (vollständig) | Echtzustand, Schnelleinstieg |
| 8 | Fächer-Screen | Fächer + Materialien verwaltbar |

Aktuelle Stufe vor dem Start bestimmen, eine Stufe vollständig abschließen bevor die nächste beginnt.

## Architektur-Regeln

- **AppState** — zentrales State-Objekt, wird beim Start instanziiert und an alle Screens übergeben. Keine globalen Variablen.
- **core/ kennt kein tkinter** — `data.py`, `api.py`, `git_manager.py`, `context.py` importieren kein Tkinter.
- **Streaming im Thread** — API-Calls laufen in eigenem Thread, GUI-Updates via `root.after()`.
- **DAU-Prinzip** — keine Terminal-Begriffe, keine Dateipfade, keine Git-Begriffe in der UI-Sprache.

## Regeln

- Nach jeder abgeschlossenen Stufe committen — ohne explizite Aufforderung
- Keine Features aus einer späteren Stufe vorwegnehmen
- Wenn eine Designfrage nicht in der Spec steht: fragen, nicht raten
- Tkinter-Standard-Widgets in V1 — keine Icons, keine Assets, kein visueller Overhead

---

## Aktueller Stand

**V1.9-stable ist erreicht (Stufen 1–78 committet). Kaskade 10 hat die App
um Kosten-Transparenz und ein Hybrid-Modell-System erweitert: pro
Cloud-Modus zwei Modelle (Hauptmodell für Lerngespräche, Hilfsmodell für
Wochenplan und Verbindungstests). Pro Cloud-Aufruf wird Token-Verbrauch in
`<schueler>/_verbrauch.jsonl` mitgeschrieben (lokale Aufrufe nicht). Preise
liegen hardcoded in `core/preise.json` mit Eltern-Override-Möglichkeit pro
Modell und einem Wechselkurs-Override. Cockpit zeigt eine neue Karte
„Kosten & Verbrauch": Wochen-/Monats-EUR, Stapel-Balken nach Pfad, Top-3
Modelle. PDF-Bericht enthält eine Kosten-Tabelle. Optionaler Budget-
Hinweis im Lehrerpult — sanft, nicht-blockierend, nur für Eltern sichtbar.
Tech-Stack unverändert (keine neuen Dependencies). Datenmodell bleibt
Open/Closed: zwei neue optionale Schüler-Ordner-Dateien (`_verbrauch.jsonl`,
`_kosten_cache.json` — Cache, in `.gitignore`). Nächste Themen: Lokal-Token-
Tracking, Auto-Switching, Klassenarbeit-Modus.**
Plan: [`plans/ralph-kaskade-10.md`](plans/ralph-kaskade-10.md).

Vorgänger-Kaskade 9 (V1.8) hat das Eltern-/Lehrer-Cockpit eingeführt:
fünfter Nav-Eintrag hinter dem Eltern-PIN, Wochenbericht mit
Sessions/Themen/Foto-Status pro Fach, Heft-Veränderungs-Sicht für die
laufende Woche, Foto-Status-Übersicht mit Stapel-Balken und Top-Themen,
PDF-Export via reportlab für Druckbericht. Datenschicht read-only — keine
Änderungen an Heft, Sitzungen, Foto-Sidecars oder Lehrplan. Tech-Stack
erstmals seit Kaskade 1 um eine Runtime-Dep erweitert: reportlab.
Plan: [`plans/ralph-kaskade-9.md`](plans/ralph-kaskade-9.md).

Kaskade 8 (V1.7) hat einen inhaltlichen Lehrplan-Modus eingeführt: pro
Fach optionale `lehrplan.json` mit Wochen/Themen/Unterthemen, automatisch
berechneter Fortschritt aus Heft, Sitzungen und Foto-Tags,
KI-vorgeschlagener Wochenplan, „Heute lernen"-Karte im Lehrerpult,
Lehrplan-Auszug im System-Prompt. Die KI lenkt sanft, ohne zu zwingen —
das Kind darf abweichen. Datenmodell bleibt Open/Closed: neue optionale
Dateien `<fach>/lehrplan.json`, `<fach>/_fortschritt.json` (Cache, in
`.gitignore`), `<fach>/_wochenplan.json` (Cache).
Plan: [`plans/ralph-kaskade-8.md`](plans/ralph-kaskade-8.md).

Vorgänger-Kaskade 7 (V1.6) hat die App installierbar gemacht: PyInstaller-Bundles
für Linux/Windows/macOS, automatischer Build durch CI-Pipeline bei Tag-Push,
First-Run-Crash-Schutz mit Klartext-Fehlern, dezenter Update-Check (Opt-Out
möglich, keine Telemetrie). Die README richtet sich jetzt an Endnutzer, die
Entwickler-Doku ist in README-dev.md ausgelagert.
Plan: [`plans/ralph-kaskade-7.md`](plans/ralph-kaskade-7.md).

Vorgänger-Kaskade 6 (V1.5) hat Multi-Profil in die Nav gebracht, dem Heft die
Würde-konforme Schüler-Anmerkung gegeben, Foto-Themen pflegbar gemacht und die
Sitzungs-Suche fach-übergreifend ausgebaut.
Plan: [`plans/ralph-kaskade-6.md`](plans/ralph-kaskade-6.md).

Vorgänger-Kaskade 5 (V1.4) hat den Chat ins Lehrerpult gezogen, die Aufgabenhefte
zur „Mappe" umgewidmet und ein dreistufiges Foto-Tag-System eingeführt.
Plan: [`plans/ralph-kaskade-5.md`](plans/ralph-kaskade-5.md).

Den exakten Stand zeigt `git log --grep="feat(stufe"`.

Für gezielte Bugfixes außerhalb der Kaskade gilt weiterhin: direkt im Code-Mode
oder per `claude -p` mit konkretem Auftrag — ohne Ralph-Loop.

## Umgebungs-Hinweise

- **Tkinter im Flatpak-VSCodium-Python nicht verfügbar.** `python3 -c "import tkinter"`
  schlägt in der Sandbox fehl. Smoke-Test-Empfehlung: `python3 -m py_compile <datei>`.
  Die App nicht im integrierten Terminal von Flatpak-VSCodium starten.
- **App nur im nativen Terminal testen** (GNOME Terminal, Konsole, …):
  ```bash
  cd ~/Source/ki-lehrer
  python3 -m venv .venv          # einmalig
  .venv/bin/pip install -r requirements.txt
  .venv/bin/python3 main.py
  ```
  Auf Linux muss `python3-tk` (Debian/Ubuntu) bzw. `tk` (Arch) systemweit installiert sein.
  Ubuntu/Debian verwalten Python systemweit (PEP 668) — `pip install` ohne venv schlägt fehl.
- **`gitpython` ist optional**: [`core/git_manager.py`](core/git_manager.py) hat
  `try/except ImportError` — die App läuft auch ohne, Git-Operationen werden dann
  übersprungen.
- **Datenverzeichnis** liegt unter `~/KI-Lehrer/` (außerhalb des Projekt-Repos).
  Beim ersten Start nach Wizard-Abschluss wird es angelegt; danach erkennt die App
  beim Start `config.json` und überspringt den Wizard.

## Erweiterungen seit Spec

Während der Implementierung haben sich kleine Architektur-Details ergänzt, die nicht
in der Spec stehen aber konsistent in den Stufen verwendet werden:

- **`AppState.navigate(name)`** — Callback für inter-Screen-Navigation, wird in
  [`app.py`](app.py) gesetzt. Lehrerpult nutzt ihn für „Starten" → Aufgabenhefte.
- **`AppState.active_fach`** + **`AppState.goto_chat_on_show`** — vom Lehrerpult
  beim „Starten"-Klick gesetzt, vom Aufgabenhefte-Screen in `on_show()` ausgewertet.
- **Statischer Eröffnungsgruß** in [`gui/screens/aufgabenhefte.py`](gui/screens/aufgabenhefte.py) —
  lokal aus `profil.json` + letztem Sitzungs-Log konstruiert, kein API-Call. Wird einmalig beim
  Öffnen des Chat-Tabs gesetzt und für die Dauer der Session nicht neu erzeugt (Token-Sparsamkeit).
  Der Gruß erscheint als KI-Nachricht in der Chat-Anzeige, fließt aber **nicht** in die
  API-Message-Liste ein — die erste echte User-Eingabe startet den API-Verlauf.
- **JSON-Schema-Versionierung**: `{"version": 1, ...}` in `config.json` und `profil.json`
  als Vorbereitung für Migrationen.
- **`AppState.welcome_message`** — wird vom Wizard-Abschluss gesetzt, vom Lehrerpult
  beim ersten Anzeigen genutzt.

## Tests ausführen

```bash
pip install -r requirements-dev.txt
pytest -q
```

Die Tests liegen unter `tests/`. Sie setzen kein `~/KI-Lehrer/` voraus – der Smoke-Test
`HOME=/tmp/ki-lehrer-test pytest -q` muss ebenfalls grün sein.

## Logs lesen

Die App schreibt strukturierte Logs nach `~/KI-Lehrer/_log/app.jsonl` — eine JSON-Line pro
Eintrag (jede Zeile ist ein valides JSON-Objekt). Rotation greift bei 1 MB, 5 Backups.

```bash
# letzte Einträge live verfolgen
tail -f ~/KI-Lehrer/_log/app.jsonl

# strukturiert filtern (jq muss installiert sein)
jq 'select(.event == "stream_start")' ~/KI-Lehrer/_log/app.jsonl
jq 'select(.level == "WARNING")' ~/KI-Lehrer/_log/app.jsonl
```

Wichtige `event`-Felder:

| event | Felder | Bedeutung |
|---|---|---|
| `stream_start` | `modus`, `modell` | KI-Antwort startet |
| `stream_done` | `chunks`, `dauer_ms` | KI-Antwort abgeschlossen |
| `sync_tick` | `neue_dateien`, `fehler` | Material-Sync-Durchlauf |
| `git_commit` | `hash`, `dateien` | Git-Commit erstellt |

**Detailliertes Stderr-Logging**: `KI_LEHRER_DEBUG=1` vor App-Start exportieren —
setzt Stderr-Level auf DEBUG, ohne die Logdatei zu beeinflussen.

```bash
KI_LEHRER_DEBUG=1 python3 main.py
```

---

## Hinweise zum ralph.sh-Loop

Falls der Loop erneut gebraucht wird (z.B. für eine neue Stufe nach Spec-Erweiterung):

- **Modell**: `${RALPH_MODEL:-sonnet}`. Opus überspringen — 5× teurer ohne erkennbaren
  Qualitätsvorteil für diese App-Größe.
- **Permission-Mode**: `bypassPermissions`. Im headless `-p`-Modus gibt es keinen User
  zum Approven — Default-Mode hängt sich auf.
- **Smoke-Test**: `py_compile` + `ast.parse` (Flatpak-tauglich), nicht `import tkinter`.
- **Budget kappen**: `RALPH_BUDGET_USD=N ./ralph.sh` — verhindert Kostenexplosion bei
  Hängern. Empfehlung: 3 USD pro Stufe als Obergrenze.
- **Kosten anschauen**: `./ralph.sh kosten` — aggregiert über `log/*.jsonl`.
- **Authentifizierung**: erwartet `ANTHROPIC_API_KEY` als ENV-Variable (kein OAuth/Abo).
- **Promise-Match**: `<promise>STUFE_N_COMPLETE</promise>` mit Tags, nicht nur Konstante.

Bei Loop-Hängern: zuerst `pgrep -af "claude -p"` und Letzte-Aktivität im Log prüfen
(`tail -5 log/ralph-stufeN-iterM.jsonl`), bevor abgebrochen wird.

## Bekannte manuelle Regressions-Checks

- **Fach-Fokus (Stufe 28)**: Im Mathe-Chat „Erklär mir den 30-jährigen Krieg" fragen —
  KI sollte freundlich zum Geschichtsheft verweisen, nicht die Frage beantworten.
  Wenn Deutsch aktiviert ist aber nicht Geschichte: KI soll zurück zu Mathe lenken.
- **Foto-Tag-Override (Stufe 39)**: Status manuell auf „verstanden" setzen → KI darf
  beim nächsten Session-Ende nicht überschreiben. `darf_setzen` muss `False` zurückgeben
  wenn `status_quelle == "mensch"`.
- **Pult-Chat Stream-Persistenz (Stufe 33)**: Stream starten, in Mappe wechseln,
  zurück ins Lehrerpult — Antwort komplett, kein Crash. `chat_view.on_hide()` darf
  laufenden Stream nicht abbrechen.
- **Multi-Profil (Stufe 43)**: Nav-Wechsler funktioniert — Klick auf Schülername
  öffnet Profil-Liste, Wechsel setzt aktives Profil, neues Profil legt eigenen Ordner
  an, altes Profil bleibt im Verzeichnis erhalten (nur `versteckte_schueler`-Filter).
- **Heft-Anmerkung (Stufe 44)**: Anmerkung anlegen → erscheint optisch abgehoben
  im Heft (Blockquote-Format mit Markierung). Rechtsklick → „Anmerkung entfernen" →
  Block verschwindet. KI-Blöcke sind zu keinem Zeitpunkt editierbar.
- **Lehrplan-Import (Stufe 61)**: Fächer-Screen → Tab „Lehrplan" → Beispiel-Datei
  laden → Wochen und Themen sichtbar → Status-Pillen konsistent mit Heft- und
  Foto-Stand. Lehrplan entfernen löscht nur `lehrplan.json` + `_fortschritt.json`,
  nicht Heft oder Sitzungen.
- **Wochenplan-KI (Stufe 62)**: Erste Woche ohne Inhalt → KI-Vorschlag erscheint
  nach ~5 Sekunden in der „Heute lernen"-Karte → manuelles Aktualisieren
  überschreibt den Cache. Ohne KI-Verbindung: nur Unterthemen-Liste sichtbar.
- **Cockpit-Zugang (Stufe 66)**: Ohne PIN → Cockpit-Eintrag nicht in der Nav sichtbar.
  Eltern-PIN konfigurieren und eingeben → Eintrag erscheint sofort, Cockpit navigierbar.
- **PDF-Export (Stufe 70)**: Cockpit → „Bericht als PDF speichern" → Dateidialog → PDF
  wird gespeichert, öffnet sich im System-PDF-Reader, enthält Wochendaten korrekt
  (Sessions, Themen, Heft-Einträge, Foto-Status).
- **Verbrauch-Erfassung (Stufe 74)**: ein Lerngespräch führen → `_verbrauch.jsonl` erhält
  eine neue Zeile mit Modell, Pfad `"lerngespraech"`, Input- und Output-Token-Zahlen.
- **Kosten-Karte (Stufe 76)**: Cockpit öffnen → neue Karte zeigt EUR-Werte für diese
  Woche, Stapel-Balken nach Pfad (Lerngespräch/Wochenplan/…), Top-3 Modelle.
  Bei lokalem Modus: Karte zeigt Hinweis „Lokaler Modus — keine Cloud-Kosten erfasst".
- **Budget-Hinweis (Stufe 77)**: Wochen-Budget auf 0,01 EUR setzen → nach erstem
  Cloud-Aufruf erscheint dezenter grauer Banner im PIN-entsperrten Lehrerpult.
  „× Schließen" → Banner weg, kommt bei neuer Woche wieder wenn erneut überschritten.
  Banner erscheint **nicht** ohne PIN-Entsperrung.
- **Hybrid-Modell (Stufe 73)**: Wochenplan-Vorschlag generieren → Log-Eintrag enthält
  `rolle: "hilfs"` mit Haiku-Modell-Namen. Lerngespräch führen → `rolle: "haupt"` und
  Sonnet-Modell im Log.
