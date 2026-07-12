---
date: 2026-07-12
type: konzept
tags: [konzept, forensik, automatisierung, enterprise, schema]
status: draft
---

# T.E.A.M.-Forensik (Konzeptskizze)

**Zusammenfassung**: Konzeptskizze für die Anpassung der ausgehärteten [T.E.A.M.-Vorlage](../vorlagen/claude-md-ki-team.md) an die forensische Legacy-Analyse: vier übernommene Rollen (Architekt, Ralph, Auditor, Axel) plus ein neuer, nur hier eingesetzter Spezialist — der **Lotse**. 🟡 **Zielbild ohne Feldlauf** — Kandidat ist eine reale Enterprise-Produktionsumgebung.
**Quellen**: [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) (Rollen, Infrastruktur, Betriebslehren), [claude-md-legacy-forensik](../vorlagen/claude-md-legacy-forensik.md) (Methodik, Konfidenzmodell), Brainstorming-Session 2026-07-12 (Strippenzieher-Entscheide: Konzeptskizze statt Vollvorlage, Quartett ohne Frank, Lotse als Spezialist)
**Zuletzt aktualisiert**: 2026-07-12

---

> **Status: Skizze, bewusst weich.** Nach Kaskaden-Planungsregel 1 der
> T.E.A.M.-Vorlage: Ziel, Umfang und Rollen sind umrissen — **ohne** feste
> Stufen-Nummern, ohne Caps, ohne generierte Skripte. Ausgehärtet wird erst,
> wenn der Enterprise-Feldlauf startet. Alles hier ist 🟡, nichts ✅.

## Anlass

Die [T.E.A.M.-Vorlage](../vorlagen/claude-md-ki-team.md) ist nach fünfzehn scharfen Kaskaden im Feldprojekt website-maxron-de ausgehärtet (Quelle: claude-md-ki-team.md). Gleichzeitig existiert die [Legacy-Forensik-Vorlage](../vorlagen/claude-md-legacy-forensik.md) mit Konfidenzmodell und Veteran-Erweiterungen — aber ohne Rollenteam und ohne Automatisierung. Diese Skizze verbindet beides für einen konkreten Kandidaten: eine **sehr große Produktionsumgebung auf Enterprise-Niveau** (Strippenzieher-Angabe, 2026-07-12).

## Die Kern-Inversion

Im Bau-T.E.A.M. ist der Produktivcode das Produkt; in der Forensik ist er unantastbare **Quelle** und das **Wiki ist das Produkt**. Daraus folgt:

- **`{{Produktivcode-Globs}}` → `raw/**` + `clippings/**`** — tabu für **alle** Rollen, nicht nur fürs Red Team. Der [3-Linien-Guard](read-only-guard.md) schützt nicht mehr das Produkt vor dem Angreifer, sondern die Quellen vor Kontamination — und wird simpler, weil es keine Rolle mit Schreibrecht auf die Quellen gibt.
- **Smoke-Test → `wiki_lint.py`** — statt `py_compile` prüft der Loop nach jedem Commit das Wiki auf tote Links, Waisen und Format-Fehler. Die Verifikations-Infrastruktur existiert bereits (`~/.claude/scripts/wiki_lint.py`).
- **Whitelist pro Rolle statt einer Front**: Ralph schreibt Befundseiten, der Auditor nur sein Einwandsbuch, Axel nur Ermittlungsakten, der Architekt nur Pläne, der Lotse nur Leitfäden und `offene-fragen/`.

## Der Kader: Quartett + Lotse

