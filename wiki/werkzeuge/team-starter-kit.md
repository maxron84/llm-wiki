---
date: 2026-08-01
type: werkzeug
tags: [werkzeug, automatisierung, softwareprojekt, setup, kosten]
status: active
---

# T.E.A.M.-Starterkit

**Zusammenfassung**: Ein installierbares Bündel, das die [T.E.A.M.-Vorlage](../vorlagen/claude-md-ki-team.md) mit einem Konsolenbefehl als lauffähiges KI-Rollenteam in ein neues Software-Projekt bringt — 53 Dateien, sieben Fragen, Selbsttest. Sprach- und stackagnostisch.
**Quellen**: Repo `~/Source/team-kit` (eigenes Git-Repo, Version 2.1.0); Code übernommen aus dem Feldprojekt `website-maxron-de` (22 Kaskaden, 2026-07-10 bis 2026-08-01).
**Zuletzt aktualisiert**: 2026-08-01

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

- Installation: 53 Dateien, Platzhalter korrekt gefüllt, abgeleitete Pfade und Whitelists stimmen
- **Stack-Neutralität in Go-, Rust- und PHP-Strukturen**: keine Python-Dateien in fremden Projektordnern, kein `scripts/` oder `prompts/` im Wurzelverzeichnis angelegt, eigene Dateien unangetastet
- Shell-Syntax aller Skripte, Python-Kompilierung beider Werkzeuge
- **127 Regressionstests grün** (25 Testdateien der Team-Infrastruktur)
- Read-Only-Guard: chirurgischer Rollback des Verletzers, erlaubte Datei blieb erhalten, Produktivcode unangetastet
- Verhalten aller Rollen ohne Arbeitsvorrat (Exit 3 = nichts zu tun)
- **Idempotenz**: zweiter Lauf schreibt 0 Dateien, eigene Änderungen bleiben, `.gitignore` wird nicht doppelt ergänzt

**Nicht verifiziert**: ein vollständiger scharfer `vollautomatik.sh`-Lauf in einem neuen Projekt. Das kostet echtes Geld und braucht einen echten Kaskaden-Plan. Der erste scharfe Lauf bleibt ein Feldtest — das Kit sorgt dafür, dass er nicht an Formalien scheitert.

## Erstlauf-Regeln stehen in den Artefakten, nicht im Gespräch

Beim Abnahmegespräch fiel auf, dass die Empfehlungen für den ersten Lauf nirgends festgehalten waren — ein kalt startender Architekt in einem frischen Projekt hätte sie nicht gekannt. Seit Version 2.1.0 stehen sie dort, wo sie wirken:

- **Architekten-Briefing**, Abschnitt „Die erste Kaskade eines Projekts": Smoke-Test hat Vorrang vor jedem Feature, drei bis fünf Stufen, `BUDGET_EMPFEHLUNG_USD` konservativ aber nicht knauserig, nach dem Lauf den Bauweg ehrlich bewerten.
- **Bootstrap-Roadmap** bringt „Skizze 1: Verifikationsfähigkeit herstellen" mit und zeigt über den gefüllten Platzhalter selbst an, ob sie noch gebraucht wird.
- **Installer-Abschlussmeldung** nennt `TEAM_BUDGET_USD=15` für den Erstlauf.

Das ist dieselbe Lehre wie Planungsregel 5 der Vorlage: Was nicht im Git steht, existiert für die nächste Instanz nicht — ob es nun ein Terminal-Abschlussbericht ist oder eine mündliche Empfehlung.

## Reihenfolge nach der Installation

1. `team.config.sh` und `CLAUDE.md` prüfen, TODO-Stellen füllen
2. **Committen** — vor dem ersten Guard-Lauf. Der Guard betrachtet uncommittete Dateien außerhalb der Whitelist als Verletzung; im Ursprungsprojekt löschte er einmal die gesamte frisch gebaute Infrastruktur (siehe [read-only-guard](../konzepte/read-only-guard.md))
3. `./team-test.sh` — prüft die Infrastruktur, nicht das Projekt
4. Erste Kaskade planen: Claude-Sitzung mit Opus, *„Du bist unser Architekt, lies `team/prompts/rolle-architekt.md`."* Er härtet eine Skizze aus, setzt `RALPH_CAP` und `BUDGET_EMPFEHLUNG_USD` und gibt die Scharfschalt-Sequenz aus. **Die Erstlauf-Regeln stehen in seinem Briefing** — Smoke-Test vor jedem Feature, drei bis fünf Stufen, Budget konservativ aber nicht knauserig
5. Scharfschalten: `echo plans/ralph-kaskade-1-….md > .ralph-plan`
6. `./vollautomatik.sh`
7. **Nach dem Lauf**: `./team-status.sh --rollen-abschluss <N> <domaene>` und `--architekt-abschluss <USD> <domaene>`. Der Architekt läuft interaktiv außerhalb der Kostenlogs — ohne diesen Schritt bleibt seine Sitzung unerfasst (im Feld ~16 USD)

## Verwandte Seiten

- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) — Die Vorlage, die das Kit installiert
- [team-skripte-generieren](../anleitungen/team-skripte-generieren.md) — Anhang A: die Bau-Anleitung, falls doch generiert statt kopiert wird
- [read-only-guard](../konzepte/read-only-guard.md) — Die 3-Linien-Verteidigung, im Kit verifiziert
- [finder-fixer-prinzip](../konzepte/finder-fixer-prinzip.md) — Das tragende Prinzip des Rollenteams
- [kostencounter](../konzepte/kostencounter.md) — Kostenstandard, den das Kit mitbringt
- [ralph-schleife](../konzepte/ralph-schleife.md) — Das Loop-Muster hinter Ralph
- [claude-code](claude-code.md) — Die CLI, die alle Rollen ausführt
- [ki-team-forensik](../konzepte/ki-team-forensik.md) — Konzeptskizze: dieselbe Infrastruktur für Legacy-Forensik

---

[Wiki-Index](../index.md)
