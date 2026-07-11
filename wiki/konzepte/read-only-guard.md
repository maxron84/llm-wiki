---
date: 2026-07-10
type: konzept
tags: [konzept, automatisierung, schema, softwareprojekt]
status: active
---

# Read-Only-Guard (3-Linien-Verteidigung)

**Zusammenfassung**: Defense-in-Depth-Muster, um KI-Rollen technisch auf Lesezugriff zu beschränken — drei unabhängige Linien, weil Prompt-Vertrauen allein nachweislich nicht genügt.
**Quellen**: `raw/claude-md-team.md`
**Zuletzt aktualisiert**: 2026-07-11

---

## Das Problem

Manche KI-Rollen sollen ausschließlich beobachten, testen und dokumentieren — niemals Produktivcode ändern. Ein Red-Team-Tester etwa (siehe [finder-fixer-prinzip](finder-fixer-prinzip.md)) verliert seinen Zweck, sobald er selbst reparieren darf. Die naheliegende Lösung — „schreib einfach in die Rolleninstruktion, dass nur gelesen werden darf" — ist laut Quelle **notwendig, aber nicht hinreichend**: ein Prompt ist keine harte Garantie.

## Die drei Linien

1. **Prompt** — Rolleninstruktion, ausschließlich in Test-/Plan-Ordner zu schreiben. Schwächste Linie, aber Grundvoraussetzung.
2. **Tool-Permissions** — `Write`/`Edit` technisch nur auf Test-/Plan-Ordner-Globs erlauben, Produktivcode-Pfade explizit verbieten; `Read` bleibt überall erlaubt.
3. **Post-Hook (deterministische Garantie)** — nach jeder Iteration `git status --porcelain`/`git diff --name-only <START_HASH> HEAD` gegen die Whitelist prüfen. Bei Verletzung **chirurgisch** zurücksetzen: **nur die konkret gelisteten Verletzer-Pfade** (getrackt → `git checkout <START_HASH> -- <pfad>`; neu entstanden → gezielt `rm`/`git rm`), dann Abbruch. Ergänzt durch einen optionalen rollenspezifischen `pre-commit`-Hook.

Die dritte Linie ist die eigentliche Garantie: Sie greift unabhängig davon, ob die eingesetzte CLI ein `permissions.deny`-Format überhaupt unterstützt. Fehlt dieses Feature, trägt der Post-Hook allein die Verantwortung — das Muster ist bewusst so gebaut, dass es in beiden Fällen hält.

> ⚠️ **Chirurgisch, nicht mit dem Vorschlaghammer (Feldtest-Lektion, website-maxron-de 2026-07-10):** Ein früher Entwurf setzte Linie 3 mit **`git reset --hard <START_HASH>` + `git clean -fd`** um — also einem Rollback des **gesamten** Arbeitsbaums. Das ist ein Footgun: Beim Bau der Automatik selbst, als die neuen Rollen-Skripte noch **uncommittet** waren, wertete der Guard sie als „Nicht-Whitelist" und **löschte die komplette Team-Infrastruktur** (`git clean -fd` entfernt alle untracked Dateien, `reset --hard` verwirft alle Modifikationen). Konsequenzen: (a) nur einzelne Verletzer-Pfade zurücksetzen, nie den ganzen Baum; (b) Infrastruktur committen, **bevor** je ein Guard läuft — im Normalbetrieb ist der Baum zwischen den Phasen ohnehin sauber; (c) Guard-Tests ausschließlich in einem **Wegwerf-Repo** ausführen.

> ⚠️ **Whitelist-Prüfung ≠ datei-genaues Staging (Feldlauf website-maxron-de, 2026-07-11):** Wenn das Guard-Skript die erlaubten Änderungen selbst committet, muss es **datei-genau** stagen — **nicht ordner-weit**. Ein Red-Team-Sweep staged zunächst das ganze Plan-Verzeichnis (`git add plans/`), obwohl die Rolle laut Prompt nur ins Beutebuch (+ Test-Ordner) schreibt. Der Haken: Der interaktive **planende Akteur läuft außerhalb des `flock`-Locks** (kein Loop), kann also gleichzeitig **uncommittete** Dateien unter derselben Whitelist (`plans/`) liegen haben — ein parallel laufender Sweep zieht sie dann in seinen Commit. Die Ordner-Whitelist (`^(tests/|plans/)`) lässt sie formal durch, weil sie darauf passen. Konsequenz: gezielt nur die eigenen Ausgabepfade stagen (`git add plans/beutebuch.md tests/`); die Ordner-Whitelist bleibt als zweite Absicherung. Die beiden Linien haben verschiedene Aufgaben — die Whitelist verhindert **fremde Schreibziele**, das datei-genaue Staging verhindert **fremde Mitnahme innerhalb erlaubter Ordner**.

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
