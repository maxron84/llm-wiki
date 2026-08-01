---
date: 2026-08-02
type: konzept
tags: [konzept, automatisierung, qualitaet, kosten, setup]
status: active
---

# Rückkanal Feld → Werkzeug

**Zusammenfassung**: Wer eine Vorlage oder ein Kit in mehrere Projekte ausrollt, braucht einen benannten Weg zurück — sonst bleibt jeder Fund im Feldprojekt liegen und wird vom nächsten Update überschrieben.
**Quellen**: `~/Source/team-kit` (Roadmap-Skizze C, Backlog `BL-2`/`BL-11`/`BL-12`/`BL-13`, Releases 2.3.0–2.4.1, 2026-08-01)
**Zuletzt aktualisiert**: 2026-08-02

---

## Das Problem entsteht erst beim zweiten Projekt

Solange Werkzeug und Anwendung dasselbe Repo sind, gibt es kein Problem: Ein Fund am Werkzeug wird dort behoben, wo er auffällt. Sobald ein [Starterkit](../werkzeuge/team-starter-kit.md) oder eine Vorlage in ein zweites Projekt ausgerollt wird, spaltet sich das auf — und die Funde fallen weiterhin **im Feld** an, weil dort gearbeitet wird.

Ohne benannten Rückweg passiert dreierlei:

1. Der Fund wird im Feldprojekt gefixt und bleibt dort. Jedes weitere Projekt hat ihn weiter.
2. Der Fix wird beim nächsten Update **überschrieben** — vom Werkzeug, das ihn nicht kennt.
3. Der Fund ist ohne die konkrete Feldsituation nicht mehr rekonstruierbar. Er verfällt.

## Der teure Beleg

`BL-11` im Kit: Ein Regex im Fund-Werkzeug erkannte per Pytest-Node-ID referenzierte Dateien nicht (`datei.py::test_x[param]`). Folge — die Fixer-Rolle hielt jeden Fix für themenfremd, der nur diese Datei berührte, und lief in einen endlosen Rollback-Zyklus. Realer Verbrauch, bevor jemand es merkte: **12,00 USD an einem einzigen Befund.**

Fix und Reproducer entstanden im Feldprojekt. Sie **lagen dort zwei Kaskaden lang**, während das Kit weiter ohne sie ausgeliefert wurde.

Und dann kam `BL-12`: Der erste echte Update-Lauf schrieb die Werkzeugdatei mit der älteren Kit-Fassung zurück und nahm den 12-USD-Fix **wortlos** zurück. Der fehlende Rückkanal hat den Fund also nicht nur verzögert — er hat ihn beinahe gelöscht.

## Konvention statt Werkzeug

Naheliegend wäre ein Automatismus: Funde mit Kit-Bezug automatisch ins Werkzeug-Repo synchronisieren. Bewusst **nicht** gebaut. Begründung: Bei einem Menschen und zwei Repos ist jede Automatisierung teurer als das Problem. Neu bewertet wird ab dem dritten Feldprojekt.

Stattdessen drei Textstellen, die zusammen den Kanal bilden:

| Wo | Was dort steht |
|---|---|
| Backlog-Vorlage des Zielprojekts | **Erkennungsmerkmal** — woran man sieht, dass ein Fund am Werkzeug steckt, nicht am Projekt |
| Rollen-Briefing des planenden Akteurs | **Zielort** — wohin der Fund gehört |
| Statuswert im Backlog | **„ans Kit gemeldet"** — ein eigener Status, damit der Fund nicht als „erledigt" verschwindet |

Der dritte Punkt trägt den meisten Wert. Ein lokal gefixter Fund ist im Feldprojekt legitim „erledigt" — genau diese Wahrheit verdeckt, dass er anderswo offen ist. Ein eigener Status hält beide Aussagen gleichzeitig.

## Der geteilte Nummernraum

Das Kit führt seine Backlog-Nummern (`BL-n`) **bewusst gemeinsam** mit dem Ursprungsprojekt und den Feldprojekten. `BL-1` bis `BL-5` bedeuten in beiden Repos dasselbe. Das kostet etwas Ordnung im Nummernraum und erhält dafür die Spur: Ein Fund lässt sich über Repo-Grenzen hinweg lesen, ohne eine Übersetzungstabelle.

## Was das Update können muss

