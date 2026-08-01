---
date: 2026-08-01
type: werkzeug
tags: [werkzeug, automatisierung, softwareprojekt, setup, kosten]
status: active
---

# T.E.A.M.-Starterkit

**Zusammenfassung**: Ein installierbares Bündel, das die [T.E.A.M.-Vorlage](../vorlagen/claude-md-ki-team.md) mit einem Konsolenbefehl als lauffähiges KI-Rollenteam in ein neues Software-Projekt bringt — 55 Dateien, sieben Fragen, Selbsttest. Sprach- und stackagnostisch.
**Quellen**: Repo `~/Source/team-kit` (eigenes Git-Repo, Version 2.4.1); Code übernommen aus dem Feldprojekt `website-maxron-de` (22 Kaskaden, 2026-07-10 bis 2026-08-01); Erntelauf aus dem ersten Feldprojekt `team-kit_project_platformer` (Kaskaden 1–2, 2026-08-01).
**Zuletzt aktualisiert**: 2026-08-02

---

## Aufruf

```bash
cd ~/Source/team-kit
bash install.sh ~/Source/mein-neues-projekt
```

Kurzform von überall: `bash ~/.claude/scripts/team-init.sh <zielpfad>` — ein dünner Launcher auf denselben Installer.

Danach im Zielordner eine Claude-Sitzung mit Opus starten und sagen: *„Du bist unser Architekt, lies `team/prompts/rolle-architekt.md`."*

## Warum es das gibt

Die [T.E.A.M.-Vorlage](../vorlagen/claude-md-ki-team.md) beschreibt das Rollenteam vollständig — aber sie liefert **bewusst keine Skripte**, sondern eine Bau-Anleitung ([team-skripte-generieren](../anleitungen/team-skripte-generieren.md)). Das ist für fremde Umgebungen richtig: unbekannte CLI, fremde Repo-Konventionen.

Auf **derselben Maschine mit derselben CLI** ist es aber die schlechtere Wahl. 3.200 Zeilen über 22 Kaskaden gehärteter Bash und Python lassen sich nicht verlustfrei aus einer Beschreibung rekonstruieren; jeder dabei übersehene Detailfix kostet real Geld (`BL-55` war eine 77-%-Fehlmessung, die Stagnations-Lücke ~13,8 USD Leerlauf). Das Kit übernimmt den Code deshalb **wörtlich** und parametrisiert nur, was projektspezifisch ist.

## Was übernommen und was angepasst wurde

Bei der Feldinspektion zeigte sich, dass die harten Projektbezüge stark konzentriert sind:

| Baustein | Zeilen | Behandlung |
|---|---|---|
| `team/lib.sh` | 821 | **wörtlich** (bis auf Config-Sourcing + zwei Helfer) |
| `kosten.py` | 952 | **wörtlich** |
| `beutebuch.py` | 275 | **wörtlich** |
| `vollautomatik.sh`, `halbautomatik.sh`, `team-status.sh` | 643 | **wörtlich** |
| `ralph.sh`, `frank.sh`, `axel.sh`, `redteam.sh` | 504 | 32 Stellen parametrisiert |
| Rollen-Briefings | 6 Dateien | vollständig templatisiert (v2.0.0) |

Alle Projektwerte stehen in einer einzigen Datei `team.config.sh` im Zielprojekt. Sie wird von `team/lib.sh` gesourct, also von jeder Rolle — eine Änderung dort wirkt sofort überall, ohne Neuinstallation.

## Sprach- und stackagnostisch

Version 1.0.0 setzte an mehreren Stellen still den Stack des Ursprungsprojekts voraus. Version 2.0.0 trennt sauber:

```
projekt/
├── vollautomatik.sh …   Entrypoints sichtbar oben — man tippt sie direkt
├── team.config.sh       die eine Konfigdatei
├── team/                lib · redteam · tools/ · prompts/ · tests/
├── CLAUDE.md CHANGELOG.md plans/
└── <projektcode>/       unberührt
```

