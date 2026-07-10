---
date: 2026-07-10
type: konzept
tags: [konzept, automatisierung, schema, softwareprojekt]
status: active
---

# Read-Only-Guard (3-Linien-Verteidigung)

**Zusammenfassung**: Defense-in-Depth-Muster, um KI-Rollen technisch auf Lesezugriff zu beschränken — drei unabhängige Linien, weil Prompt-Vertrauen allein nachweislich nicht genügt.
**Quellen**: `raw/claude-md-team.md`
**Zuletzt aktualisiert**: 2026-07-10

---

## Das Problem

Manche KI-Rollen sollen ausschließlich beobachten, testen und dokumentieren — niemals Produktivcode ändern. Ein Red-Team-Tester etwa (siehe [finder-fixer-prinzip](finder-fixer-prinzip.md)) verliert seinen Zweck, sobald er selbst reparieren darf. Die naheliegende Lösung — „schreib einfach in die Rolleninstruktion, dass nur gelesen werden darf" — ist laut Quelle **notwendig, aber nicht hinreichend**: ein Prompt ist keine harte Garantie.

## Die drei Linien

1. **Prompt** — Rolleninstruktion, ausschließlich in Test-/Plan-Ordner zu schreiben. Schwächste Linie, aber Grundvoraussetzung.
2. **Tool-Permissions** — `Write`/`Edit` technisch nur auf Test-/Plan-Ordner-Globs erlauben, Produktivcode-Pfade explizit verbieten; `Read` bleibt überall erlaubt.
3. **Post-Hook (deterministische Garantie)** — nach jeder Iteration `git status`/`git diff --name-only` gegen die Whitelist prüfen. Bei Verletzung: `git reset --hard <START_HASH>` und Abbruch. Ergänzt durch einen rollenspezifischen `pre-commit`-Hook.

Die dritte Linie ist die eigentliche Garantie: Sie greift unabhängig davon, ob die eingesetzte CLI ein `permissions.deny`-Format überhaupt unterstützt. Fehlt dieses Feature, trägt der Post-Hook allein die Verantwortung — das Muster ist bewusst so gebaut, dass es in beiden Fällen hält.

## Ausnahme: die Fixer-Rolle

Eine Rolle, die *darf* Produktivcode ändern (z. B. Frank in der [claude-md-ki-team](../vorlagen/claude-md-ki-team.md)-Vorlage), bekommt keinen Guard — stattdessen eine **Dreisatz-Verifikation** nach jedem Fix (Commit mit definiertem Präfix, CHANGELOG ergänzt, Backlog-/Beutebuch-Status gesetzt). Der Guard und die Dreisatz-Verifikation sind zwei Varianten desselben Grundgedankens: eine automatisierbare Nachprüfung statt reinem Vertrauen in die Rolleninstruktion.

## Faktencheck-Pflicht statt Annahme

Vor der Umsetzung muss an der real installierten CLI verifiziert werden, ob das Tool-Permission-Format der Linie 2 tatsächlich unterstützt wird — nicht geraten. Das deckt sich mit dem generellen Wiki-Grundsatz „Spec ist Wahrheit vor Annahmen".

## Übertragbarkeit

Das Muster passt auf jede Situation, in der eine Automatisierung (nicht nur KI-Agenten) bewusst mit eingeschränkten Schreibrechten laufen soll, aber die Schreibrechte im eingesetzten Tool nicht granular genug konfigurierbar sind — die dritte Linie (Post-Hook mit Hard-Reset) funktioniert als Fallback unabhängig vom Funktionsumfang des Tools.

## Verwandte Seiten

- [finder-fixer-prinzip](finder-fixer-prinzip.md) — Die Prozess-Regel, die dieser Guard technisch durchsetzt
- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) — Die Vorlage, aus der dieses Muster stammt
- [claude-md-ki-team](../quellen/claude-md-ki-team.md) — Quellenseite

---

[Wiki-Index](../index.md)
