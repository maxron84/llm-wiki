---
date: 2026-08-02
type: konzept
tags: [konzept, metrik, qualitaet, automatisierung]
status: active
---

# Alarmmüdigkeit

**Zusammenfassung**: Eine Kennzahl, die immer null zeigt, und ein Gate, das man regelmäßig umgeht, erziehen beide zum Wegsehen — Signale müssen aussagekräftig bleiben, sonst kosten sie Aufmerksamkeit, ohne sie zu verdienen.
**Quellen**: `~/Source/team-kit` (Releases 2.3.0, 2.4.0 und 2.4.1, Backlog `BL-6`/`BL-9`/`BL-13`/`BL-14`, Roadmap-Skizze D, 2026-08-01)
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

## Nachtrag: Die Prüfung gegen Alarmmüdigkeit erzeugte selbst welche

Einen Tag später lief genau die Prüfung aus Form 2 erstmals gegen ein fremdes, gewachsenes Ledger — und meldete **drei Warnungen, von denen keine echt und keine je auflösbar war** (`BL-13`, `BL-14`, Release 2.4.1). Das Werkzeug, das aus Rücksicht auf Alarmmüdigkeit bewusst kein hartes Gate wurde, war bei jedem einzelnen Aufruf rot.

Lehrreich ist der Fall wegen der Ursache von `BL-14`: Die Regel *„eine Kaskade mit Sweep-Zeile, aber ohne Bau-Zeile ist verdächtig"* stimmt — für **nummerierte** Kaskaden. Für **benannte** Kaskaden (Fixserien nach dem Lauf) stimmt sie nicht, dort hat niemand gebaut, das Fehlen ist korrekt. Die Ausnahme war keine Randerscheinung: Sie kam bei jedem Kontostand-Abruf vor.

**Eine Regel, die im Normalfall gilt und deren Ausnahme dauernd eintritt, ist im Betrieb keine Regel, sondern eine Fehlerquelle.** Sie schlägt so oft grundlos an, dass niemand mehr prüft, ob dieses Mal etwas dran war.

Behoben genau auf Weg 2 der Rangfolge unten — **herabstufen**: Die Warnung bleibt für nummerierte Kaskaden, wird für benannte zum `hinweis` und nennt den Grund im Klartext. Zwei Dinge machen das zu einer Herabstufung und nicht zu einer Vertuschung:

- Der Befund **verschwindet nicht**, er ändert nur seinen Schweregrad und erklärt sich.
- Die **Gegenrichtung ist eigens abgesichert**: Ein Test hält fest, dass bei einer nummerierten Kaskade die fehlende Bau-Zeile weiterhin eine Warnung ist. Der ursprünglich gejagte Fehler `BL-4` wird also nicht mit entschärft.

Der zweite Punkt ist die Trennlinie zum verbotenen vierten Weg. Wer eine Warnung leiser dreht, muss beweisen können, dass sie im echten Fall noch laut wird — sonst hat er die Schwelle verschoben, bis es grün war.

## Die gemeinsame Regel

**Ein Signal muss unterscheiden können.** Es verdient Aufmerksamkeit nur, solange „rot" und „grün" beide vorkommen und beide etwas bedeuten. Sobald ein Signal konstant ist — immer null, immer rot, immer umgangen —, ist es kein Signal mehr, sondern Rauschen mit Anspruch auf Beachtung.

Drei Auswege, in dieser Reihenfolge:

1. **Wegnehmen**, wenn die Kennzahl strukturell nichts mehr misst (`BL-9`)
2. **Herabstufen** von Gate auf Warnung, wenn Fehlalarme legitim sind (Skizze D) — und von Warnung auf erklärten Hinweis, wenn die Ausnahme dauernd eintritt (`BL-14`), aber nur mit einem Test, der die Gegenrichtung festnagelt
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