**Das Kit fasst die Ordner des Projekts nicht an.** `tests/` und `scripts/` bleiben dem Projekt und seinem eigenen Testrunner; die Team-Regressionstests laufen getrennt über `./team-test.sh`. Die `.gitignore`-Ergänzungen sind auf `team/**` eingegrenzt.

`python3` bleibt nötig, weil die Team-Werkzeuge (`kosten.py`, `beutebuch.py`, zusammen 1.227 Zeilen) Python sind. Sie in Shell neu zu schreiben würde genau die Härtung wegwerfen, für die es das Kit gibt. Es ist damit eine Abhängigkeit der **Team-Infrastruktur** — auf einer Ebene mit `git`, `flock` und der Agenten-CLI —, nicht des Projekts.

**Verifiziert in drei fremden Stacks** (Go, Rust, PHP): keine stack-fremden Dateien in den Projektordnern, Briefings mit den richtigen Pfaden, eigene Domänen akzeptiert, 127 Regressionstests grün.

## Die sieben Fragen

| Frage | Default | Bedeutung |
|---|---|---|
| Projektname | Ordnername | erscheint in Berichten und Ledger |
| Produktivcode-Ordner | `src/` | tabu für Harry, Marv, Axel |
| Test-Ordner | `tests/` | wo Reproducer hindürfen — bleibt dem Projekt-Testrunner |
| Plan-Ordner | `plans/` | Kaskaden, Beutebuch, Akten, Roadmap |
| **Smoke-Test-Befehl** | *(leer)* | der eine Befehl, der prüft, ob das Projekt heil ist |
| Domänen | `produkt team` | Kostentrennung Produktarbeit ↔ Team-Infrastruktur |
| Architekt committet selbst? | `n` | sonst liefert er die Befehle zum Kopieren |

Der Smoke-Test ist der kritische Wert: Ralph schließt keine Stufe ohne ihn ab, Frank verifiziert keinen Fix. Bleibt er leer, melden die Rollen das in **jedem** Prompt als offenen Punkt — das Team läuft, aber sichtbar ohne Sicherheitsnetz. Siehe [kontextrahmen-5w1h](../konzepte/kontextrahmen-5w1h.md) für den verwandten Gedanken, Rahmenbedingungen explizit zu machen statt sie zu raten.

Die übrigen Platzhalter bekommen die im Feld erprobten Defaults (Budget 5/10 USD, Modelle Sonnet/Opus, Abo-first).

## Vier Defekte, die erst außerhalb des Ursprungsprojekts sichtbar wurden

Die Selbsttests in Wegwerf-Repos förderten vier Fehler zutage, die im Feldprojekt **nie** auftreten konnten — zwei beim Erstlauf, zwei beim Wechsel des Stacks:

`ralph.sh` und `team_plan_datei()` lasen die Zeiger-Datei `.ralph-plan` mit `head`. Fehlt die Datei, liefert `head` einen Rückgabewert ≠ 0 — und unter `set -e -o pipefail` riss das den Loop weg, **bevor** die erklärende Fehlermeldung erreicht wurde. Der Anwender sah einen blanken Exit 1 ohne jeden Hinweis.

Im Feldprojekt existiert `.ralph-plan` seit Kaskade 1, deshalb ist das dort nie aufgefallen. In einem frischen Projekt ist die fehlende Datei der **Normalfall beim allerersten Start**.

**Die Rollen-Briefings waren nicht parametrisiert.** In Version 1.0.0 wurden sie wörtlich übernommen — und nannten damit `site/**` als Guard-Grenze und `python3 scripts/smoke_test.py` als Smoke-Test. In einem fremden Projekt bekamen Harry, Marv und Axel so die **falsche Grenze** genannt und Ralph einen Befehl, den es nicht gibt. Briefings sind Prompts; sie werden jetzt wie alles andere beim Installieren gefüllt.

**`kosten.py` erzwang die Domänen `website` und `team`.** Ein Projekt mit anderer Namensgebung konnte seine Kosten nicht sinnvoll trennen. Jetzt projektdefiniert über `TEAM_DOMAENEN`. Der Lesepfad validiert bewusst **nicht** mehr, damit historische Ledger-Zeilen mit heute unbekannten Domänen filterbar bleiben.