| Rolle | Herkunft | Forensik-Aufgabe |
|-------|----------|------------------|
| **Der Architekt** | 1:1 übernommen | **Untersuchungsleiter**: plant Analyse-Kaskaden (welches Modul zuerst, welche Tiefe), setzt Caps, pflegt das Backlog. Skizze → Aushärtung on demand gilt unverändert. |
| **Ralph** | Umgewidmet | **Ingest-/Analyse-Loop**: arbeitet eine Modul-Liste bzw. `inbox/` ab, ein Commit pro analysiertem Modul, Promise `MODUL_N_COMPLETE`. Quasi [wiki-ralph-sh](../werkzeuge/wiki-ralph-sh.md) mit T.E.A.M.-Robustheit (429, Budget, Guard). Arbeitet **auch Auditor-Einwände ab** (siehe „Warum kein Frank"). |
| **Der Auditor** | Harry ⊕ Marv verschmolzen | **Konfidenz-Auditor + Widerspruchs-Jäger**: greift die *Befunde* an, nicht die App. Spot-Checking gegen Rohquellen („Ist dieses 🟢 wirklich gedeckt?"), Code↔Doku-Widersprüche, veraltete 🔴-Befunde, PII-/Secret-Lecks im Wiki. Read-only bis auf sein **Einwandsbuch** (`AU-<Nr>`: Befund, angezweifeltes Niveau, Gegenbeleg). |
| **Axel** | 1:1 übernommen | **Forensiker im Forensik-Team** — knackt die harten Rätsel: „Warum wird dieser tot aussehende Code doch aufgerufen?", archäologische Schichten, unauflösbare Widersprüche. Starkes Modell, Cap pro Fall, nie im Dauer-Loop. Ermittlungsakte = Befundseite mit Root-Cause. |
| **Der Lotse** 🆕 | Nur hier | Siehe unten — die einzige Rolle, die **Fragen an Menschen** produziert. |

**Entfallen:** Frank (Begründung unten) und die Harry/Marv-Trennung — die Security/Chaos-Aufteilung trägt bei Wiki-Befunden zu wenig, um zwei Loops zu rechtfertigen.

## Warum kein Frank

Das [Finder-Fixer-Prinzip](finder-fixer-prinzip.md) bleibt vollständig erhalten, braucht aber keinen eigenen Fixer: **Der Auditor findet, Ralph fixt.** Auditor-Einwände landen als Backlog-Einträge und werden von Ralph als normale Loop-Aufgaben abgearbeitet — Wiki-Edits sind ohnehin Ralphs Medium, ein separater Korrektor wäre redundant. Die Status-Kette bleibt wörtlich die des Bau-T.E.A.M.s, nur mit Ralph statt Frank:

`offen → an Ralph übergeben → an Axel übergeben → Fix-Plan liegt vor → erledigt (Ralph, <commit>) → an Lotse übergeben`

Der letzte Status ist neu — siehe nächster Abschnitt.

## Der Lotse (neuer Spezialist)

Der Lotse ist die **strukturierte Mensch-Schnittstelle** — das, was im Bau-T.E.A.M. der vage Endstatus `an Mensch eskaliert` war, wird hier eine eigene Rolle mit eigenem Handwerk:

1. **Pflegt `wiki/offene-fragen/`** als kuratierten Fragenkatalog: Was können Code und Doku prinzipiell nicht beantworten? (Fachliche Absichten, historische Entscheidungen, ungeschriebene Betriebsregeln.)
2. **Erstellt Interview-Leitfäden pro Wissensträger**: gruppiert offene Fragen nach Person/Team (aus der Wissensträger-Analyse der Forensik-Vorlage), formuliert sie interviewtauglich, priorisiert nach Untersuchungsziel.
3. **Schließt den Kreis**: Der Mensch führt das Interview, legt die Gesprächsnotiz in `raw/interviews/` ab — und Ralph nimmt sie als neue Quelle auf. Beantwortete Fragen werden im Katalog quittiert.

**Eskalations-Endpunkt:** Was selbst Axel nicht klären kann, endet nicht als Sackgasse, sondern wird vom Lotsen in eine präzise Interviewfrage übersetzt (`an Lotse übergeben`). Unauflösbare Widersprüche sind in der Forensik kein Scheitern, sondern **Interview-Material**.

**Betrieb:** Kein Dauer-Loop nötig — der Lotse läuft auf Abruf oder am Kaskaden-Ende (wenn frische Befunde und Einwände vorliegen). Günstiges Modell reicht vermutlich; das Destillieren von Fragen ist keine Root-Cause-Analyse. (⚪ Hypothese — im Feldlauf prüfen.)

**Namens-Kür:** Analog zu den Film-Anspielungen des Bau-T.E.A.M.s böte sich **Columbo** an — der Mann, der Fälle löst, indem er Menschen „nur noch eine Frage" stellt. Wie immer gilt Designhinweis 7: Namen sind Kür, das Prozess-Skelett ist Pflicht.

## Was 1:1 übernommen wird

Die gesamte gehärtete Infrastruktur der T.E.A.M.-Vorlage ist domänen-agnostisch und wandert unverändert mit (Quelle: claude-md-ki-team.md, Anhang A):

- `team-lib.sh`-Muster: Abo-first + API-Fallback, 429/Exit-42-Pausenmechanik, Stagnations-Bremse, `flock`
- Zwei-Schwellen-Budget (Soft-/Hard-Cap, `BL-30`), Ledger + Log-Rotation, Akteur-Kostenerfassung (`BL-28`/`BL-29`/`BL-33`)
- Scharfschalt-Sequenz-Pflicht des Architekten, Zeiger-Datei statt Skript-Edit, `TEAM_REDTEAM_FOCUS` (hier: Auditor-Fokus)
- Kostenmetrik: „Kosten pro analysiertem Modul" steht bereits in der Forensik-Vorlage; [usd-pro-wiki-seite](usd-pro-wiki-seite.md) liefert die Benchmark-Größenordnung

## Neue Design-Regeln dieser Variante

1. **Konfidenz-Deckel für die Automatik**: Vollautomatisch erzeugte Befunde starten **nie höher als 🟡**. Ein 🟢 vergibt nur der Mensch oder ein bestandener Auditor-Spot-Check. Das entschärft den Kernkonflikt zwischen autonomem Ingest und dem [Kontaminierungsrisiko](kontaminierungsrisiko.md) — je mehr Ralph unbeaufsichtigt einliest, desto wichtiger die adversariale Schicht.
2. **Halbautomatik als Default**: Die Forensik-Vorlage verlangt „Bespreche Befunde, bevor du schreibst" — der headless Loop hebelt das aus. Default ist daher `halbautomatik.sh` (Quittung pro Modul); Vollautomatik nur für risikoarme Massen-Ingests (z. B. Ticket-Exporte), abgesichert durch den Konfidenz-Deckel.
3. **Token-Sparregeln zurückhaltend**: Die harten Sparquoten der Veteran-Quelle (20 Zeilen, einmal lesen) widersprechen dem Kernjob — tiefes Lesen. Billige falsche Befunde sind teurer als teure richtige. Sparregeln nur bei Budget-Zwang, Axel und Auditor grundsätzlich ausgenommen.
4. **DSGVO als vierte Guard-Linie**: Statt einer eigenen Zensor-Rolle ein deterministischer Scan (Skript) im Post-Hook: kein Commit, wenn PII-/Secret-Muster aus `raw/` im Wiki-Diff auftauchen. Der Auditor prüft zusätzlich semantisch (was Regex nicht sieht). Im Enterprise-Umfeld Pflicht (Quelle: claude-md-legacy-forensik-veteran.md).

## Offene Fragen (vor der Aushärtung zu klären)

- **Umgebung des Kandidaten**: Windows/PowerShell (wie die Veteran-Quelle) oder Linux? Bestimmt, ob die `*.sh`-Bausteine direkt tragen oder ein PowerShell-Port nötig ist.
- **Skalierung**: Bei Enterprise-Größe droht das [index.md-Bottleneck](skalierungsgrenzen.md) früh — [qmd](../werkzeuge/qmd.md)/[jdocmunch](../werkzeuge/jdocmunch.md) von Anfang an einplanen oder erst ab ~100 Einträgen?
- **Einwandsbuch-Format**: Eigenes Präfix `AU-<Nr>` oder das `HM-`-Schema weiterführen (Vergleichbarkeit mit dem Bau-T.E.A.M.)?
- **Lotsen-Modell**: Reicht wirklich das günstige Modell, oder braucht gutes Fragen-Destillieren die starke Klasse? (⚪ — Feldlauf entscheidet.)
- **Zugriffsweg auf die Produktionsumgebung**: Snapshot in `raw/code/` (Forensik-Standard) oder Live-Lesezugriff? Snapshot ist forensisch sauberer (unveränderlicher Beweisstand).

## Verwandte Seiten

- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) — Mutter-Vorlage: Rollen, Dreisätze, gehärtete Infrastruktur
- [claude-md-legacy-forensik](../vorlagen/claude-md-legacy-forensik.md) — Mutter-Vorlage: Methodik, Konfidenzmodell, Veteran-Erweiterungen
- [finder-fixer-prinzip](finder-fixer-prinzip.md) — bleibt erhalten: Auditor findet, Ralph fixt
- [read-only-guard](read-only-guard.md) — wird hier zum universellen Quellen-Schutz
- [kontaminierungsrisiko](kontaminierungsrisiko.md) — der stärkste Grund für den Auditor
- [ralph-schleife](ralph-schleife.md) — das Loop-Muster hinter dem Ingest-Ralph
- [wiki-ralph-sh](../werkzeuge/wiki-ralph-sh.md) — existierender Ingest-Loop als Ausgangspunkt
- [skalierungsgrenzen](skalierungsgrenzen.md) — Enterprise-Größe trifft das Index-Bottleneck früh
- [enterprise-skalierung](enterprise-skalierung.md) — warum persönliche Muster bei Unternehmen brechen

---

[Wiki-Index](../index.md)
