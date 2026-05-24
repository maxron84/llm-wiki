---
date: 2026-05-24
type: konzept
tags: [konzept, ux, desktop, ki-lehrer]
status: draft
---

# KI-Lehrer App — Setup-Wizard

**Zusammenfassung**: Der Einrichtungs-Wizard läuft einmalig beim ersten App-Start. Neun Schritte, jeder Dialog stellt genau eine Frage. Am Ende ist die App vollständig eingerichtet und einsatzbereit — kein Terminal, kein Dateisystem, kein Fachjargon.
**Quellen**: Baut auf [ki-lehrer-app](ki-lehrer-app.md), [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md) und [ki-lehrer-screens](ki-lehrer-screens.md) auf.
**Zuletzt aktualisiert**: 2026-05-24

---

## Struktur des Wizards

Der Wizard ersetzt beim ersten Start den normalen App-Inhalt vollständig. Die linke Navigationsleiste ist ausgeblendet — es gibt keinen Weg, am Wizard vorbeizukommen, ohne ihn abzuschließen. Nach dem letzten Schritt erscheint die normale App mit Lehrerpult.

**Fensterlayout während des Wizards:**

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ○ ○ ● ○ ○ ○ ○  (Fortschrittsanzeige: 7 Punkte)           │
│                                                             │
│  [Schritt-Inhalt]                                           │
│                                                             │
│                                                             │
│                              [Zurück]  [Weiter]            │
└─────────────────────────────────────────────────────────────┘
```

**Fortschrittsanzeige**: Sieben Punkte (einer pro Schritt mit Dateneingabe). Der aktuelle Schritt ist ausgefüllt, abgeschlossene ebenfalls, zukünftige leer. Keine Schritt-Nummer, keine Prozentzahl — zu technisch. Nur Punkte.

**Navigation**: Jeder Schritt (außer dem ersten und letzten) hat einen „Zurück"-Button links und „Weiter" rechts. „Weiter" ist nur aktiv wenn die Pflichtfelder des Schritts ausgefüllt sind. Kein Schritt ist endgültig bevor der Wizard abgeschlossen ist.

---

## Schritt 0: Willkommen

Kein Fortschrittspunkt, kein Eingabefeld. Reiner Einstieg.

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│                                                             │
│            Willkommen beim KI-Lehrer!                       │
│                                                             │
│     Diese App hilft beim Lernen — mit einer KI die          │
│     erklärt, fragt und geduldig auf Antworten wartet.       │
│                                                             │
│     Wir richten jetzt alles ein.                            │
│     Das dauert nur ein paar Minuten.                        │
│                                                             │
│                                                             │
│                                    [ Los geht's! ]         │
└─────────────────────────────────────────────────────────────┘
```

Kein Hinweis auf Tkinter, Python, Anthropic oder API. Keine Lizenzvereinbarung. Kein „Bitte lesen Sie die Dokumentation." Einfach: worum geht es, wie lange dauert es, starten.

---

## Schritt 1: Name des Schülers

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ● ○ ○ ○ ○ ○ ○                                             │
│                                                             │
│  Wer wird mit der App lernen?                               │
│                                                             │
│  ┌────────────────────────────────────────────────────┐    │
│  │ Anna                                               │    │
│  └────────────────────────────────────────────────────┘    │
│                                                             │
│  Tipp: Gib den Vornamen ein, so wie er oder sie             │
│  angesprochen werden möchte.                                │
│                                                             │
│                                                             │
│                                    [Weiter]                │
└─────────────────────────────────────────────────────────────┘
```

Ein einziges Textfeld, kein Label davor. Die Frage darüber ist der Label. „Weiter" wird aktiv sobald mindestens ein Zeichen eingegeben wurde.

Der Name wird zum Ordnernamen (kleingeschrieben, Sonderzeichen ersetzt — unsichtbar für den Nutzer) und zum Anzeigenamen in der App. Wenn zwei Schüler denselben Namen haben, hängt die App beim zweiten automatisch eine Zahl an den Ordnernamen, nicht an den Anzeigenamen.

---

## Schritt 2: Klasse

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ● ● ○ ○ ○ ○ ○                                             │
│                                                             │
│  In welcher Klasse ist Anna?                                │
│                                                             │
│  ┌──┐ ┌──┐ ┌──┐ ┌──┐ ┌──┐ ┌──┐                           │
│  │ 1│ │ 2│ │ 3│ │ 4│ │●5│ │ 6│                           │
│  └──┘ └──┘ └──┘ └──┘ └──┘ └──┘                           │
│  ┌──┐ ┌──┐ ┌──┐ ┌──┐ ┌──┐ ┌──────────┐                  │
│  │ 7│ │ 8│ │ 9│ │10│ │11│ │  Andere  │                  │
│  └──┘ └──┘ └──┘ └──┘ └──┘ └──────────┘                  │
│                                                             │
│                                                             │
│                             [Zurück]  [Weiter]             │
└─────────────────────────────────────────────────────────────┘
```