Alle vier gehören zur selben Fehlerklasse, die schon die [Feldinspektion](../quellen/claude-md-ki-team.md) zutage gefördert hat: **Was zur Selbstverständlichkeit geworden ist, meldet niemand zurück** — und es fällt erst auf, wenn man den Kontext wechselt.

## Was verifiziert ist — und was nicht

**Verifiziert** (Wegwerf-Repo, 2026-08-01):

- Installation: 55 Dateien, Platzhalter korrekt gefüllt, abgeleitete Pfade und Whitelists stimmen
- **Stack-Neutralität in Go-, Rust- und PHP-Strukturen**: keine Python-Dateien in fremden Projektordnern, kein `scripts/` oder `prompts/` im Wurzelverzeichnis angelegt, eigene Dateien unangetastet
- Shell-Syntax aller Skripte, Python-Kompilierung beider Werkzeuge
- **127 Regressionstests grün** (25 Testdateien der Team-Infrastruktur)
- Read-Only-Guard: chirurgischer Rollback des Verletzers, erlaubte Datei blieb erhalten, Produktivcode unangetastet
- Verhalten aller Rollen ohne Arbeitsvorrat (Exit 3 = nichts zu tun)
- **Idempotenz**: zweiter Lauf schreibt 0 Dateien, eigene Änderungen bleiben, `.gitignore` wird nicht doppelt ergänzt

**Scharf gelaufen am 2026-08-01** — erstmals mit echten CLI-Aufrufen statt Fixtures, in einem Wegwerf-Projekt:

| Rolle | Ergebnis | Kosten |
|---|---|---|
| **Ralph** | Auth aufgelöst (abo) → realer Aufruf → Code gebaut → Smoke-Test grün → genau ein `feat(stufe1)`-Commit → Promise erkannt → State auf 2 → `RALPH_CAP` respektiert → Exit 0 | 0,2728 USD |
| **Harry** | realer Sweep über die Historie, Exit 0, State auf HEAD, **Produktivcode nachweislich unangetastet** | 0,4751 USD |

**Der Read-Only-Guard ist damit unter echten Bedingungen belegt**: Das Log enthält zwei `permission_denials` — die `--allowedTools`-Allowlist hat zwei Bash-Aufrufe von Harry real verweigert, ohne dass der Aufruf als Fehler zählte. Die Kostenerfassung wies 0,7479 USD korrekt als **Abo-Gegenwert** aus, getrennt von real abgerechneten API-Kosten.

Damit ist die Kette Konfiguration → Briefing → `team_claude` → Auth → Promise-Auswertung → Budget-Check → State-Fortschritt → Guard → Kostenlog **durchgängig verifiziert**.

**Stand nach dem ersten Feldeinsatz** (`team-kit_project_platformer`, Kaskaden 1–2): **Frank ist scharf gelaufen** und hat drei echte Funde gefixt — allerdings außerhalb der `vollautomatik.sh`-Fixphase. Für **Axel** gilt die Aussage weiter: nie an einem echten Fund gelaufen. Eine komplette Kaskade über alle vier Phasen in einem Durchlauf hat es damit noch nicht gegeben (`BL-7`).

## Der erste Feldeinsatz — sechs Fehler in einem Erntelauf

Am 2026-08-01 lief das Kit erstmals in einem fremden Projekt über eine volle Kaskade. Das förderte in wenigen Stunden mehr zutage als alle Wegwerf-Repo-Tests zusammen: drei Fehler kamen aus dem Feld zurück, drei fielen beim Aufräumen auf, zwei weitere verursachte die Behebung selbst.

**Die Fixphase war in jeder Installation seit 2.0.0 tot (`BL-1`).** Das Fund-Werkzeug löste die Projektwurzel eine Ebene zu hoch auf und suchte eine Datei, die es nie gibt. Eine fehlende Datei galt als „keine Funde" statt als Fehler — also übersprang die Fixer-Rolle jeden übergebenen Fund, und der Lauf endete **grün**. Drei Releases lang unbemerkt.

