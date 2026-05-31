---
date: 2026-05-31
type: konzept
tags: [konzept, software, release, qualitaet]
status: active
---

# Feldtest-Sperre

**Zusammenfassung**: Ein vollständiger Änderungsstopp während echter Nutzertests — verhindert, dass laufende Beobachtungen durch gleichzeitige Code-Änderungen kontaminiert werden.
**Quellen**: `raw/CLAUDE-MD-Softwareprojekt-Battetested-Rookie.md` (KI-Lehrer App, FELDTEST-SPERRE ab V1.9, 2026-05-31)
**Zuletzt aktualisiert**: 2026-05-31

---

## Das Muster

Wenn eine Software-Version in den echten Einsatz mit echten Nutzern geht, beginnt eine Phase, in der Beobachtung wichtiger ist als Weiterentwicklung. Die Feldtest-Sperre formalisiert diesen Übergang: während des Tests ändert sich nichts am Code.

Ein einfaches Prinzip, aber ohne explizite Regel leicht verletzt. Der Entwicklungsreflex, einen gemeldeten Bug sofort zu fixen oder ein beobachtetes Problem zu "schnell" zu lösen, ist stark — und genau er macht Feldtest-Beobachtungen unzuverlässig.

## Warum die Sperre nötig ist

Wenn Code und Verhalten während eines Tests gleichzeitig geändert werden, lässt sich hinterher nicht mehr sagen:
- Hatte der Nutzer Probleme mit V1.9 oder mit dem Fix, der am dritten Testtag eingespielt wurde?
- Ist das beobachtete Verhalten ein Feature-Problem oder ein Regressions-Problem?
- Gelten die Beobachtungen noch, oder hat der Hotfix die Grundlage verändert?

Die Feldtest-Sperre erzwingt eine saubere Trennung: Beobachten, dann entscheiden, dann ändern.

## Auslösebedingung

Die Sperre wird durch einen Übergang von "Entwicklung" zu "echtem Einsatz" ausgelöst:
- Ein Kind nutzt die App erstmals allein
- Ein Pilot-Nutzer testet die Software im Arbeitsalltag
- Ein MVP geht live

Der genaue Auslöser wird explizit im CLAUDE.md benannt — nicht implizit durch einen Commit-Tag.

## Was die Sperre blockiert

| Gesperrt | Erlaubt |
|---|---|
| Neue Features | Status-Inspektion, git log |
| Refactorings | Lesen von Dateien und Code |
| Bugfixes "auf Verdacht" | Dokumentation des Feldtest-Verlaufs |
| Ralph-Loop / Kaskaden-Fortsetzung | Notizen über Beobachtungen |
| Neuer Major-Release | Vorbereitung der Änderungsliste für danach |

"Auf Verdacht" ist der entscheidende Begriff. Ein Absturz, der den Test komplett blockiert, ist etwas anderes als ein ärgerliches UI-Detail. Die Sperre schließt Letzteres aus, nicht Ersteres — das explizit so zu formulieren hilft Claude, Grenzfälle richtig einzuordnen.

## Aufhebungsbedingung

Die Sperre endet nur durch eine explizite Nachricht mit zwei Elementen:
1. Klares Signal, dass der Feldtest abgeschlossen ist ("Feldtest abgeschlossen", "Test beendet", o.ä.)
2. Eine Änderungswunschliste auf Basis der Feldtest-Erkenntnisse

Das zweite Element ist entscheidend: Es zwingt den Entwickler, Beobachtungen zuerst zu konsolidieren, bevor neue Entwicklung beginnt. Die Erkenntnisse gehen nicht verloren.

## Implementierung in CLAUDE.md

Die Sperre wird als benannter, prominenter Abschnitt in die CLAUDE.md eingefügt:

```markdown
## FELDTEST-SPERRE (seit {{DATUM}})

**Status:** V{{N}} ist im Feldtest. Bis zum Abschluss gilt ein vollständiger Änderungsstopp.
...
**Aufhebung** erfolgt nur durch ...
```

Die Platzierung ganz oben in der CLAUDE.md sorgt dafür, dass Claude sie bei jedem Session-Start liest.

## Wann einsetzen

Nicht bei jedem kleinen Deployment — nur wenn echte Nutzerbeobachtungen gesammelt werden sollen, die als Grundlage für die nächste Entwicklungsrunde dienen. Bei reinen Smoke-Tests oder kurzen technischen Checks ist die Sperre zu schwer.

Indikator: "Ich will nach dem Test wissen, was ich als Nächstes bauen soll." Dann ist eine Sperre sinnvoll.

## Abgrenzung zu ähnlichen Konzepten

**Freeze vor Release (Code Freeze)**: Schützt einen Entwicklungsstand kurz vor dem Release, damit keine last-minute-Bugs hineinkommen. Endet mit dem Release. Die Feldtest-Sperre beginnt nach dem Release.

**Feature-Flag**: Deaktiviert einzelne Features zur Laufzeit, ohne den Code zu ändern. Ergänzt die Feldtest-Sperre, ersetzt sie nicht.

**Staged Rollout / Canary**: Kontrolliert, wer eine neue Version bekommt. Ist unabhängig von der Feldtest-Sperre — beide können gleichzeitig aktiv sein.

## Verwandte Seiten

- [claude-md-softwareprojekt-rookie](../vorlagen/claude-md-softwareprojekt-rookie.md) — Vorlage, die die Sperre enthält
- [ralph-schleife](ralph-schleife.md) — Der Loop, der während der Sperre eingefroren wird
- [ki-lehrer-app](ki-lehrer-app.md) — Das Projekt, aus dem das Muster stammt
- [claude-md-design](claude-md-design.md) — Wie CLAUDE.md-Abschnitte wirksam formuliert werden

---

[Wiki-Index](../index.md)
