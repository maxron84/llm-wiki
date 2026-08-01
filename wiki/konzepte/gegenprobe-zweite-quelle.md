---
date: 2026-08-02
type: konzept
tags: [konzept, metrik, kosten, qualitaet, automatisierung]
status: active
---

# Gegenprobe aus zweiter Quelle

**Zusammenfassung**: Ein Bericht, der seine Kennzahl aus derselben Quelle zieht wie der Fehler, bestätigt ihn, statt ihn zu zeigen — Selbstprüfung trägt nur, wenn mindestens eine Prüfung ihre Zahl woanders herholt.
**Quellen**: `~/Source/team-kit` (Release 2.4.0, Roadmap-Skizze D, Backlog `BL-1`/`BL-4`/`BL-5`/`BL-13`, 2026-08-01)
**Zuletzt aktualisiert**: 2026-08-02

---

## Der Befund

Das [T.E.A.M.-Starterkit](../werkzeuge/team-starter-kit.md) hatte 149 grüne Regressionstests, einen Live-Kontostand und ein committetes Kosten-Ledger — und trotzdem drei Fehler, die alle Werkzeuge unbemerkt passierten:

| Fehler | Wirkung | Wie er gefunden wurde |
|---|---|---|
| `BL-1` | Die Fixphase übersprang still jeden Fund; der Lauf endete grün | Mensch las den Bericht |
| `BL-4` | Ralphs Baukosten landeten in **keiner** Ledger-Zeile (2,1621 von 9,4204 USD) | Mensch hielt den Bericht neben das Ledger |
| `BL-5` | Ein zweiter Kostenabschluss ersetzte den ersten (1,0969 USD verschwanden hinter 2,4114 USD) | ebenso |

Dreimal derselbe Mechanismus: Der Bericht las dieselbe Datenquelle, die der Fehler bereits verfälscht hatte. Eine fehlende Datei galt als „keine Funde". Eine fehlende Ledger-Zeile galt als „nichts angefallen". Der Bericht war korrekt — er berichtete korrekt über eine unvollständige Wahrheit.

## Die Regel

**Mindestens eine Prüfung muss ihre Kennzahl aus einer anderen Quelle ziehen als das Geprüfte.** Alles andere ist Selbstbestätigung mit Testabdeckung.

Im Kit umgesetzt als `kosten.py ledger-pruefen` mit drei Fragen — und nur die dritte erfüllt die Regel:

1. *Trägt jede gelaufene Kaskade eine Zeile je Quelle?* — liest Ledger gegen Ledger. Findet Auslassungen, nicht Verfälschungen.
2. *Liegen unarchivierte Logs herum, obwohl die Kaskade schon gebucht ist?* — liest Dateisystem gegen Ledger, aber nur auf Anwesenheit.
3. **Ergeben die archivierten Rohlogs mehr, als das Ledger ausweist?** — die Rohlogs sind die unabhängige Quelle. Diese Prüfung hätte `BL-4` und `BL-5` beide gefunden; beide sind mit ihren echten Feldzahlen als Regressionstest hinterlegt.

## Warum die dritte Prüfung fast nicht gebaut worden wäre

Die ursprüngliche Skizze wollte je **Kaskade** prüfen. Das war mit der vorhandenen Ablage nicht ehrlich beantwortbar: Log-Dateinamen tragen keine Kaskadennummer, das Archiv ist ein flacher Ordner je Quelle. Zuordnen ließe sich nur über mtime-Fenster — also raten.

**In der Kostenmechanik wird nicht geraten.** Statt die Ablage umzubauen (was eine andere Kostenprüfung gebrochen hätte, `BL-55`), lief der Vergleich je **Quelle**: Archivordner und Ledger-Rolle entsprechen einander eindeutig, es braucht gar keine Zuordnung. Die schwächere, aber ehrliche Prüfung hätte beide Fehler trotzdem gefunden.

Das ist die praktische Hälfte der Regel: Eine unabhängige Quelle, die grob zuordnet, schlägt eine präzise Zuordnung, die geraten ist.

## Übertragbarkeit

Das Muster ist nicht kostenspezifisch. Es passt überall, wo eine Automatisierung ihren eigenen Erfolg meldet:

- **Ein Testlauf, der bei fehlender Testdatei „0 Fehler" meldet**, ist derselbe Fehler — die Gegenprobe ist ein erwarteter Fehlschlag (siehe die Gegenproben-Praxis in [ki-team-forensik](ki-team-forensik.md) und Franks Dreisatz).
- **Ein Deploy-Skript, das den Erfolg aus seinem eigenen Exit-Code liest** statt aus einem Abruf der laufenden Anwendung.
- **Ein Wiki-Lint, das nur Links innerhalb des Wikis prüft** — deshalb prüft [lint-pruefung](lint-pruefung.md) Quellenangaben gegen `raw/` und `clippings/`, also gegen Dateien außerhalb des Wikis.

## Abgrenzung

Die Gegenprobe aus zweiter Quelle ist **nicht** dasselbe wie ein zweiter Test. Zwei Tests derselben Datenquelle sind eine Quelle. Entscheidend ist der Wechsel des Mediums: Rohlog gegen Ledger, laufende Anwendung gegen Deploy-Log, Quelldatei gegen Wiki-Seite.

Sie ist auch keine Ersatzhandlung für [alarmmuedigkeit](alarmmuedigkeit.md): Eine unabhängige Prüfung, die bei jedem Lauf rot ist, wird genauso weggesehen wie eine abhängige.

## Verwandte Seiten

- [alarmmuedigkeit](alarmmuedigkeit.md) — Was mit einer Prüfung passiert, die zu oft anschlägt
- [rueckkanal-feld-kit](rueckkanal-feld-kit.md) — Der zweite Fund-Kanal: Feldprojekt zurück ins Werkzeug
- [kostencounter](kostencounter.md) — Der Kostenstandard, an dem das Muster aufgefallen ist
- [team-starter-kit](../werkzeuge/team-starter-kit.md) — Das Werkzeug, in dem die Prüfung sitzt
- [lint-pruefung](lint-pruefung.md) — Dieselbe Idee im Wiki: gegen `raw/` prüfen, nicht gegen sich selbst
- [finder-fixer-prinzip](finder-fixer-prinzip.md) — Personelle Variante: wer findet, repariert nicht

---

[Wiki-Index](../index.md)
