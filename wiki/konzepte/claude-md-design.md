---
date: 2026-04-23
type: konzept
tags: [konzept, schema]
status: active
---

# CLAUDE.md-Design: Anweisungsdateien für LLM-Wikis

**Zusammenfassung**: Erkenntnisse aus der Praxis — wie man eine effektive Instruktionsdatei für ein LLM-gesteuertes Wiki gestaltet und welche Fehler man vermeiden sollte.
**Quellen**: Eigene Erfahrung aus der Pflege eines YouTube-Verlauf-Wikis; übertragbar auf alle LLM-Wiki-Projekte
**Zuletzt aktualisiert**: 2026-05-14 (Designprinzip 8: Git-Setup hinzugefügt)

---

## Was ist eine CLAUDE.md?

Eine CLAUDE.md (oder allgemeiner: eine LLM-Instruktionsdatei) ist eine Markdown-Datei im Projektstamm, die einem AI-Coding-Assistenten wie [Claude Code](../werkzeuge/claude-code.md) die Regeln, Strukturen und Workflows eines Projekts erklärt. Sie ist das „Betriebssystem" des Projekts — was für Menschen ein Onboarding-Dokument wäre, ist für die AI die CLAUDE.md.

Im Kontext des [LLM-Wiki-Musters](llm-wiki-muster.md) bildet die CLAUDE.md die **dritte Ebene** der [drei-ebenen-architektur](drei-ebenen-architektur.md): das Schema, das die Verarbeitung von Rohquellen zu Wiki-Seiten steuert.

## Designprinzip 1: Direkte Anweisungen, keine Meta-Vorlagen

**Problem**: Die CLAUDE.md wurde versehentlich zu einer *Vorlage über eine Vorlage* umgeschrieben — die eigentlichen Regeln steckten in einem 4-Backtick-Code-Fence (` ```` `). Claude liest Inhalte in Code-Fences als **Illustration**, nicht als operative Anweisungen.

**Lösung**: Alles, was Claude tun soll, muss als direkte Prosa auf Top-Level stehen. Templates für Seitenformate gehören in normale Code-Fences (3 Backticks), aber die *Regeln selbst* dürfen nie in einem Template-Wrapper stecken.

**Faustregel**: Wenn du den Inhalt entfernen könntest und Claude trotzdem korrekt arbeiten würde — dann liest Claude ihn nicht als Anweisung.

**Konsequenz für Vorlagen in diesem Wiki**: Die Vorlagen unter `wiki/vorlagen/` enthalten den kopierbaren Vorlagentext bewusst in 4-Backtick-Fences — das ist korrekt, weil es dort als *Referenzmaterial* dient. Aber sobald jemand die Vorlage in ein neues Projekt kopiert, muss der Fence entfernt werden — die Regeln müssen auf Top-Level stehen.

## Designprinzip 2: Link-Konsistenz zwischen Instruktion und Output

**Problem**: Eine überarbeitete CLAUDE.md verwendete `[[wiki-links]]` (Obsidian-Syntax), während das gesamte Wiki durchgehend `[text](pfad.md)` (Standard-Markdown) nutzte. Das ist ein Widerspruch: Claude sieht einen Stil in der Instruktionsdatei und einen anderen im Output. Das kann zur Kontamination des Wikis mit dem falschen Link-Stil führen. (Siehe auch [kontaminierungsrisiko](kontaminierungsrisiko.md))

**Lösung**: Die Instruktionsdatei muss denselben Link-Stil verwenden wie das Wiki selbst. Wenn das Wiki Standard-Markdown nutzt, muss auch die CLAUDE.md Standard-Markdown nutzen.

## Designprinzip 3: Keine Referenzen auf nicht-existierende Seiten

**Problem**: Eine CLAUDE.md referenzierte Seiten, die in einem anderen Obsidian-Vault existierten, aber nicht in diesem Repo. Claude kann diese Referenzen nicht auflösen und ignoriert sie oder halluziniert Zusammenhänge.

**Lösung**: Jede Referenz in der CLAUDE.md muss entweder auf eine existierende Datei im Repo verweisen oder als Konzept inline erklärt werden. Keine toten Links. Das ist verwandt mit der [lint-pruefung](lint-pruefung.md) — auch dort wird nach verwaisten Links gesucht.

## Designprinzip 4: Frontmatter nur wo nötig