**Ralphs Baukosten landeten in keiner Ledger-Zeile (`BL-4`).** Der Kostenabschluss ledgerte per Definition nur die Logs der Sweep- und Fix-Rollen; für die Bau-Logs existierte ein Helfer, den im gesamten Kit **kein Skript aufrief**. Weil der Ordner `.gitignore`t ist, verlor ein frischer Clone die gesamte Bau-Kostenhistorie — genau das, wogegen das Ledger gebaut wurde. Im Feld: 2,1621 von 9,4204 USD.

**Ein zweiter Kostenabschluss löschte den ersten (`BL-5`).** Gezählt werden nur die noch nicht archivierten Logs, und jeder Abschluss archiviert, was er zählte — aufeinanderfolgende Aufrufe sehen also **disjunkte** Mengen. Der kleinere Nachlaufwert **ersetzte** trotzdem den größeren; im Feld verschwanden 1,0969 USD hinter 2,4114 USD. Für disjunkte Mengen ist Addieren richtig, aber automatisch addiert wird trotzdem nicht: Ohne Archivierung zählen zwei Aufrufe dieselben Logs, dann wäre Addieren eine Doppelbuchung. Die Unterscheidung gehört dem Menschen, nicht einer Heuristik — deshalb bricht der zweite Aufruf ab und nennt Alt-, Neu- und Summenwert, mit `--addieren` und `--ersetzen` als ausdrücklichen Wegen.

Alle drei fielen **nicht durch ein Werkzeug** auf, sondern weil ein Mensch den gedruckten Bericht neben das Ledger hielt — siehe [gegenprobe-zweite-quelle](../konzepte/gegenprobe-zweite-quelle.md). Der Fehler war dabei jedes Mal zur Hälfte ein Dokumentationsfehler: Die Closeout-Pflicht nannte Ralph in keinem der drei Dokumente.

## Das Kit prüft sich selbst: `kit-test.sh`

Das Kit hatte keinen eigenen Prüfbefehl (`BL-6`). `pytest team/tests` schlug im Kit-Repo mit 17 von 138 Tests fehl — alle aus einer Ursache: Die Tests setzen die **installierte** Ablage voraus, im Kit liegen die Entrypoints unter `entry/` und `bootstrap/`. Kein echter Regressionsfund, aber der einzige verfügbare Testlauf war damit unbrauchbar, und jeder committete Fix blieb bis zur nächsten Feldinstallation ungeprüft. **Genau so ging `BL-1` durch drei Releases.**

`kit-test.sh` löst das nicht, indem es die 17 Fehlschläge grün macht, sondern indem es daneben einen Lauf stellt, der grün sein **muss**:

1. installiert in ein `mktemp`-Wegwerf-Repo
2. sucht ungefüllte Platzhalter
3. committet, wie `TEAM.md` es vorschreibt
4. fährt dort den vollständigen Testlauf
5. prüft seit 2.3.0 zusätzlich den Update-Pfad

Gegenprobe gefahren: Ein erzwungener Fehlschlag reicht Exit 5 durch. Die 17 Fehlschläge im Kit-Repo bleiben bestehen und sind ausdrücklich **erwartet** — sie sind nur nicht mehr das Gate. Siehe [alarmmuedigkeit](../konzepte/alarmmuedigkeit.md).

**182 Testfälle in 32 Dateien** (Stand 2.4.1, von 127 bei 2.2.0).

## `install.sh --update` — der fehlende Weg nach vorn

Bis 2.3.0 gab es **keinen sicheren Weg**, ein bestehendes Projekt auf eine neue Kit-Version zu heben (`BL-8`). Ohne Flag übersprang der Installer jede vorhandene Datei, änderte also nichts; mit `--force` überschrieb er auch die **Projektdaten**. Empirisch nachgestellt: Ledger geleert, Bauzeiger von 5 auf 1 zurück, Beutebuch-Fund weg, Smoke-Test-Befehl aus der Konfiguration verschwunden. Die einzige dokumentierte Update-Option war damit datenvernichtend — und das Feldprojekt hätte die `BL-4`/`BL-5`-Fixes gar nicht bekommen können, ohne seine Geschichte zu verlieren.

