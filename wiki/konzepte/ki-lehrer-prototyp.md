---
date: 2026-05-24
type: konzept
tags: [konzept, ux, desktop, ki-lehrer, anleitung]
status: draft
---

# KI-Lehrer App — Prototyp-Konzept

**Zusammenfassung**: Tech-Stack, Projektstruktur, Abhängigkeiten und Build-Reihenfolge für die Umsetzung der KI-Lehrer App. Grundlage für das eigenständige Entwicklungsprojekt.
**Quellen**: Baut auf [ki-lehrer-app](ki-lehrer-app.md), [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md), [ki-lehrer-screens](ki-lehrer-screens.md), [ki-lehrer-setup-wizard](ki-lehrer-setup-wizard.md) auf.
**Zuletzt aktualisiert**: 2026-05-24

---

## Tech-Stack

| Schicht | Technologie | Begründung |
|---|---|---|
| GUI-Framework | **Tkinter** (Python-Stdlib) | Keine Drittanbieter-Abhängigkeit, auf jedem Rechner verfügbar (python3-tk), ausreichend für DAU-Desktop-App |
| KI-Cloud | **anthropic** Python SDK | Direktanbindung Anthropic API, Streaming-Support |
| KI-Lokal | **openai** Python SDK (OpenAI-compat.) | Ollama und LM Studio sprechen die OpenAI-kompatible REST-API — ein SDK für beide Backends |
| Git-Operationen | **gitpython** | `git init` und `git commit` aus Python heraus, kein Shell-Aufruf nötig |
| Datenhaltung | JSON + Markdown (Stdlib) | Kein ORM, keine DB — der Ordner ist der Speicher (→ [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md)) |
| Dateipfade | **pathlib** (Stdlib) | Plattformunabhängige Pfadbehandlung (Windows/macOS/Linux) |

**Python-Mindestversion**: 3.10 (für `match`-Ausdrücke und zuverlässige Stdlib-Abdeckung).

---

## Projektstruktur

```
ki-lehrer/
│
├── main.py                  # Entry-Point: App-Instanz erzeugen, mainloop starten
├── app.py                   # Hauptfenster, Nav-Container, Screen-Switching-Logik
├── requirements.txt         # anthropic, openai, gitpython
│
├── gui/
│   ├── nav.py               # Linke Navigationsleiste (immer sichtbar)
│   ├── screens/
│   │   ├── lehrerpult.py    # Screen 1: Fachkarten, Wochenfortschritt
│   │   ├── aufgabenhefte.py # Screen 2: Tabs Heft / Sitzungen / Chat
│   │   ├── faecher.py       # Screen 3: Tabs Meine Fächer / Materialien
│   │   └── einstellungen.py # Screen 4: Tabs KI-Verbindung / Schülerprofil / App
│   └── wizard/
│       ├── wizard.py        # Wizard-Controller: Schritt-Steuerung, Navigation
│       └── steps/
│           ├── s0_willkommen.py
│           ├── s1_name.py
│           ├── s2_klasse.py
│           ├── s3_faecher.py
│           ├── s4_anrede.py
│           ├── s5_erklaertiefe.py
│           ├── s6_verbindung.py
│           ├── s7a_api_key.py
│           ├── s7b_lokal.py
│           └── s8_fertig.py
│
└── core/
    ├── data.py              # JSON lesen/schreiben, Ordner anlegen, heft.md append
    ├── git_manager.py       # git init, commit mit formatiertem Commit-Message
    ├── api.py               # API-Calls: Anthropic SDK + OpenAI-compat. für lokal
    └── context.py           # Kontext-Ladestrategie (profil.json + materialien + logs + heft)
```

Keine `assets/`-Ordner, keine Icons in V1 — Tkinter-Standard-Widgets, DAU-tauglich ohne visuellen Overhead.

---

## Abhängigkeiten

```
# requirements.txt
anthropic>=0.40.0
openai>=1.50.0
gitpython>=3.1.40
```

Tkinter und pathlib kommen aus der Stdlib. Auf Linux muss `python3-tk` über den Paketmanager installiert sein — das ist die einzige Systembedingung.

---

## Build-Reihenfolge (MVP → vollständig)

Die App wird in Schichten gebaut — jede Stufe ist eigenständig lauffähig.

### Stufe 1 — Grundgerüst (läuft, zeigt nichts außer Navigation)

- `main.py` + `app.py`: Fenster öffnen, Größe, Titel
- `gui/nav.py`: Linke Leiste mit den vier Nav-Punkten, aktiver Punkt hervorgehoben
- `gui/screens/*.py`: Vier Platzhalter-Frames, je ein Label mit Screen-Namen
- Screen-Switching: Klick auf Nav-Punkt tauscht den sichtbaren Frame aus

**Ziel**: App startet, Navigation funktioniert, keine Logik.

---

### Stufe 2 — Setup-Wizard

- `gui/wizard/wizard.py`: Schritt-Container, Fortschrittspunkte, Zurück/Weiter-Buttons
- Alle 10 Wizard-Schritte (s0–s8, inkl. 7a/7b) als einzelne Frame-Klassen
- Validierungslogik: „Weiter" nur aktiv wenn Pflichtfelder gesetzt
- Am Ende: Daten gesammelt im Wizard-State-Dict bereithalten

**Ziel**: Wizard ist durchklickbar, Daten werden gesammelt, noch kein Schreiben.

---

### Stufe 3 — Datenmodell

