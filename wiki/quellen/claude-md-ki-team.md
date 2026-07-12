---
date: 2026-07-12
type: quelle
tags: [quelle, vorlage, software, claude-md, ki-lehrer, automatisierung, kosten]
status: active
---

# CLAUDE.md T.E.A.M. — KI-Rollenteam unter einem Strippenzieher

**Zusammenfassung**: Eine CLAUDE.md-Vorlage für ein **Team aus KI-Rollen** unter der Regie eines einzelnen menschlichen Senior-Entwicklers. Sechs Rollen — Ralph (Bau-Loop), der Architekt (Planung), Frank (Ad-hoc-Fixer), Harry & Marv (read-only Red Team) und Axel (read-only Forensiker) — arbeiten arbeitsteilig, mit klaren Übergabepunkten über CHANGELOG und Backlog. Abgeleitet aus einem realen Projekt (KI-Lehrer-App) und zur allgemeinen Vorlage verallgemeinert.
**Quellen**: `raw/claude-md-team.md`, `raw/claude-md-team-v2.md` (verfeinerte Fassung, entstanden in Zusammenarbeit mit dem Architekten des KI-Lehrer-App-Projekts, Modell Opus 4.8)
**Zuletzt aktualisiert**: 2026-07-12

---

## Kontext

Diese Quelle ist selbst schon in Wiki-Vorlagenform geschrieben (inkl. Reifegrad-Marken, Vorlagenblock, Aufnahme-Interview) — vergleichbar mit den bereits vorhandenen [claude-md-lehrer](../vorlagen/claude-md-lehrer.md)- und [claude-md-team](../vorlagen/claude-md-team.md)-Vorlagen. Sie verallgemeinert das Rollen-, Prozess- und Automatisierungskonzept aus dem realen [KI-Lehrer-App](../konzepte/ki-lehrer-app.md)-Projekt (dessen `CLAUDE.md`, Planungsdokumente `plans/team-automation-loops.md` und `plans/roadmap-skizzen.md` — dort Punkte R2 und R4 — sowie `ralph.sh`). Diese referenzierten Dateien liegen im Ursprungsprojekt, nicht in diesem Wiki-Repo.

**Namenskonflikt beim Ingest**: Das Wiki hatte bereits eine Vorlage namens `claude-md-team` (Team-Wiki für 2–8 Menschen, siehe [claude-md-team](../vorlagen/claude-md-team.md)) — ein völlig anderes Konzept. Diese neue Quelle beschreibt kein Menschen-Team, sondern ein **KI-Rollenteam unter einem Solo-Menschen**. Um Verwechslung zu vermeiden, wurde die neue Vorlage als `claude-md-ki-team` aufgenommen.

## Muster und Konzepte in dieser Datei

### T.E.A.M. — sechs KI-Rollen unter einem Strippenzieher

„T.E.A.M. = Toll, Ein Anderer Macht's" (mit Augenzwinkern) beschreibt die Grundidee: Der Mensch (**Strippenzieher**) entscheidet Richtung und Priorität, sechs KI-Rollen erledigen die Umsetzung:

| Rolle | Aufgabe | Reifegrad |
|---|---|---|
| **Ralph** | Headless Bau-Loop, arbeitet den aktiven Plan Stufe für Stufe ab | ✅ erprobt |
| **Der Architekt** | Plant Kaskaden, pflegt Roadmap/Backlog, setzt Kosten-Caps | ✅ erprobt |
| **Frank** | Spontane Out-of-Loop-Bugfixes nach festem Dreisatz | ✅ erprobt |
| **Harry** | Read-Only Red Team — Security/Pentest | ✅ erprobt (manuell) |
| **Marv** | Read-Only Red Team — Chaos/Regression/Fuzzing | ✅ erprobt (manuell) |
| **Axel** | Read-Only Forensiker (stärkstes Modell) für Fälle, an denen Frank scheitert | ✅ erprobt (manuell) |

→ Eigene Konzeptseite mit vollständiger Vorlage: [claude-md-ki-team](../vorlagen/claude-md-ki-team.md)

### Finder ≠ Fixer

Die tragende Gewaltenteilung des Musters: Wer einen Fehler findet (Harry, Marv, Axel), fixt ihn **nicht selbst** — das übernimmt ausschließlich Frank. Jede Übergabe bleibt so über eine dokumentierte Status-Kette nachvollziehbar.

→ Eigene Konzeptseite: [finder-fixer-prinzip](../konzepte/finder-fixer-prinzip.md)

### 3-Linien-Guard (Read-Only-Erzwingung)

Für Harry, Marv und Axel muss die Beschränkung auf Test-/Plan-Ordner **technisch** erzwungen werden — Prompt-Vertrauen allein reicht laut Quelle nicht. Drei Verteidigungslinien: Prompt-Anweisung, Tool-Permission-Whitelist, deterministischer Post-Hook (`git diff --name-only` gegen Whitelist, sonst Hard-Reset).

→ Eigene Konzeptseite: [read-only-guard](../konzepte/read-only-guard.md)