**Problem**: YAML-Frontmatter (`---` Blöcke mit `date`, `type`, `tags`) wurde zur CLAUDE.md hinzugefügt, obwohl keine einzige Wiki-Seite Frontmatter verwendete. Das schafft eine Inkonsistenz: Claude könnte anfangen, Frontmatter in neue Wiki-Seiten einzubauen.

**Lösung**: Frontmatter-Nutzung muss konsistent sein. Wenn das Wiki [YAML-Frontmatter](yaml-frontmatter.md) verwendet (wie dieses Wiki hier), dann gehört es auch in die CLAUDE.md. Wenn nicht, dann nicht. Die Regel ist **Konsistenz**, nicht Frontmatter an sich.

## Designprinzip 5: Wertvolle Ergänzungen als Top-Level-Sektionen

Bestimmte Ergänzungen bringen erst dann Nutzen, wenn sie als eigenständige Sektionen in der CLAUDE.md stehen statt in einem Template-Wrapper zu verstecken:

| Ergänzung | Nutzen |
|---|---|
| **3-Stufen-Tiefe** (Minimal/Mittel/Vollständig) | Vermeidet Über- oder Unterverarbeitung von Quellen (siehe [ingest-workflow](ingest-workflow.md)) |
| **Extraktionsstrategien nach Quelltyp** | Verschiedene Quellen brauchen verschiedene Schwerpunkte |
| **Benannte Abfragetypen** | Standardisierte Frage-Muster (siehe [query-templates](query-templates.md)) |
| **[Kontaminierungsrisiko](kontaminierungsrisiko.md)-Regel** | Zusammenfassungen müssen gegen Rohquellen prüfbar bleiben |
| **Skalierungshinweise** | Plan B für 100+ Seiten (siehe [skalierungsgrenzen](skalierungsgrenzen.md), [qmd](../werkzeuge/qmd.md), [jdocmunch](../werkzeuge/jdocmunch.md)) |

## Designprinzip 6: Die CLAUDE.md entwickelt sich mit dem Projekt

Eine CLAUDE.md ist kein statisches Dokument. Sie sollte regelmäßig überprüft und angepasst werden, wenn:

- Neue Ordner oder Seitentypen entstehen
- Wiederkehrende Fehler ein neues Muster erfordern
- Das Wiki eine Größe erreicht, bei der bestehende Konventionen nicht mehr skalieren (siehe [skalierungsgrenzen](skalierungsgrenzen.md))

Im YouTube-Verlauf-Projekt wuchs die CLAUDE.md von ~100 Zeilen (initiales Setup) auf ~190 Zeilen (nach Integration aller Verbesserungen) über 7 Aufnahme-Zyklen.

## Designprinzip 7: Platzhalter-Interview beim ersten Start

Jede Vorlage mit `{{PLATZHALTER}}`-Feldern muss im ersten Start explizit anweisen, alle noch offenen Platzhalter durch ein Interview zu ermitteln — bevor irgendein anderer Workflow startet.

**Problem ohne dieses Prinzip**: Das Modell liest `{{Name}}` in der CLAUDE.md und ignoriert es stillschweigend, erfindet einen Wert, oder arbeitet mit dem Platzhaltertext weiter.

**Lösung**: Der erste Schritt jedes Aufnahme-Workflows prüft explizit auf `{{PLATZHALTER}}` und fragt jeden offenen Wert einzeln nach — nicht als Liste, sondern als natürliches Gespräch:

```
1. Prüfe [Abschnitt mit Platzhaltern] auf noch nicht ausgefüllte {{PLATZHALTER}}.
   Für jedes offene Feld stelle eine freundliche Frage — eines nach dem anderen:
   - {{Name}} → „Wie heißt du?"
   - {{Ziel}} → „Was soll am Ende entstehen?"
   - ...
   Trage die Antworten als Kontext ein — der Nutzer ändert die CLAUDE.md nicht selbst.
```

**Warum einzeln, nicht als Liste**: Eine Fragenliste wirkt wie ein Formular. Einzelne Fragen fühlen sich wie ein Gespräch an — das ist besonders bei Lern- und Kreativprojekten wichtig.

**Für zukünftige Vorlagen**: Jede neue Vorlage muss diesen Schritt als erstes Element ihres Initialisierungs-Workflows enthalten.

## Designprinzip 8: Git-Repository beim ersten Start einrichten