`--update` fasst nur Infrastruktur an, liest die Projektwerte aus der **installierten** Konfiguration (sonst stünde die falsche Guard-Grenze in den Briefings) und rettet den Commit-Entscheid aus dem alten Briefing.

Der erste echte Einsatz deckte prompt zwei weitere Löcher auf, beide im Update selbst:

**Das Update lief in einen aktiven Lauf hinein (`BL-10`).** Es legte uncommittete Dateien in `team/` ab, während die Vollautomatik lief. Der unmittelbar folgende Axel-Lauf — read-only, Whitelist nur `plans/` — wertete sie als **Guard-Verletzung**, rollte sie chirurgisch zurück und buchte seine Runde als Fehlschlag, obwohl er seine Ermittlungsakte geliefert hatte. Dritte Stagnation in Folge, Lauf gestoppt, Update spurlos weg. **Der [Guard](../konzepte/read-only-guard.md) hat exakt richtig gehandelt**, die Projektdaten blieben unversehrt; gefehlt hat die Sperre im Installer. `--update` prüft jetzt per `flock -n`, ob die Loop-Sperre **gehalten** wird (nicht bloß, ob die Datei existiert), bricht mit Exit 2 ab und macht das anschließende Committen zur Pflicht.

**Das Update löschte projekteigene Tests und nahm lokale Fixes still zurück (`BL-12`).** Ein pauschales `rm team/tests/test_*.py` sollte umbenannte Kit-Tests entfernen — im Feld löschte es einen vom Projekt geschriebenen Infrastruktur-Test, und im selben Lauf wurde ein Werkzeug mit der älteren Kit-Fassung überschrieben, samt einem lokalen Fix, der real **12,00 USD** gekostet hatte. Die Annahme „`team/tests/` gehört exklusiv dem Kit" ist falsch, sobald ein Projekt eine Lücke im Team selbst schließt. Seit 2.3.2 löscht das Update **nichts** mehr: Unbekannte Tests bleiben liegen und werden gemeldet, abweichende Infrastrukturdateien mit `git diff`-Hinweis ausgewiesen. Mehr dazu in [rueckkanal-feld-kit](../konzepte/rueckkanal-feld-kit.md).

## Der Abschluss wird geprüft, nicht geglaubt (2.4.0–2.4.1)

`./team-status.sh --ledger-pruefen` beantwortet nach dem Kostenabschluss drei Fragen: Fehlt einer gelaufenen Kaskade eine Zeile je Quelle? Liegen unarchivierte Logs herum, obwohl sie schon gebucht ist? Und **ergeben die archivierten Rohlogs mehr, als das Ledger ausweist?**

Nur die dritte zieht ihre Kennzahl aus einer anderen Quelle als das Geprüfte — und genau das fehlte bei allen drei bisherigen Kit-Fehlern. `BL-4` und `BL-5` sind mit ihren echten Feldzahlen als Regressionstest hinterlegt und schlagen beide an.

Bewusst **kein hartes Gate** im Closeout: Eine Kaskade mit legitim fehlender Zeile könnte sonst nicht abschließen, und ein Gate, das man regelmäßig umgeht, ist wirkungslos. Stattdessen Exit `4` für Warnbefunde (`1` bleibt dem Bedienfehler), zwei Schweregrade, und die Prüfung läuft bei jedem Kontostand-Abruf ungefragt mit.

**Eine Domäne ist jetzt der Normalfall (`BL-9`).** Der Kontostand zeigte eine fest verdrahtete „T.E.A.M."-Domänenzeile — in einem Feldprojekt strukturell `0.0000`, weil dort nicht am Team entwickelt wird. Der Domänenblock erscheint nur noch bei mehreren konfigurierten Domänen und listet dann jede.

### Der erste Lauf gegen ein fremdes Ledger (2.4.1)

