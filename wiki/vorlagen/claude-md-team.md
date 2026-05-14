---
date: 2026-05-03
type: vorlage
tags: [vorlage, schema, team]
status: active
---

# CLAUDE.md-Vorlage: Team-Wiki (2–8 Personen)

**Zusammenfassung**: Eine CLAUDE.md-Vorlage für kleine Teams, die ein gemeinsames LLM-Wiki pflegen. Löst das Kern-Problem des Enterprise-Musters auf Mini-Skala: Wer ist für welche Seite verantwortlich, wie werden Konflikte vermieden und wie bleibt das Wiki lebendig ohne Vollzeitkurator.
**Quellen**: Abgeleitet aus [enterprise-skalierung](../konzepte/enterprise-skalierung.md), [llm-wiki-v2](../konzepte/llm-wiki-v2.md), [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md), [lernschleifen](../konzepte/lernschleifen.md) und [claude-md-software](claude-md-software.md)
**Zuletzt aktualisiert**: 2026-05-14

---

## Warum eine eigene Team-Vorlage

Das persönliche LLM-Wiki funktioniert, weil **eine Person** alle Entscheidungen trifft und die Wartungsschleife antreibt. Sobald zwei Menschen an einem Wiki arbeiten, entstehen drei neue Probleme:

1. **Ownership** — Wer aktualisiert welche Seite? Ohne klare Zuordnung: niemand
2. **Konflikte** — Wenn zwei Leute gleichzeitig dieselbe Seite ändern, wer hat Recht?
3. **Vertrauen** — Wenn mehrere Autoren (Mensch + KI) schreiben, wie erkennt man den Status eines Eintrags?

Diese Vorlage löst alle drei — ohne Falconer-Infrastruktur, ohne Jira, ohne Tooling außer Git und Markdown.

## Wann diese Vorlage passt

- 2–8 Personen teilen ein gemeinsames Wiki (Studiengruppe, Startup-Team, kleine Abteilung)
- Gemeinsames Thema: Projektdokumentation, Lerngruppe, gemeinsame Recherche
- Jede Person hat ihren eigenen Claude Code Zugang
- Git wird als Synchronisierungskanal genutzt

**Nicht geeignet für**: Solo-Projekte (→ Software-Vorlage), >20 Personen (→ dediziertes Enterprise-Tooling)

## Kernkonzepte

### Ownership-Marker

Jede Wiki-Seite hat einen klar benannten Verantwortlichen in ihrem Frontmatter. Nur diese Person merged finale Änderungen; andere schreiben Entwürfe.

### Provenienz-Tags

Jeder Eintrag trägt sichtbar seinen Ursprung:
- `(Mensch)` — human-reviewed, vertrauenswürdig
- `(KI-Entwurf)` — LLM-generiert, noch nicht menschlich geprüft
- `(KI-geprüft)` — LLM-generiert + menschlich validiert
- `(überprüfungsbedürftig)` — unsicher, braucht Review

### Ingest-Prinzip: Entwurf zuerst

Wenn die KI eine neue Seite schreibt oder eine bestehende aktualisiert, ist das zunächst ein **Entwurf**. Der Verantwortliche prüft und merged — erst dann ist es `(KI-geprüft)`. Das verhindert unkontrollierte [Kontaminierung](../konzepte/kontaminierungsrisiko.md) des gemeinsamen Wissens.

## Ordnerstruktur

```
mein-team-wiki/
  CLAUDE.md              ← Diese Vorlage
  wiki/
    index.md             ← Inhaltsverzeichnis + Ownership-Tabelle
    log.md               ← Chronologisches Änderungsprotokoll
    konzepte/            ← Geteiltes Konzeptwissen (alle lesen, Owner schreibt)
    personen/            ← Personenprofile, Kontexte (individuell, aber geteilt)
    entscheidungen/      ← Gruppenentscheidungen (ADRs)
    entwuerfe/           ← KI-Entwürfe vor menschlicher Prüfung
  raw/                   ← Unveränderliche Quellen (jeder trägt bei)
  clippings/             ← Unveränderliche Web-Artikel (jeder trägt bei)
```

## Benutzung

1. Kopiere **nur den Inhalt** des Vorlagenblocks unten (ohne ` ```` ` Fence-Markierungen) als `CLAUDE.md`
2. Ersetze alle `{{PLATZHALTER}}`
3. Lege `wiki/index.md` mit Ownership-Tabelle an (Abschnitt unten)
4. Jedes Teammitglied klont das Repo und nutzt eigenen Claude Code
5. KI-Entwürfe landen in `wiki/entwuerfe/` → Verantwortlicher prüft → moved in den richtigen Ordner