- `core/data.py`: Ordnerstruktur anlegen (`~/KI-Lehrer/`), `config.json` und `profil.json` schreiben, `heft.md` erzeugen
- `core/git_manager.py`: `git init` im Schüler-Ordner, erster Commit
- Wizard-Abschluss löst Datenanlage aus (Stufe 8 → „Jetzt starten!")
- `core/data.py`: Profile laden, Schülerliste lesen, aktiven Schüler setzen

**Ziel**: Wizard legt echte Dateien an, App startet danach mit Lehrerpult.

---

### Stufe 4 — Einstellungen-Screen

- Tab „KI-Verbindung": Radiobutton Cloud/Lokal, API-Key-Feld (maskiert), Modell, „Verbindung testen"-Button
- Tab „Schülerprofil": Felder aus `profil.json`, Speichern-Button
- Tab „App": Schriftgröße, Daten-Ordner anzeigen, „Im Dateimanager öffnen"
- Alle Änderungen persistieren in `config.json` / `profil.json`

**Ziel**: Einstellungen lesbar und schreibbar, API-Test funktioniert.

---

### Stufe 5 — Chat-Tab (Kern der App)

- `core/api.py`: Anthropic SDK (Cloud) + OpenAI-compat. (lokal), Streaming-Antworten
- `core/context.py`: Vier Quellen laden (profil.json, materialien, letzte 3 Logs, heft-Anfang), System-Prompt zusammenbauen
- `gui/screens/aufgabenhefte.py` → Tab „Chat": Nachrichten-Scrollbereich, Eingabefeld, Senden-Button
- Automatischer Eröffnungsgruß wenn Tab geöffnet wird
- „Session beenden": KI schreibt Zusammenfassung, `heft.md` wird appended, Sitzungs-Log gespeichert, git commit

**Ziel**: Echtes Gespräch mit der KI, Heft wird geschrieben.

---

### Stufe 6 — Aufgabenhefte-Screen (vollständig)

- Tab „Heft": `heft.md` lesen und in scrollbarem Text-Widget anzeigen (read-only)
- Tab „Sitzungen": Sitzungsliste aus `sitzungen/`-Ordner, Klick öffnet Chat-Log
- Fach-Auswahl links im Screen

**Ziel**: Schüler kann sein Heft und vergangene Sessions nachlesen.

---

### Stufe 7 — Lehrerpult (vollständig)

- Fachkarten aus aktiven Fächern in `profil.json` generieren
- Letzte Session pro Fach auslesen (Datum, Thema aus Log-Header)
- Wochenfortschritt: Sitzungen der letzten 7 Tage zählen, Punkte rendern
- Schüler-Dropdown: alle Profile in `schueler/`, Schüler wechseln
- „Starten"-Button → Aufgabenhefte-Screen, Chat-Tab direkt aktiv

**Ziel**: Lehrerpult zeigt echten Zustand, schneller Einstieg in zwei Klicks.

---

### Stufe 8 — Fächer-Screen

- Tab „Meine Fächer": Checkbox-Liste, Aktivieren/Deaktivieren persistiert in `profil.json`
- Tab „Materialien": Dateiliste aus `materialien/[fach]/`, „+ Datei hinzufügen" (Dateidialog + Kopieren), „Ordner öffnen"
- „+ Eigenes Fach": Inline-Dialog, Ordner anlegen

**Ziel**: Fächer und Materialien verwaltbar ohne Terminal.

---

## Architektur-Prinzipien für die Umsetzung

**Screens als eigenständige Frame-Klassen** — jeder Screen erbt von `tk.Frame`, kennt seinen Parent-Container, aber nicht die anderen Screens. App-weiter Zustand (aktiver Schüler, geladene Config) liegt in einem zentralen `AppState`-Objekt, das beim Start instanziiert und an alle Screens übergeben wird.

**Kein globaler Tkinter-State** — keine globalen Variablen, kein `StringVar`-Flickenteppich über Modul-Grenzen. Alle Werte laufen durch `AppState`.

**core/ kennt kein tkinter** — `data.py`, `api.py`, `git_manager.py`, `context.py` importieren kein Tkinter. Das trennt GUI-Logik von Domänen-Logik und macht Unit-Tests möglich.

**Streaming im Thread** — API-Calls (Stufe 5) laufen in einem eigenen Thread, damit die GUI während des Streamings nicht einfriert. Tkinter ist nicht threadsafe: Nachrichten-Updates kommen über `root.after()` zurück in den Haupt-Thread.

---

## Erste Session im neuen Projekt

Wenn das Entwicklungsprojekt angelegt ist, empfehlen sich folgende Einstiegsfragen an die KI in diesem Wiki:

- „Was genau passiert wenn `Session beenden` geklickt wird?" → [ki-lehrer-screens](ki-lehrer-screens.md)
- „Welche Felder schreibt der Wizard in config.json?" → [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md)
- „Was bekommt die KI als System-Prompt?" → [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md) Abschnitt „Was die KI als Kontext bekommt"

---

## Verwandte Seiten

- [ki-lehrer-app](ki-lehrer-app.md) — DAU-Prinzip, Wireframe, Cloud/Lokal-Entscheidung
- [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md) — Ordnerstruktur, JSON-Schemas, Kontext-Ladestrategie
- [ki-lehrer-screens](ki-lehrer-screens.md) — Die vier Screens im Detail
- [ki-lehrer-setup-wizard](ki-lehrer-setup-wizard.md) — Alle neun Wizard-Schritte
- [foto-review](foto-review.md) — Optionaler Vision-Workflow für spätere Ausbaustufe

---

[Wiki-Index](../index.md)
