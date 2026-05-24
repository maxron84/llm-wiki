---
date: 2026-05-24
type: konzept
tags: [konzept, ux, desktop, ki-lehrer]
status: draft
---

# KI-Lehrer App — Die vier Screens

**Zusammenfassung**: Detaillierter Entwurf aller vier Navigations-Screens der KI-Lehrer App mit Wireframes, Tab-Struktur und DAU-Begründungen für jede Designentscheidung.
**Quellen**: Baut auf [ki-lehrer-app](ki-lehrer-app.md) und [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md) auf.
**Zuletzt aktualisiert**: 2026-05-24

---

## Navigation

Das App-Fenster ist dauerhaft zweigeteilt: linke Navigationsleiste (fest), rechter Hauptbereich (wechselt je nach aktivem Nav-Punkt). Vier Nav-Punkte — mehr nicht.

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │                                             │
│  Lehrerpult    │  [Hauptbereich]                             │
│                │                                             │
│  Aufgaben-     │                                             │
│  hefte         │                                             │
│                │                                             │
│  Fächer        │                                             │
│                │                                             │
│  Einstellungen │                                             │
│                │                                             │
└────────────────┴────────────────────────────────────────────┘
```

Der aktive Nav-Punkt ist visuell hervorgehoben (fetter Text, Hintergrundfarbe). Kein Hamburger-Menü, kein Verstecken — die Navigation ist immer sichtbar.

---

## Screen 1: Lehrerpult

Der Startscreen. Jedes Mal wenn die App geöffnet wird, landet man hier.

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │                                             │
│  ● Lehrerpult  │  Hallo, Anna! Was lernst du heute?          │
│                │                                             │
│  Aufgaben-     │  ┌───────────────────┐  ┌───────────────┐  │
│  hefte         │  │  Mathematik       │  │  Deutsch      │  │
│                │  │                   │  │               │  │
│  Fächer        │  │  Zuletzt: 22.05.  │  │  Zuletzt:     │  │
│                │  │  Bruchrechnung    │  │  19.05.       │  │
│  Einstellungen │  │                   │  │  Aufsätze     │  │
│                │  │  [ Starten  ]     │  │               │  │
│                │  └───────────────────┘  │  [ Starten ]  │  │
│                │                         └───────────────┘  │
│                │  Diese Woche: ● ● ○ ○ ○ ○ ○               │
│                │                                             │
│                │                       [ Schüler: Anna ▾ ]  │
└────────────────┴────────────────────────────────────────────┘
```

### Was der Screen zeigt

**Begrüßung mit Schülername** — nicht „Willkommen in der App", sondern „Hallo, Anna!". Der Name kommt aus `profil.json`. Das ist der einzige personalisierte Text auf diesem Screen und signalisiert: die App kennt dich.

**Fachkarten** — eine Karte pro aktivem Fach. Jede zeigt: Fachname, Datum der letzten Session, Thema der letzten Session (aus dem letzten Sitzungs-Log), und einen großen „Starten"-Button. Das ist die schnellste Route von App-Start zu laufender Session: ein Klick.

**Wochenfortschritt** — sieben Punkte, einer pro Wochentag. Gefüllte Punkte = Tage mit abgeschlossener Session. Kein Prozentsatz, kein Notenäquivalent, keine Wertung — nur eine stille visuelle Rückmeldung: „Diese Woche war ich an zwei Tagen da."

**Schüler wechseln** — Dropdown unten rechts zeigt den aktiven Schüler. Klick öffnet eine Liste aller Profile mit „Neues Profil anlegen"-Eintrag. Für Familien mit mehreren Kindern. Für das Einzelkind ist das unsichtbar im Hintergrund.

### DAU-Entscheidungen

- Keine Tabs auf diesem Screen — der Lehrerpult ist bewusst einfach
- Kein Fortschrittsdiagramm, keine Statistiken — zu viel Komplexität für den Eingangsscreen
- Die Fachkarten sind der Call-to-Action, nicht ein Menüpunkt; Kinder klicken auf das Fach, nicht auf „Aufgabenhefte → Mathematik → Chat starten"
- „Starten" ist absichtlich vage — ob das eine neue Session beginnt oder die letzte fortsetzt, entscheidet die App anhand der letzten Sitzungsdatei

---

## Screen 2: Aufgabenhefte

Das Herzstück der App — hier passiert die eigentliche Arbeit. Der Screen hat drei Tabs, die einen natürlichen Workflow abbilden: Heft lesen → vergangene Sitzungen nachschlagen → aktuelle Session starten.

