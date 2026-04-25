---
date: 2026-04-25
type: vorlage
tags: [vorlage, schema]
status: active
---

# CLAUDE.md-Vorlage: Software-Projekte

**Zusammenfassung**: Eine CLAUDE.md-Vorlage für Software-Projekte in zwei Modi: **Lean** (reines Coding-Tool) und **Dokumentiert** (Coding + fortlaufendes Projekt-Wiki nach dem LLM-Wiki-Muster).
**Quellen**: Abgeleitet aus [[llm-wiki-muster]], [[drei-ebenen-architektur]], [[claude-code]], [[claude-md-design]] und [[skalierungsgrenzen]]
**Zuletzt aktualisiert**: 2026-04-25

---

## Zweck

Diese Vorlage implementiert die dritte Ebene der [[drei-ebenen-architektur]] — das **Schema** — für Software-Projekte. Sie definiert, wie [[claude-code|Claude Code]] mit einer Codebase interagieren soll.

**Konzeptioneller Unterschied zu den anderen Vorlagen**: Die YouTube-Verlauf-, Rezepte- und Forensik-Vorlagen sind reine *Wissens-Wikis* — der gesamte Kontext ist das Wiki. In einem Software-Projekt konkurriert ein optionales Wiki mit dem **Sourcecode** um das Token-Budget. Deshalb bietet diese Vorlage zwei Modi.

## Zwei Modi

### Modus "Lean" (Standard)

Nur eine CLAUDE.md im Projektstamm. Kein `wiki/`, kein `raw/`. Funktioniert mit jedem Modell, auch lokalen 30B-Modellen mit 8K-Kontext.

```
mein-projekt/
  CLAUDE.md              ← Aus dieser Vorlage
  src/
  tests/
  package.json
```

**Wann Lean reicht**: Kleine bis mittlere Projekte, Solo-Entwicklung, Code spricht weitgehend für sich.

### Modus "Dokumentiert"

CLAUDE.md + ein `wiki/`-Verzeichnis, in dem Claude parallel zum Coding eine fortlaufende Projektdokumentation aufbaut. Folgt dem [[llm-wiki-muster]] — der Sourcecode ist die "Rohquelle", das Wiki das "kompilierte" Verständnis (siehe [[kompilierungs-metapher]]).

```
mein-projekt/
  CLAUDE.md              ← Aus dieser Vorlage (mit Wiki-Sektion aktiviert)
  src/                   ← Sourcecode = Rohquelle (Ebene 1)
  tests/
  wiki/                  ← Von Claude gepflegte Doku (Ebene 2)
    index.md
    log.md
    architektur/         ← Systemarchitektur, Datenflüsse
    module/              ← Analyse einzelner Module
    entscheidungen/      ← Architecture Decision Records (ADRs)
    schnittstellen/      ← APIs, Protokolle, Datenformate
```

**Wann Dokumentiert sinnvoll ist**: Wachsende Projekte, Teamarbeit, komplexe Domänen, wenn man sein eigenes Projekt nach 3 Monaten noch verstehen will.

## Kontextbudget und lokale Modelle

Das Wiki konkurriert mit dem Sourcecode um Token. Die ehrliche Rechnung:

| Modell | Kontextfenster | Code braucht | Wiki-Budget übrig |
|---|---|---|---|
| Claude Opus/Sonnet | 200K | ~50–100K | ~100K+ — kein Problem |
| Lokales 30B, langes Fenster | 128K | ~30–50K | ~50K — geht |
| Lokales 30B, kurzes Fenster | 8–32K | ~10–20K | ~5–10K — **eng** |

**Empfehlungen nach Modellgröße**:

| Situation | Empfehlung |
|---|---|
| Cloud-API (Claude, GPT-4) | Modus Dokumentiert problemlos nutzbar |
| Lokales 30B + 128K Kontext | Dokumentiert möglich, aber `index.md` schlank halten |
| Lokales 30B + 8–32K Kontext | Modus Lean verwenden; oder Dokumentiert nur mit [[qmd]]/[[jdocmunch]] (lädt nur die relevante Wiki-Sektion) |
| Lokales 7B | Nur Lean — Wiki-Pflege übersteigt die Modellkapazität |

Siehe auch [[skalierungsgrenzen]] für die allgemeinen Schwellenwerte.

