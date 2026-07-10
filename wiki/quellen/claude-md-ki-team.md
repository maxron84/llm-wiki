---
date: 2026-07-10
type: quelle
tags: [quelle, vorlage, software, claude-md, ki-lehrer, automatisierung]
status: active
---

# CLAUDE.md T.E.A.M. — KI-Rollenteam unter einem Strippenzieher

**Zusammenfassung**: Eine CLAUDE.md-Vorlage für ein **Team aus KI-Rollen** unter der Regie eines einzelnen menschlichen Senior-Entwicklers. Sechs Rollen — Ralph (Bau-Loop), der Architekt (Planung), Frank (Ad-hoc-Fixer), Harry & Marv (read-only Red Team) und Axel (read-only Forensiker) — arbeiten arbeitsteilig, mit klaren Übergabepunkten über CHANGELOG und Backlog. Abgeleitet aus einem realen Projekt (KI-Lehrer-App) und zur allgemeinen Vorlage verallgemeinert.
**Quellen**: `raw/claude-md-team.md`
**Zuletzt aktualisiert**: 2026-07-10

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

Die Vorlage bildet bewusst auch unproduktive Teile ab und kennzeichnet sie ehrlich: ✅ **erprobt** (im Ursprungsprojekt produktiv gelaufen) vs. 🟡 **Zielbild** (Konzept vollständig, aber noch nicht battletested). Konkret ✅: Ralph-Loop, manuell angestoßene Rollen, alle Dreisätze, Status-Ketten, Auth `api|abo`. Konkret 🟡: Voll-Automatik der Rollen als Hintergrund-Loops, Abo-Default mit API-Fallback.

Dieses Prinzip — Spec ist Wahrheit vor Annahmen, unfertige Teile werden nicht als produktiv ausgegeben — ist eine direkte Anwendung dessen, was in diesem Wiki bereits an anderer Stelle als Grundhaltung gilt (Faktencheck vor Annahme, siehe Anhang A der Quelle).

### Dreisätze und Status-Ketten

Drei parallele Dreisatz-Prozesse strukturieren die Übergaben:

- **Franks Dreisatz**: Code-Fix committen → CHANGELOG-Eintrag (`[Unreleased]`) → Backlog-Status aktualisieren
- **Der Beutezug-Dreisatz** (Harry/Marv): Fund ins Beutebuch → optionaler Reproducer-Test → Übergabe an Frank
- **Der Ermittlungs-Dreisatz** (Axel): Ermittlungsakte mit Root-Cause und Fix-Plan → Status zurück an Frank → Frank setzt um

Status-Kette: `offen → an Frank übergeben → an Axel übergeben → Fix-Plan liegt vor → erledigt (Frank-Fix, …) → an Mensch eskaliert`.

### Kaskaden-Planungsregeln

Dreistufig: (1) neue Themen zunächst als lose Skizze ohne Stufennummern, (2) Aushärtung erst wenn ein Strang zur nächsten Kaskade freigegeben wird, (3) Nummerierung erst bei der Aushärtung — verhindert, dass ferne Plan-Nummern später verschoben werden müssen.

### Anhang A — Team-Skripte kontextabhängig generieren

Statt fertige Skripte zu kopieren, generiert die einrichtende KI-Instanz `team-lib.sh`, den Loop, `frank.sh`, den Read-Only-Guard und `harry.sh`/`marv.sh` **angepasst an die real installierte CLI** des Zielprojekts — inklusive Vorbedingungs-Check (Git-Repo? Claude-CLI? existierender Loop? Commit-Konvention?) und expliziter Faktencheck-Pflicht statt Annahme.

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
