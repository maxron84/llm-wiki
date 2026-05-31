---
date: 2026-05-31
type: vorlage
tags: [vorlage, software, claude-md, ralph-schleife]
status: active
---

# CLAUDE.md-Vorlage: Softwareprojekt (Rookie, Battle-tested)

**Zusammenfassung**: Eine CLAUDE.md-Vorlage für Desktop- oder CLI-Softwareprojekte, die mit Claude und optionalem ralph.sh-Loop entwickelt werden — mit FELDTEST-SPERRE, stufenweiser Build-Reihenfolge, Regressions-Checklisten und Umgebungshinweisen.
**Quellen**: Abgeleitet aus `raw/CLAUDE-MD-Softwareprojekt-Battetested-Rookie.md` (KI-Lehrer App V1.9)
**Zuletzt aktualisiert**: 2026-05-31

---

## Zweck

Diese Vorlage richtet sich an Solo-Entwickler oder kleine Teams, die ein überschaubares Softwareprojekt (Desktop-App, CLI-Tool, kleines Web-Backend) iterativ mit Claude aufbauen. "Rookie" bedeutet hier: Der Entwickler ist neu in diesem Anwendungstyp, nicht unbedingt in der Programmierung.

Unterschied zur [claude-md-software](claude-md-software.md)-Basisvorlage: Diese Variante enthält battle-tested Muster für den späteren Projektverlauf — wenn das Projekt gewachsen ist und echte Nutzer testet.

## Enthaltene Muster

| Muster | Wozu |
|---|---|
| [FELDTEST-SPERRE](../konzepte/feldtest-sperre.md) | Release-Freeze während echter Nutzertests |
| Stufenweise Build-Reihenfolge | Jede Stufe läuft, bevor die nächste beginnt |
| Regressions-Checkliste | Feature-Tests direkt in der CLAUDE.md |
| Aktueller-Stand-Abschnitt | Claude liest Projektzustand ohne Commit-Log |
| Ralph.sh-Konfiguration | Budget-Obergrenzen, Promise-Match, Smoke-Test |

## Benutzung

1. Kopiere **nur den Vorlageninhalt** unten als `CLAUDE.md` in dein Projektverzeichnis
2. Ersetze alle `{{PLATZHALTER}}`
3. Entferne den Anweisungsblock ganz oben
4. Starte mit Stufe 1 und schließe sie vollständig ab, bevor du Stufe 2 beginnst

## Vorlage

````markdown
# CLAUDE — {{Projektname}}

{{Kurzbeschreibung: Ein Satz, was die App tut und für wen.}}

---

## Spezifikation

Alle Designentscheidungen liegen in:

```
docs/spec/
├── {{modul-1}}.md    — {{Kurzbeschreibung}}
└── {{modul-2}}.md    — {{Kurzbeschreibung}}
```

Vor jeder Implementierung: relevante Spec-Seite lesen. Die Spec ist die Wahrheit — nicht Annahmen. Wenn eine Designfrage nicht in der Spec steht: fragen, nicht raten.

## Tech-Stack

| Schicht | Technologie |
|---|---|
| GUI | {{z.B. Tkinter, Qt, Electron}} |
| KI | {{z.B. anthropic Python SDK}} |
| Datenhaltung | {{z.B. JSON + Markdown}} |
| Build | {{z.B. pyinstaller}} |

**Mindestversion**: {{z.B. Python 3.10}}

## Projektstruktur

```
{{projektname}}/
├── main.py           # Entry-Point
├── app.py            # Hauptfenster / AppState
├── gui/              # Alle UI-Komponenten
├── core/             # Geschäftslogik (kein Tkinter)
└── docs/
    └── spec/
```

## Build-Reihenfolge

Wir bauen in Stufen — jede ist eigenständig lauffähig:

| Stufe | Was | Ziel |
|---|---|---|
| 1 | {{Grundgerüst}} | App startet |
| 2 | {{Zweite Stufe}} | {{Testkriterium}} |
| 3 | {{Dritte Stufe}} | {{Testkriterium}} |

Aktuelle Stufe vor dem Start bestimmen. Eine Stufe vollständig abschließen, bevor die nächste beginnt.

## Architektur-Regeln

- **AppState** — zentrales State-Objekt, wird beim Start instanziiert und an alle Screens übergeben. Keine globalen Variablen.
- **core/ kennt kein {{GUI-Framework}}** — Geschäftslogik importiert kein Tkinter/Qt/etc.
- **Streaming im Thread** — API-Calls laufen in eigenem Thread, GUI-Updates via `root.after()` oder Äquivalent.

## Regeln