## Designhinweise

1. **Ownership ist der Kern** — Ohne klare Zuordnung degeneriert jedes Team-Wiki zur Allmende, die niemand pflegt
2. **Git ist der Synchronisierungskanal** — Commits + Pull Requests als Merge-Gate für KI-generierte Inhalte
3. **`entwuerfe/` als Pufferzone** — Trennt rohes KI-Output von validiertem Wissen; verhindert unkontrollierten Drift
4. **Fence entfernen beim Kopieren** — Vorlage ist in einem 4-Backtick-Fence dargestellt (wie alle Vorlagen hier); entfernen wenn produktiv genutzt

## Vorlage

````markdown
# CLAUDE.md — Team-Wiki

> **Anleitung**: Kopiere diese Datei als `CLAUDE.md` ins Projekt-Wurzelverzeichnis.
> Ersetze alle `{{PLATZHALTER}}`. Entferne diesen Block vor der produktiven Nutzung.

---

# CLAUDE

## Team

**Projektname**: {{NAME DES WIKIS / PROJEKTS}}
**Beschreibung**: {{Was sammelt dieses Wiki? Für wen?}}
**Teammitglieder**: {{Liste der Namen mit kurzer Rolle}}

## Deine Rolle

Du unterstützt das Team beim Aufbau und der Pflege eines gemeinsamen Wikis. Du schreibst Entwürfe, fasst zusammen, findest Widersprüche und machst Verlinkungsvorschläge.

**Wichtig**: Du schreibst keine finalen Seiten direkt in `wiki/konzepte/` oder `wiki/entscheidungen/`. Entwürfe kommen nach `wiki/entwuerfe/` — ein Teammitglied prüft und merged.

## Ordnerstruktur

```
wiki/
  index.md             -- Inhaltsverzeichnis + Ownership-Tabelle
  log.md               -- Änderungsprotokoll
  konzepte/            -- Geteiltes Konzeptwissen
  entscheidungen/      -- Gruppenentscheidungen (ADRs)
  personen/            -- Personenprofile (optional)
  entwuerfe/           -- KI-Entwürfe vor menschlicher Prüfung
raw/                   -- Unveränderliche Originalquellen (niemals bearbeiten)
clippings/             -- Unveränderliche Web-Clips (niemals bearbeiten)
```

## Ownership

Jede Wiki-Seite hat einen Owner im Frontmatter:

```yaml
---
owner: {{name}}
date: YYYY-MM-DD
type: konzept
status: entwurf | geprüft | veraltet
---
```

Die Ownership-Tabelle in `wiki/index.md` gibt einen Überblick. Nur der Owner merged finale Änderungen an seiner Seite. Entwürfe für fremde Seiten gehen nach `wiki/entwuerfe/fremdname-entwurf.md`.

## Provenienz-Tags

Jeden Eintrag klar kennzeichnen:

- `(Mensch)` — vom Teammitglied direkt geschrieben oder final geprüft
- `(KI-Entwurf)` — von der KI generiert, noch nicht menschlich validiert
- `(KI-geprüft)` — KI-generiert, vom zuständigen Teammitglied reviewed
- `(überprüfungsbedürftig)` — unsicher, Review nötig

## Initialisierung (erster Start)

Beim allerersten Start — bevor irgendwas anderes passiert:

1. **Prüfe `## Team`** auf noch nicht ausgefüllte `{{PLATZHALTER}}`. Für jedes offene Feld stelle eine freundliche Frage — eines nach dem anderen, nicht als Liste:
   - `{{NAME DES WIKIS / PROJEKTS}}` → „Wie soll das Team-Wiki heißen?"
   - `{{Beschreibung}}` → „Was sammelt dieses Wiki, und für wen ist es gedacht?"
   - `{{Teammitglieder}}` → „Wer gehört zum Team? Nenn kurz Namen und Rollen (z.B. 'Alice — Architektur, Bob — Frontend')."
   Trage die gesammelten Antworten anschließend in `## Team` ein — ersetze die Platzhalter und speichere CLAUDE.md. Der Nutzer macht das nicht selbst.
2. **Git-Setup**: Prüfe ob Git verfügbar ist (`git --version`):
   - Verfügbar und kein Repo vorhanden: `git init` ausführen, `.gitignore` anlegen, ersten Commit erstellen: „Projekt initialisiert"
   - Verfügbar und Repo bereits vorhanden: nichts tun
   - Nicht gefunden: „Git wurde nicht gefunden. Git sichert deinen Fortschritt automatisch. (j) Git installieren: https://git-scm.com — danach neu starten | (n) Ohne Git weitermachen" — warte auf Antwort