Ein Rückkanal ist nur die halbe Miete — der Weg nach vorn muss den Fix respektieren, der noch nicht zurückgeflossen ist. Aus `BL-12` folgten zwei harte Regeln für den Update-Pfad:

- **Nichts löschen.** Die Annahme „dieser Ordner gehört exklusiv dem Werkzeug" ist falsch, sobald ein Projekt eine Lücke im Werkzeug selbst schließt. Unbekannte Dateien bleiben liegen und werden gemeldet.
- **Abweichungen ausweisen, nicht überfahren.** Jede ersetzte Infrastrukturdatei, die vorher von der Werkzeugfassung abwich, wird mit `git diff`-Hinweis genannt — samt der Aufforderung, den darin steckenden eigenen Fix **erst zurückzuspielen und dann erneut zu updaten**.

Das ist die operative Form derselben Einsicht: Eine Abweichung im Feld ist ein Signal, kein Müll.

## Die dritte Richtung: zurück zum Ahnherrn

Der Kanal war als **Feld → Werkzeug** gedacht und genau so geregelt. Übersehen wurde die dritte Beteiligte: die Quelle, aus der das Werkzeug ursprünglich herausgelöst wurde. Sie ist selbst ein Projekt, läuft weiter — und bekommt die Fixe des Werkzeugs **nicht** zurück.

Beim Kit fiel das erst auf, als die Feld-Fixe eingepflegt wurden: Derselbe 12-USD-Fehler (`BL-11`), der zwei Kaskaden im Feldprojekt lag, lag im Ursprungsprojekt **bis zuletzt**. Drei Fixe fehlten dort, jeder einzeln nachgetragen und im dortigen Backlog vermerkt.

Erschwerend: Der Ahnherr trägt noch das Layout von **vor** dem Werkzeug und nimmt den Update-Pfad deshalb gar nicht an. Eine Migration auf das Kit-Layout wären 531 Pfadverweise in 61 Dateien gewesen — bewusst nicht gemacht, per ausdrücklichem Entscheid des Strippenziehers.

Daraus zwei Regeln:

- **Wer ein Werkzeug aus einem laufenden Projekt herauslöst, erbt drei Kanäle, nicht einen**: Feld → Werkzeug, Werkzeug → Feld (das Update) und Werkzeug → Ursprungsprojekt. Der dritte wird vergessen, weil das Ursprungsprojekt sich nicht wie ein Kunde anfühlt — es *war* ja der Ort, an dem alles entstand.
- **Wenn der automatische Weg für ein Projekt nicht gilt, muss das ein benannter Entscheid sein**, kein stiller Zustand. Sonst gilt es als angeschlossen und wird beim nächsten Fix übersehen. Der Aufwand der Migration gehört dabei beziffert — 531 Verweise in 61 Dateien ist eine Zahl, gegen die man abwägen kann, „wäre viel Arbeit" nicht.

## Übertragbarkeit

Das Muster gilt für jede Ausrollbeziehung, nicht nur für KI-Rollenteams:

- **CLAUDE.md-Vorlagen** in diesem Wiki: Was ein Projekt an einer Vorlage lernt, gehört zurück in die Vorlage — genau der Vorgang, den `wiki/log.md` mit dem Präfix `update` protokolliert.
- **Geteilte Skripte** unter `~/.claude/scripts/`: Ein Fix am Skript, der nur in einem Projektordner liegt, ist verloren.
- **Boilerplate-Repos und Cookiecutter-Templates** allgemein.

Das Erkennungsmerkmal ist immer dasselbe: *Wäre dieser Fund in einem anderen Projekt mit demselben Werkzeug auch aufgetreten?* Wenn ja, steckt er am Werkzeug.

## Verwandte Seiten

- [team-starter-kit](../werkzeuge/team-starter-kit.md) — Das Kit, an dem der Kanal entstand
- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) — Die Vorlage, die das Kit ausrollt
- [gegenprobe-zweite-quelle](gegenprobe-zweite-quelle.md) — Der andere Weg, wie Funde sichtbar werden
- [alarmmuedigkeit](alarmmuedigkeit.md) — Warum die „T.E.A.M."-Kostenzeile aus den Feldprojekten verschwand
- [read-only-guard](read-only-guard.md) — Hat einen Update-Lauf mitten im Betrieb zurückgerollt (`BL-10`)
- [vault-trennung](vault-trennung.md) — Verwandte Frage: was gehört in welches Repo

---

[Wiki-Index](../index.md)