- Nach jeder abgeschlossenen Stufe committen — ohne explizite Aufforderung
- Keine Features aus einer späteren Stufe vorwegnehmen
- Wenn eine Designfrage nicht in der Spec steht: fragen, nicht raten

---

## FELDTEST-SPERRE (bei Bedarf aktivieren)

> Diese Sektion wird eingefügt, wenn das Projekt in einen echten Nutzertest geht.

**Status:** V{{N.N}} ist im Feldtest. Bis zum Abschluss gilt ein vollständiger Änderungsstopp:

- Keine neuen Features, keine Refactorings, keine Bugfixes auf Verdacht
- Kein Ralph-Lauf
- Kein neuer Major-Release

**Aufhebung** erfolgt nur durch eine explizite Nutzer-Nachricht mit:
1. "Feldtest abgeschlossen" (oder Äquivalent)
2. Neuer Feature- oder Änderungswunschliste auf Basis der Feldtest-Erkenntnisse

---

## Aktueller Stand

**V{{N.N}}: {{Was ist in dieser Version enthalten?}}**

Vorgänger-Version V{{N-1}}: {{Kurze Beschreibung der letzten Änderung}}.

Den exakten Stand zeigt `git log --grep="feat(stufe"`.

## Bekannte manuelle Regressions-Checks

- **{{Feature A}} (Stufe {{N}})**: {{Beschreibung des manuellen Tests und des erwarteten Ergebnisses}}
- **{{Feature B}} (Stufe {{N}})**: {{Beschreibung des manuellen Tests und des erwarteten Ergebnisses}}

## Umgebungs-Hinweise

- {{Bekannte Umgebungseinschränkungen, z.B. Flatpak/Sandbox-Probleme}}
- **App nur im nativen Terminal testen** (nicht im integrierten IDE-Terminal, wenn Sandbox-Probleme bekannt sind)

## Hinweise zum ralph.sh-Loop

Falls der Loop gebraucht wird:

- **Modell**: `${RALPH_MODEL:-sonnet}`. Opus überspringen — teurer ohne Qualitätsvorteil für kleine Apps.
- **Permission-Mode**: `bypassPermissions`. Im headless `-p`-Modus gibt es keinen User zum Approven.
- **Smoke-Test**: `py_compile` + `ast.parse`, nicht `import {{framework}}` — muss Sandbox-tauglich sein.
- **Budget kappen**: `RALPH_BUDGET_USD=N ./ralph.sh` — Empfehlung: 3 USD pro Stufe.
- **Promise-Match**: `<promise>STUFE_N_COMPLETE</promise>` mit Tags.

Bei Loop-Hängern: zuerst `pgrep -af "claude -p"` und letzten Log-Eintrag prüfen, bevor abgebrochen wird.
````

## Designhinweise

Die [CLAUDE.md-Designprinzipien](../konzepte/claude-md-design.md) beachten:

1. **Fence entfernen**: Der Vorlageninhalt steht in einem 4-Backtick-Fence. Beim Kopieren entfernen — sonst liest Claude die Regeln als Illustration, nicht als operative Anweisung.
2. **FELDTEST-SPERRE**: Anfangs weglassen. Erst einfügen, wenn ein echter Nutzertest beginnt. Den Versionsbezug konkret setzen (V1.9, nicht "aktuelle Version").
3. **Regressions-Checkliste**: Jeden neuen Feature-Check direkt nach dem Merge eintragen. Die Liste wächst mit dem Projekt — das ist Absicht.
4. **Aktueller-Stand-Abschnitt**: Mit jeder abgeschlossenen Kaskade/Version aktualisieren. Veralteter Stand ist schlimmer als kein Stand.

## Verhältnis zu anderen Vorlagen

| Vorlage | Für wen |
|---|---|
| [claude-md-software](claude-md-software.md) | Allgemeines Software-Projekt, schlank |
| **claude-md-softwareprojekt-rookie** | Solo-Projekt mit Ralph-Loop, ab mittlerem Reifegrad |
| [claude-md-legacy-forensik](claude-md-legacy-forensik.md) | Forensische Analyse bestehender Systeme |

## Verwandte Seiten

- [feldtest-sperre](../konzepte/feldtest-sperre.md) — Das Freeze-Muster in der Tiefe
- [ralph-schleife](../konzepte/ralph-schleife.md) — Wie der ralph.sh-Loop grundsätzlich funktioniert
- [claude-md-design](../konzepte/claude-md-design.md) — Designprinzipien für CLAUDE.md-Dateien
- [claude-md-softwareprojekt-rookie](../quellen/claude-md-softwareprojekt-rookie.md) — Die Quell-Datei: KI-Lehrer App V1.9

---

[Wiki-Index](../index.md)
