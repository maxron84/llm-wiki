---
date: 2026-08-01
type: vorlage
tags: [vorlage, schema, softwareprojekt, automatisierung, kosten]
status: active
---

# CLAUDE.md-Vorlage: T.E.A.M. (KI-Rollenteam)

**Zusammenfassung**: Eine wiederverwendbare CLAUDE.md-Vorlage für ein **Team aus KI-Rollen** unter der Regie eines menschlichen Senior-Entwicklers (des „Strippenziehers"). Das Muster stammt aus einem realen Projekt ([KI-Lehrer-App](../konzepte/ki-lehrer-app.md)) und verallgemeinert dessen Rollen-, Prozess- und Automatisierungs-Konzept: ein autonomer Bau-Loop (Ralph), ein planender Architekt, ein Ad-hoc-Fixer (Frank), ein read-only Red Team (Harry & Marv) und ein read-only Forensiker (Axel). Die Vorlage liefert **keine fertigen Skripte**, sondern eine **Bau-Anleitung**, mit der die aufnehmende KI-Instanz die Team-Skripte **kontextabhängig zum Zielprojekt** generiert.
**Quellen**: Abgeleitet aus `CLAUDE.md`, `plans/team-automation-loops.md`, `plans/roadmap-skizzen.md` (R2, R4) und `ralph.sh` des [KI-Lehrer-App](../konzepte/ki-lehrer-app.md)-Ursprungsprojekts (Dateien liegen dort, nicht in diesem Wiki-Repo); Feldtest der Auth-Mechanik (Abo-Prio-1 + stufen-lokaler API-Fallback): Projekt `website-maxron-de` (2026-07-10, dort `team-lib.sh`/`ralph.sh`); **Voll-Automatik-Feldtest über zweiundzwanzig Kaskaden** (`website-maxron-de`, 2026-07-10 bis 2026-08-01): scharfe Vollautomatik-Läufe mit realen Red-Team-Funden (HM-1…HM-53) und Frank-Fixes, plus die Budget-Governance-Bausteine und Betriebslehren aus den Kaskaden 6–22 (siehe Anhang A.7–A.10): Session-Limit-Robustheit (429, Exit-42-Pausen-Mechanik, `BL-20`/`BL-25`), Auth-Startwarnung (`BL-27`), Zwei-Schwellen-Budgetmodell (`BL-30`), automatische & domänengetrennte Architekt-/Akteur-Kostenerfassung (`BL-28`/`BL-29`/`BL-33`), die Scharfschalt-Sequenz-Pflicht (Planungsregel 4), die Abschluss-Doc-Pflicht (Planungsregel 5), die Reparatur der Kostenmessung (`BL-55`) und die Doku-Konsolidierung samt Regel-Inventar (`BL-54`); Format-Vorbild: [claude-md-lehrer](claude-md-lehrer.md).
**Zuletzt aktualisiert**: 2026-08-01

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

Konkret: **✅** Ralph-Loop, manuell angestoßene Rollen (Frank/Harry/Marv/Axel), alle Dreisätze, Status-Ketten, Auth `api|abo`, **Abo-first-mit-API-Fallback für alle automatisierten Rollen**, die **Voll-Automatik** (Orchestrator + `redteam.sh`/`frank.sh`/`axel.sh` + chirurgischer 3-Linien-Guard + `flock` + Beutebuch-Zustandsmaschine + Monitoring), der **kaskaden-abhängige Red-Team-Fokus** (`TEAM_REDTEAM_FOCUS`), die **„success ohne Promise"-Härtung** (Prompt- + Logik-Härtung), die **Session-Limit-Robustheit** (429 → Exit-42-Pausen-Mechanik + Auto-Retry mit Deckel, `BL-20`/`BL-25`), die **Auslauf-Bremse gegen Leerlaufkosten** (`TEAM_FIX_MAX_STAGNATION`), die **aktive Auth-Startwarnung** (`BL-27`), das **Zwei-Schwellen-Budgetmodell** (Soft-/Hard-Cap, `BL-30`), die **automatische & domänengetrennte Kostenerfassung** interaktiver Rollen (Architekt/Frank-im-Abo, `BL-28`/`BL-29`/`BL-33`), die **Scharfschalt-Sequenz-Pflicht** (Planungsregel 4), die **Abschluss-Doc-Pflicht** (Planungsregel 5) und die **Doku-Hygiene mit Regel-Inventar** (`BL-54`, A.10). Der erste echte Ende-zu-Ende-Vollautomatik-Lauf (Feldtest der `--allowedTools`-Wirksamkeit, A.5) ist **inzwischen über zweiundzwanzig Kaskaden scharf gelaufen** — im Zweitprojekt website-maxron-de (2026-07-10 bis 2026-08-01), mit realen Red-Team-Funden (HM-1…HM-53), die Frank gefixt hat, und wirksamem Read-Only-Guard (`permission_denials` in den Logs belegen die Durchsetzung). Damit ist auch die Voll-Automatik **✅ erprobt** (zuvor 🟡). Die im Feld gewonnenen Betriebslehren (Budget-Cap-Timing, kaskaden-spezifischer Red-Team-Fokus, „success ohne Promise"-Behandlung, Log-Rotation gegen Doppelzählung, A/B-Kennzahlen-Trennung, Session-Limit-Pause, Stagnations-Bremse, Zwei-Schwellen-Budget, interaktive Akteur-Kostenerfassung, **Prosa gehört nicht in den Bau-Loop**, **Kostenmessung darf nicht blind werden**) siehe Anhang A.7–A.10.

