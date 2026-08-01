---
date: 2026-08-01
type: werkzeug
tags: [werkzeug, automatisierung, softwareprojekt, setup, kosten]
status: active
---

# T.E.A.M.-Starterkit

**Zusammenfassung**: Ein installierbares Bündel, das die [T.E.A.M.-Vorlage](../vorlagen/claude-md-ki-team.md) mit einem Konsolenbefehl als lauffähiges KI-Rollenteam in ein neues Software-Projekt bringt — 51 Dateien, fünf Fragen, Selbsttest.
**Quellen**: Repo `~/Source/team-kit` (eigenes Git-Repo, Version 1.0.0); Code übernommen aus dem Feldprojekt `website-maxron-de` (22 Kaskaden, 2026-07-10 bis 2026-08-01).
**Zuletzt aktualisiert**: 2026-08-01

---

## Aufruf

```bash
bash ~/.claude/scripts/team-init.sh ~/Source/mein-neues-projekt
```

Der Befehl ist ein dünner Launcher; der versionierte Installer liegt im Kit-Repo unter `~/Source/team-kit/install.sh`.

## Warum es das gibt

Die [T.E.A.M.-Vorlage](../vorlagen/claude-md-ki-team.md) beschreibt das Rollenteam vollständig — aber sie liefert **bewusst keine Skripte**, sondern eine Bau-Anleitung ([team-skripte-generieren](../anleitungen/team-skripte-generieren.md)). Das ist für fremde Umgebungen richtig: unbekannte CLI, fremde Repo-Konventionen.

Auf **derselben Maschine mit derselben CLI** ist es aber die schlechtere Wahl. 3.200 Zeilen über 22 Kaskaden gehärteter Bash und Python lassen sich nicht verlustfrei aus einer Beschreibung rekonstruieren; jeder dabei übersehene Detailfix kostet real Geld (`BL-55` war eine 77-%-Fehlmessung, die Stagnations-Lücke ~13,8 USD Leerlauf). Das Kit übernimmt den Code deshalb **wörtlich** und parametrisiert nur, was projektspezifisch ist.

## Was übernommen und was angepasst wurde

Bei der Feldinspektion zeigte sich, dass die harten Projektbezüge stark konzentriert sind:

| Baustein | Zeilen | Behandlung |
|---|---|---|
| `team-lib.sh` | 821 | **wörtlich** (bis auf Config-Sourcing + zwei Helfer) |
| `kosten.py` | 952 | **wörtlich** |
| `beutebuch.py` | 275 | **wörtlich** |
| `vollautomatik.sh`, `halbautomatik.sh`, `team-status.sh` | 643 | **wörtlich** |
| `ralph.sh`, `frank.sh`, `axel.sh`, `redteam.sh` | 504 | 32 Stellen parametrisiert |

Alle Projektwerte stehen in einer einzigen Datei `team.config.sh` im Zielprojekt. Sie wird von `team-lib.sh` gesourct, also von jeder Rolle — eine Änderung dort wirkt sofort überall, ohne Neuinstallation.

## Die fünf Fragen

| Frage | Default | Bedeutung |
|---|---|---|
| Projektname | Ordnername | erscheint in Berichten und Ledger |
| Produktivcode-Ordner | `src/` | tabu für Harry, Marv, Axel |
| Test-Ordner | `tests/` | Reproducer und Regressionstests |
| Plan-Ordner | `plans/` | Kaskaden, Beutebuch, Akten, Roadmap |
| **Smoke-Test-Befehl** | *(leer)* | der eine Befehl, der prüft, ob das Projekt heil ist |

Der Smoke-Test ist der kritische Wert: Ralph schließt keine Stufe ohne ihn ab, Frank verifiziert keinen Fix. Bleibt er leer, melden die Rollen das in **jedem** Prompt als offenen Punkt — das Team läuft, aber sichtbar ohne Sicherheitsnetz. Siehe [kontextrahmen-5w1h](../konzepte/kontextrahmen-5w1h.md) für den verwandten Gedanken, Rahmenbedingungen explizit zu machen statt sie zu raten.

Die übrigen elf Platzhalter bekommen die im Feld erprobten Defaults (Budget 5/10 USD, Modelle Sonnet/Opus, Abo-first).

## Zwei Defekte, die erst beim Erstlauf sichtbar wurden

Der Selbsttest in einem Wegwerf-Repo förderte zwei Fehler zutage, die im Feldprojekt **nie** auftreten konnten:

`ralph.sh` und `team_plan_datei()` lasen die Zeiger-Datei `.ralph-plan` mit `head`. Fehlt die Datei, liefert `head` einen Rückgabewert ≠ 0 — und unter `set -e -o pipefail` riss das den Loop weg, **bevor** die erklärende Fehlermeldung erreicht wurde. Der Anwender sah einen blanken Exit 1 ohne jeden Hinweis.

Im Feldprojekt existiert `.ralph-plan` seit Kaskade 1, deshalb ist das dort nie aufgefallen. In einem frischen Projekt ist die fehlende Datei der **Normalfall beim allerersten Start**. Genau die Fehlerklasse, die auch die [Feldinspektion](../quellen/claude-md-ki-team.md) zutage gefördert hat: Was zur Selbstverständlichkeit geworden ist, meldet niemand zurück.

## Was verifiziert ist — und was nicht

**Verifiziert** (Wegwerf-Repo, 2026-08-01):

- Installation: 51 Dateien, Platzhalter korrekt gefüllt, abgeleitete Pfade und Whitelists stimmen
- Shell-Syntax aller Skripte, Python-Kompilierung beider Werkzeuge
- **127 Regressionstests grün** (25 Testdateien der Team-Infrastruktur)
- Read-Only-Guard: chirurgischer Rollback des Verletzers, erlaubte Datei blieb erhalten, Produktivcode unangetastet
- Verhalten aller Rollen ohne Arbeitsvorrat (Exit 3 = nichts zu tun)
- **Idempotenz**: zweiter Lauf schreibt 0 Dateien, eigene Änderungen bleiben, `.gitignore` wird nicht doppelt ergänzt

**Nicht verifiziert**: ein vollständiger scharfer `vollautomatik.sh`-Lauf in einem neuen Projekt. Das kostet echtes Geld und braucht einen echten Kaskaden-Plan. Der erste scharfe Lauf bleibt ein Feldtest — das Kit sorgt dafür, dass er nicht an Formalien scheitert.

## Reihenfolge nach der Installation

1. `team.config.sh` und `CLAUDE.md` prüfen, TODO-Stellen füllen
2. **Committen** — vor dem ersten Guard-Lauf. Der Guard betrachtet uncommittete Dateien außerhalb der Whitelist als Verletzung; im Ursprungsprojekt löschte er einmal die gesamte frisch gebaute Infrastruktur (siehe [read-only-guard](../konzepte/read-only-guard.md))
3. Erste Kaskade planen (Rolle *Der Architekt*): Skizze aushärten, `RALPH_CAP` und `BUDGET_EMPFEHLUNG_USD` setzen
4. Scharfschalten: `echo plans/ralph-kaskade-1-….md > .ralph-plan`
5. `./vollautomatik.sh`

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
