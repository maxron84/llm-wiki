---
date: 2026-07-10
type: vorlage
tags: [vorlage, schema, softwareprojekt, automatisierung]
status: active
---

# CLAUDE.md-Vorlage: T.E.A.M. (KI-Rollenteam)

**Zusammenfassung**: Eine wiederverwendbare CLAUDE.md-Vorlage für ein **Team aus KI-Rollen** unter der Regie eines menschlichen Senior-Entwicklers (des „Strippenziehers"). Das Muster stammt aus einem realen Projekt ([KI-Lehrer-App](../konzepte/ki-lehrer-app.md)) und verallgemeinert dessen Rollen-, Prozess- und Automatisierungs-Konzept: ein autonomer Bau-Loop (Ralph), ein planender Architekt, ein Ad-hoc-Fixer (Frank), ein read-only Red Team (Harry & Marv) und ein read-only Forensiker (Axel). Die Vorlage liefert **keine fertigen Skripte**, sondern eine **Bau-Anleitung**, mit der die aufnehmende KI-Instanz die Team-Skripte **kontextabhängig zum Zielprojekt** generiert.
**Quellen**: Abgeleitet aus `CLAUDE.md`, `plans/team-automation-loops.md`, `plans/roadmap-skizzen.md` (R2, R4) und `ralph.sh` des [KI-Lehrer-App](../konzepte/ki-lehrer-app.md)-Ursprungsprojekts (Dateien liegen dort, nicht in diesem Wiki-Repo); Feldtest der Auth-Mechanik (Abo-Prio-1 + stufen-lokaler API-Fallback): Projekt `website-maxron-de` (2026-07-10, dort `team-lib.sh`/`ralph.sh`); Format-Vorbild: [claude-md-lehrer](claude-md-lehrer.md).
**Zuletzt aktualisiert**: 2026-07-10

---

> **Namenshinweis**: Diese Vorlage heißt bewusst `claude-md-ki-team`, nicht `claude-md-team` — letzterer Name ist bereits von der [Team-Wiki-Vorlage](claude-md-team.md) belegt (2–8 **Menschen** teilen sich ein Wiki, ein anderes Thema als hier). Diese Vorlage beschreibt ein **KI-Rollenteam unter einem Solo-Menschen**.

## Wann diese Vorlage passt

- Ein **Software-Projekt** soll mit **mehreren KI-Rollen** vorangetrieben werden, koordiniert von **einem Menschen**, der Richtung und Prioritäten vorgibt, die Umsetzung aber delegiert.
- Es gibt (oder soll geben) einen **autonomen Entwicklungs-Loop** (headless `claude -p` o. Ä.), der eine Plan-Liste Stufe für Stufe abarbeitet — vergleiche [ralph-schleife](../konzepte/ralph-schleife.md).
- Man will eine saubere **Gewaltenteilung**: Wer plant, wer baut, wer angreift, wer fixt, wer die harten Fälle knackt — mit **klaren Übergabepunkten**, damit nichts verloren geht oder doppelt gemacht wird.
- Das Projekt nutzt **Git** und hält Historie/Übergaben in Dateien fest (CHANGELOG, Backlog, Plan-Dokumente).

## Was diese Vorlage nicht ist

- **Keine pädagogische Bedienmodell-Vorlage.** Für KI-gestützten Unterricht siehe die Schwester-Vorlagen [claude-md-lehrer](claude-md-lehrer.md), [claude-md-nachhilfe](claude-md-nachhilfe.md), [claude-md-laienlehrer](claude-md-laienlehrer.md).
- **Kein fertiges Skript-Bundle.** Die Team-Skripte (`ralph.sh`, `team-lib.sh`, `harry.sh`/`marv.sh`/`frank.sh`) werden **nicht mitgeliefert**, sondern nach Anhang A **kontextabhängig generiert** — angepasst an die real installierte CLI und die Repo-Konventionen des Zielprojekts.
- **Kein Ersatz für den Menschen.** Der Strippenzieher zieht die Fäden; die Vorlage strukturiert nur, wie die KI-Rollen ihm zuarbeiten.
- **Kein Menschen-Team-Wiki.** Für 2–8 Menschen, die sich ein Wiki teilen, siehe [claude-md-team](claude-md-team.md).

## Reifegrad-Legende

Die Vorlage bildet das **komplette Zielbild** ab — auch Teile, die im Ursprungsprojekt noch **nicht produktiv erprobt** sind. Jeder Abschnitt trägt daher eine ehrliche Marke:

| Marke | Bedeutung |
|-------|-----------|
| ✅ **erprobt** | Im Ursprungsprojekt produktiv gelaufen. Direkt übernehmbar. |
| 🟡 **Zielbild** | Konzept vollständig, aber **noch nicht battletested**. Vor produktivem Einsatz an der realen Umgebung verifizieren. |

Konkret: **✅** Ralph-Loop, manuell angestoßene Rollen (Frank/Harry/Marv/Axel), alle Dreisätze, Status-Ketten, Auth `api|abo`, **Abo-first-mit-API-Fallback für alle automatisierten Rollen** und die **Voll-Automatik** (Orchestrator-Wache + `redteam.sh`/`frank.sh`/`axel.sh` + chirurgischer 3-Linien-Guard + `flock` + Beutebuch-Zustandsmaschine + Monitoring) — Mechanik im Zweitprojekt website-maxron-de am 2026-07-10 implementiert und ohne LLM-Aufrufe getestet. **🟡** Erster echter Ende-zu-Ende-`wache.sh`-Lauf (Feldtest der `--allowedTools`-Wirksamkeit, A.5) steht noch aus.

## Benutzung

1. Kopiere **nur den Inhalt** des Vorlagenblocks unten (ohne den 4-Backtick-Rahmen) als `CLAUDE.md` ins Wurzelverzeichnis des Zielprojekts — oder füge ihn als eigenständige **„Das Team"-Sektion** in eine vorhandene `CLAUDE.md` ein.
2. Führe das **Aufnahme-Interview** (im Vorlagenblock enthalten) mit dem Strippenzieher und ersetze alle `{{PLATZHALTER}}` mit den Antworten.
3. Arbeite **Anhang A** ab: Vorbedingungen prüfen, dann die Team-Skripte **kontextabhängig generieren** (nicht blind kopieren).
4. Setze die Reifegrad-Marken so, dass klar bleibt, was im **Zielprojekt** bereits läuft und was noch Zielbild ist.

## Designhinweise

1. **Fence entfernen**: Beim Kopieren den 4-Backtick-Wrapper des `## Vorlage`-Blocks entfernen.
2. **Finder ≠ Fixer**: Das ist keine Empfehlung, sondern die tragende Gewaltenteilung. Wer einen Fehler findet (Harry/Marv/Axel), fixt ihn **nicht** selbst — das macht Frank. So bleibt jede Übergabe nachvollziehbar. → [finder-fixer-prinzip](../konzepte/finder-fixer-prinzip.md)
3. **Read-Only-Regel ist eisern**: Für das Red Team (Harry/Marv) und den Forensiker (Axel) muss die Beschränkung auf Test-/Plan-Ordner **technisch erzwungen** werden (3-Linien-Guard, Anhang A) — Prompt-Vertrauen allein genügt nicht. → [read-only-guard](../konzepte/read-only-guard.md)
4. **Kosten-Caps sind Pflicht**: Das starke/teure Modell (Axel, Architekt) läuft **nie im Dauer-Loop**; jede Iteration/jeder Fall bekommt ein Budget-Cap. → siehe auch [kostencounter](../konzepte/kostencounter.md)
5. **Reifegrad ehrlich lassen**: 🟡-Teile nicht als produktiv ausgeben — sonst verliert die Vorlage ihren Wert („Spec ist Wahrheit vor Annahmen").
6. **Skripte kontextabhängig generieren**: An die real installierte CLI und die Repo-Konvention anpassen (Faktencheck-Pflicht, Anhang A) — nicht raten.
7. **Namen sind austauschbar**: Die augenzwinkernden Anspielungen (Matrix, *Kevin allein zu Haus*, *Beverly Hills Cop*) sind Kür. Das **Prozess-Skelett** (Rollen, Dreisätze, Status-Ketten) ist Pflicht.
8. **Kostenkontroll-Block ist Teil des kopierbaren Blocks**: Der Abschnitt `## Kostenkontrolle` steht bewusst **innerhalb** des `## Vorlage`-Fences — er wird also in die Ziel-CLAUDE.md mitkopiert. Nur die Wiki-Meta (diese Designhinweise, Anhang A, „Verwandte Seiten") bleibt außerhalb.

## Vorlage

````markdown
# CLAUDE.md — {{Projektname}}

> **Anleitung**: Dieser Block definiert das **T.E.A.M.** (die KI-Rollen) des
> Projekts. Ersetze alle `{{PLATZHALTER}}` über das Aufnahme-Interview unten.
> Entferne diesen Anleitungs-Block vor der produktiven Nutzung.

---

## Das Team (Rollen)

Am Projekt arbeiten sechs KI-Rollen — unter der Regie **eines menschlichen
Senior-Entwicklers** (des **{{Strippenzieher}}**). Jede Instanz sollte wissen,
**welche Rolle sie gerade ausfüllt** — das bestimmt Aufgabe, Befugnis und Prozess.

> **T.E.A.M. = „Toll, Ein Anderer Macht's"** — und zwar mit voller Absicht. 😎
> Gemeint ist: **Der Mensch ({{Strippenzieher}}) delegiert seine Arbeit ans
> T.E.A.M.** — Ralph baut, der Architekt plant, Frank fixt, Harry & Marv brechen
> ein, und Axel klärt die harten Fälle auf. Der {{Strippenzieher}} zieht die Fäden,
> entscheidet Richtung und Prioritäten; die Umsetzung machen die KI-Rollen. Damit
> das reibungslos klappt, hat jede Rolle einen sauber definierten Übergabepunkt
> (der `[Unreleased]`-Block + Backlog), sodass zwischen ihnen nichts verloren geht
> oder doppelt gemacht wird.

> **T.E.A.M. international** — für Projekte auf Englisch oder Italienisch bleiben
> die Initialen **T-E-A-M** zwingend erhalten, ebenso die selbstironische Pointe
> („die Arbeit macht — mit voller Absicht — ein anderer"):
> - 🇬🇧 **Thankfully, Everyone (but me) Achieves More** — dreht das bekannte
>   Motivationsposter „Together Everyone Achieves More" ironisch um.
> - 🇮🇹 **Tanto, Ecco, Altri (lo fanno)… Ma certo!** — „Ach, sieh an, andere
>   machen's… aber sicher!"; das achselzuckende *„Tanto…"* spiegelt das ironische
>   deutsche „Toll".

| Rolle | Wer/Was | Aufgabe | Kern-Prinzip |
|-------|---------|---------|--------------|
| **Ralph** | Der headless Bau-Loop ([`{{loop-skript}}`]({{loop-skript}})) | Arbeitet den **aktiven Plan** Stufe für Stufe ab, ein Commit pro Stufe (`{{feat-präfix}}(stufeN): …`). | Nimmt **keine** Features aus späteren Stufen vorweg. Liest vor jeder Stufe den `[Unreleased]`-Block und baut dort gelistete Fixes **nicht erneut**. ✅ erprobt |
| **Der Architekt** | Planungs-Instanz (interaktiv, Architect-Mode) | **Plant Kaskaden**, schreibt Plan-Dokumente unter [`{{Plan-Ordner}}`]({{Plan-Ordner}}), pflegt Roadmap/Backlog, setzt Caps. Trifft die Struktur-Entscheidungen, die Ralph ausführt. **Committet Plan-/Doku-Änderungen selbständig** (`docs(plan): …`). | Spec ist Wahrheit vor Annahmen. **Pflicht vor jedem Entwurf:** `[Unreleased]` + `Frank-Fix`-Zeilen abgleichen. **Läuft im `{{starkes-modell}}`/API-Modus** — kein Abo-Default. Greift **normalerweise nicht** selbst in Produktivcode ein. ✅ erprobt |
| **Frank der Fixer** | Spontane Out-of-Loop-Bugfixes | Behebt **akut auffallende** Bugs/UX-Reibungen **außerhalb** des Loops, ohne auf die nächste Kaskade zu warten. | Jeder Fix folgt dem **Dreisatz** (Commit `{{fix-präfix}}: …` → CHANGELOG → Backlog). Der **Normalweg** für Ad-hoc-Fixes. ✅ erprobt |
| **Harry** | Read-Only Red Team — **Security/Pentest** | Versucht die App **bewusst auszuhebeln**: Auth/PINs/Tokens umgehen, Angriffsfläche der Netz-Schnittstellen, Pfad-/Injection-Tricks, Datenlecks in Logs/Exports. | **Rührt keinen Produktivcode an.** Nur lesen, angreifen, dokumentieren — dann Übergabe an Frank. ✅ erprobt (manuell) |
| **Marv** | Read-Only Red Team — **Chaos/Regression** | Wirft der App Steine in den Weg: kaputte/riesige/leere Inputs (Fuzzing), Race-Conditions, korrupte Dateien, Migrations-Edge-Cases, „DAU klickt dreimal". | **Rührt keinen Produktivcode an.** Nur lesen, brechen, dokumentieren — dann Übergabe an Frank. ✅ erprobt (manuell) |
| **Axel** | Read-Only **Forensiker** — stärkstes Modell, **auf Abruf** | Knackt **besonders schwierige** Fälle, an denen Frank scheitert: tiefe Root-Cause-Analyse von Heisenbugs, Race-Conditions, subtiler Datenkorruption, verschachtelten Sicherheitslücken. Liefert eine **Ermittlungsakte** (Ursache + Fix-Plan). | **Rührt keinen Produktivcode an.** Läuft **nie im Dauer-Loop** (teuer). **Immer `{{starkes-modell}}`/API.** Übergibt den Fix-Plan zurück an Frank. ✅ erprobt (manuell) |

> **Wer fixt spontan?** Out-of-Loop-Fixes sind **Franks** Aufgabe. Der Architekt
> greift **nur im Ausnahmefall** selbst zum Produktivcode — hält sich dann aber
> **exakt an Franks Dreisatz**.

---

## Franks Dreisatz — Out-of-Loop-Fixes  ✅ erprobt

Damit weder Ralph noch der Architekt dasselbe Problem doppelt behandeln, gilt für
jeden spontanen Fix:

1. **Code-Fix committen** mit klarem Präfix, z. B. `{{fix-präfix}}: …`.
2. **CHANGELOG-Eintrag** unter [`{{CHANGELOG-Pfad}}`]({{CHANGELOG-Pfad}}) →
   `## [Unreleased] — Fixes …` anlegen (Was + Warum). Dieser Block ist die
   **Single Source of Truth** für alle Out-of-Loop-Fixes.
3. **Backlog pflegen** (falls ein Finding berührt ist): Status in
   [`{{Backlog-Pfad}}`]({{Backlog-Pfad}}) auf `erledigt (Frank-Fix, <commit>)`.

Ralph liest den `[Unreleased]`-Block vor jeder Stufe und baut ein dort gelistetes
Problem **nicht erneut**.

---

## Harry & Marv — Read-Only Red Team  ✅ erprobt (manuell) · 🟡 automatisiert

**Eiserne Regel — die Handschuhe bleiben an (Read-Only):**

- **Kein Produktivcode.** Harry & Marv ändern **niemals** Dateien in
  `{{Produktivcode-Globs}}`. Sie **fixen nichts**.
- **Erlaubt** ist nur: Lesen, kreativ testen (Reproducer-Tests unter
  [`{{Test-Ordner}}`]({{Test-Ordner}}) oder Wegwerf-Skripte) und **präzise
  dokumentieren**.
- Ein Fund wird sauber dokumentiert und **an Frank übergeben** (Finder ≠ Fixer).

**Der Beutezug-Dreisatz** (jeder Einbruch endet so):

1. **Fund ins Beutebuch** [`{{Beutebuch-Pfad}}`]({{Beutebuch-Pfad}}) eintragen:
   `HM-<Nr>`, Angreifer (Harry/Marv), Schweregrad (kritisch/hoch/mittel/klein),
   **Reproschritte**, Erwartung vs. Realität.
2. **Optional: Reproducer-Test** unter [`{{Test-Ordner}}`]({{Test-Ordner}}) anlegen
   (darf rot sein — klar als `xfail`/Skip kennzeichnen, damit die Suite grün bleibt).
3. **Übergabe an Frank**: Status auf `offen → an Frank übergeben` setzen. Frank
   quittiert nach dem Fix mit `erledigt (Frank-Fix, <commit>)` und trägt den Fix in
   den `[Unreleased]`-Block ein.

---

## Axel — Read-Only Forensiker  ✅ erprobt (manuell)

**Wann Axel gerufen wird (Trigger):**

1. **Frank-Eskalation** (Hauptfall): Ein Fund, den Frank nach N Versuchen (Default 3)
   **nicht** knacken konnte, wird auf `an Axel übergeben` gesetzt — **bevor** an den
   Menschen eskaliert wird.
2. **Direkter Ruf** durch den {{Strippenzieher}}.
3. **Harry/Marv-Direktweg**: Ein Fund, der `kritisch` **und** offenkundig `komplex`
   ist, darf direkt an Axel gehen.

**Die eiserne Regel — read-only wie Harry/Marv:** Axel ändert **niemals**
`{{Produktivcode-Globs}}`. **Axel denkt, Frank tippt.**

**Kostenkontrolle:** Immer `{{starkes-modell}}`/API. **Nie im Dauer-Loop.** Harte
Budget-Cap pro Fall. Nur bei nachgewiesenem Bedarf.

**Der Ermittlungs-Dreisatz:**

1. **Ermittlungsakte** in [`{{Ermittlungsakten-Pfad}}`]({{Ermittlungsakten-Pfad}})
   anlegen: `AX-<Nr>`, Bezug zum Fund (`HM-<Nr>`), **Root-Cause**, warum Franks
   Versuche scheiterten, **schrittweiser Fix-Plan**.
2. **Status zurück an Frank**: Fund von `an Axel übergeben` auf `Fix-Plan liegt vor`.
3. **Übergabe**: Frank setzt um und quittiert mit `erledigt (Frank-Fix, <commit>)`.
   Nur wenn **selbst Axel** nicht weiterkommt: `an Mensch eskaliert`.

**Status-Kette** (Beutebuch/Backlog):
`offen → an Frank übergeben → an Axel übergeben → Fix-Plan liegt vor →
erledigt (Frank-Fix, …) → an Mensch eskaliert`.

---

## Kaskaden-Planungsregeln (verbindlich für den Architekten)  ✅ erprobt

**1. Skizze zuerst.** Neue Themen/Stränge werden **zunächst als Skizze** in
[`{{Roadmap-Skizzen-Pfad}}`]({{Roadmap-Skizzen-Pfad}}) gehalten: Ziel, grober
Umfang, Bezug, offene Fragen — **ohne** feste Stufen-Nummern, **ohne** Cap.

**2. Aushärtung on demand.** **Erst wenn ein Strang zur nächsten Kaskade wird**
(der {{Strippenzieher}} gibt frei), härtet der Architekt ihn voll aus: eigene
`{{Plan-Präfix}}-N-….md`, fester Stufen-Bogen mit konkreten Nummern, einzelne
`## Stufe N`-Blöcke (Umsetzung / Verifikation / Promise), gesetzter `RALPH_CAP`-Wert
im Loop-Skript und „Vor dem Start"-Checkliste. Nur die **jeweils nächste** Kaskade
wird so ausgehärtet; alles Fernere bleibt Skizze.

**3. Nummerierung erst bei der Aushärtung.** Der Start richtet sich nach dem
**aktuellen** Stand in [`{{state-Datei}}`]({{state-Datei}}) — es entstehen keine
„fernen" fixen Nummern, die verschoben werden müssten.

---

## Loop-Mechanik & Auth (Ralph)

- **Modell:** `{{Modell-Default}}` (z. B. Sonnet — teures Opus für den Loop meist
  überflüssig). ✅ erprobt
- **Permission-Mode:** `bypassPermissions` (headless hat keinen Approver). ✅ erprobt
- **Smoke-Test:** Syntaxprüfung (z. B. `py_compile`/`ast.parse` — im Zielprojekt
  auf die reale Sprache/Toolchain anpassen), **kein** GUI-Import.
- **Budget kappen:** `RALPH_BUDGET_USD=N` (Empfehlung: kleiner Betrag pro Stufe).
- **Promise-Match:** `<promise>STUFE_N_COMPLETE</promise>` mit Tags.
- **Zustand:** [`{{state-Datei}}`]({{state-Datei}}) = nächste auszuführende Stufe.
- **Bei Hängern:** Prozess- und Log-Prüfung, bevor abgebrochen wird.

**Auth-Modi** (`{{Auth-Modus}}`):

- **Auflösung (✅ erprobt, Prio absteigend):** Env `AUTH_MODE` → maschinenlokale
  Config `~/.config/claude-team/auth-mode` → Rollen-Default (**Loop-Rollen:
  `abo`**, starke Rollen Axel/Architekt: `api`).
- `abo` = Claude-Abo via `claude login` (nicht token-abgerechnet). **Achtung,
  Verdrängungsfalle (✅ im Feld beobachtet):** Ein exportierter
  `ANTHROPIC_API_KEY` hat Vorrang vor dem Abo-Login („takes precedence"-Warnung
  der CLI) — `team-lib.sh` entfernt den Key im Abo-Modus deshalb aktiv aus der
  Prozess-Umgebung. Der Key gehört **nie** per `export` in `.bashrc` & Co.;
  bereits offene Terminals/IDE-Prozesse behalten einen geerbten Key außerdem
  bis `unset` bzw. Neustart (Env-Vererbung!).
- `api` = Pay-per-Use. Key-Quelle: Env `ANTHROPIC_API_KEY`, sonst
  `~/.config/claude-team/api-key` (eine Zeile, `chmod 600`).
- **Abo-Default mit API-Fallback (✅ bei Ralph erprobt, übrige Loop-Rollen 🟡):**
  Loop-Rollen starten im **Abomodus**; scheitert ein Aufruf (Timeout/Limit),
  folgt **ein** API-Retry nur **bis zum Abschluss der laufenden Stufe**, danach
  wieder Abo. **Axel und Der Architekt bleiben immer `api`** (starkes/teures
  Modell, kein Abo-Default, kein Umschalter). Rezept: Anhang A.3.
- **Einrichtung pro Maschine:** einmalig `~/.claude/scripts/team-auth-setup.sh`
  (idempotent: legt Config an, migriert Keys aus Shell-Profilen in die
  Key-Datei, testet den Abo-Login headless).

---

## Kostenkontrolle

**Modell / Plattform**: {{z. B. Sonnet via Claude Code — Loop-Rollen;
{{starkes-modell}} via API — Axel/Architekt}}
**Budget-Limit**: {{N}} USD pro Stufe (Ralph) · {{M}} USD pro Fall (Axel) ·
{{K}} USD gesamt
**Warnschwelle**: {{N×0,8}} USD pro Stufe/Fall — bei Erreichen den
{{Strippenzieher}} informieren, bevor weitergemacht wird.

**Grundregeln (stets aktiv):**

- Das starke/teure Modell (Axel, Architekt) läuft **nie im Dauer-Loop**; jede
  Iteration/jeder Fall bekommt ein hartes Budget-Cap.
- Loop-Rollen nutzen das günstige Modell (Default {{Modell-Default}}), pro Rolle
  über Env übersteuerbar.

**Token-Sparregeln (optional, projektabhängig — Default AUS):**

> Diese Regeln senken die Kosten, können aber die **Arbeitsqualität**
> beschneiden — besonders bei Axel, der für die Root-Cause-Analyse **tief lesen
> können muss**. Nur aktivieren, wenn das Budget es erzwingt, und Axel möglichst
> **ausnehmen**.
>
> - Dateien nur einmal pro Stufe/Fall lesen (Ausnahme: Axel darf nachlesen).
> - Bei großen Dateien mit `offset`/`limit` gezielt lesen statt komplett.
> - Antworten knapp halten, wenn nicht ausdrücklich mehr gefragt ist.

**Projektabschluss-Pflicht (Abschluss-Variante — Projekt hat definiertes Ende pro
Kaskade):** Vor dem letzten Commit `wiki/kosten.md` (oder projekteigenes Pendant)
mit vollständiger Gesamtauswertung abschließen — Tabelle aller Stufen/Fälle mit
Kosten, Gesamtsumme, Kosteneffizienz-Notiz (z. B. Kosten pro Stufe / pro gefixtem
Finding).

---

## Aufnahme-Interview (führt der {{Strippenzieher}} mit der einrichtenden Instanz)

Bevor die Platzhalter ersetzt werden, klärt die einrichtende Instanz mit dem
{{Strippenzieher}}:

1. **`{{Projektname}}`** → „Wie heißt das Projekt / Repo?"
2. **`{{Strippenzieher}}`** → „Wie soll die menschliche Regie-Rolle heißen?
   (z. B. „Strippenzieher", „Maintainer", „Lead")"
3. **`{{Produktivcode-Globs}}`** → „Welche Pfade sind **Produktivcode** und damit
   für das Red Team + Axel tabu? (z. B. `src/**`, `core/**`, `app.py`)"
4. **`{{Test-Ordner}}` / `{{Plan-Ordner}}`** → „Wo dürfen Red Team & Architekt
   schreiben? (z. B. `tests/`, `plans/`)"
5. **`{{fix-präfix}}` / `{{feat-präfix}}`** → „Welche Commit-Konventionen gelten?
   (Default: `fix(uat)` bzw. `feat`)"
6. **`{{CHANGELOG-Pfad}}` / `{{Backlog-Pfad}}` / `{{Beutebuch-Pfad}}` /
   `{{Ermittlungsakten-Pfad}}`** → „Wo liegen die Übergabepunkte?"
7. **`{{Roadmap-Skizzen-Pfad}}` / `{{Plan-Präfix}}` / `{{state-Datei}}`** → „Wo liegt
   die Planungs-Infrastruktur? (Default: `plans/roadmap-skizzen.md`,
   `plans/ralph-kaskade`, `.ralph-state`)"
8. **`{{loop-skript}}`** → „Gibt es schon einen Bau-Loop? (z. B. `ralph.sh`) Sonst
   nach Anhang A generieren."
9. **`{{Modell-Default}}` / `{{starkes-modell}}` / `{{Auth-Modus}}`** → „Modell- und
   Kosten-Politik: Loop = günstiges Modell (Sonnet) + Abo-Zielbild; Axel/Architekt =
   starkes Modell (Opus) + immer API. Budget-Limits siehe `## Kostenkontrolle`."

Trage die Antworten anschließend selbst ein und speichere die `CLAUDE.md`. Der
{{Strippenzieher}} macht das nicht selbst.
````

---

## Anhang A — Team-Skripte kontextabhängig generieren

**Statt Skripte zu kopieren**, generiert die aufnehmende Instanz sie passend zum Zielprojekt — **sofern das zum Zeitpunkt des Einspielens möglich ist**.

### A.1 Vorbedingungs-Check (immer zuerst)

Vor jeder Skript-Generierung prüfen:

- **Git-Repo vorhanden?** (`git rev-parse --is-inside-work-tree`) — sonst nur die Doku-Sektion einspielen, Skript-Teil überspringen.
- **Claude-CLI verfügbar?** (`claude --version`) — bestimmt, ob Loop-Skripte überhaupt sinnvoll sind.
- **Existiert schon ein Loop?** (`ls {{loop-skript}}`) — falls ja, **nicht überschreiben**, sondern nur `team-lib.sh` + Rollen-Skripte ergänzen und den bestehenden Loop darauf umstellen.
- **Commit-Konvention** des Zielprojekts erfassen (`git log --oneline -20`) — bestätigt `{{fix-präfix}}` / `{{feat-präfix}}`.

### A.2 Was generiert wird (empfohlene Reihenfolge)

Referenz-Bausteine — nach dem bewährten Loop-Muster **beschrieben**, nicht als fertiger Code geliefert. Reifegrad wie angegeben:

1. **`team-lib.sh`** 🟡 — gemeinsame Bausteine (Config-Sourcing, `flock`-Guard, Promise-Auswertung, Logging, Budget-Flags) + zentrale **Auth-Logik** (A.3).
2. **`{{loop-skript}}`** ✅ — Ralph-Äquivalent: falls keiner existiert, nach dem Loop-Muster generieren; sonst auf `team-lib.sh` umstellen.
3. **`frank.sh`** 🟡 — Event-Loop am Beutebuch (einfachster Loop, kein Guard): greift Funde mit Status `an Frank übergeben`, fixt nach Franks Dreisatz, Promise `<promise>FRANK_FIX_COMPLETE</promise>`, Versuchszähler (Default 3) → dann `an Mensch eskaliert`.
4. **Read-Only-Guard** 🟡 — 3 Linien (A.4) + rollenspezifischer `pre-commit`-Hook (aktiv nur bei `{{ROLE-ENV}}=harry|marv`).
5. **`harry.sh` / `marv.sh`** 🟡 — State = letzter geprüfter Commit-Hash; Trigger = neue Commits seit State (Angriff auf **stabilen** Code, idealerweise am Kaskaden-Übergang); Promise `<promise>REDTEAM_SWEEP_COMPLETE</promise>`; **Guard Pflicht**.
6. **Polling-Wache** 🟡 — dünne Schleife, die die Loops sequenziell startet (`inotify`/`post-commit` als späterer Ausbau).
7. **`.gitignore`** ergänzen um `.{{rolle}}-state`, `.team-loop.lock`.

### A.3 Auth-Fallback  ✅ bei Ralph erprobt (website-maxron-de, 2026-07-10) · 🟡 übrige Loop-Rollen

Zentral in `team-lib.sh` (Feldprojekt: Helfer `team_claude`): Rollen starten im **Abomodus**, fallen bei einem gescheiterten Aufruf **aufruf-lokal** auf `api` zurück, danach zurück zu Abo. **Nur Der Architekt** (interaktiv, kein Loop) bleibt bewusst API. **Hinweis:** Ob **Axel** ausgenommen bleibt, ist eine **Strippenzieher-Entscheidung** — im Feldprojekt website-maxron-de wurde Axel am 2026-07-10 bewusst **in die Abo-first-Regel aufgenommen** (starkes Modell im Abo ist günstiger; das Budget-Cap pro Fall bleibt als Airbag). Das erprobte Rezept:

- **`team_resolve_auth_mode [rollen-default]`**: löst Env `AUTH_MODE` → `~/.config/claude-team/auth-mode` → Rollen-Default auf. `abo` **entfernt** `ANTHROPIC_API_KEY` aus der Prozess-Umgebung (Verdrängungsfalle, s. o.); `api` lädt den Key notfalls aus `~/.config/claude-team/api-key` (`chmod 600`) — erst diese Key-Datei macht den Fallback möglich, wenn der Loop ohne Key in der Env gestartet wurde.
- **Stufen-lokal durch frische Auflösung**: Der Loop merkt sich die etwaige Nutzer-Übersteuerung beim Start (`AUTH_MODE_START="${AUTH_MODE:-}"`) und löst **pro Stufe neu** auf — damit endet jeder Fallback automatisch mit der Stufe.
- **Fehlersignal** (✅ verifiziert, siehe A.5): Aufruf gilt als gescheitert bei Exit-Code ≠ 0 **oder** `is_error: true` in der `--output-format json`-Antwort (Helfer `team_result_is_error`; unlesbares JSON zählt als Fehler).
- **Genau ein Retry**: Scheitert der Abo-Aufruf, folgt ein einziger API-Versuch mit eigener Log-Datei (z. B. `stufe-N-api-fallback-….json`); scheitert auch der → harter Abbruch, Mensch schaut in die Logs.
- **Maschinen-Einrichtung**: `~/.claude/scripts/team-auth-setup.sh` (idempotent; Config anlegen, Key-Migration aus Shell-Profilen mit Backup und Ersatz der Export-Zeile, optionaler headless Abo-Test inkl. Erkennung der „takes precedence"-Warnung).

### A.4 Read-Only-Guard (3 Linien, Defense-in-Depth)  ✅ erprobt (website-maxron-de, 2026-07-10)

1. **Prompt** — „Du bist Harry/Marv, schreibe ausschließlich `{{Test-Ordner}}` und `{{Plan-Ordner}}`." (notwendig, nicht hinreichend)
2. **Tool-Permissions** — headless `--permission-mode default` + enge `--allowedTools`-Liste: `Read`/`Grep`/`Glob` überall, `Write`/`Edit` nur auf `{{Test-Ordner}}**` + `{{Plan-Ordner}}**`, **kein** `git commit` in der Allowlist (das Skript committet die Whitelist-Änderungen deterministisch — der Angreifer selbst nicht).
3. **Post-Hook** (deterministische Garantie) — nach der Iteration `git diff --name-only <START_HASH> HEAD` + `git status --porcelain` gegen die Whitelist.

> ⚠️ **Guard-Härtungs-Lektion (Feldtest 2026-07-10, teuer gelernt):** Der Rollback in Linie 3 muss **chirurgisch** sein — **nur die konkret gelisteten Verletzer-Pfade** zurücksetzen (getrackt → `git checkout <START_HASH> -- <pfad>`; neu → gezielt `rm`/`git rm`). Ein **blindes `git reset --hard` + `git clean -fd`** ist ein Footgun: Im Feldtest löschte es die **gesamte noch uncommittete Team-Infrastruktur**, weil im Testmoment alle neuen Skripte als „Nicht-Whitelist" galten. Zwei Betriebsregeln dazu: (a) **Infrastruktur committen, bevor** je ein Guard läuft — im Normalbetrieb ist der Baum zwischen den Phasen ohnehin sauber (jede Rolle committet); (b) **Guard-Tests nur in einem Wegwerf-Repo**, nie im echten. Ein rollenspezifischer `pre-commit`-Hook bleibt optionaler Zusatz (Gürtel + Hosenträger).

Ausführlich: [read-only-guard](../konzepte/read-only-guard.md)

> **Frank-Variante:** Frank *darf* Produktivcode ändern → statt Guard eine **Dreisatz-Verifikation** (ein `{{fix-präfix}}`-Commit im Bereich `START_HASH..HEAD`, CHANGELOG ergänzt, Beutebuch-Status auf `erledigt`).
>
> ⚠️ **Verifikations-Lektion (Feldlauf website-maxron-de, 2026-07-10):** **Nicht** verlangen, dass **HEAD selbst** der `{{fix-präfix}}`-Commit ist — der Fixer darf den CHANGELOG-/Status-Edit legitim in einen `docs:`-**Folgecommit** legen (der Prompt erlaubt das sogar ausdrücklich). Prüfe stattdessen `git log START_HASH..HEAD --pretty=%s | grep {{fix-präfix}}`. Der ursprüngliche „letzter Commit"-Check rollte korrekt gefixte Arbeit fälschlich zurück.

### A.5 Faktencheck-Pflicht (Spec vor Annahme)

An der **real installierten** CLI verifizieren — **nicht raten**:

- **Tool-Permission-Format** (Settings-Datei vs. Flags; ob `permissions.deny` unterstützt wird). Falls `deny` fehlt: **Post-Hook (Linie 3) ist die Haupt-Garantie** — der Guard ist gegen beide Fälle robust. ✅ **verifiziert an der Claude-CLI (2026-07-10):** headless `--permission-mode default` + `--allowedTools`-Allowlist greift; ein Red-Team-Sweep mit auf `{{Test-Ordner}}`/`{{Plan-Ordner}}` beschränkter Allowlist ließ Produktivcode unangetastet.
- **Provider-Timeout-Signal** für den Auth-Fallback — ✅ verifiziert an der Claude-CLI (2026-07-10): Exit-Code ≠ 0 **oder** Feld `is_error` in der `--output-format json`-Ausgabe; die „takes precedence"-Warnung im Text signalisiert zusätzlich, dass eine andere Auth-Quelle das Abo verdrängt.

### A.6 Parallelität & Reproducer

- **Empfehlung:** sequenziell (Rollen hängen inhaltlich voneinander ab: Ralph → Red Team → Frank) + **`flock`-Airbag** in **alle** Loops, gegen `index.lock`-/`status`-Races. Echte Parallelität (Git-Worktrees) bleibt späterer Ausbau.
- **Guard-Reproducer:** ein Loop, der absichtlich `{{Produktivcode-Globs}}` anfasst, **muss** vom Post-Hook hart zurückgerollt werden (grüner Regressions-Schutz).

---

## Verwandte Seiten

- [claude-md-ki-team](../quellen/claude-md-ki-team.md) — Quellenseite mit Einordnung und Namenskonflikt-Historie
- [ki-lehrer-app](../konzepte/ki-lehrer-app.md) — Das reale Ursprungsprojekt (KI-Lehrer-App)
- [finder-fixer-prinzip](../konzepte/finder-fixer-prinzip.md) — Die Gewaltenteilungs-Regel hinter Harry/Marv/Axel vs. Frank
- [read-only-guard](../konzepte/read-only-guard.md) — Die 3-Linien-Durchsetzung von Anhang A.4
- [ralph-schleife](../konzepte/ralph-schleife.md) — Das Loop-Muster, das Ralph in dieser Vorlage nutzt
- [kostencounter](../konzepte/kostencounter.md) — Kostenkontroll-Standard dieses Wikis
- [claude-md-team](claude-md-team.md) — Namensvetter mit anderem Thema (Menschen-Team statt KI-Rollenteam)
- [claude-md-lehrer](claude-md-lehrer.md) — Format-Vorbild dieser Vorlage
- [feldtest-sperre](../konzepte/feldtest-sperre.md) — Verwandtes Muster aus demselben Ursprungsprojekt (Freeze statt Rollenteilung)

---

[Wiki-Index](../index.md)