Jede Vorlage soll beim ersten Start prüfen, ob Git verfügbar ist, und — wenn ja — automatisch ein Repository anlegen und den ersten Commit erstellen. Ist Git nicht installiert, wird der Nutzer freundlich informiert und gefragt, ob er es installieren möchte oder ohne Git weitermachen will.

**Problem ohne dieses Prinzip**: Fortschritte, Lernstände und generierte Dateien gehen verloren, wenn das Modell versehentlich Dateien überschreibt oder der Nutzer eine frühere Version zurückbraucht.

**Lösung**: Der zweite Schritt jeder Initialisierung (nach dem Platzhalter-Interview) richtet Git ein:

```
2. Git-Setup:
   Prüfe ob Git verfügbar ist (git --version):
   - Verfügbar und kein Repo vorhanden: git init ausführen, .gitignore anlegen
     (typische Einträge: *.pyc, __pycache__/, .env, node_modules/, dist/),
     ersten Commit erstellen: „Projekt initialisiert"
   - Verfügbar und Repo bereits vorhanden: nichts tun, kurz bestätigen
   - Nicht gefunden: freundlich mitteilen:
     „Git wurde nicht gefunden. Git sichert deinen Fortschritt automatisch.
      (j) Git installieren: https://git-scm.com — danach neu starten
      (n) Ohne Git weitermachen"
     Warte auf Antwort bevor du fortfährst.
```

**Warum .gitignore anlegen**: Ohne .gitignore landen Build-Artefakte, virtuelle Umgebungen und Secrets versehentlich im Repo — das ist bei Lern- und Kreativprojekten besonders häufig.

**Commit-Rhythmus**: Nach der Initialisierung soll das Modell am Ende jeder Sitzung oder nach bedeutenden Änderungen automatisch einen kurzen Commit erstellen (`git add -A && git commit -m "Kurzbeschreibung"`).

**Für zukünftige Vorlagen**: Jede neue Vorlage muss diesen Schritt als zweites Element ihres Initialisierungs-Workflows enthalten — direkt nach dem Platzhalter-Interview (Designprinzip 7).

## Zusammenfassung der Lessons Learned

1. **Operative Regeln auf Top-Level** — nie in Code-Fences oder Template-Wrapper
2. **Ein Link-Stil überall** — Instruktion und Output müssen konsistent sein
3. **Keine toten Referenzen** — alles Verlinkte muss existieren oder inline erklärt werden
4. **Frontmatter-Konsistenz** — wenn die Wiki-Seiten Frontmatter haben, hat die CLAUDE.md auch welches; wenn nicht, dann nicht
5. **Wertvolle Sektionen auf Top-Level** — Tiefe-Auswahl, Abfragetypen, Kontaminierungsregeln direkt als Prosa
6. **Regelmäßig reviewen** — die CLAUDE.md altert mit dem Projekt
7. **Platzhalter-Interview** — beim ersten Start alle `{{PLATZHALTER}}` durch Einzelfragen ermitteln, nie stumm übernehmen
8. **Git-Setup** — beim ersten Start Repo anlegen (oder auf fehlendes Git hinweisen), danach regelmäßig committen

## Verwandte Seiten

- [drei-ebenen-architektur](drei-ebenen-architektur.md) — CLAUDE.md ist die dritte Ebene (Schema)
- [llm-wiki-muster](llm-wiki-muster.md) — Das übergeordnete Konzept, das die CLAUDE.md steuert
- [kontaminierungsrisiko](kontaminierungsrisiko.md) — Designprinzip 2 und 5 adressieren dieses Risiko direkt
- [lint-pruefung](lint-pruefung.md) — Designprinzip 3 (keine toten Links) ist eine Lint-Regel
- [yaml-frontmatter](yaml-frontmatter.md) — Designprinzip 4 betrifft die Frontmatter-Entscheidung
- [query-templates](query-templates.md) — Designprinzip 5: Benannte Abfragetypen als Top-Level-Sektion
- [skalierungsgrenzen](skalierungsgrenzen.md) — Designprinzip 6: Wann die CLAUDE.md wachsen muss
- [claude-md-software](../vorlagen/claude-md-software.md) — Vorlage, die diese Prinzipien anwenden sollte
- [claude-md-legacy-forensik](../vorlagen/claude-md-legacy-forensik.md) — Vorlage, die diese Prinzipien anwenden sollte
- [claude-md-youtube-verlauf](../vorlagen/claude-md-youtube-verlauf.md) — Die Vorlage, aus deren Praxis diese Prinzipien stammen

---

[Wiki-Index](../index.md)