Große, klickbare Zahlen-Buttons statt Dropdown. Kein Tipp-Fehler möglich. „Andere" für Vorschule, Erwachsene, nicht-schulische Anwender — öffnet ein kleines Freitext-Feld direkt darunter.

Die Klasse fließt in `profil.json` und von dort in den System-Prompt: die KI weiß, dass sie mit einer Fünftklässlerin spricht, und passt Wortwahl und Schwierigkeitsgrad automatisch an.

---

## Schritt 3: Fächer auswählen

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ● ● ● ○ ○ ○ ○                                             │
│                                                             │
│  Welche Fächer soll der KI-Lehrer unterrichten?             │
│                                                             │
│  ☑  Mathematik       ☑  Deutsch                           │
│  ☐  Englisch         ☐  Sachkunde / NWA                   │
│  ☐  Physik           ☐  Chemie                            │
│  ☐  Biologie         ☐  Geschichte                        │
│  ☐  Musik            ☐  Kunst                             │
│                                                             │
│  [ + Eigenes Fach ]                                         │
│                                                             │
│  Tipp: Du kannst Fächer später jederzeit hinzufügen.        │
│                                                             │
│                             [Zurück]  [Weiter]             │
└─────────────────────────────────────────────────────────────┘
```

Mindestens ein Fach muss ausgewählt sein, sonst bleibt „Weiter" inaktiv. „+ Eigenes Fach" öffnet einen kleinen Inline-Dialog mit einem Textfeld — kein neues Fenster.

Für jedes aktivierte Fach legt die App später einen Unterordner und eine leere `heft.md` an. Das passiert gesammelt am Ende des Wizards, nicht hier.

---

## Schritt 4: Anrede

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ● ● ● ● ○ ○ ○                                             │
│                                                             │
│  Wie soll der Lehrer mit Anna sprechen?                     │
│                                                             │
│  ┌──────────────────────┐  ┌──────────────────────┐        │
│  │                      │  │                      │        │
│  │  Du                  │  │  Sie                 │        │
│  │                      │  │                      │        │
│  │  Locker und direkt   │  │  Förmlich und         │       │
│  │  — für Kinder und    │  │  respektvoll          │       │
│  │  Jugendliche         │  │  — für Erwachsene    │        │
│  │                      │  │                      │        │
│  │  [  Auswählen  ]     │  │  [  Auswählen  ]     │        │
│  └──────────────────────┘  └──────────────────────┘        │
│                                                             │
│                             [Zurück]  [Weiter]             │
└─────────────────────────────────────────────────────────────┘
```

Zwei große Karten, keine Radiobuttons. Eine Karte ist nach dem Klick visuell hervorgehoben (Rahmenfarbe, Hintergrund). „Du" ist vorausgewählt, da die Primärzielgruppe Kinder sind.

Die gewählte Anrede steuert nicht nur `du/Sie`, sondern den gesamten Ton: kurze Sätze vs. ausformulierte Erklärungen, „Cool!" vs. „Das ist korrekt.", spielerische Metaphern vs. sachliche Beschreibungen.

---

