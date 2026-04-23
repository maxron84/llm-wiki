---
date: 2026-04-22
type: vorlage
tags: [vorlage, schema]
status: active
---

# CLAUDE.md-Vorlage: Software-Projekte

**Zusammenfassung**: Eine CLAUDE.md-Vorlage für allgemeine Software-Projekte — kopierbar in jedes Projekt, mit Platzhaltern zum Ausfüllen durch die KI oder den Entwickler.
**Quellen**: Abgeleitet aus dem Wissen über [[llm-wiki-muster]], [[drei-ebenen-architektur]] und [[claude-code]]
**Zuletzt aktualisiert**: 2026-04-22

---

## Zweck

Diese Vorlage implementiert die dritte Ebene der [[drei-ebenen-architektur]] — das **Schema** — für Software-Projekte jeglicher Art. Sie definiert, wie [[claude-code|Claude Code]] mit einer Codebase interagieren soll: Projektstruktur, Konventionen, Befehle, Testregeln und Verhaltensrichtlinien.

## Benutzung

1. Kopiere den Vorlageninhalt (unten) als `CLAUDE.md` ins Wurzelverzeichnis deines Projekts
2. Ersetze alle `{{PLATZHALTER}}` durch die tatsächlichen Werte
3. Lösche nicht benötigte Abschnitte
4. Entferne den Anleitungsblock am Anfang
5. Alternativ: Lass Claude Code die Platzhalter selbst ausfüllen — „Analysiere dieses Projekt und fülle die CLAUDE.md aus"

## Enthaltene Abschnitte

| Abschnitt | Zweck |
|---|---|
| Projekt | Name, Sprache, Framework, Paketmanager |
| Schnelleinstieg | Install, Dev, Test, Lint, Build |
| Projektstruktur | Verzeichnisbaum |
| Architektur | Datenfluss in 3–5 Sätzen |
| Konventionen | Code-Stil, Dateien, Git, Kommentare |
| Abhängigkeiten | Wichtigste Pakete als Tabelle |
| Umgebungsvariablen | Secrets-Verwaltung |
| Tests | Framework, Befehle, Konventionen |
| Regeln für Claude | Was tun, was lassen |
| Bekannte Probleme | Technische Schulden |
| Nützliche Befehle | DB-Reset, Docker etc. |

## Designhinweise

Beim Einsatz dieser Vorlage die [[claude-md-design|CLAUDE.md-Designprinzipien]] beachten:

1. **Fence entfernen**: Der Vorlageninhalt unten steht in einem 4-Backtick-Fence — das ist korrekt für diese Referenzseite. Beim Kopieren in ein Projekt **muss der Fence-Wrapper entfernt werden**, sonst liest Claude die Regeln als Illustration statt als Anweisungen.
2. **Link-Stil anpassen**: Die Vorlage verwendet `[[wiki-links]]`. Wenn dein Projekt Standard-Markdown-Links `[text](pfad.md)` nutzt, durchgehend ersetzen — Inkonsistenz zwischen CLAUDE.md und Output führt zur [[kontaminierungsrisiko|Kontamination]].
3. **Frontmatter-Entscheidung**: Die Vorlage enthält kein [[yaml-frontmatter|Frontmatter]] in den Seitenformat-Beispielen. Falls dein Projekt Frontmatter nutzt, ergänze es — aber nur wenn *alle* Seiten es haben.
4. **Keine toten Links**: Entferne alle `{{PLATZHALTER}}`-Referenzen, die du nicht ausfüllst, statt sie stehenzulassen.
5. **Regelmäßig reviewen**: Die CLAUDE.md altert mit dem Projekt. Nach ~5 neuen Patterns lohnt sich ein Durchgang.

## Designentscheidungen

- **Platzhalter-Format `{{...}}`**: Universell erkennbar, leicht durchsuchbar mit `grep '{{' CLAUDE.md`
- **Tabellen für Abhängigkeiten und Envvars**: Kompakt, scannbar, erweiterbar
- **Explizite Verbotsliste**: „Was du NICHT tun sollst" verhindert häufige KI-Fehler (ungefragt Deps hinzufügen, Architektur umwerfen)
- **Konventionen statt Regeln**: Richtlinien mit Beispielen statt starrer Vorschriften — passt sich an jede Sprache an

## Vorlage

````markdown
# CLAUDE.md — Vorlage für Software-Projekte

> **Anleitung**: Kopiere diese Datei als `CLAUDE.md` in das Wurzelverzeichnis deines Projekts.
> Ersetze alle `{{PLATZHALTER}}` durch deine Werte. Lösche Abschnitte, die nicht passen.
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
````

## Verwandte Seiten

- [[claude-md-design]] — Die 6 Designprinzipien für effektive CLAUDE.md-Dateien
- [[drei-ebenen-architektur]] — Die Vorlage implementiert Ebene 3 (Schema)
- [[claude-code]] — Der Agent, der mit dieser Vorlage gesteuert wird
- [[llm-wiki-muster]] — Das übergeordnete Konzept
- [[kontaminierungsrisiko]] — Warum Link-Stil-Konsistenz wichtig ist
- [[claude-md-legacy-forensik]] — Schwester-Vorlage für Legacy-Analyse
- [[claude-md-youtube-verlauf]] — Schwester-Vorlage für YouTube-Verlauf-Wikis

---

[Wiki-Index](../index.md)
