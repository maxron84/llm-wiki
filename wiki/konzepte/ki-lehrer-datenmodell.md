---
date: 2026-05-24
type: konzept
tags: [konzept, ux, desktop, ki-lehrer]
status: draft
---

# KI-Lehrer App — Datenmodell & Ordnerstruktur

**Zusammenfassung**: Was die KI-Lehrer App auf dem Rechner anlegt — Ordnerstruktur, JSON-Schemas, Git-Strategie, Kontext-Ladestrategie. Das Fundament für Wizard und Screens.
**Quellen**: Konzept aus [ki-lehrer-app](ki-lehrer-app.md), [heft-muster](heft-muster.md)
**Zuletzt aktualisiert**: 2026-05-24

---

## Wo liegt der App-Ordner?

```
~/KI-Lehrer/
```

Nicht versteckt (kein `.ki-lehrer`), nicht tief in AppData vergraben. Ein Elternteil oder ein technisch versierter Nutzer kann den Ordner finden, per Cloud-Dienst sichern, auf einen neuen Rechner kopieren. Die App braucht keinen Installer, kein Datenbank-Backend — der Ordner *ist* der Speicher.

---

## Vollständige Ordnerstruktur

```
~/KI-Lehrer/
├── config.json                          # App-Konfiguration (API-Key, Modell, aktiver Schüler)
│
├── schueler/
│   ├── anna/                            # Ein Schüler = ein Ordner
│   │   ├── profil.json                  # Name, Klasse, Fächer, Vorkenntnisse, Toneinstellung
│   │   ├── mathematik/
│   │   │   ├── heft.md                  # Das lebende Aufgabenheft — wächst jede Session
│   │   │   └── sitzungen/
│   │   │       ├── 2026-05-20.md        # Chat-Log einer Session
│   │   │       └── 2026-05-24.md
│   │   ├── deutsch/
│   │   │   ├── heft.md
│   │   │   └── sitzungen/
│   │   └── .git/                        # Ein Git-Repo pro Schüler (nicht pro Fach)
│   │
│   └── tim/
│       ├── profil.json
│       ├── mathematik/
│       │   ├── heft.md
│       │   └── sitzungen/
│       └── .git/
│
└── materialien/                         # Lehrer-Clippings — geteilt, fachweise sortiert
    ├── mathematik/
    │   └── bruchrechnung-klett.md
    └── deutsch/
        └── aufsatz-tipps.md
```

---

## Schlüsselentscheidungen

### 1. Ein Git-Repo pro Schüler — nicht pro Fach

Ein Repo deckt alles ab, was ein Schüler lernt. `git log` zeigt die gesamte Lernhistorie — nach Datum, Fach und Thema. Fachspezifische Repos wären verwaltungsaufwand ohne Mehrwert.

Der DAU-Nutzer sieht nichts davon. Git läuft im Hintergrund. Die technisch versierte Person findet unter `~/KI-Lehrer/schueler/anna/.git/` eine vollständige, lesbare Geschichte.

Commit-Format:
```
[2026-05-24] Mathematik — Bruchrechnung (Session 3)
```

### 2. Heft ist append-only

Die App hängt nach jeder Session einen neuen Abschnitt an `heft.md` an — sie überschreibt nie, sie löscht nie. Git versioniert den Rest. Das Heft ist das sichtbare Produkt für den Schüler; die Sitzungs-Logs sind das Rohmaterial.

### 3. Sitzungs-Logs getrennt vom Heft

`heft.md` enthält, was der Schüler *gelernt* hat (Notizen, Aufgaben, Zusammenfassungen).
`sitzungen/YYYY-MM-DD.md` enthält den *vollständigen Chat-Verlauf* einer Session.

Diese Trennung erlaubt: Heft ist kurz und lesbar, Logs sind vollständig als Kontext für die KI abrufbar.

### 4. Materialien sind global, nicht pro Schüler

Lehrermaterial (`materialien/`) gilt fachübergreifend für alle Schüler. Die App lädt beim Fachstart alle Dateien aus `materialien/[fach]/` als Kontext. Kein Pro-Schüler-Materialordner — das wäre Verwaltungsaufwand.

### 5. JSON, nicht SQLite, nicht YAML

JSON ist mit jedem Texteditor lesbar, braucht keine Libraries für einfache Lese-/Schreibvorgänge, und überlebt jeden App-Versions-Wechsel. SQLite wäre für eine Single-User-Desktop-App Overkill. YAML ist fehleranfälliger bei manueller Bearbeitung.

---

## JSON-Schemas

### config.json — App-Konfiguration

```json
{
  "version": 1,
  "api": {
    "modus": "cloud",
    "key": "sk-ant-...",
    "modell": "claude-sonnet-4-6",
    "lokale_url": null
  },
  "app": {
    "sprache": "de",
    "schriftgroesse": "mittel",
    "aktiver_schueler": "anna"
  }
}
```

