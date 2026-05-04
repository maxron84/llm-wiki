---
date: 2026-05-03
type: anleitung
tags: [anleitung, setup, vorlage]
status: active
---

# Vorlage einrichten: Von Null zum laufenden Projekt

**Zusammenfassung**: Schritt-für-Schritt-Anleitung — wie du eine CLAUDE.md-Vorlage aus diesem Wiki nimmst, für dein eigenes Projekt anpasst und damit arbeitest. Als Beispiel: ein Pygame-Lernprojekt mit der KI-Lehrer-Vorlage.
**Zuletzt aktualisiert**: 2026-05-03

---

## Was du brauchst

Bevor du anfängst, stelle sicher dass folgendes installiert ist:

- **Claude Code** — [Anleitung: Erste Schritte](erste-schritte.md)
- **Ein Terminal** — Windows: PowerShell oder Windows Terminal / Mac: Terminal / Linux: Terminal
- **Einen Text-Editor** — Notepad (Windows), TextEdit (Mac) oder besser [VS Code](https://code.visualstudio.com/) / [VS Codium](https://vscodium.com/) (kostenlos, alle Systeme, VS Codium ohne Microsoft-Telemetrie)
- **Obsidian** — zum Lesen und Durchsuchen des Wikis. Falls noch nicht installiert: [erste-schritte](erste-schritte.md)

---

## Schritt 1: Ordner für dein Projekt anlegen

Öffne dein Terminal und tippe:

```bash
mkdir pygame-lernen
cd pygame-lernen
```

**Was das macht:**
- `mkdir pygame-lernen` — legt einen neuen Ordner namens `pygame-lernen` an
- `cd pygame-lernen` — wechselt in diesen Ordner

Du kannst den Ordnernamen frei wählen — keine Leerzeichen, nur Buchstaben, Zahlen und Bindestriche.

---

## Schritt 2: Die richtige Vorlage finden

Dieses Wiki hat 9 Vorlagen für verschiedene Einsatzzwecke. Für ein **Lernprojekt** ist die KI-Lehrer-Vorlage richtig:

| Vorlage | Wann? |
|---|---|
| [KI-Lehrer](../vorlagen/claude-md-lehrer.md) | Du willst etwas lernen — KI führt dich mit Lehrplan |
| [Nachhilfe](../vorlagen/claude-md-nachhilfe.md) | Ein Schulfach, konkrete Aufgaben lösen — Kind am Gerät |
| [Laienlehrer](../vorlagen/claude-md-laienlehrer.md) | Elternteil unterrichtet — KI bereitet den Erwachsenen vor |
| [Software](../vorlagen/claude-md-software.md) | Du weißt schon was du baust, KI hilft beim Coden |
| [Begleiter](../vorlagen/claude-md-software-begleiter.md) | Du codest selbst, KI dokumentiert und reviewt |
| [Team-Wiki](../vorlagen/claude-md-team.md) | Gemeinsames Projekt mit 2–8 Personen |

Vollständige Übersicht aller 9 Vorlagen → [erste-schritte](erste-schritte.md)

Für Pygame als Anfänger → **KI-Lehrer**.

Öffne die Vorlage in diesem Wiki: [wiki/vorlagen/claude-md-lehrer.md](../vorlagen/claude-md-lehrer.md)

---

## Schritt 3: Den Vorlageninhalt kopieren

Scrolle auf der Vorlagenseite nach unten bis zum Abschnitt **„Vorlage"**. Dort siehst du einen grau hinterlegten Codeblock — er beginnt mit einer Zeile aus vier Backticks gefolgt von `markdown`:

    ````markdown

und endet mit einer Zeile aus vier Backticks:

    ````

**Diese zwei Rahmenzeilen selbst nicht mitkopieren.** Nur den Inhalt dazwischen — alles von `# CLAUDE.md — KI-Lehrer` bis zur letzten Zeile vor dem schließenden ` ```` `.

Das sind ca. 100–150 Zeilen. Alles davon kommt in deine neue `CLAUDE.md`.

> **Zur Kontrolle:** Deine kopierte CLAUDE.md sollte mit `# CLAUDE.md — KI-Lehrer` beginnen — nicht mit ` ````markdown `.

---

## Schritt 4: CLAUDE.md anlegen

Öffne deinen Text-Editor und erstelle eine neue Datei. Füge den kopierten Inhalt ein.

Speichere die Datei als **`CLAUDE.md`** (genau so, Großbuchstaben) direkt in deinem Projektordner `pygame-lernen/`.

So sieht dein Ordner jetzt aus:

```
pygame-lernen/
  CLAUDE.md    ← diese Datei hast du gerade angelegt
```

> **Tipp für VS Code**: `Datei → Neue Datei`, einfügen, dann `Strg+S` (Windows) oder `Cmd+S` (Mac) und als `CLAUDE.md` speichern.

---

## Schritt 5: Platzhalter ausfüllen

Öffne deine `CLAUDE.md` im Editor. Ganz oben siehst du diesen Block:

```
> **Anleitung**: Kopiere diese Datei als `CLAUDE.md` ...
> ...
> Entferne diesen Block vor der produktiven Nutzung.
```

**Lösche diesen Anleitungsblock komplett.** Er ist nur für dich als Erinnerung gedacht — Claude soll ihn nicht lesen.

Dann suche nach den `{{PLATZHALTERN}}` und ersetze sie. Für ein Pygame-Lernprojekt zum Beispiel:

| Platzhalter | Ersetzen durch |
|---|---|
| `{{z.B. "Mein erstes Jump'n'Run mit Pygame"}}` | `Mein erstes Spiel mit Pygame` |
| `{{Name}}` | deinen Namen |
| `{{Alter}} Jahre` | dein Alter |
| `{{z.B. "Keine" / "Etwas Python" ...}}` | `Python-Grundlagen vorhanden` |
| `{{z.B. "30 Minuten pro Sitzung, 2× pro Woche"}}` | `45 Minuten pro Sitzung` |

Danach sieht der Abschnitt in etwa so aus:

```markdown
## Lernprojekt

**Projekttitel**: Mein erstes Spiel mit Pygame
**Schüler**: Max, 28 Jahre
**Vorkenntnisse**: Python-Grundlagen vorhanden
**Zeitbudget**: 45 Minuten pro Sitzung
**Sprache des Unterrichts**: Deutsch
```

**Alle anderen Abschnitte lässt du so wie sie sind** — die sind bereits fertig für dich.

Speichern nicht vergessen.

---

## Schritt 6: Unterordner anlegen

Die Vorlage erwartet einen `src/`-Ordner für deinen Code. Leg ihn jetzt an:

```bash
mkdir src
mkdir -p wiki/sitzungen
```

Dein Ordner sieht jetzt so aus:

```
pygame-lernen/
  CLAUDE.md
  src/              ← hier schreibst du später deinen Code
  wiki/
    sitzungen/      ← hier landen die Sitzungsnotizen
```

---

## Schritt 7: Claude Code starten

Stelle sicher, dass du noch im Projektordner bist:

```bash
pwd
```

Das sollte etwas wie `/home/max/pygame-lernen` ausgeben. Falls nicht:

```bash
cd /home/max/pygame-lernen
```

Dann Claude Code starten:

```bash
claude
```

Ein Prompt erscheint. Tippe:

```
Wir fangen an.
```

Das ist die magische erste Nachricht. Claude liest jetzt automatisch deine CLAUDE.md und weiß:
- Was du lernen willst
- Wie alt du bist
- Was du schon kannst
- Wie viel Zeit du hast

---

## Was jetzt passiert: Die erste Sitzung

Claude macht jetzt **Phase 1** aus der Vorlage:

1. **Begrüßung** — Claude stellt sich als Lehrbegleiter vor
2. **Aufnahmefragen** — falls noch nicht in CLAUDE.md: Vorkenntnisse, Zeitbudget, Wünsche
3. **Lehrplan** — Claude schreibt `wiki/lehrplan.md` mit Lektionen, Zielen und geschätzter Dauer
4. **Erklärung** — „Heute fangen wir mit X an, am Ende kannst du Y"
5. **Lektion 1 startet direkt**

Du musst dabei nichts organisieren. Claude führt.

---

## Schritt 8: Folgesitzungen

Jedes Mal wenn du wieder anfängst, öffnest du das Terminal, gehst in den Ordner und startest Claude:

```bash
cd /home/max/pygame-lernen
claude
```

Dann sagst du:

```
Weiter.
```

Claude liest `wiki/lehrplan.md` und `wiki/fortschritt.md` und weiß sofort:
- Welche Lektion als nächste kommt
- Wie die letzte Sitzung gelaufen ist
- Was dein Tempo ist

**Du musst nie erklären wo du aufgehört hast** — das Wiki merkt sich alles.

---

## Was in deinem Ordner entsteht

Nach ein paar Sitzungen sieht dein Projekt so aus:

```
pygame-lernen/
  CLAUDE.md                         ← deine Projektregeln (einmalig ausgefüllt)
  src/
    main.py                         ← dein Code
    sprites.py
  wiki/
    lehrplan.md                     ← generierter Lehrplan mit allen Lektionen
    fortschritt.md                  ← aktueller Stand, abgeschlossene Lektionen
    sitzungen/
      2026-05-03.md                 ← Notiz nach erster Sitzung
      2026-05-05.md                 ← Notiz nach zweiter Sitzung
```

Das Wiki wächst mit jeder Sitzung. Wenn du in drei Monaten wiederkommst: alles ist noch da.

---

## Häufige Fehler

**„Claude ignoriert meine CLAUDE.md"**
→ Prüfe: Liegt die Datei wirklich im Projektordner? Heißt sie genau `CLAUDE.md`?
→ Prüfe: Hast du den Anleitungsblock oben in der Datei gelöscht?
→ Prüfe: Hast du Claude aus dem richtigen Ordner gestartet? (`pwd` zeigt wo du bist)

**„Claude schreibt meinen Code, obwohl er das nicht soll"**
→ Das steht in der CLAUDE.md schon als Regel — aber manchmal rutscht es durch.
→ Sag einfach: „Du solltest keinen Projektcode schreiben — nur Konzept-Beispiele."

**„Der Lehrplan ist zu schnell / zu langsam"**
→ Sag Claude direkt: „Das war zu viel auf einmal, mach kleinere Schritte."
→ Oder: „Das war zu einfach, ich will schneller voran."
→ Claude passt `wiki/lehrplan.md` sofort an.

**„Ich habe die CLAUDE.md falsch kopiert (mit den Backtick-Fences)"**
→ Öffne die Datei im Editor. Wenn die erste Zeile ` ````markdown ` lautet — lösche diese Zeile und die letzte ` ```` `-Zeile.

---

## Verwandte Seiten

- [erste-schritte](erste-schritte.md) — Claude Code installieren, falls noch nicht geschehen
- [claude-md-lehrer](../vorlagen/claude-md-lehrer.md) — Die Vorlage, die wir hier benutzt haben
- [claude-md-nachhilfe](../vorlagen/claude-md-nachhilfe.md) — Alternative: reaktiv, aufgabengetrieben
- [claude-md-software](../vorlagen/claude-md-software.md) — Wenn du weißt was du baust

---

[Wiki-Index](../index.md)