### Reifegrad-Marken (✅/🟡)

Die Vorlage bildet bewusst auch unproduktive Teile ab und kennzeichnet sie ehrlich: ✅ **erprobt** (im Ursprungsprojekt produktiv gelaufen) vs. 🟡 **Zielbild** (Konzept vollständig, aber noch nicht battletested). Konkret ✅: Ralph-Loop, manuell angestoßene Rollen, alle Dreisätze, Status-Ketten, Auth `api|abo`. **Update 2026-07-11:** Die zunächst als 🟡 geführte **Voll-Automatik** (Rollen als Hintergrund-Loops, Abo-Default mit API-Fallback) ist inzwischen **✅ erprobt** — im Feldprojekt `website-maxron-de` scharf gelaufen (reale Red-Team-Funde, Frank-Fixes, wirksamer Read-Only-Guard). Aus Kaskade 6 kamen Budget-Governance-Bausteine und drei Betriebslehren hinzu (Budget-Cap-Timing, kaskaden-spezifischer Red-Team-Fokus, „success ohne Promise"-Behandlung). **Kaskade 7 + Folge-Fixes** haben zwei davon von der bloßen Idee zum **gebauten, erprobten Baustein** gemacht (der kaskaden-abhängige Red-Team-Fokus als Env `TEAM_REDTEAM_FOCUS`; die „success ohne Promise"-Behandlung als Prompt- **und** Logik-Härtung) und **zwei neue Betriebslehren** ergänzt: **Log-Rotation gegen Doppelzählung** der Budget-Ledger und die **A/B-Trennung** von Pro-Lauf-Kosten (Durchsetzung) vs. Gesamt-Kontostand (nur Anzeige). Dazu eine **Guard-Lektion**: datei-genaues Staging statt ordner-weiter Whitelist, weil der interaktive Architekt außerhalb des `flock` gleichzeitig uncommittete Dateien unter derselben Whitelist liegen haben kann (siehe Vorlage, Anhang A.4/A.7 und [read-only-guard](../konzepte/read-only-guard.md)).

**Update 2026-07-12 (Kaskaden 9–15, HM-1…HM-32):** Fünf weitere Feld-Erkenntnisse sind in die Vorlage zurückgeflossen. **(1) Session-Limit-Robustheit (429), Strategie A+B** (`BL-20`/`BL-25`): ein Session-Limit ist eine eigene **dritte Fehlerklasse** — API-Fallback zuerst (separates Kontingent), dann Auto-Retry mit Deckel, sonst sauberer **Exit-42-Pausen-Exit**, den alle Rollen durchreichen (kein State-Fortschritt, kein Datenverlust); dazu die **Auslauf-Bremse** `TEAM_FIX_MAX_STAGNATION` gegen teures Leerdrehen am selben Fund (realer Auslöser: ~13,8 USD Leerlauf). **(2) Aktive Auth-Startwarnung** (`BL-27`): `team_resolve_auth_mode()` warnt einmal pro Prozessbaum, wenn im Abo-Modus ein `ANTHROPIC_API_KEY` in der Env liegt und das Abo-first still aushebelt. **(3) Zwei-Schwellen-Budgetmodell** (`BL-30`): ein zentraler Soft-Cap (Hinweis) + Hard-Cap (Abbruch) statt divergierender Pro-Rolle-Defaults — Kernlehre HM-32: ein zu tiefer Pro-Fall-Cap wirft teure, aber plausible Fixes per Rollback weg und **vervielfacht** die Kosten. **(4) Automatische & domänengetrennte Kostenerfassung interaktiver Rollen** (`BL-28`/`BL-29`/`BL-33`): Architekt und Frank-im-Abo laufen außerhalb `team_claude` und waren strukturell unerfasst (real ~16 USD/Architekt-Session) — jetzt A2-Live-Schätzung (Churn) + rollen-agnostischer A1-Abschluss aus der Konsole, mit rückwärtskompatiblem Ledger-Schema (`domaene`/`rolle`). **(5) Scharfschalt-Sequenz-Pflicht** (Planungsregel 4). Damit deckt die Vorlage den Feldstand über fünfzehn Kaskaden ab (Anhang A.7–A.9).

Dieses Prinzip — Spec ist Wahrheit vor Annahmen, unfertige Teile werden nicht als produktiv ausgegeben — ist eine direkte Anwendung dessen, was in diesem Wiki bereits an anderer Stelle als Grundhaltung gilt (Faktencheck vor Annahme, siehe Anhang A der Quelle).

### Dreisätze und Status-Ketten

Drei parallele Dreisatz-Prozesse strukturieren die Übergaben:

- **Franks Dreisatz**: Code-Fix committen → CHANGELOG-Eintrag (`[Unreleased]`) → Backlog-Status aktualisieren
- **Der Beutezug-Dreisatz** (Harry/Marv): Fund ins Beutebuch → optionaler Reproducer-Test → Übergabe an Frank
- **Der Ermittlungs-Dreisatz** (Axel): Ermittlungsakte mit Root-Cause und Fix-Plan → Status zurück an Frank → Frank setzt um

