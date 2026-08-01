---
date: 2026-08-02
type: konzept
tags: [konzept, metrik, qualitaet, automatisierung]
status: active
---

# Alarmmüdigkeit

**Zusammenfassung**: Eine Kennzahl, die immer null zeigt, und ein Gate, das man regelmäßig umgeht, erziehen beide zum Wegsehen — Signale müssen aussagekräftig bleiben, sonst kosten sie Aufmerksamkeit, ohne sie zu verdienen.
**Quellen**: `~/Source/team-kit` (Releases 2.3.0 und 2.4.0, Backlog `BL-6`/`BL-9`, Roadmap-Skizze D, 2026-08-01)
**Zuletzt aktualisiert**: 2026-08-02

---

## Drei Formen desselben Fehlers

Beim Härten des [T.E.A.M.-Starterkits](../werkzeuge/team-starter-kit.md) traten in kurzer Folge drei Varianten auf. Sie sehen unterschiedlich aus und haben dieselbe Wirkung: Der Mensch hört auf hinzuschauen.

### 1. Die Kennzahl, die strukturell immer null ist (`BL-9`)

Der Kontostand zeigte einen Domänenblock mit einer fest verdrahteten Zeile `🔧 T.E.A.M.` — die Kosten der Arbeit **am Team** statt am Produkt. Im Ursprungsprojekt war das sinnvoll, dort wurde die Infrastruktur im Projekt gebaut. Seit es das Kit gibt, ist sie in jedem Feldprojekt strukturell `0.0000`: Am Team wird dort nicht entwickelt, Funde gehen ins Kit zurück (siehe [rueckkanal-feld-kit](rueckkanal-feld-kit.md)).

Eine Zeile, die nie etwas zeigt, wird nicht überlesen — sie bringt einem bei, **den ganzen Block** zu überlesen. Der Fix war Wegnehmen: Default eine Domäne, der Block erscheint nur bei mehreren.

### 2. Das Gate, das man regelmäßig umgehen muss

Die neue Ledger-Prüfung (siehe [gegenprobe-zweite-quelle](gegenprobe-zweite-quelle.md)) hätte als hartes Gate in den Kaskadenabschluss gehängt werden können: keine fehlende Zeile, kein Abschluss. Bewusst verworfen — eine Kaskade mit **legitim** fehlender Zeile könnte dann nicht abschließen, und der Mensch lernt binnen weniger Läufe, das Gate zu übergehen.

Was stattdessen gebaut wurde:

- **Exit `4`** für Warnbefunde, getrennt vom Bedienfehler `1` — maschinell auswertbar, ohne den Ablauf zu blockieren
- **Zwei Schweregrade**: `warnung` (sehr wahrscheinlich verlorenes Geld) und `hinweis` (kann legitim sein)
- **Kein Extra-Aufruf**: Die Prüfung läuft bei jedem Kontostand-Abruf ungefragt mit — sichtbar, aber nicht blockend

Ein Gate, das man regelmäßig umgeht, ist wirkungslos. Eine Warnung, die man sieht, ohne sie wegklicken zu müssen, hält länger.

### 3. Der Testlauf, der erwartet rot ist (`BL-6`)

`pytest team/tests` schlug im Kit-Repo mit 17 von 138 Tests fehl — kein echter Fund, nur die Tests setzten die *installierte* Ablage voraus. Das Ergebnis: Der einzige verfügbare Testlauf war unbrauchbar, also lief er nicht mehr, also war jeder committete Fix bis zur nächsten Feldinstallation ungeprüft. Genau so ging `BL-1` durch drei Releases.

Der Fix machte den erwarteten Fehlschlag **nicht** grün, sondern schuf daneben ein Kommando, das grün sein **muss**: `kit-test.sh` installiert in ein Wegwerf-Repo und fährt die Tests dort. Die 17 Fehlschläge bleiben bestehen und sind ausdrücklich dokumentiert als erwartet — sie sind nur nicht mehr das Gate.

## Die gemeinsame Regel

**Ein Signal muss unterscheiden können.** Es verdient Aufmerksamkeit nur, solange „rot" und „grün" beide vorkommen und beide etwas bedeuten. Sobald ein Signal konstant ist — immer null, immer rot, immer umgangen —, ist es kein Signal mehr, sondern Rauschen mit Anspruch auf Beachtung.

Drei Auswege, in dieser Reihenfolge:

1. **Wegnehmen**, wenn die Kennzahl strukturell nichts mehr misst (`BL-9`)
2. **Herabstufen** von Gate auf Warnung, wenn Fehlalarme legitim sind (Skizze D)
3. **Daneben stellen**, wenn der bestehende Lauf aus gutem Grund rot bleibt (`BL-6`)

Nicht dazu gehört: die Schwelle so lange verschieben, bis es grün wird. Das nimmt dem Signal die Aussage, ohne den Lärm zu beseitigen.

## Bezug zu den anderen Mustern

Der [Read-Only-Guard](read-only-guard.md) ist das Gegenbeispiel, das die Regel bestätigt: Er blockt hart und wird trotzdem nicht umgangen — weil er in der Praxis fast nie anschlägt, und wenn doch, dann zu Recht (siehe den `BL-10`-Vorfall dort). Ein hartes Gate ist erlaubt, solange der Fehlalarm die Ausnahme bleibt.

## Verwandte Seiten

- [gegenprobe-zweite-quelle](gegenprobe-zweite-quelle.md) — Prüfungen, die tatsächlich etwas finden können
- [read-only-guard](read-only-guard.md) — Ein hartes Gate, das seinen Platz verdient
- [kostencounter](kostencounter.md) — Wo die Null-Kennzahl saß
- [team-starter-kit](../werkzeuge/team-starter-kit.md) — Das Werkzeug, an dem alle drei Formen auftraten
- [claude-md-design](claude-md-design.md) — Verwandter Gedanke für Instruktionen: was immer gilt, wird nicht gelesen

---

[Wiki-Index](../index.md)