## Benutzung

1. **Modus wählen**: Lean oder Dokumentiert. Im Zweifel mit Lean starten — man kann jederzeit upgraden.
2. Kopiere **nur den Inhalt** des Vorlagenblocks unten (ohne die ` ```` ` Fence-Markierungen) als `CLAUDE.md` ins Wurzelverzeichnis deines Projekts. Der 4-Backtick-Fence dient hier nur als Darstellung — in deinem Projekt muss der Inhalt als direkter Top-Level-Markdown stehen (siehe [[claude-md-design|Designprinzip 1]]).
3. Ersetze alle `{{PLATZHALTER}}` durch die tatsächlichen Werte
4. **Für Lean**: Lösche den gesamten Abschnitt "Projekt-Wiki" aus der kopierten CLAUDE.md
5. **Für Dokumentiert**: Behalte den Wiki-Abschnitt und passe ihn an
6. Lösche weitere nicht benötigte Abschnitte, entferne den Anleitungsblock
7. Alternativ: Lass Claude Code die Platzhalter selbst ausfüllen — „Analysiere dieses Projekt und fülle die CLAUDE.md aus"

## Enthaltene Abschnitte

| Abschnitt | Modus | Zweck |
|---|---|---|
| Projekt | Beide | Name, Sprache, Framework, Paketmanager |
| Schnelleinstieg | Beide | Install, Dev, Test, Lint, Build |
| Projektstruktur | Beide | Verzeichnisbaum |
| Architektur | Beide | Datenfluss in 3–5 Sätzen |
| Konventionen | Beide | Code-Stil, Dateien, Git, Kommentare |
| Abhängigkeiten | Beide | Wichtigste Pakete als Tabelle |
| Umgebungsvariablen | Beide | Secrets-Verwaltung |
| Tests | Beide | Framework, Befehle, Konventionen |
| Regeln für Claude | Beide | Was tun, was lassen |
| Bekannte Probleme | Beide | Technische Schulden |
| Nützliche Befehle | Beide | DB-Reset, Docker etc. |
| **Projekt-Wiki** | **Nur Dokumentiert** | Wiki-Workflow, Seitenformat, Prüfung |

## Designhinweise

Beim Einsatz dieser Vorlage die [[claude-md-design|CLAUDE.md-Designprinzipien]] beachten:

1. **Fence entfernen**: Der Vorlageninhalt unten steht in einem 4-Backtick-Fence — das ist korrekt für diese Referenzseite. Beim Kopieren in ein Projekt **muss der Fence-Wrapper entfernt werden**, sonst liest Claude die Regeln als Illustration statt als Anweisungen.
2. **Link-Stil anpassen**: Die Vorlage verwendet `[[wiki-links]]`. Wenn dein Projekt Standard-Markdown-Links `[text](pfad.md)` nutzt, durchgehend ersetzen — Inkonsistenz zwischen CLAUDE.md und Output führt zur [[kontaminierungsrisiko|Kontamination]].
3. **Frontmatter-Entscheidung**: Die Vorlage enthält kein [[yaml-frontmatter|Frontmatter]] in den Seitenformat-Beispielen. Falls dein Projekt Frontmatter nutzt, ergänze es — aber nur wenn *alle* Seiten es haben.
4. **Keine toten Links**: Entferne alle `{{PLATZHALTER}}`-Referenzen, die du nicht ausfüllst, statt sie stehenzulassen.
5. **Regelmäßig reviewen**: Die CLAUDE.md altert mit dem Projekt. Nach ~5 neuen Patterns lohnt sich ein Durchgang.

## Designentscheidungen

- **Zwei Modi statt zwei Vorlagen**: Die Lean-Basis ist identisch — nur der Wiki-Abschnitt kommt hinzu. Das vermeidet Doppelpflege.
- **Platzhalter-Format `{{...}}`**: Universell erkennbar, leicht durchsuchbar mit `grep '{{' CLAUDE.md`
- **Tabellen für Abhängigkeiten und Envvars**: Kompakt, scannbar, erweiterbar
- **Explizite Verbotsliste**: „Was du NICHT tun sollst" verhindert häufige KI-Fehler
- **Wiki-Ordner `wiki/` statt `docs/`**: Konsistent mit dem LLM-Wiki-Muster. `docs/` bleibt für handgeschriebene Doku oder generierte API-Docs.