Status-Kette: `offen → an Frank übergeben → an Axel übergeben → Fix-Plan liegt vor → erledigt (Frank-Fix, …) → an Mensch eskaliert`.

### Kaskaden-Planungsregeln

Vierstufig: (1) neue Themen zunächst als lose Skizze ohne Stufennummern, (2) Aushärtung erst wenn ein Strang zur nächsten Kaskade freigegeben wird, (3) Nummerierung erst bei der Aushärtung — verhindert, dass ferne Plan-Nummern später verschoben werden müssen, (4) **Scharfschalt-Sequenz als Pflicht-Ausgabe** (Feld-Lehre 2026-07-11): der Architekt gibt am Ende jeder Aushärtung immer automatisch die fertige, aus dem Plankopf abgeleitete Startsequenz aus (Zeiger umlegen → Konsistenz-Check → Budget → ggf. Red-Team-Fokus → Start), damit der Strippenzieher sie nie selbst zusammensuchen muss und der stille Fehlstart „veralteter Zeiger" ausbleibt.

### Anhang A — Team-Skripte kontextabhängig generieren

Statt fertige Skripte zu kopieren, generiert die einrichtende KI-Instanz `team-lib.sh`, den Loop, `frank.sh`, den Read-Only-Guard und `harry.sh`/`marv.sh` **angepasst an die real installierte CLI** des Zielprojekts — inklusive Vorbedingungs-Check (Git-Repo? Claude-CLI? existierender Loop? Commit-Konvention?) und expliziter Faktencheck-Pflicht statt Annahme.

## Verfeinerung (v2)

Eine zweite Fassung (`raw/claude-md-team-v2.md`) wurde in Zusammenarbeit mit dem Architekten des KI-Lehrer-App-Projekts (Modell Opus 4.8) nachgereicht und in die Vorlage übernommen. Änderungen gegenüber v1:

- **Kostenkontrolle grundlegend überarbeitet**: Token-Sparregeln sind jetzt **optional, projektabhängig, Default AUS** — nicht mehr „stets aktiv" wie im generischen Kostencounter-Standard. Begründung: Axel (der Forensiker) braucht für Root-Cause-Analysen tiefes, ungekürztes Lesen; Sparregeln würden seine Arbeitsqualität beschneiden. Neue „Grundregeln (stets aktiv)" trennen das Kosten-Cap (immer Pflicht) von den Lese-Sparmaßnahmen (optional). Das ist eine bewusste, begründete Abweichung vom generischen [kostencounter](../konzepte/kostencounter.md)-Standard für diese eine Vorlage.
- **Designhinweis 8 neu**: Klarstellung, dass der `## Kostenkontrolle`-Block bewusst *innerhalb* des kopierbaren Vorlagenblocks steht (wird mit in die Ziel-CLAUDE.md übernommen), während Designhinweise, Anhang A und „Verwandte Seiten" reine Wiki-Meta bleiben.
- **Smoke-Test-Hinweis präzisiert**: `py_compile`/`ast.parse` ist Python-spezifisch — Hinweis ergänzt, im Zielprojekt auf die reale Sprache/Toolchain anzupassen.
- **R4-Referenz entfernt**: „Auth-Fallback (R4-Zielbild)" → „Auth-Fallback (Zielbild)" — der Rückbezug auf die Roadmap-Nummerierung des Ursprungsprojekts (nicht Teil dieses Wikis) wurde entfernt.

## Bewertung

Die Quelle ist ungewöhnlich für dieses Wiki, da sie selbst bereits im Zielformat (Wiki-Vorlagenseite mit Reifegrad-Marken, Vorlagenblock, Aufnahme-Interview) vorliegt — der Ingest-Aufwand bestand primär darin, Frontmatter zu vereinheitlichen, projektinterne Links (die auf Dateien im Ursprungsprojekt zeigen, nicht auf dieses Wiki-Repo) zu entschärfen und den Namenskonflikt mit der bestehenden `claude-md-team`-Vorlage aufzulösen.

## Verwandte Seiten

- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) — Die vollständige Vorlage
- [ki-lehrer-app](../konzepte/ki-lehrer-app.md) — Das reale Ursprungsprojekt
- [finder-fixer-prinzip](../konzepte/finder-fixer-prinzip.md) — Die Gewaltenteilungs-Regel
- [read-only-guard](../konzepte/read-only-guard.md) — Die technische Durchsetzung von Read-Only-Rollen
- [ralph-schleife](../konzepte/ralph-schleife.md) — Das Loop-Muster, das Ralph in dieser Vorlage nutzt
- [claude-md-team](../vorlagen/claude-md-team.md) — Namensvetter mit anderem Thema (Menschen-Team statt KI-Rollenteam)
- [kostencounter](../konzepte/kostencounter.md) — Kostenkontroll-Standard, in die Vorlage übernommen

---

[Wiki-Index](../index.md)