Ein Sub-Navigationselement (einfache Liste) links im Hauptbereich wählt das Fach; die Tabs wechseln den Modus für das ausgewählte Fach.

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [Heft] [Sitzungen] [Chat]                   │
│  Lehrerpult    ├──────────────┬──────────────────────────────┤
│                │              │                              │
│  ● Aufgaben-   │ Mathematik   │  [Heft-Inhalt hier]          │
│    hefte       │ Deutsch      │                              │
│                │              │                              │
│  Fächer        │              │                              │
│                │              │                              │
│  Einstellungen │              │                              │
└────────────────┴──────────────┴──────────────────────────────┘
```

### Tab 1: Heft

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [● Heft] [Sitzungen] [Chat]                 │
│  Lehrerpult    ├──────────────┬──────────────────────────────┤
│                │              │ Matheheft — Anna             │
│  ● Aufgaben-   │ ● Mathematik │                              │
│    hefte       │   Deutsch    │ ── 24.05.2026 ─────────────  │
│                │              │ Bruchrechnung                │
│  Fächer        │              │ Was ich heute gelernt habe:  │
│                │              │ Ich habe gelernt, dass ich   │
│  Einstellungen │              │ zuerst den gleichen Nenner   │
│                │              │ herstellen muss...           │
│                │              │                              │
│                │              │ ── 20.05.2026 ─────────────  │
│                │              │ Grundrechenarten             │
│                │              │ ...                          │
│                │              │                              │
│                │              │              [Heft drucken]  │
└────────────────┴──────────────┴──────────────────────────────┘
```

Das Heft zeigt `heft.md` — scrollbar, read-only. Der Schüler sieht, was er über Wochen erarbeitet hat. Keine Editierfunktion: das Heft wird von der App und der KI geschrieben, nicht vom Schüler direkt.

„Heft drucken" exportiert den Inhalt als PDF — optional, für Eltern die einen Ausdruck wollen.

### Tab 2: Sitzungen

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [Heft] [● Sitzungen] [Chat]                 │
│  Lehrerpult    ├──────────────┬──────────────────────────────┤
│                │              │ Sitzungen — Mathematik       │
│  ● Aufgaben-   │ ● Mathematik │                              │
│    hefte       │   Deutsch    │ 24.05.2026   Bruchrechnung   │
│                │              │ 20.05.2026   Grundrechnen    │
│  Fächer        │              │ 14.05.2026   Textaufgaben    │
│                │              │                              │
│  Einstellungen │              │ [Klick auf Zeile öffnet den  │
│                │              │  Chat-Log in neuem Bereich]  │
└────────────────┴──────────────┴──────────────────────────────┘
```

Chronologische Liste aller Sitzungen für das aktive Fach — Datum und Thema. Klick öffnet den vollständigen Chat-Log (read-only). Für Eltern die nachvollziehen wollen, was besprochen wurde.

### Tab 3: Chat

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [Heft] [Sitzungen] [● Chat]                 │
│  Lehrerpult    ├──────────────┬──────────────────────────────┤
│                │              │                              │
│  ● Aufgaben-   │ ● Mathematik │  Lehrer: Hallo Anna! Schön   │
│    hefte       │   Deutsch    │  dass du da bist. Was üben   │
│                │              │  wir heute in Mathematik?   │
│  Fächer        │              │                              │
│                │              │     Anna: Ich verstehe       │
│  Einstellungen │              │     Brüche nicht.            │
│                │              │                              │
│                │              │  Lehrer: Das kriegen wir     │
│                │              │  hin! Stell dir vor, du      │
│                │              │  hast eine Pizza in vier     │
│                │              │  gleiche Teile geschnitten…  │
│                │              │                              │
│                │              │     Anna: Ah okay, also...   │
│                │              │                              │
│                │              ├──────────────────────────────┤
│                │              │ Schreib hier...              │
│                │              │                              │
│                │              ├──────────────────────────────┤
│                │              │[Session beenden] [Senden ▶ ] │
└────────────────┴──────────────┴──────────────────────────────┘
```

Der Chat ist der Kern der App. Alles andere ist Rahmen.

**Automatischer Eröffnungsgruß** — wenn der Tab geöffnet wird, schickt die KI die erste Nachricht. Der Schüler muss nicht „starten" oder eine Anfrage formulieren. Das senkt die Einstiegshürde radikal, besonders für jüngere Kinder.

**Visueller Gesprächsfluss** — Schüler-Nachrichten rechtsbündig (oder hellgrau hinterlegt), KI-Antworten linksbündig. Keine Avatare, keine Bilder — nur Farbe und Ausrichtung. Lesbar für Kinder.

