---
date: 2026-05-15
type: werkzeug
tags: [werkzeug, automatisierung, setup]
status: active
---

# Ralph Wiggum Plugin (Claude Code)

**Zusammenfassung**: Offizielles Claude-Code-Plugin, das die [Ralph-Schleife](../konzepte/ralph-schleife.md) direkt in Claude Code integriert — kein externes Bash-Skript nötig. Claude arbeitet autonom in Iterationen, bis es eine definierte Abschluss-Promise ausgibt oder ein Limit erreicht.
**Quellen**: [anthropics/claude-code – plugins/ralph-wiggum](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum), [The Register – Ralph Wiggum loop](https://www.theregister.com/2026/01/27/ralph_wiggum_claude_loops/), [paddo.dev – Autonomous Loops](https://paddo.dev/blog/ralph-wiggum-autonomous-loops/)
**Zuletzt aktualisiert**: 2026-05-15

---

## Was das Plugin macht

Das Plugin implementiert die [Ralph-Schleife](../konzepte/ralph-schleife.md) als nativen Claude-Code-Mechanismus: Ein **Stop-Hook** fängt jeden Versuch ab, die Sitzung zu beenden, und füttert Claude denselben Prompt erneut. Claude sieht bei jeder neuen Iteration die bereits modifizierten Dateien und die Git-History — und verbessert seine Arbeit, bis es die Abschluss-Bedingung selbst ausgibt oder ein Iterations-Limit greift.

Gegenüber dem manuellen [wiki-ralph.sh](wiki-ralph-sh.md)-Skript hat das Plugin einen Vorteil: alles läuft innerhalb der laufenden Claude-Code-Sitzung, ohne externen Prozess.

---

## Installation

```
/plugin install ralph-wiggum@claude-plugins-official
```

Das Plugin stammt aus dem offiziellen `anthropics/claude-code`-Repository und ist über Anthropics Plugin-Verzeichnis verfügbar. Windows-Nutzer benötigen ggf. `jq` als Abhängigkeit.

Alternativ kann das Plugin manuell aus dem Repository eingebunden werden — dann die Dateien aus `plugins/ralph-wiggum/` in das lokale `.claude/`-Verzeichnis kopieren und in `.claude/settings.json` registrieren (Details → [Plugin-Dokumentation](https://docs.claude.com/en/docs/claude-code/plugins)).

---

## Befehle

### `/ralph-loop` — Schleife starten

```
/ralph-loop "<Aufgabenbeschreibung>" --max-iterations <N> --completion-promise "<Text>"
```

| Option | Bedeutung |
|---|---|
| `--max-iterations N` | Maximale Anzahl Iterationen — **immer setzen**, sonst kein automatischer Stopp |
| `--completion-promise "Text"` | Exakter String, den Claude ausgeben muss, damit die Schleife endet |

### `/cancel-ralph` — Laufende Schleife abbrechen

```
/cancel-ralph
```

---

## Wie eine Iteration abläuft

1. `/ralph-loop` mit Prompt und Optionen starten
2. Claude arbeitet am Task (liest Dateien, schreibt, committet)
3. Claude versucht die Sitzung zu beenden
4. Der Stop-Hook greift — Sitzung wird **nicht** beendet
5. Derselbe Prompt wird erneut vorgelegt
6. Claude sieht alle Änderungen aus Schritt 2 (geänderte Dateien, `git log`) und macht weiter
7. Wiederholen bis: Claude gibt die Completion-Promise aus **oder** `--max-iterations` erreicht

Das Prinzip ist identisch mit [wiki-ralph.sh](wiki-ralph-sh.md), aber ohne externe Bash-Schleife.

---

## Gute Prompts schreiben

### Klare Abschlussbedingung definieren

```
Nimm die Quelle in inbox/neue-quelle.md auf.
Folge dem Aufnahme-Workflow in CLAUDE.md:
- Zusammenfassungsseite erstellen
- Konzeptseiten aktualisieren
- index.md und log.md aktualisieren
- Git-Commit erstellen

Wenn alles abgeschlossen ist, schreibe: <promise>INGESTED</promise>
```

### Verifikationsschritte einbauen

Claude überprüft seine eigene Arbeit, bevor es die Promise ausgibt — das reduziert Iterationen:

```
Nach dem Aufnehmen:
1. Lauf python3 ~/.claude/scripts/wiki_lint.py
2. Wenn der Lint Fehler findet: behebe sie
3. Erst wenn der Lint sauber durchläuft: <promise>DONE</promise>
```

### Iterationslimit immer setzen

Ohne `--max-iterations` läuft die Schleife theoretisch unbegrenzt. Als Faustregel:
- Einzelne Quelle aufnehmen: `--max-iterations 5`
- Inbox mit 5–10 Quellen: `--max-iterations 30`
- Großes Refactoring oder Lint-Bereinigung: `--max-iterations 20`

---

## Anwendungsfall: Dieses Wiki automatisch befüllen

Statt Quellen manuell aufzunehmen, legt man sie in `inbox/` und startet einen Ralph-Loop:

```
/ralph-loop "
Lies CLAUDE.md für den Aufnahme-Workflow.
Prüfe inbox/ auf neue Quelldateien.
Nimm alle gefundenen Quellen nacheinander auf:
- Zusammenfassungsseite in wiki/quellen/
- Konzeptseiten erstellen oder aktualisieren
- index.md und log.md aktualisieren
- Quelle nach raw/ verschieben
- Git-Commit nach jeder Quelle

Wenn inbox/ leer ist, führe python3 ~/.claude/scripts/wiki_lint.py aus.
Wenn lint sauber: <promise>COMPLETE</promise>
Wenn lint Fehler findet: behebe sie, dann <promise>COMPLETE</promise>
" --max-iterations 50 --completion-promise "COMPLETE"
```

Dann kann man den Rechner verlassen — Claude arbeitet bis zur leeren Inbox durch.

---

## Wann sinnvoll, wann nicht

| Geeignet | Nicht geeignet |
|---|---|
| Inbox mit mehreren Quellen aufnehmen | Erste Quelle eines komplett neuen Themas (noch kein Strukturgefühl vorhanden) |
| Lint-Fehler automatisch bereinigen | Entscheidungen über Kategorisierung oder Seitenstruktur |
| Serien ähnlicher Aufgaben (z.B. 10 Werkzeugseiten) | Subjektive Redaktionsfragen |
| Tests oder Lint als objektive Abbruchbedingung | Alles, wo menschliches Urteil gefragt ist |

---

## Kosten im Blick behalten

Jede Iteration verbraucht Token. Orientierungswerte bei Sonnet 4.6:

| Aufgabe | Iterationen (typisch) | Kosten (ca.) |
|---|---|---|
| 1 Quelle aufnehmen | 1–3 | $0,50–1,50 |
| 10 Quellen aufnehmen | 10–20 | $5–10 |
| Vollständiger Lint-Pass | 2–5 | $1–3 |

Mehr zur Kostenstruktur: [usd-pro-wiki-seite](../konzepte/usd-pro-wiki-seite.md)

---

## Verwandte Seiten

- [ralph-schleife](../konzepte/ralph-schleife.md) — Das zugrundeliegende Muster (Konzept, Herkunft, Kostenbenchmarks)
- [wiki-ralph-sh](wiki-ralph-sh.md) — Manuelle Bash-Implementierung derselben Idee (ohne Plugin)
- [claude-code](claude-code.md) — Claude Code: die Plattform, auf der das Plugin läuft
- [ingest-workflow](../konzepte/ingest-workflow.md) — Der manuelle Workflow, den Ralph automatisiert
- [usd-pro-wiki-seite](../konzepte/usd-pro-wiki-seite.md) — Kostenmetrik US$/WP
- [geoffrey-huntley](../personen/geoffrey-huntley.md) — Entwickler des Musters

---

[Wiki-Index](../index.md)