## Benutzung

1. Kopiere **nur den Inhalt** des Vorlagenblocks unten (ohne den 4-Backtick-Rahmen) als `CLAUDE.md` ins Wurzelverzeichnis des Zielprojekts — oder füge ihn als eigenständige **„Das Team"-Sektion** in eine vorhandene `CLAUDE.md` ein.
2. Führe das **Aufnahme-Interview** (im Vorlagenblock enthalten) mit dem Strippenzieher und ersetze alle `{{PLATZHALTER}}` mit den Antworten.
3. Arbeite **Anhang A** ab — jetzt eine eigene Seite: [team-skripte-generieren](../anleitungen/team-skripte-generieren.md). Vorbedingungen prüfen, dann die Team-Skripte **kontextabhängig generieren** (nicht blind kopieren).
4. Setze die Reifegrad-Marken so, dass klar bleibt, was im **Zielprojekt** bereits läuft und was noch Zielbild ist.

## Designhinweise

1. **Fence entfernen**: Beim Kopieren den 4-Backtick-Wrapper des `## Vorlage`-Blocks entfernen.
2. **Finder ≠ Fixer**: Das ist keine Empfehlung, sondern die tragende Gewaltenteilung. Wer einen Fehler findet (Harry/Marv/Axel), fixt ihn **nicht** selbst — das macht Frank. So bleibt jede Übergabe nachvollziehbar. → [finder-fixer-prinzip](../konzepte/finder-fixer-prinzip.md)
3. **Read-Only-Regel ist eisern**: Für das Red Team (Harry/Marv) und den Forensiker (Axel) muss die Beschränkung auf Test-/Plan-Ordner **technisch erzwungen** werden (3-Linien-Guard, Anhang A) — Prompt-Vertrauen allein genügt nicht. → [read-only-guard](../konzepte/read-only-guard.md)
4. **Kosten-Caps sind Pflicht**: Das starke/teure Modell (Axel, Architekt) läuft **nie im Dauer-Loop**; jede Iteration/jeder Fall bekommt ein Budget-Cap. → siehe auch [kostencounter](../konzepte/kostencounter.md)
5. **Reifegrad ehrlich lassen**: 🟡-Teile nicht als produktiv ausgeben — sonst verliert die Vorlage ihren Wert („Spec ist Wahrheit vor Annahmen").
6. **Skripte kontextabhängig generieren**: An die real installierte CLI und die Repo-Konvention anpassen (Faktencheck-Pflicht, Anhang A) — nicht raten.
7. **Namen sind austauschbar — Entrypoint-Namen aber sprechend**: Die augenzwinkernden Rollen-Anspielungen (Matrix, *Kevin allein zu Haus*, *Beverly Hills Cop*) sind Kür; das **Prozess-Skelett** (Rollen, Dreisätze, Status-Ketten) ist Pflicht. **Feld-Lehre (website-maxron-de, 2026-07-11):** Die **Orchestrierungs-Entrypoints** dagegen bewusst **sprechend** benennen — `vollautomatik.sh` / `halbautomatik.sh` statt kryptischer Marken-Namen. Im Feldprojekt wurden `wache.sh`/`pock.sh` genau darum zu `vollautomatik.sh`/`halbautomatik.sh` umbenannt (echtes `git mv`, alle Referenzen + internes Wording nachgezogen): Wer die Skripte tippt, soll aus dem Namen erkennen, was sie tun.
8. **Kostenkontroll-Block ist Teil des kopierbaren Blocks**: Der Abschnitt `## Kostenkontrolle` steht bewusst **innerhalb** des `## Vorlage`-Fences — er wird also in die Ziel-CLAUDE.md mitkopiert. Nur die Wiki-Meta (diese Designhinweise, Anhang A, „Verwandte Seiten") bleibt außerhalb.
9. **Betriebsregeln und Bau-Anleitung getrennt halten**: Was das Team im Betrieb befolgt, steht hier; **wie** die Skripte einmalig entstehen, steht in [team-skripte-generieren](../anleitungen/team-skripte-generieren.md). Vor 2026-08-01 war beides eine 69,8-KB-Datei — die Trennung ist A.10 („kürzt Text, nie Geltung") auf die Vorlage selbst angewandt.

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
| **Der Architekt** | Planungs-Instanz (interaktiv, Architect-Mode) | **Plant Kaskaden**, schreibt Plan-Dokumente unter [`{{Plan-Ordner}}`]({{Plan-Ordner}}), pflegt Roadmap/Backlog, setzt Caps. Trifft die Struktur-Entscheidungen, die Ralph ausführt. **Committet je nach Projekt-Entscheid** entweder Plan-/Doku-Änderungen selbständig (`docs(plan): …`) **oder liefert die fertigen Commit-Befehle zum Kopieren** — der Strippenzieher führt sie dann händisch aus (so im Feldprojekt website-maxron-de, Entscheid 2026-07-11). | Spec ist Wahrheit vor Annahmen. **Pflicht vor jedem Entwurf:** `[Unreleased]` + `Frank-Fix`-Zeilen abgleichen. **Modell stark (`{{starkes-modell}}`); Auth Abo-first** (Feldprojekt website-maxron-de, Entscheid 2026-07-13 — die frühere „Architekt immer API"-Regel ist aufgehoben). Kosten daher **Abo-Gegenwert**, erfasst per Live-Schätzung statt Konsolenwert (siehe `## Kostenkontrolle`). Greift **normalerweise nicht** selbst in Produktivcode ein. ✅ erprobt |
| **Frank der Fixer** | Spontane Out-of-Loop-Bugfixes | Behebt **akut auffallende** Bugs/UX-Reibungen **außerhalb** des Loops, ohne auf die nächste Kaskade zu warten. | Jeder Fix folgt dem **Dreisatz** (Commit `{{fix-präfix}}: …` → CHANGELOG → Backlog). Der **Normalweg** für Ad-hoc-Fixes. ✅ erprobt |
| **Harry** | Read-Only Red Team — **Security/Pentest** | Versucht die App **bewusst auszuhebeln**: Auth/PINs/Tokens umgehen, Angriffsfläche der Netz-Schnittstellen, Pfad-/Injection-Tricks, Datenlecks in Logs/Exports. | **Rührt keinen Produktivcode an.** Nur lesen, angreifen, dokumentieren — dann Übergabe an Frank. ✅ erprobt (manuell) |
| **Marv** | Read-Only Red Team — **Chaos/Regression** | Wirft der App Steine in den Weg: kaputte/riesige/leere Inputs (Fuzzing), Race-Conditions, korrupte Dateien, Migrations-Edge-Cases, „DAU klickt dreimal". | **Rührt keinen Produktivcode an.** Nur lesen, brechen, dokumentieren — dann Übergabe an Frank. ✅ erprobt (manuell) |
| **Axel** | Read-Only **Forensiker** — stärkstes Modell, **auf Abruf** | Knackt **besonders schwierige** Fälle, an denen Frank scheitert: tiefe Root-Cause-Analyse von Heisenbugs, Race-Conditions, subtiler Datenkorruption, verschachtelten Sicherheitslücken. Liefert eine **Ermittlungsakte** (Ursache + Fix-Plan). | **Rührt keinen Produktivcode an.** Läuft **nie im Dauer-Loop** (teuer). **Modell immer stark** (`{{starkes-modell}}`, analog zum Architekten) — **auch im Abomodus**; **Auth** dagegen wie die Loop-Rollen **Abo-first mit API-Fallback** (Modell und Auth sind zwei getrennte Achsen, siehe Axel-Sektion). Übergibt den Fix-Plan zurück an Frank. ✅ erprobt (manuell) |

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

## Harry & Marv — Read-Only Red Team  ✅ erprobt (manuell **und** automatisiert)

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

**Kostenkontrolle — zwei getrennte Achsen (nicht vermengen):**

- **Modell:** **immer stark** (`{{starkes-modell}}`, Default Opus; z. B. `fable`
  möglich) — analog zum Architekten, **auch im Abomodus**. Die übrigen Loop-Rollen
  (Ralph/Harry/Marv/Frank) bleiben beim günstigen Modell.
- **Auth:** **Abo-first mit aufruf-lokalem API-Fallback** (wie die anderen
  automatisierten Rollen; Strippenzieher-Entscheid im Feldprojekt
  website-maxron-de, 2026-07-10 — die frühere „Axel immer API"-Regel ist
  aufgehoben, weil das starke Modell im Abo schlicht günstiger ist).
  **Seit 2026-07-13 gilt das auch für Den Architekten** — **keine** Rolle ist
  mehr fest API. Das ändert nur die Auth-Achse; die Modell-Achse (stark für
  Axel/Architekt, günstig für die Loop-Rollen) bleibt unberührt.

**Nie im Dauer-Loop** (ein Fall pro Aufruf). Budget pro Fall im
**Zwei-Schwellen-Modell** (`BL-30`, siehe `## Kostenkontrolle`): **Soft-Cap**
(`{{AXEL-Soft-Cap}}`, Default 5 USD — **nur Hinweis**, kein Rollback) und
**Hard-Cap** (`{{AXEL-Hard-Cap}}`, Default 10 USD — Abbruch mit Rollback als
Airbag). Nur bei nachgewiesenem Bedarf.

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

> **Was nicht in den Loop gehört (✅ Feld-Lehre website-maxron-de, Kaskade 22):**
> **Textvolumen-gebundene Prosa-Arbeit** (Doku umbauen, verdichten, umziehen)
> plant der Architekt als **eigene Handarbeit** ein — auch wenn der Rest der
> Kaskade in den Loop gehört. Der Loop zahlt pro Stufe einen **Kaltstart** und
> liest die gewachsene Datei erneut; der interaktive Architekt hält denselben
> Kontext durchgehend. Im Feld kosteten Prosa-Stufen **3,23 / 3,97 / 4,68 USD**
> gegenüber **2,16 / 2,35 USD** für Code-Stufen derselben Kaskade (Vergleichs-
> Kaskade: 1,85 USD/Stufe) — die teuerste Prosa-Stufe lag bereits **über der
> 80-%-Warnschwelle** des Pro-Stufe-Caps. Details siehe Anhang A.7, Lehre 7.

**3. Nummerierung erst bei der Aushärtung.** Der Start richtet sich nach dem
**aktuellen** Stand in [`{{state-Datei}}`]({{state-Datei}}) — es entstehen keine
„fernen" fixen Nummern, die verschoben werden müssten.

**4. Scharfschalt-Sequenz ist Pflicht-Ausgabe (✅ erprobt, website-maxron-de,
2026-07-11).** **Am Ende jeder Aushärtung** (und ebenso, wenn der Architekt eine
bereits ausgehärtete Kaskade für den Start vorbereitet) gibt der Architekt
**immer automatisch** eine fertige, kopierbare **Startsequenz** aus — der
{{Strippenzieher}} soll sie nie selbst aus dem Plan zusammensuchen müssen. Sie
wird **aus dem Plankopf abgeleitet** (nicht geraten) und enthält verbindlich:

1. **Zeiger umlegen:** `echo {{Plan-Präfix}}-N-….md > .ralph-plan` (Pfad des
   frisch ausgehärteten Plans) — die **einzige** Quelle, aus der der Loop
   Plan-Pfad **und** `RALPH_CAP` liest und aus der die Vollautomatik die
   `BUDGET_EMPFEHLUNG_USD` zieht. Ein veralteter Zeiger löst den stillen
   Fehlstart aus (`{{state-Datei}} > altem RALPH_CAP` → „Feierabend", die Kaskade
   wird nie gebaut).
2. **Konsistenz-Check:** `cat {{state-Datei}} .ralph-plan` gegen die
   Soll-Erwartung (Startstufe + neuer Plan-Pfad), damit der Zeiger-Blocker sofort
   auffällt.
3. **Budget ansehen:** `./team-status.sh --budget` (reine Anzeige; die
   Deckel-Anhebung auf `BUDGET_EMPFEHLUNG_USD` macht `vollautomatik.sh` selbst).
4. **Red-Team-Fokus**, **falls** die Kaskade **Team-Infrastruktur statt
   {{Produktivcode-Globs}}** anfasst: `export TEAM_REDTEAM_FOCUS="…"` (Pflicht,
   sonst prüft das Red Team am Bau vorbei). Bei einer reinen Produktivcode-Kaskade
   entfällt dieser Schritt.
5. **Start:** `./vollautomatik.sh`.

**Grenze:** Der Architekt **gibt die Sequenz nur aus** — das tatsächliche
Umschalten von `.ralph-plan`/[`{{state-Datei}}`]({{state-Datei}}) und der Start
bleiben {{Strippenzieher}}-Handarbeit. Die Regel automatisiert die **Ausgabe der
Vorlage**, nicht ihre Ausführung.

**5. Abschluss-Doc ist Pflicht pro gebauter Kaskade (✅ erprobt,
website-maxron-de, Entscheid 2026-07-13 — „ab sofort immer so").** Nach **jedem**
Vollautomatik-Lauf legt der Architekt ein committetes
**`{{Plan-Ordner}}/kaskade-N-abschluss.md`** an.

**Warum:** Der im Terminal gedruckte Abschlussbericht ist **flüchtig** — er liegt
nur in `.gitignore`-ten Logs, und die Roh-Kostenlogs werden beim Kostenabschluss
zusätzlich **wegarchiviert**. Ohne Abschluss-Doc gibt es **kein** dauerhaftes, im
Git nachlesbares Lauf-Protokoll, aus dem ein später **kalt startendes**
Architekt-Ich den Lauf eigenständig auswerten kann. Das Doc hält verbindlich
fest:

- **Ist-Stand** (Bau / Deploy / State / Qualitäts-Gates),
- **echte Lauf-Kosten** inklusive der Nuance **„gedruckte ‚Dieser Lauf'-Zahl vs.
  Ledger-Basis + Restlogs"** — die gedruckte Zahl **untertreibt**, weil bereits
  geledgerte/archivierte Stufen herausfallen (siehe A.7, Lehre 8),
- **Funde** (Fund-/Akten-/Backlog-Nummern + Status),
- **Release-Strategie** und **Deploy-Bedarf**,
- die **offenen operativen Schritte** (Kostenabschluss, Backlog/Roadmap nachziehen).

Wie die Scharfschalt-Sequenz (Regel 4) ist es Architekt-**Ausgabe**; das
Committen bleibt {{Strippenzieher}}-Handarbeit, sofern das Projekt den
Architekten nicht selbst committen lässt.

---

## Doku-Hygiene — die Regeldatei bleibt Regelquelle  ✅ erprobt

Diese `CLAUDE.md` ist **geltendes Recht**, kein Bautagebuch. Herleitungen
(„warum diese Zahl") und Historie („am TT.MM. ergänzt") wachsen sonst über die
Regeln hinaus und werden **doppelt bezahlt**: Die Datei liegt ohnehin im
Systemprompt jeder Instanz — und wenn die Rollen-Prompts zusätzlich „Rolle siehe
CLAUDE.md, lies sie zuerst" verlangen, kommt pro Rollenaufruf ein **zweiter
Voll-Read** obendrauf (bei ~20–30 Aufrufen je Kaskade). Verbindlich:

1. **Schichten.** Geltende Regel → hierher. Herleitung/Historie → Wiki oder
   Historien-Doc, **wörtlich verschoben**, nie ersatzlos gestrichen.
2. **Rollen-Briefings statt Volltext.** Jede automatisierte Rolle bekommt ein
   kurzes eigenes Briefing (`prompts/rolle-*.md`, ~20 Zeilen: wer ich bin, mein
   Auftrag, meine eiserne Grenze, mein Dreisatz, mein Promise) — mit Fallback auf
   diese Datei, falls das Briefing fehlt. **Guards und Promise-Strings im Prompt
   bleiben unangetastet.**
3. **Fund-/Aufgabenlisten archivieren**, sobald sie überwiegend abgeschlossen
   sind — mit **archiv-bewusster Nummernvergabe**, sonst vergibt die
   Zustandsmaschine nach der Rotation **doppelte Fund-Nummern**.
4. **Leitplanke: kürzt Text, nie Geltung.** Regel streichen, Default ändern,
   Guard lockern, Rolle umdefinieren sind bei einem Doku-Umbau **verboten** —
   dafür braucht es einen eigenen, benannten Entscheid. Absicherung: das
   **Regel-Inventar** (Anhang A.10).

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
  - **Aktive Startwarnung (✅ erprobt, `BL-27`, website-maxron-de 2026-07-12):**
    `team_resolve_auth_mode()` warnt zusätzlich **einmal pro Prozessbaum** auf
    stderr, wenn `AUTH_MODE=abo` gilt, aber ein `ANTHROPIC_API_KEY` in der
    Prozess-Umgebung liegt — **bevor** der `unset` greift. Reine Sichtbarkeit,
    **kein Abbruch**; abschaltbar über `TEAM_ABO_KEY_WARNUNG=0` (Default an), im
    `api`-Modus erscheint sie nie. Realer Auslöser: ein ~13,8-USD-Leerlauf-Lauf
    lief komplett über API statt Abo-first, weil ein `.bashrc`-Key das Design
    still aushebelte.
- `api` = Pay-per-Use. Key-Quelle: Env `ANTHROPIC_API_KEY`, sonst
  `~/.config/claude-team/api-key` (eine Zeile, `chmod 600`).
- **Abo-Default mit API-Fallback (✅ erprobt):** Loop-Rollen starten im
  **Abomodus**; scheitert ein Aufruf (Timeout/Limit), folgt **ein** API-Retry nur
  **bis zum Abschluss der laufenden Stufe**, danach wieder Abo. **Auch Axel und
  Der Architekt laufen Abo-first** (Entscheide 2026-07-10/13) — das starke Modell
  im Abo ist schlicht günstiger, die Budget-Caps bleiben als Airbag. **Keine**
  Rolle ist mehr fest `api`. Rezept: Anhang A.3.
- **Einrichtung pro Maschine:** einmalig `~/.claude/scripts/team-auth-setup.sh`
  (idempotent: legt Config an, migriert Keys aus Shell-Profilen in die
  Key-Datei, testet den Abo-Login headless).

**Session-Limit (429) — dritte Fehlerklasse, Strategie A+B  ✅ erprobt
(`BL-20`/`BL-25`, website-maxron-de 2026-07-11):** Ein Claude-Session-Limit
(`api_error_status: 429`, `result`-Text „session limit"/„resets HH:MMpm") ist
**weder** ein sauberer Erfolg **noch** ein „echter" Fehler, sondern eine eigene,
klar benannte dritte Klasse — sie weicht Guard/Read-Only-Regeln **nicht** auf.

- **API-Fallback zuerst — auch beim 429.** Scheitert der Abo-Aufruf aus
  **irgendeinem** Grund (Timeout, Fehler ODER 429), versucht `team_claude()`
  **sofort** den einmaligen Abo→API-Fallback; der API-Key hat ein **eigenes,
  separates Kontingent**. Erst wenn **auch** der API-Weg 429/Fehler liefert,
  greift die 429-Sonderbehandlung.
- **A) Auto-Retry mit hartem Deckel.** Bleibt das finale Ergebnis 429, parst
  `team_claude()` die Reset-Zeit (12h→24h, tageswechselsicher), wartet
  (Reset + Puffer) und wiederholt — begrenzt durch `TEAM_429_MAX_RETRIES` /
  `TEAM_429_MAX_WARTEN` / `TEAM_429_PUFFER`. Unbekannter/zu ferner Reset → sofort B.
- **B) Sauberer Pausen-Exit.** Sind die Retries erschöpft, gibt `team_claude()`
  **Exit-Code 42** zurück (statt des generischen Fehlers). **Alle** Loop-Rollen
  (`ralph.sh`, `redteam.sh`, `frank.sh`, `axel.sh`) reichen 42 **unverändert als
  eigenen Exit 42** durch — **kein** State-Fortschritt, **kein** Fehlversuchs-
  Zähler (`.frank-attempts` bleibt stehen, keine Axel-Eskalation). Der
  Read-Only-Guard läuft dabei auf **jedem** Pfad (auch Pause), bevor der Code
  ausgewertet wird. `vollautomatik.sh` erkennt Exit 42 in **allen** Phasen und
  beendet mit „⏸ Session-Limit erreicht — Lauf pausiert, bitte später erneut
  starten. Kein Fehler, kein Datenverlust." statt der „ECHTER Fehler"-Meldung.
- **Auslauf-Bremse gegen Leerlaufkosten (`TEAM_FIX_MAX_STAGNATION`, `BL-25`).**
  Die Frank↔Axel-Fixphase vergleicht pro Runde einen Beutebuch-Snapshot
  vorher/nachher; bleibt **weder** ein Frank-Fix **noch** eine neue Axel-Akte
  **noch** ein Status­wechsel, zählt eine `stagnation`-Kennzahl hoch. Ab
  `TEAM_FIX_MAX_STAGNATION` Runden in Folge bricht die Phase mit „⛔ Fix-Phase
  stagniert — Mensch prüfen" ab, statt bis zur groben Obergrenze
  `TEAM_MAX_RUNDEN` teuer leerzudrehen. Realer Auslöser: ~13,8 USD Leerlauf über
  8 Runden am selben Fund. **Kein** Aufweichen echter Fehler — die Bremse misst
  ausschließlich Fortschritt.

> Die numerischen **Default-Deckel** dieser Env-Knöpfe (`TEAM_429_*`,
> `TEAM_FIX_MAX_STAGNATION`, `TEAM_MAX_RUNDEN`) stehen in **Anhang A.8** — hier
> zählt der Verhaltens-Vertrag, nicht die Feinabstimmung.

---

## Kostenkontrolle

**Modell / Plattform**: {{z. B. Sonnet via Claude Code — Loop-Rollen;
{{starkes-modell}} — Axel/Architekt}} (**Modell** und **Auth** sind zwei
getrennte Achsen: starkes Modell heißt **nicht** API — auch Axel und Der
Architekt laufen Abo-first, siehe „Loop-Mechanik & Auth")
**Budget-Modell (Zwei-Schwellen, ✅ erprobt, `BL-30`)**: Ein zentraler
**Soft-Cap** `{{TEAM_ROLE_BUDGET_USD}}` (Default **5 USD**) gilt für **alle**
Rollen; ein **Hard-Cap** `{{TEAM_ROLE_HARDCAP_USD}}` (Default **10 USD**) nur für
die iterierenden „Sorgenkinder" **Frank & Axel**. Beide zentral in `team-lib.sh`
— **eine** Zahl statt mehrerer divergierender Defaults.
**Warnschwelle**: 80 % des Soft-Caps — reiner Hinweis, kein Abbruch.

**Zwei-Schwellen-Modell — warum (realer Auslöser HM-32, 2026-07-12):** Ein zu
tiefer Pro-Fall-Cap greift **nach** dem bereits bezahlten Claude-Aufruf. Als ein
Frank-Versuch den 1-USD-Cap sprengte, wertete der Loop den plausiblen Fix als
Fehlversuch und warf ihn per **Rollback** weg — der nächste Versuch kostete
erneut. Der zu tiefe Cap „sparte" nichts, sondern **vervielfachte** die Kosten.
Konsequenz:

- **Frank & Axel:** ein überschrittener **Soft-Cap** ist nur ein **Hinweis** —
  **kein** Rollback, **kein** Fehlversuch; Fix/Akte bleiben gültig und werden
  normal geprüft. Erst der **Hard-Cap** (echter Ausreißer: Endlosschleife/
  kaputter Prompt) bricht mit Rollback+Cleanup ab.
- **Ralph/Harry/Marv:** sofortiger **Hard-Cap** beim Soft-Wert (kein
  Soft-Fenster) — Ralph stoppt nach dem Commit **vor** dem
  {{state-Datei}}-Weiterschalten (kein Rollback, Mensch schaltet weiter),
  Harry/Marv sind read-only (nichts Bezahltes geht verloren).

Übersteuerung je Lauf/Rolle bleibt möglich (`RALPH_BUDGET_USD`,
`ROLE_BUDGET_USD`/`ROLE_HARDCAP_USD`, `{{AXEL-Soft-Cap}}`/`{{AXEL-Hard-Cap}}`).

**Grundregeln (stets aktiv):**

- Das starke/teure Modell (Axel, Architekt) läuft **nie im Dauer-Loop**; jede
  Iteration/jeder Fall bekommt ein hartes Budget-Cap.
- Loop-Rollen nutzen das günstige Modell (Default {{Modell-Default}}), pro Rolle
  über Env übersteuerbar (`{{TEAM_MODEL_LOOP}}`).

**Token-Sparregeln (optional, projektabhängig — Default AUS):**

> Diese Regeln senken die Kosten, können aber die **Arbeitsqualität**
> beschneiden — besonders bei Axel, der für die Root-Cause-Analyse **tief lesen
> können muss**. Nur aktivieren, wenn das Budget es erzwingt, und Axel möglichst
> **ausnehmen**.
>
> - Dateien nur einmal pro Stufe/Fall lesen (Ausnahme: Axel darf nachlesen).
> - Bei großen Dateien mit `offset`/`limit` gezielt lesen statt komplett.
> - Antworten knapp halten, wenn nicht ausdrücklich mehr gefragt ist.

**Interaktive Akteur-Kosten erfassen (✅ erprobt, `BL-28`/`BL-29`/`BL-33`,
website-maxron-de 2026-07-12):** **Interaktiv** (außerhalb `team_claude`)
arbeitende Rollen — **Der Architekt** und **Frank-im-Abo** — schreiben **keine**
`total_cost_usd`-JSONs und fallen sonst durch die automatische Kostenerfassung
(die nur `.{{rolle}}-logs/` liest). Realer Auslöser: eine einzelne
Architekten-Session kostete laut Konsole **~16 USD** — strukturell unerfasst.
Operativer Vertrag (Bau-Details in **Anhang A.9**):

- **A2 — grobe Live-Schätzung (nur Architekt):** `--budget` zeigt eine aus dem
  Plan-/Doku-Churn abgeleitete **Größenordnung** der laufenden Architekt-Kosten,
  klar als „geschätzt" markiert — **nie** persistiert, nie als exakter Wert
  getarnt.
- **A1 — Wert beim Kaskaden-Abschluss (rollen-agnostisch), auth-abhängig:**
  `./team-status.sh --akteur-abschluss <rolle> <auth:abo|api> <USD> <domaene> ["<notiz>"]`.
  Das Tool **ersetzt** (statt verdoppelt) eine vorhandene Zeile **derselben Rolle
  + Kaskade** — Schätzung und eingetragener Wert zählen so nie doppelt.
  `--architekt-abschluss` bleibt als dünner Alias.
  - **Im API-Betrieb:** Der {{Strippenzieher}} liest den realen Verbrauch aus der
    Anbieter-Konsole ab und trägt ihn ein.
  - **Im Abo-Betrieb (Regelfall, seit alle Rollen Abo-first laufen):** Es gibt
    **keinen** Konsolenwert — im Abo wird nichts abgerechnet. Persistiert wird
    stattdessen die **A2-Schätzung als Abo-Gegenwert** (`… <rolle> abo <USD> …`).
    Das ist die belastbarste Zahl, die das Abo zulässt; sie **nie** stillschweigend
    als abgerechneten Betrag ausgeben.
- **Ledger domänengetrennt (`BL-29`):** `--budget` weist die Kosten nach Domäne
  getrennt aus; das Ledger-Schema ist rückwärtskompatibel um `domaene`/`rolle`
  erweitert (Altzeilen bleiben „unzugeordnet").

**Kaskadenabschluss-Pflicht (✅ erprobt, website-maxron-de bis Kaskade 22):** Pro
gebauter Kaskade sind **zwei** Dinge Pflicht — das **Abschluss-Doc**
(Planungsregel 5) **und** der **Kostenabschluss** über das Kontostand-Werkzeug
(`--rollen-abschluss`, `--akteur-abschluss`), beides **nach** dem Lauf im
Architekten-Closeout, **nie** in einer Loop-Stufe (siehe A.7, Lehre 8).

> **Feld-Lehre gegen die naheliegende Alternative:** Eine fortgeschriebene
> Kosten-Prosaseite (`wiki/kosten.md` o. Ä.) als Abschlusspflicht **trägt nicht**
> — im Feld blieb genau diese Seite bei Kaskade 17 stehen, während die Kaskaden
> weiterliefen, und die Regel zeigte ins Leere. Konsequenz: Prosa-Kostenseite als
> **Historie einfrieren**, maschinelle Wahrheit ist die committete
> {{ledger-datei}} plus das Kontostand-Werkzeug; die erzählende Auswertung je Lauf
> übernimmt das Abschluss-Doc.

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
   Kosten-Politik (Modell und Auth sind **zwei getrennte Achsen**): Loop-Rollen =
   günstiges Modell (Sonnet) + Abo-first mit API-Fallback. **Axel** = starkes
   Modell (Opus/Fable) **auch im Abomodus**, Auth aber ebenfalls Abo-first mit
   Fallback. **Der Architekt** = starkes Modell, Auth ebenfalls **Abo-first**
   (interaktiv, kein Loop) — **keine** Rolle ist fest API. Budget-Limits siehe
   `## Kostenkontrolle`."
10. **`{{TEAM_ROLE_BUDGET_USD}}` / `{{TEAM_ROLE_HARDCAP_USD}}`** → „Budget-Schwellen
    (Default 5 / 10 USD): der zentrale **Soft-Cap** (Hinweis) gilt für alle Rollen,
    der **Hard-Cap** (Abbruch) für die iterierenden Frank & Axel. Axel-eigene
    Werte optional als `{{AXEL-Soft-Cap}}`/`{{AXEL-Hard-Cap}}`."
11. **`{{ledger-datei}}` / `{{ARCHITEKT_USD_PRO_CHURN_ZEILE}}`** → „Optional, falls
    Budget-Governance gewünscht (A.7–A.10): committete Ledger-Datei (Default
    `.budget-ledger`) und Eichfaktor für die Architekt-Live-Schätzung (an einer
    realen Session eichen)."

Trage die Antworten anschließend selbst ein und speichere die `CLAUDE.md`. Der
{{Strippenzieher}} macht das nicht selbst.
````

---

## Anhang A — Team-Skripte kontextabhängig generieren

**Ausgelagert:** [team-skripte-generieren](../anleitungen/team-skripte-generieren.md)

Die Bau-Anleitung für die Team-Skripte (A.1 Vorbedingungs-Check, A.2 Generierungs-Reihenfolge,
A.3 Auth-Fallback, A.4 Read-Only-Guard, A.5 Faktencheck-Pflicht, A.6 Parallelität,
A.7 Budget-Governance & acht Feld-Betriebslehren, A.8 Session-Limit-Robustheit,
A.9 Interaktive Akteur-Kosten, A.10 Doku-Konsolidierung) steht seit 2026-08-01 auf einer
eigenen Seite. Die Abschnittsnummern sind unverändert — Verweise wie „siehe Anhang A.7"
zeigen weiterhin auf denselben Abschnitt.

**Grund:** Anhang A ist **Einrichtungsarbeit** (einmal beim Aufsetzen gelesen), der Rest dieser
Seite beschreibt die **Betriebsregeln** (bei jeder Änderung an der Vorlage gelesen). Zusammen
waren es 69,8 KB in einer Datei. Das ist genau der Fall, den A.10 für die Regeldatei des
Zielprojekts beschreibt — hier auf die Vorlage selbst angewandt.

## Verwandte Seiten

- [team-skripte-generieren](../anleitungen/team-skripte-generieren.md) — Anhang A: die vollständige Bau-Anleitung für die Team-Skripte (A.1–A.10)
- [claude-md-ki-team](../quellen/claude-md-ki-team.md) — Quellenseite mit Einordnung und Namenskonflikt-Historie
- [ki-lehrer-app](../konzepte/ki-lehrer-app.md) — Das reale Ursprungsprojekt (KI-Lehrer-App)
- [finder-fixer-prinzip](../konzepte/finder-fixer-prinzip.md) — Die Gewaltenteilungs-Regel hinter Harry/Marv/Axel vs. Frank
- [read-only-guard](../konzepte/read-only-guard.md) — Die 3-Linien-Durchsetzung von Anhang A.4
- [ralph-schleife](../konzepte/ralph-schleife.md) — Das Loop-Muster, das Ralph in dieser Vorlage nutzt
- [kostencounter](../konzepte/kostencounter.md) — Kostenkontroll-Standard dieses Wikis
- [claude-md-team](claude-md-team.md) — Namensvetter mit anderem Thema (Menschen-Team statt KI-Rollenteam)
- [claude-md-lehrer](claude-md-lehrer.md) — Format-Vorbild dieser Vorlage
- [feldtest-sperre](../konzepte/feldtest-sperre.md) — Verwandtes Muster aus demselben Ursprungsprojekt (Freeze statt Rollenteilung)
- [ki-team-forensik](../konzepte/ki-team-forensik.md) — Konzeptskizze: dieses Rollenteam für Legacy-Forensik umgebaut (Quartett + Lotse)

---

[Wiki-Index](../index.md)