**Eingabefeld** — mehrzeilig, groß, deutlich abgegrenzt. Kein „Enter zum Absenden" standardmäßig — Kinder drücken Enter mitten im Satz. Stattdessen ein großer „Senden"-Button.

**„Session beenden"** — bewusst links positioniert (nicht prominent, aber erreichbar). Klick löst aus: KI schreibt eine Zusammenfassung ins Heft, git commit mit Datum und Thema, Chat-Log wird als Datei gespeichert. Der Schüler sieht: „Super! Deine Notizen wurden gespeichert." — kein technischer Output.

**Was die App im Hintergrund tut wenn „Chat" geöffnet wird:**
1. Lädt `profil.json` → Schülername, Ton, Erklärtiefe → System-Prompt
2. Lädt `materialien/[fach]/` → Unterrichtsmaterial als Kontext
3. Lädt die letzten 3 Sitzungs-Logs → Kontinuität
4. Lädt den Anfang von `heft.md` → Wo steht der Schüler?
5. Konstruiert die KI-Begrüßungsnachricht aus diesem Kontext

Das alles passiert in Sekunden, unsichtbar. Der Schüler sieht: die KI sagt Hallo.

---

## Screen 3: Fächer

Verwaltungsscreen für Fächer und Unterrichtsmaterial. Primär für den Elternteil oder Lehrer, weniger für den Schüler selbst.

### Tab 1: Meine Fächer

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [● Meine Fächer] [Materialien]              │
│  Lehrerpult    ├─────────────────────────────────────────────┤
│                │                                             │
│  Aufgaben-     │  ☑  Mathematik                             │
│  hefte         │  ☑  Deutsch                                │
│                │  ☐  Englisch                               │
│  ● Fächer      │  ☐  Sachkunde                              │
│                │  ☐  Musik                                  │
│  Einstellungen │                                             │
│                │  [ + Eigenes Fach hinzufügen ]             │
│                │                                             │
└────────────────┴────────────────────────────────────────────┘
```

Checkbox-Liste aller Fächer. Aktivierte Fächer erscheinen auf dem Lehrerpult und im Aufgabenhefte-Screen. Deaktivieren entfernt das Fach aus der Sicht, löscht keine Daten.

„+ Eigenes Fach hinzufügen" öffnet einen einfachen Dialog mit einem Textfeld: Fachname. Die App legt den Unterordner automatisch an.

### Tab 2: Materialien

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [Meine Fächer] [● Materialien]              │
│  Lehrerpult    ├──────────────┬──────────────────────────────┤
│                │              │ Materialien — Mathematik     │
│  Aufgaben-     │ ● Mathematik │                              │
│  hefte         │   Deutsch    │  bruchrechnung-klett.md      │
│                │              │  textaufgaben-tipps.md       │
│  ● Fächer      │              │                              │
│                │              │  [ + Datei hinzufügen ]      │
│  Einstellungen │              │  [ Ordner öffnen ]           │
│                │              │                              │
└────────────────┴──────────────┴──────────────────────────────┘
```

Zeigt alle Dateien in `~/KI-Lehrer/materialien/[fach]/`. Zwei Aktionen:

- **„+ Datei hinzufügen"** — öffnet den System-Dateidialog. Ausgewählte Datei wird in den Materialien-Ordner kopiert. Fertig.
- **„Ordner öffnen"** — öffnet `~/KI-Lehrer/materialien/[fach]/` im Dateimanager. Für den technisch versierten Nutzer der lieber direkt arbeitet.

Kein Editor, kein Drag-and-Drop im Fenster — zu fehleranfällig für DAU. Nur kopieren und anzeigen.

Die Materialien werden jeder Chat-Session als Kontext mitgegeben. Je mehr (und je relevanter) die Dateien hier, desto spezifischer die KI-Antworten.

---

## Screen 4: Einstellungen

Drei Tabs: API-Verbindung, Schülerprofil, App. Dieser Screen ist beim ersten Start automatisch geöffnet (nach dem Setup-Wizard) und danach über die Nav erreichbar.