Die Prüfung ging mit 176 grünen Tests raus — alle gegen Daten desselben Projekts geschrieben, das sie prüfen sollte. Beim Einpflegen der Kit-Fixe ins Ursprungsprojekt traf sie erstmals ein gewachsenes Ledger: **67 Zeilen aus 22 Kaskaden, drei Warnungen, keine davon echt und keine je auflösbar.**

**Eine Rohquelle kann mehrere Ledger-Rollen speisen (`BL-13`).** Die Prüfung bildete jeden Log-Ordner auf genau eine Rolle ab. Tatsächlich schreiben `redteam.sh`, `frank.sh`, `axel.sh` und `vollautomatik.sh` alle nach `.team-logs`, während das Ursprungsprojekt Franks Out-of-Loop-Arbeit als eigene Ledger-Zeile bucht — wofür `akteur-abschluss --rolle <X>` ausdrücklich existiert. Dessen 17,00 USD meldete die Prüfung als „archiviert, aber nie gebucht", und nachbuchen kann man nichts, was bereits gebucht ist. Der Fix verlängert die Zuordnungsliste nicht, sondern **leitet die Rollenmenge aus dem Ledger ab** — eine gepflegte Liste wäre beim nächsten projekteigenen Rollennamen wieder falsch. Die Architekten-Zeile bleibt ausdrücklich draußen: Sie ist eine gemessene Schätzung ohne Rohlog und hätte mit ihren 275 USD im Ursprungsprojekt jede echte Untergebuchung maskiert (eigener Schutzwächter-Test).

**Benannte Kaskaden sind Out-of-Loop-Buchungen (`BL-14`).** Die Regel „`roles`-Zeile ohne `ralph`-Zeile ⇒ Warnung" gilt nur für **nummerierte** Kaskaden. Benannte (`post-20`, `roles-post-k13`) sind Fixserien nach dem Lauf, in denen Ralph gar nicht gebaut hat — dort ist das Fehlen korrekt, die Warnung dauerhaft unauflösbar, und sie erschien bei **jedem** `--budget`. Jetzt Warnung nur bei nummerierter Kaskade, sonst ein Hinweis, der den Grund nennt. Die Gegenrichtung ist eigens getestet: Bei einer nummerierten Kaskade bleibt es eine Warnung, `BL-4` wird nicht mit entschärft.

Dazu eine kleine, aber tragende Änderung: Der Befund **nennt die Rollen, die er zusammengezählt hat**. `BL-1`, `BL-4` und `BL-5` fand kein Werkzeug, sondern ein nachrechnender Mensch — ein Befund, der nur ein Delta zeigt, macht dieses Nachrechnen unmöglich. Mehr dazu in [gegenprobe-zweite-quelle](../konzepte/gegenprobe-zweite-quelle.md) und [alarmmuedigkeit](../konzepte/alarmmuedigkeit.md).

**Nebenbefund**: Das Ursprungsprojekt trägt noch das Layout von vor dem Kit und nimmt `install.sh --update` deshalb nicht an. Drei Kit-Fixe fehlten dort und wurden einzeln nachgetragen; eine Migration auf das Kit-Layout wären 531 Pfadverweise in 61 Dateien gewesen und wurde bewusst nicht gemacht. Siehe [rueckkanal-feld-kit](../konzepte/rueckkanal-feld-kit.md).

## Erstlauf-Regeln stehen in den Artefakten, nicht im Gespräch

Beim Abnahmegespräch fiel auf, dass die Empfehlungen für den ersten Lauf nirgends festgehalten waren — ein kalt startender Architekt in einem frischen Projekt hätte sie nicht gekannt. Seit Version 2.1.0 stehen sie dort, wo sie wirken:

- **Architekten-Briefing**, Abschnitt „Die erste Kaskade eines Projekts": Smoke-Test hat Vorrang vor jedem Feature, drei bis fünf Stufen, `BUDGET_EMPFEHLUNG_USD` konservativ aber nicht knauserig, nach dem Lauf den Bauweg ehrlich bewerten.
- **Bootstrap-Roadmap** bringt „Skizze 1: Verifikationsfähigkeit herstellen" mit und zeigt über den gefüllten Platzhalter selbst an, ob sie noch gebraucht wird.
- **Installer-Abschlussmeldung** nennt `TEAM_BUDGET_USD=15` für den Erstlauf.