3. Lege `wiki/index.md` mit leerer Ownership-Tabelle an, falls noch nicht vorhanden
4. Warte auf die erste Quelle oder Anfrage

## Aufnahme-Workflow (Ingest)

Wenn ein Teammitglied eine neue Quelle hinzufügt:

1. Quelle kommt nach `raw/` oder `clippings/` (unveränderlich)
2. KI liest die Quelle, erstellt Entwurf-Seite in `wiki/entwuerfe/`
3. Entwurf ist mit `(KI-Entwurf)` gekennzeichnet
4. Zuständiges Teammitglied prüft, korrigiert und moved nach `wiki/konzepte/` oder passenden Ordner
5. Status wird auf `geprüft` gesetzt, `(KI-Entwurf)` → `(KI-geprüft)`
6. `wiki/index.md` und `wiki/log.md` aktualisieren

## Abfrage-Workflow (Query)

1. Lies `wiki/index.md` für relevante Seiten
2. Beantworte die Frage auf Basis der geprüften Wiki-Seiten
3. Kennzeichne was aus `(KI-Entwurf)` vs. `(geprüft)` Seiten stammt
4. Wenn die Antwort einen neuen Eintrag verdient: Entwurf nach `wiki/entwuerfe/`

## Konflikte

Wenn zwei Wiki-Seiten einander widersprechen:

1. Zeige beide Aussagen nebeneinander
2. Benenne die Quelle jeder Aussage
3. Markiere mit `(Widerspruch — Klärung nötig: {{namen}})`
4. Erstelle keinen eigenen Entscheid — das ist eine Team-Entscheidung

## Prüfung (Lint)

Wenn das Team das Wiki prüfen lässt:

- Verwaiste Seiten finden (keine eingehenden Links)
- Seiten ohne Owner finden
- `status: veraltet` Seiten finden, die aktualisiert werden sollten
- `(KI-Entwurf)` Seiten finden, die länger als 2 Wochen nicht geprüft wurden
- Widersprüche zwischen Konzeptseiten markieren
- Seiten ohne Provenienz-Tag melden
- Bei unlösbaren Widersprüchen: To-do-Liste für das Team erstellen

## Regeln

- **Entwürfe immer nach `wiki/entwuerfe/`** — niemals direkt in finale Ordner schreiben
- Verändere **NIEMALS** etwas in `raw/` oder `clippings/`
- Aktualisiere immer `wiki/index.md` und `wiki/log.md` nach Änderungen
- Seitennamen: Kleinbuchstaben mit Bindestrichen
- Kennzeichne jeden Eintrag mit einem Provenienz-Tag — Transparenz ist wichtiger als Schönheit
- Wiki-Seiten mit Standard-Markdown-Links: `[text](pfad.md)` — keine Obsidian-`[[wiki-links]]`
- Wenn du dir unsicher bist, wer Owner einer neuen Seite sein sollte: frage nach

## Skalierung

Wenn das Wiki wächst:
- **qmd** (`npm install -g @tobilu/qmd`): Semantische Suche über alle Wiki-Seiten
- **jDocMunch** (`pip install jdocmunch-mcp`): Nur relevante Abschnitte laden
- Ab ~20+ Personen: Falconer oder vergleichbare Enterprise-Lösung erwägen
````

## Index-Format (Ownership-Tabelle)

In `wiki/index.md` sollte eine Ownership-Tabelle am Anfang stehen:

```markdown
## Ownership

| Seite | Owner | Status | Zuletzt aktualisiert |
|---|---|---|---|
| konzepte/kernkonzept.md | Alice | geprüft | 2026-05-01 |
| konzepte/prozess.md | Bob | entwurf | 2026-04-28 |
| entscheidungen/2026-04-15-xyz.md | Alice, Bob | geprüft | 2026-04-15 |
```

Das macht auf einen Blick sichtbar, wer was verantwortet und wie aktuell es ist.

## Verwandte Seiten

- [enterprise-skalierung](../konzepte/enterprise-skalierung.md) — Die Enterprise-Probleme, die diese Vorlage auf Mini-Skala löst
- [llm-wiki-v2](../konzepte/llm-wiki-v2.md) — Ownership und Provenienz aus v2 übernommen
- [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md) — Warum die `entwuerfe/`-Pufferzone wichtig ist
- [lernschleifen](../konzepte/lernschleifen.md) — Wöchentliche Team-Reviews als Lernschleife
- [claude-md-software](claude-md-software.md) — Schwester-Vorlage für Solo/Coding-Kontext
- [claude-md-software-begleiter](claude-md-software-begleiter.md) — Begleiter-Variante

---

[Wiki-Index](../index.md)