### Tab 1: KI-Verbindung

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [● KI-Verbindung] [Schülerprofil] [App]     │
│  Lehrerpult    ├─────────────────────────────────────────────┤
│                │                                             │
│  Aufgaben-     │  Wie soll die KI erreichbar sein?           │
│  hefte         │                                             │
│                │  (●) Über das Internet (empfohlen)          │
│  Fächer        │  ( ) Auf diesem Computer                    │
│                │                                             │
│  ● Einstell-   │  ── Internet-Einstellungen ───────────────  │
│    ungen       │  API-Schlüssel:  [sk-ant-●●●●●●●●●] [✎]    │
│                │  Modell:  claude-sonnet-4-6        [✎]      │
│                │                                             │
│                │  [ Verbindung testen ]  ✓ Verbunden         │
│                │                                             │
└────────────────┴────────────────────────────────────────────┘
```

**Keine technischen Begriffe** — nicht „API-Modus: cloud/lokal", sondern „Über das Internet / Auf diesem Computer". Der Radiobutton-Wechsel blendet den jeweils passenden Einstellungsblock ein.

Der API-Key wird maskiert angezeigt, mit einem Stift-Symbol zum Bearbeiten. Er wird niemals im Klartext auf dem Screen stehen — falls jemand über die Schulter schaut.

**„Verbindung testen"** — schickt eine minimale API-Anfrage und zeigt „✓ Verbunden" oder eine klare Fehlermeldung in Klartext (nicht den HTTP-Statuscode).

### Tab 2: Schülerprofil

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [KI-Verbindung] [● Schülerprofil] [App]     │
│  Lehrerpult    ├─────────────────────────────────────────────┤
│                │                                             │
│  Aufgaben-     │  Name:      [ Anna            ]            │
│  hefte         │  Klasse:    [ 5               ]            │
│                │                                             │
│  Fächer        │  Wie soll der Lehrer sprechen?              │
│                │  (●) Du       ( ) Sie                       │
│  ● Einstell-   │                                             │
│    ungen       │  Wie ausführlich soll erklärt werden?        │
│                │  ( ) Kurz   (●) Mittel   ( ) Ausführlich    │
│                │                                             │
│                │  Vorkenntnisse (optional):                  │
│                │  Mathematik: [ Brüche noch unsicher    ]    │
│                │  Deutsch:    [ Aufsätze üben           ]    │
│                │                                             │
│                │                         [ Speichern ]      │
└────────────────┴────────────────────────────────────────────┘
```

Alle Felder aus `profil.json`, in menschlicher Sprache. „Wie ausführlich soll erklärt werden?" statt `erklaer_tiefe: mittel`. Die gespeicherten Werte landen direkt im System-Prompt der nächsten Session.

### Tab 3: App

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer                                         [─][□][×] │
├────────────────┬────────────────────────────────────────────┤
│                │ [KI-Verbindung] [Schülerprofil] [● App]     │
│  Lehrerpult    ├─────────────────────────────────────────────┤
│                │                                             │
│  Aufgaben-     │  Schriftgröße:  ( ) Klein  (●) Mittel       │
│  hefte         │                 ( ) Groß                    │
│                │                                             │
│  Fächer        │  Daten-Ordner:                              │
│                │  ~/KI-Lehrer/                               │
│  ● Einstell-   │  [ Im Dateimanager öffnen ]                 │
│    ungen       │                                             │
│                │  Sicherung: Kopiere diesen Ordner           │
│                │  regelmäßig auf einen USB-Stick oder in     │
│                │  einen Cloud-Speicher (iCloud, OneDrive).   │
│                │                                             │
└────────────────┴────────────────────────────────────────────┘
```

Minimal gehalten. Schriftgröße als einzige visuelle Einstellung. Der Daten-Ordner ist sichtbar und kann im Dateimanager geöffnet werden — das beantwortet die Frage „Wo sind meine Dateien?" ohne technischen Support. Der Backup-Hinweis ist Klartext, kein automatisches Backup — einfacher, robuster, keine falschen Versprechen.

---

## Zusammenfassung: Wie die Screens zusammenhängen

```
App-Start
    │
    ▼
Lehrerpult ──[Fach-Starten-Button]──▶ Aufgabenhefte → Chat (Tab automatisch aktiv)
    │
    ├──[Nav: Aufgabenhefte]──▶ Aufgabenhefte → Heft (Tab aktiv: letzter Stand)
    │
    ├──[Nav: Fächer]──▶ Fächer → Meine Fächer
    │
    └──[Nav: Einstellungen]──▶ Einstellungen → KI-Verbindung
```

Der schnellste Weg zur laufenden Session: App öffnen → Fachkarte auf dem Lehrerpult klicken. Zwei Klicks. Kein Menü, kein Untermenü, kein Dialog.

---

## Verwandte Seiten

- [ki-lehrer-app](ki-lehrer-app.md) — Übergeordnetes Konzept: DAU-Prinzip, Wireframe, Cloud/Lokal
- [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md) — Was die App auf dem Rechner anlegt — Ordner, JSON, Git
- [heft-muster](heft-muster.md) — Das Heft-Konzept im Detail

---

[Wiki-Index](../index.md)