## Vorlage

````markdown
# CLAUDE.md — Vorlage für Software-Projekte

> **Anleitung**: Kopiere diese Datei als `CLAUDE.md` in das Wurzelverzeichnis deines Projekts.
> Ersetze alle `{{PLATZHALTER}}` durch deine Werte. Lösche Abschnitte, die nicht passen.
> Für **Modus Lean**: Lösche den Abschnitt "Projekt-Wiki".
> Für **Modus Dokumentiert**: Behalte ihn und passe die Ordnerstruktur an.
> Entferne diesen Anleitungsblock, bevor du die Datei produktiv nutzt.

---

# CLAUDE

## Projekt

**Name**: {{PROJEKTNAME}}
**Beschreibung**: {{Ein bis zwei Sätze, was dieses Projekt tut.}}
**Sprache**: {{z.B. TypeScript, Python, Rust, Go, Java}}
**Framework**: {{z.B. Next.js, Django, Axum, Spring Boot — oder "keins"}}
**Paketmanager**: {{z.B. npm, pnpm, pip, uv, cargo, gradle}}

## Schnelleinstieg

```bash
# Abhängigkeiten installieren
{{z.B. npm install / pip install -e ".[dev]" / cargo build}}

# Entwicklungsserver starten
{{z.B. npm run dev / python manage.py runserver / cargo run}}

# Tests ausführen
{{z.B. npm test / pytest / cargo test}}

# Linter/Formatter
{{z.B. npm run lint / ruff check . / cargo clippy}}

# Build erstellen
{{z.B. npm run build / python -m build / cargo build --release}}
```

## Projektstruktur

```
{{Passe die Struktur an dein Projekt an}}
src/              -- Quellcode
tests/            -- Tests
docs/             -- Dokumentation
scripts/          -- Hilfsskripte
config/           -- Konfigurationsdateien
.github/          -- CI/CD Workflows
wiki/             -- Projekt-Wiki (nur Modus Dokumentiert — lösche wenn Lean)
```

## Architektur

