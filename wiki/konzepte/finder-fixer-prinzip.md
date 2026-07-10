---
date: 2026-07-10
type: konzept
tags: [konzept, automatisierung, schema, softwareprojekt]
status: active
---

# Finder ≠ Fixer

**Zusammenfassung**: Gewaltenteilungs-Prinzip für KI-Rollenteams — wer einen Fehler findet, fixt ihn nicht selbst. Trennt Diagnose- von Umsetzungsrollen und macht jede Übergabe über eine dokumentierte Status-Kette nachvollziehbar.
**Quellen**: `raw/claude-md-team.md`
**Zuletzt aktualisiert**: 2026-07-10

---

## Die Regel

In einem KI-Rollenteam mit mehreren spezialisierten Instanzen (z. B. Security-Tester, Chaos-Tester, Forensiker) ist es verlockend, jede Rolle ihren eigenen Fund auch gleich selbst reparieren zu lassen. Das Finder-≠-Fixer-Prinzip verbietet das ausdrücklich: **Wer findet, fixt nicht.** Ein separater, dafür zuständiger Fixer übernimmt jede Reparatur.

In der [claude-md-ki-team](../vorlagen/claude-md-ki-team.md)-Vorlage konkret: Harry (Security), Marv (Chaos/Regression) und Axel (Forensiker) finden und dokumentieren Fehler — aber ausschließlich Frank fixt sie.

## Warum das wichtig ist

Ohne diese Trennung entstehen zwei Probleme:

1. **Rollenvermischung** — eine Test-Rolle, die auch fixt, verliert ihre Objektivität. Sie testet nicht mehr unvoreingenommen, sondern gegen den eigenen zuletzt geschriebenen Code.
2. **Verlorene Nachvollziehbarkeit** — wenn jede Rolle direkt in den Produktivcode eingreifen darf, gibt es keinen zentralen Übergabepunkt mehr, an dem sich der Zustand aller offenen Funde ablesen lässt.

Das Prinzip erzwingt stattdessen eine **Status-Kette**: `offen → an Frank übergeben → an Axel übergeben → Fix-Plan liegt vor → erledigt (Frank-Fix, …) → an Mensch eskaliert`. Jeder Schritt ist ein Dokument-Update, kein stiller Code-Edit.

## Verhältnis zum Read-Only-Guard

Finder ≠ Fixer ist die **Prozess-Regel**; der [read-only-guard](read-only-guard.md) ist ihre **technische Durchsetzung**. Die Prozess-Regel allein verlässt sich auf Prompt-Befolgung — die Quelle stellt ausdrücklich klar, dass das nicht genügt und eine harte technische Schranke (Tool-Permissions + Post-Hook) nötig ist, damit die Trennung auch bei einem abweichenden Agenten hält.

## Übertragbarkeit

Das Prinzip ist nicht auf Software-Teams beschränkt. Es überträgt sich auf jedes Multi-Rollen-Setup, in dem eine Instanz bewusst kritisch/destruktiv testen soll (Red Team, Fuzzer, Forensiker) — Objektivität geht verloren, sobald dieselbe Instanz auch reparieren darf.

## Verwandte Seiten

- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) — Die Vorlage, aus der dieses Prinzip stammt
- [read-only-guard](read-only-guard.md) — Technische Durchsetzung der Trennung
- [claude-md-ki-team](../quellen/claude-md-ki-team.md) — Quellenseite

---

[Wiki-Index](../index.md)