## Schritt 5: Erklärtiefe

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ● ● ● ● ● ○ ○                                             │
│                                                             │
│  Wie ausführlich soll erklärt werden?                       │
│                                                             │
│  ( ) Kurz und knapp                                         │
│      Direkt zum Punkt, wenig Umschweife.                    │
│      Gut für: schnelle Wiederholung, ältere Schüler.        │
│                                                             │
│  (●) Mittel  ← empfohlen                                    │
│      Erklärungen mit einem Beispiel, Schritt für Schritt.   │
│      Gut für: die meisten Lernenden.                        │
│                                                             │
│  ( ) Ausführlich                                            │
│      Viele Details, mehrere Beispiele, alles erklärt.       │
│      Gut für: Erstlerner, jüngere Kinder, schwierige Fächer.│
│                                                             │
│  Tipp: Du kannst das später in den Einstellungen ändern.    │
│                                                             │
│                             [Zurück]  [Weiter]             │
└─────────────────────────────────────────────────────────────┘
```

Radiobuttons statt Karten, weil es drei Optionen sind und die Beschreibungstexte mehr Raum brauchen. „Mittel" ist vorausgewählt. Der Tipp unten nimmt den Druck: wenn die Wahl falsch ist, kann man sie jederzeit ändern.

---

## Schritt 6: KI-Verbindung

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ● ● ● ● ● ● ○                                             │
│                                                             │
│  Wie soll die KI erreichbar sein?                           │
│                                                             │
│  ┌──────────────────────┐  ┌──────────────────────┐        │
│  │  Über das Internet   │  │  Auf diesem Computer │        │
│  │                      │  │                      │        │
│  │  ✓ Beste Qualität    │  │  ✓ Keine laufenden   │       │
│  │  ✓ Kein leistungs-   │  │    Kosten            │        │
│  │    starker Computer  │  │  ✓ Kein Internet     │        │
│  │    nötig             │  │    nötig             │        │
│  │  ✗ Kleine laufende   │  │  ✗ Braucht einen     │        │
│  │    Kosten            │  │    leistungsstarken  │        │
│  │                      │  │    Computer          │        │
│  │  [ Auswählen ]       │  │  [ Auswählen ]       │        │
│  └──────────────────────┘  └──────────────────────┘        │
│                                                             │
│                             [Zurück]  [Weiter]             │
└─────────────────────────────────────────────────────────────┘
```

Kein Wort „API", kein Wort „Lokal", kein Wort „Cloud". Nur die konkreten Vor- und Nachteile. „Über das Internet" ist links und visuell leicht bevorzugt (Standard-Option) — weil es der robustere Weg für die Mehrheit der Nutzer ist.

„Auf diesem Computer" führt zu einem Folge-Dialog der klar sagt, was nötig ist — ohne den Nutzer in die Irre zu führen.

---

## Schritt 7a: API-Schlüssel (wenn „Über das Internet")

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ● ● ● ● ● ● ●                                             │
│                                                             │
│  Du brauchst einen API-Schlüssel.                           │
│                                                             │
│  Das ist ein spezieller Code der dem KI-Lehrer erlaubt,    │
│  die KI im Internet zu nutzen.                              │
│                                                             │
│  Wo bekommst du ihn?                                        │
│  → console.anthropic.com → Anmelden → API Keys → New Key   │
│                                                             │
│  ┌────────────────────────────────────────────────────┐    │
│  │ sk-ant-...                                         │    │
│  └────────────────────────────────────────────────────┘    │
│                                                             │
│  [ Verbindung testen ]                                      │
│                                                             │
│  ✓ Verbindung erfolgreich!   ← erscheint nach Test         │
│                                                             │
│                             [Zurück]  [Fertig!]            │
└─────────────────────────────────────────────────────────────┘
```

Der Pfad zu console.anthropic.com wird als Klick-Sequenz erklärt, nicht als Link — Links die sich in einem unbekannten Browser öffnen verwirren DAU-Nutzer. Wer den Link klicken will, kann ihn tippen oder googeln.

Das Eingabefeld maskiert die Eingabe nach dem ersten Zeichen (wie ein Passwort). Der Schlüssel ist sensitiv.

**„Verbindung testen"** — Pflicht vor „Fertig!". „Fertig!" ist erst aktiv nach einem erfolgreichen Test. Fehlerfälle:

| Fehler | Angezeigter Text |
|---|---|
| Falscher Key (401) | „Der Schlüssel scheint nicht zu stimmen. Bitte nochmal prüfen." |
| Kein Internet | „Keine Internetverbindung. Bitte Netzwerk prüfen." |
| API nicht erreichbar | „Der Dienst antwortet gerade nicht. Bitte später versuchen." |

Kein HTTP-Statuscode. Keine technischen Details. Nur was der Nutzer tun kann.

---

## Schritt 7b: Lokale Adresse (wenn „Auf diesem Computer")

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ● ● ● ● ● ● ●                                             │
│                                                             │
│  Adresse des KI-Servers auf diesem Computer:                │
│                                                             │
│  ┌────────────────────────────────────────────────────┐    │
│  │ http://localhost:11434                             │    │
│  └────────────────────────────────────────────────────┘    │
│                                                             │
│  Tipp: Die vorausgefüllte Adresse ist meistens richtig,     │
│  wenn Ollama auf diesem Computer läuft.                     │
│                                                             │
│  Welches Modell soll verwendet werden?                      │
│  ┌────────────────────────────────────────────────────┐    │
│  │ qwen3:14b                                          │    │
│  └────────────────────────────────────────────────────┘    │
│                                                             │
│  [ Verbindung testen ]                                      │
│                                                             │
│  ⚠ Kein KI-Server gefunden. Läuft Ollama?                  │
│                                                             │
│                             [Zurück]  [Fertig!]            │
└─────────────────────────────────────────────────────────────┘
```