{{Beschreibe die Architektur in 3-5 Sätzen. Welche Schichten gibt es? Wie fließen Daten?
Beispiel: "Das Projekt folgt einer dreischichtigen Architektur: API-Layer (src/api/),
Business-Logik (src/services/), Datenzugriff (src/repositories/). Requests kommen über
Express-Router rein, werden von Services verarbeitet und greifen über Repositories auf
die PostgreSQL-Datenbank zu."}}

## Konventionen

### Code-Stil

- {{z.B. Funktionen in camelCase, Typen in PascalCase, Konstanten in UPPER_SNAKE_CASE}}
- {{z.B. Maximale Zeilenlänge: 100 Zeichen}}
- {{z.B. Immer explizite Typen, kein `any` / Immer Type Hints verwenden}}
- {{z.B. Bevorzuge Komposition über Vererbung}}
- {{z.B. Keine Abkürzungen in Variablennamen — `userRepository` statt `usrRepo`}}

### Dateibenennungen

- {{z.B. Dateien in kebab-case: `user-service.ts`}}
- {{z.B. Testdateien neben der Quelldatei: `user-service.test.ts`}}
- {{z.B. Eine Klasse/Modul pro Datei}}

### Git

- Commit-Messages auf {{Deutsch / Englisch}}
- Format: {{z.B. Conventional Commits: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`}}
- {{z.B. Immer einen Branch pro Feature: `feature/kurze-beschreibung`}}

### Kommentare und Dokumentation

- Kommentare auf {{Deutsch / Englisch}}
- Erkläre das **Warum**, nicht das **Was**
- Öffentliche APIs immer dokumentieren ({{z.B. JSDoc / Docstrings / rustdoc}})
- TODOs im Format: `// TODO(name): Beschreibung`

## Abhängigkeiten

{{Liste die wichtigsten Abhängigkeiten und ihren Zweck}}

| Paket | Zweck |
|---|---|
| {{z.B. express}} | {{HTTP-Server}} |
| {{z.B. prisma}} | {{ORM / Datenbankzugriff}} |
| {{z.B. zod}} | {{Schema-Validierung}} |
| {{z.B. vitest}} | {{Test-Framework}} |

## Umgebungsvariablen

{{Falls relevant — lösche diesen Abschnitt wenn nicht nötig}}

| Variable | Beschreibung | Beispiel |
|---|---|---|
| `DATABASE_URL` | {{Datenbankverbindung}} | `postgres://user:pass@localhost:5432/db` |
| `API_KEY` | {{Externer Service}} | `sk-...` |

**Wichtig**: Niemals Secrets in den Code oder in diese Datei schreiben. Verwende `.env`-Dateien (in `.gitignore` gelistet) oder einen Secrets-Manager.

## Tests

- Testframework: {{z.B. vitest, pytest, cargo test, JUnit}}
- Tests ausführen: `{{Befehl}}`
- Einzelnen Test ausführen: `{{z.B. npm test -- --grep "testname" / pytest tests/test_xyz.py::test_name}}`
- Coverage generieren: `{{z.B. npm run test:coverage / pytest --cov}}`

### Test-Konventionen

- {{z.B. Jede öffentliche Funktion hat mindestens einen Test}}
- {{z.B. Testdatei-Naming: `*.test.ts` / `test_*.py` / `*_test.go`}}
- {{z.B. Arrange-Act-Assert Muster verwenden}}
- {{z.B. Mocks nur für externe Services, nie für eigene Module}}

## Regeln für Claude

### Allgemein

- Schreibe Code in {{Sprache}} und Kommentare auf {{Deutsch / Englisch}}
- Halte dich an die bestehenden Konventionen im Code — lies vorhandene Dateien, bevor du neue erstellst
- Wenn du dir unsicher bist, frage nach, statt zu raten
- Erkläre bedeutende Architekturentscheidungen vor der Umsetzung
- Ändere niemals Konfigurationsdateien ({{z.B. tsconfig.json, pyproject.toml, Cargo.toml}}), ohne vorher zu fragen

### Beim Erstellen von neuem Code

- Folge dem bestehenden Muster ähnlicher Dateien im Projekt
- Erstelle immer Tests für neue Funktionalität
- Halte Funktionen kurz und fokussiert (max. ~30 Zeilen als Richtlinie)
- Bevorzuge reine Funktionen wo möglich
- Fehlerbehandlung explizit — keine stillen Fehler

### Beim Ändern von bestehendem Code

- Ändere so wenig wie nötig, um das Ziel zu erreichen
- Führe nach Änderungen die relevanten Tests aus
- Wenn ein Refactoring nötig ist, schlage es separat vor
- Brich keine bestehenden Tests — wenn ein Test sich ändern muss, erkläre warum

### Was du NICHT tun sollst

- Keine Abhängigkeiten hinzufügen, ohne zu fragen
- Keine bestehende Architektur umwerfen
- Keine Code-Formatierung in Dateien ändern, die du nicht inhaltlich bearbeitest
- Keine Secrets, API-Keys oder Passwörter in den Code schreiben
- Keine unnötigen Kommentare wie `// Importiere Express` — der Code spricht für sich

## Bekannte Probleme / Technische Schulden

{{Optional — lösche wenn nicht relevant}}

- {{z.B. Die Authentifizierung nutzt noch JWTs ohne Refresh-Token}}
- {{z.B. Die Datenbankmigrationen sind nicht rückwärtskompatibel}}
- {{z.B. Der Build-Prozess dauert >2 Minuten wegen fehlender Caching-Strategie}}

## Nützliche Befehle

{{Optional — ergänze projektspezifische Befehle}}

```bash
# Datenbank zurücksetzen
{{z.B. npx prisma migrate reset / python manage.py flush}}

# Seed-Daten laden
{{z.B. npx prisma db seed / python manage.py loaddata fixtures.json}}

# Docker-Umgebung starten
{{z.B. docker compose up -d}}

# Logs anzeigen
{{z.B. docker compose logs -f app}}
```

## Projekt-Wiki (Modus Dokumentiert)

> **Hinweis**: Diesen gesamten Abschnitt löschen, wenn du den Modus **Lean** verwendest.
> Behalte ihn nur, wenn du parallel zum Coding eine fortlaufende Projektdokumentation aufbauen willst.

### Zweck

Claude baut parallel zum Coding ein Verständnis-Wiki auf: Architekturentscheidungen, Modulanalysen, Schnittstellenbeschreibungen, erkannte Muster. Der Sourcecode ist die "Rohquelle", das Wiki das "kompilierte" Verständnis.

### Wiki-Ordnerstruktur

```
wiki/
  index.md              -- Inhaltsverzeichnis aller Wiki-Seiten
  log.md                -- Chronologisches Änderungsprotokoll
  architektur/          -- Systemarchitektur, Datenflüsse, Schichten
  module/               -- Analyse einzelner Module/Services/Packages
  entscheidungen/       -- Architecture Decision Records (ADRs)
  schnittstellen/       -- APIs, Protokolle, Datenformate
  probleme/             -- Technische Schulden, bekannte Risiken
```

### Wann Wiki-Seiten entstehen

- **Bei Architekturentscheidungen**: Bevor eine strukturelle Änderung umgesetzt wird, lege eine Entscheidungsseite an (Was, Warum, Alternativen, Konsequenzen)
- **Bei Modulanalysen**: Wenn ein Modul zum ersten Mal intensiv bearbeitet wird, dokumentiere die Erkenntnisse
- **Bei Schnittstellenänderungen**: API-Änderungen, Datenbankmigrationen, Protokolländerungen
- **Bei erkannten Mustern**: Wiederkehrende Patterns oder Anti-Patterns im Code

### Wiki-Seitenformat

```markdown
# Seitentitel

**Zusammenfassung**: Ein bis zwei Sätze.
**Quellen**: Betroffene Dateien (z.B. `src/services/auth.ts`, `src/api/routes.ts`)
**Zuletzt aktualisiert**: YYYY-MM-DD

---

Hauptinhalt.

## Verwandte Seiten

- [[verwandte-seite]]

---

[Wiki-Index](wiki/index.md)
```

### Wiki-Regeln

- Aktualisiere `wiki/index.md` und `wiki/log.md` nach jeder Wiki-Änderung
- Wiki-Seiten beschreiben das **Warum** und die **Zusammenhänge** — der Code selbst zeigt das Was
- Verlinke Wiki-Seiten untereinander mit `[[wiki-links]]`
- Wenn eine Wiki-Seite durch Code-Änderungen veraltet, aktualisiere sie oder markiere sie als `(veraltet — prüfen)`
- Seitennamen in Kleinbuchstaben mit Bindestrichen

### Wiki-Prüfung

Regelmäßig (z.B. nach jedem Sprint oder bei jedem 5. Feature):

- Wiki-Seiten auf veraltete Informationen prüfen
- Verwaiste Seiten finden (keine eingehenden Links)
- Module identifizieren, die im Code wichtig sind, aber keine Wiki-Seite haben
- Ergebnisse als nummerierte Liste berichten
````

## Verwandte Seiten

- [[claude-md-design]] — Die 6 Designprinzipien für effektive CLAUDE.md-Dateien
- [[drei-ebenen-architektur]] — Die Vorlage implementiert Ebene 3 (Schema); Modus Dokumentiert nutzt alle 3 Ebenen
- [[kompilierungs-metapher]] — Im Modus Dokumentiert: Sourcecode = Quellcode, Wiki = kompiliertes Verständnis
- [[skalierungsgrenzen]] — Kontextbudget-Grenzen, besonders relevant für lokale Modelle
- [[claude-code]] — Der Agent, der mit dieser Vorlage gesteuert wird
- [[llm-wiki-muster]] — Das übergeordnete Konzept (Modus Dokumentiert folgt diesem Muster)
- [[kontaminierungsrisiko]] — Warum Link-Stil-Konsistenz wichtig ist
- [[qmd]] — Skalierungslösung: semantische Suche über Wiki-Seiten
- [[jdocmunch]] — Skalierungslösung: nur relevante Sektionen laden
- [[claude-md-legacy-forensik]] — Schwester-Vorlage für Legacy-Analyse (ähnlich wie Modus Dokumentiert, aber nur lesend)
- [[claude-md-youtube-verlauf]] — Schwester-Vorlage für YouTube-Verlauf-Wikis
- [[claude-md-rezepte-ernaehrung]] — Schwester-Vorlage für Rezepte-Wikis

---

[Wiki-Index](../index.md)