`modus` ist `"cloud"` oder `"lokal"`. Bei `"lokal"` wird `lokale_url` ausgewertet (z.B. `"http://localhost:11434"`), `key` bleibt leer. `aktiver_schueler` ist der Ordnername des zuletzt geöffneten Profils — beim Start direkt laden ohne Auswahl-Dialog.

### profil.json — Schülerprofil

```json
{
  "version": 1,
  "anzeigename": "Anna",
  "ordnername": "anna",
  "geburtsjahr": 2015,
  "klasse": "5",
  "erstellt_am": "2026-05-24",
  "faecher": ["mathematik", "deutsch"],
  "einstellungen": {
    "ton": "du",
    "tempo": "mittel",
    "erklaer_tiefe": "einfach"
  },
  "vorkenntnisse": {
    "mathematik": "Grundrechenarten bekannt, Brüche noch unsicher",
    "deutsch": "Rechtschreibung gut, Aufsätze üben"
  }
}
```

`ordnername` ist der technische Slug (Kleinbuchstaben, keine Sonderzeichen). `anzeigename` ist der sichtbare Name in der App. Beide können identisch sein. `einstellungen.ton` steuert wie die KI spricht — `"du"` (für Kinder), `"Sie"` (für Erwachsene). `erklaer_tiefe` ist `"einfach"`, `"mittel"` oder `"detailliert"` — kommt direkt aus dem Setup-Interview.

---

## Was die KI als Kontext bekommt

Pro Session lädt die App vier Dinge — in dieser Reihenfolge, von klein zu groß:

| Quelle | Inhalt | Wozu |
|---|---|---|
| `profil.json` | Name, Klasse, Vorkenntnisse, Toneinstellung | System-Prompt: Wie soll die KI sich verhalten? |
| `materialien/[fach]/` | Alle `.md`-Dateien des Fachs | Lehrstoff-Grundlage für diese Session |
| `sitzungen/` (letzte 3) | Vorherige Chat-Logs | Kontinuität: Was haben wir zuletzt besprochen? |
| `heft.md` (Anfang) | Aufgaben und Notizen des Schülers | Wo steht der Schüler gerade? |

Mehr als diese vier Quellen braucht die KI nicht. Der Kontext bleibt überschaubar — das ist entscheidend für kurze Antwortzeiten, besonders bei lokalen Modellen.

---

## Dateiformate

### heft.md

```markdown
# Matheheft — Anna

## 2026-05-20 — Grundrechenarten

### Was ich heute gelernt habe
Ich habe geübt, wie man Brüche mit gleichem Nenner addiert.

### Aufgaben
1. 1/4 + 2/4 = 3/4 ✓
2. 3/8 + 5/8 = 8/8 = 1 ✓

---

## 2026-05-24 — Bruchrechnung mit verschiedenen Nennern

...
```

### sitzungen/2026-05-24.md

```markdown
# Sitzung 2026-05-24 — Mathematik: Bruchrechnung

**[15:32]** Anna: Ich verstehe nicht, warum 1/2 + 1/3 nicht 2/5 ergibt.

**[15:33]** Lehrer: Gute Frage! Stell dir vor, du hast eine Pizza in 2 Teile geschnitten...
```

---

## Backup-Strategie für DAUs

Die App zeigt im Einstellungs-Screen den Pfad `~/KI-Lehrer/` mit dem Hinweis: „Sicherung: Diesen Ordner in iCloud, OneDrive oder auf einen USB-Stick kopieren." Keine eingebaute Backup-Funktion — der Ordner ist self-contained, jeder Datei-Sync funktioniert.

---

## Was der Setup-Wizard anlegt

Beim ersten Start erstellt der Wizard automatisch:

1. `~/KI-Lehrer/` — App-Ordner
2. `~/KI-Lehrer/config.json` — mit API-Key aus dem Interview
3. `~/KI-Lehrer/schueler/[name]/` — erster Schülerprofil-Ordner
4. `~/KI-Lehrer/schueler/[name]/profil.json` — aus den Interview-Antworten
5. `~/KI-Lehrer/schueler/[name]/[fach]/heft.md` — leer, bereit für erste Session
6. `git init` im Schüler-Ordner — automatisch, unsichtbar

Ab diesem Punkt ist die App einsatzbereit. Kein Terminal, keine Konfigurationsdateien öffnen.

---

## Verwandte Seiten

- [ki-lehrer-app](ki-lehrer-app.md) — Übergeordnetes Konzept: DAU-Prinzip, Wireframe, Cloud/Lokal
- [heft-muster](heft-muster.md) — Das Heft-Konzept im Detail: Git als implizite Lernhistorie
- [ki-lehrer-screens](ki-lehrer-screens.md) — Die fünf Screens im Detail (Lehrerpult, Aufgaben, Chat, Fächer, Einstellungen)

---

[Wiki-Index](../index.md)