Das ist dieselbe Lehre wie Planungsregel 5 der Vorlage: Was nicht im Git steht, existiert für die nächste Instanz nicht — ob es nun ein Terminal-Abschlussbericht ist oder eine mündliche Empfehlung.

## Bedienbarkeit: `TEAM.md`

Bis Version 2.1.0 fehlte der **menschliche** Einstiegspunkt. Die kritischste Warnung des Kits — *vor dem ersten Guard-Lauf committen* — stand nur in der Terminal-Ausgabe des Installers, und die scrollt weg. Genau der Fehler, den Planungsregel 5 der Vorlage für den Abschlussbericht behebt: flüchtige Ausgabe statt committetes Dokument.

Seit 2.2.0 legt der Installer eine `TEAM.md` im Projekt an, gefüllt und im Git:

- **Guard-Warnung ganz oben**, mit Begründung
- Rollenübersicht und das Finder-≠-Fixer-Prinzip
- Der Kaskaden-Ablauf in vier Schritten, vom Architekten-Trigger bis zum Closeout
- Befehlstabelle und **Exit-Code-Tabelle** — `42` ist eine Pause, kein Absturz; das ist die häufigste Verwechslung
- Ablageübersicht (`team.config.sh` als einziger Ort zum Ändern)
- Eine Fehlersuch-Tabelle für die typischen Startprobleme

Fünf Regressionstests sichern das ab: Existenz, Guard-Warnung im Kopfbereich, erklärte Exit-Codes, Closeout als Pflicht, keine offenen Platzhalter.

## Reihenfolge nach der Installation

1. **`TEAM.md` lesen** — sie erklärt alles Weitere. Dann `team.config.sh` und `CLAUDE.md` prüfen, TODO-Stellen füllen
2. **Committen** — vor dem ersten Guard-Lauf. Der Guard betrachtet uncommittete Dateien außerhalb der Whitelist als Verletzung; im Ursprungsprojekt löschte er einmal die gesamte frisch gebaute Infrastruktur (siehe [read-only-guard](../konzepte/read-only-guard.md))
3. `./team-test.sh` — prüft die Infrastruktur, nicht das Projekt
4. Erste Kaskade planen: Claude-Sitzung mit Opus, *„Du bist unser Architekt, lies `team/prompts/rolle-architekt.md`."* Er härtet eine Skizze aus, setzt `RALPH_CAP` und `BUDGET_EMPFEHLUNG_USD` und gibt die Scharfschalt-Sequenz aus. **Die Erstlauf-Regeln stehen in seinem Briefing** — Smoke-Test vor jedem Feature, drei bis fünf Stufen, Budget konservativ aber nicht knauserig
5. Scharfschalten: `echo plans/ralph-kaskade-1-….md > .ralph-plan`
6. `./vollautomatik.sh`
7. **Nach dem Lauf**: `./team-status.sh --rollen-abschluss <N> <domaene>` und `--architekt-abschluss <USD> <domaene>`. Der eine Aufruf schreibt seit 2.3.0 **zwei** Ledger-Zeilen (`ralph` für den Bau, `roles` für Sweep/Fix). Der Architekt läuft interaktiv außerhalb der Kostenlogs — ohne diesen Schritt bleibt seine Sitzung unerfasst (im Feld ~16 USD)
8. **Abschluss prüfen**: `./team-status.sh --ledger-pruefen`. Ein stehender Warnbefund gehört samt Begründung ins Abschluss-Doc
9. **Auf eine neue Kit-Version heben**: `bash ~/Source/team-kit/install.sh --update <zielpfad>` — **nie** `--force`, der überschreibt Projektdaten. Vorher committen, den Lauf beenden, und einen eigenen Fix in einer Infrastrukturdatei erst ins Kit zurückspielen