Die URL ist vorausgefüllt mit dem Ollama-Standard. Der Nutzer muss sie nur ändern, wenn er ein anderes Setup hat. Fehlermeldung im gleichen Klartext-Prinzip wie oben.

---

## Schritt 8: Fertig

Kein Fortschrittspunkt. Kein Eingabefeld.

```
┌─────────────────────────────────────────────────────────────┐
│  KI Lehrer einrichten                              [─][□][×] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│                                                             │
│            Alles bereit!                                    │
│                                                             │
│     Schüler:      Anna  (Klasse 5)                          │
│     Fächer:       Mathematik, Deutsch                       │
│     Lehrer:       Spricht Anna mit „du" an                  │
│     Erklärungen:  Mittel                                    │
│     KI:           Über das Internet                         │
│                                                             │
│                                                             │
│                         [ Jetzt starten! ]                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

Kurze Zusammenfassung was eingerichtet wurde — in menschlicher Sprache, nicht als JSON-Dump. Kein „Zurück"-Button mehr: dieser Schritt ist der Abschluss. Änderungen danach über Einstellungen.

Nach dem Klick auf „Jetzt starten!":

1. App legt `~/KI-Lehrer/` an
2. Schreibt `config.json`
3. Legt `schueler/anna/profil.json` an
4. Legt `schueler/anna/mathematik/heft.md` und `schueler/anna/deutsch/heft.md` an (leer)
5. Führt `git init` im Schüler-Ordner aus
6. Öffnet den Lehrerpult mit Begrüßungsnachricht: „Willkommen, Anna! Dein KI-Lehrer ist bereit."

Alle sechs Schritte in einer oder zwei Sekunden. Fortschritts-Spinner während der Erstellung, damit der Nutzer weiß dass etwas passiert.

---

## Was der Wizard nicht fragt

Bewusste Weglassungen — mit Begründung:

| Weggelassen | Warum |
|---|---|
| Vorkenntnisse pro Fach | Die KI erkennt das Niveau in den ersten Sätzen selbst. Optionales Feld in den Einstellungen für Eltern die es explizit eintragen wollen. |
| Modellauswahl (Cloud) | DAU-Nutzer sollen kein Modell wählen müssen. Standard ist `claude-sonnet-4-6`. Änderbar in Einstellungen. |
| Datenspeicherort | `~/KI-Lehrer/` ist immer der Ort. Kein konfigurierbarer Pfad — das ist eine Fehlerquelle, kein Feature. |
| Benachrichtigungen | Kein Lernplan, kein Erinnerungssystem — die App ist passiv, nicht aufdringlich. |
| Passwort / Kindersicherung | Außerhalb des Scope für V1. |

---

## Wizard erneut aufrufen

Nach dem ersten Abschluss ist der Wizard weg. Änderungen über Einstellungen → Schülerprofil / KI-Verbindung.

Ein „Neues Profil anlegen"-Flow im Schüler-Dropdown (Lehrerpult) führt durch einen verkürzten Wizard (Schritt 1–5, kein API-Key-Schritt — der ist bereits konfiguriert).

---

## Verwandte Seiten

- [ki-lehrer-app](ki-lehrer-app.md) — Übergeordnetes Konzept: DAU-Prinzip, Wireframe, Cloud/Lokal
- [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md) — Was der Wizard am Ende anlegt
- [ki-lehrer-screens](ki-lehrer-screens.md) — Die vier Screens die nach dem Wizard erscheinen

---

[Wiki-Index](../index.md)