## Was das Kit gekostet hat

Der Bau lief in einer einzigen interaktiven Opus-5-Sitzung, also ohne
Konsolenwert. Er wurde nachträglich aus dem Sitzungstranskript **gemessen**
(Methode: [sitzungskosten-aus-transkript](../anleitungen/sitzungskosten-aus-transkript.md)):

| Phase | USD | Anmerkung |
|---|---:|---|
| Wiki-Pflege davor | 29,57 | Audit, Log-Hygiene, `wiki_lint.py` auf 8 Prüfungen, Vorlage geteilt |
| Feldinspektion `website-maxron-de` | 12,36 | read-only, lieferte die Lückenliste |
| Kit-Bau 1.0.0 → 2.2.0 | 119,09 | Kit-Repo mit 59 Dateien, davon 55 im Zielprojekt |
| Verifikationsläufe Ralph + Harry | 0,75 | **echte** Konsolenwerte (headless, Sonnet 5) |
| **Summe** | **161,77** | Abo-Gegenwert, real nicht abgerechnet |

Zum Vergleich: der Bau der Infrastruktur im Ursprungsprojekt kostete über 22
Kaskaden ein Vielfaches davon. Das Kit macht dieses Ergebnis für 0 USD je
weiterem Projekt wiederverwendbar — genau das ist sein Zweck.

Die Kosten sind in der Domäne `team` des Feld-Ledgers verbucht
(`website-maxron-de/.budget-ledger`, Kaskade `post-22`) und laufen von dort über
`update_projekt_kosten.py` auf die T.E.A.M.-Projektkarte der Website. Der teuerste
Einzelposten der Sitzung war nicht das Schreiben, sondern das wiederholte
Vorlegen des Kontexts — 75 % der Kosten waren Cache-Reads.

> **Offen (`BL-56` im Feldprojekt)**: Die Verbuchung deckt eine Schwäche der
> abgeleiteten Kennzahl auf. Der dort ausgewiesene „Wert-Hebel" teilt den
> Engineering-Gegenwert durch die Compute-Kosten — der Gegenwert zählt aber
> Commit-Tage **des Feld-Repos**, während die Kosten inzwischen repo-übergreifend
> erfasst werden. Der Kit-Bau erzeugte dort keine Commits, also wuchs nur der
> Nenner. Die Kostenzahlen sind belastbar, die Kennzahl wird vor Ort überarbeitet.

## Verwandte Seiten

- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) — Die Vorlage, die das Kit installiert
- [rueckkanal-feld-kit](../konzepte/rueckkanal-feld-kit.md) — Wie Funde aus Feldprojekten zurück ins Kit kommen
- [gegenprobe-zweite-quelle](../konzepte/gegenprobe-zweite-quelle.md) — Warum drei Kit-Fehler an allen Werkzeugen vorbeikamen
- [alarmmuedigkeit](../konzepte/alarmmuedigkeit.md) — Warum die Ledger-Prüfung kein hartes Gate ist
- [sitzungskosten-aus-transkript](../anleitungen/sitzungskosten-aus-transkript.md) — Wie die Herstellungskosten oben gemessen wurden
- [team-skripte-generieren](../anleitungen/team-skripte-generieren.md) — Anhang A: die Bau-Anleitung, falls doch generiert statt kopiert wird
- [read-only-guard](../konzepte/read-only-guard.md) — Die 3-Linien-Verteidigung, im Kit verifiziert
- [finder-fixer-prinzip](../konzepte/finder-fixer-prinzip.md) — Das tragende Prinzip des Rollenteams
- [kostencounter](../konzepte/kostencounter.md) — Kostenstandard, den das Kit mitbringt
- [ralph-schleife](../konzepte/ralph-schleife.md) — Das Loop-Muster hinter Ralph
- [claude-code](claude-code.md) — Die CLI, die alle Rollen ausführt
- [ki-team-forensik](../konzepte/ki-team-forensik.md) — Konzeptskizze: dieselbe Infrastruktur für Legacy-Forensik

---

[Wiki-Index](../index.md)
