---
date: 2026-05-15
type: anleitung
tags: [anleitung, setup, community]
status: active
---

# Wiki-Zusammenarbeit mit Git und Claude Code

**Zusammenfassung**: Schritt-für-Schritt-Anleitung für zwei (oder mehr) Personen, die ein gemeinsames LLM-Wiki auf GitHub betreiben — mit je eigenem Claude Code Zugang, einem klaren Branch-Workflow und praktischen Tipps zum Umgang mit Konflikten.
**Quellen**: Eigene Praxis; verwandte Konzepte in [claude-md-team](../vorlagen/claude-md-team.md) und [enterprise-skalierung](../konzepte/enterprise-skalierung.md)
**Zuletzt aktualisiert**: 2026-05-15

---

Diese Anleitung geht davon aus, dass ein Wiki bereits auf GitHub liegt (wie dieses hier). Die zweite Person — nennen wir sie Alex — soll gleichberechtigt mitarbeiten können, mit eigenem Claude Code Zugang.

---

## Voraussetzungen

Beide Personen brauchen:

- **GitHub-Konto** (kostenlos)
- **Claude Code** installiert — [Desktop App](https://claude.ai) (Windows/macOS) oder CLI via `npm install -g @anthropic-ai/claude-code` (alle Plattformen)
- **Git** installiert (`git --version` im Terminal prüfen; für Windows: [git-scm.com](https://git-scm.com))
- Ein aktives **Claude.ai-Abo** (Pro oder Team) für Claude Code

---

## Schritt 1: Alex als GitHub-Mitarbeiter einladen

Als Repo-Inhaber:

1. Öffne das Repo auf GitHub (z.B. `github.com/maxron84/llm-wiki`)
2. → **Settings** → **Collaborators** → **Add people**
3. Trage Alexs GitHub-Username oder E-Mail-Adresse ein
4. Alex bekommt eine E-Mail und muss die Einladung annehmen

Alex hat danach **Push-Rechte** — er kann direkt in das Repo pushen, ohne Pull Request. Das ist für kleine Teams (2–3 Personen) praktisch; für mehr Kontrolle → [Branch-Schutz weiter unten](#optional-branch-schutz).

---

## Schritt 2: Repo auf Alexs Seite klonen

Alex führt einmalig in einem Terminal aus:

```bash
git clone https://github.com/maxron84/llm-wiki.git
cd llm-wiki
```

Das lädt das gesamte Wiki herunter — inklusive `CLAUDE.md`, aller Seiten und der Git-Historie. Alex hat damit sofort denselben Stand wie du.

Danach Git-Identität einrichten (falls noch nicht global konfiguriert):

```bash
git config user.name "Alex Mustermann"
git config user.email "ralph@beispiel.de"
```

---

## Schritt 3: Claude Code auf Alexs Seite einrichten

### Projektinstruktionen — kein Aufwand

Das `CLAUDE.md` im Repo-Root wird von Claude Code automatisch gelesen. Alex muss nichts extra konfigurieren — er bekommt sofort dieselben Regeln (Ordnerstruktur, Aufnahme-Workflow, Seitenformat usw.).

### Globale Instruktionen — optional aber empfohlen

Jeder pflegt seine eigene `~/.claude/CLAUDE.md` für globale Gewohnheiten (z.B. „Nach jedem Schritt committen"). Das ist persönliche Präferenz und wird nicht ins Repo eingecheckt.

Alex öffnet oder erstellt `~/.claude/CLAUDE.md` und trägt dort ein, was er wiki-übergreifend möchte:

```markdown
## Git-Commits

Nach jedem erfolgreichen Arbeitsschritt einen Commit erstellen.
```

### Claude Code im Projektordner starten

```bash
cd llm-wiki
claude  # Desktop App: Ordner per "Open Folder" auswählen
```

Claude liest `CLAUDE.md` und ist sofort im Kontext des Wikis.

---

## Schritt 4: Täglicher Sync-Workflow

**Goldene Regel**: Vor jeder Arbeitssitzung zuerst pullen.

```bash
git pull
```

Das holt Änderungen der anderen Person(en). Wenn du das vergisst und beide gleichzeitig auf demselben Branch arbeiten, entsteht ein Merge-Konflikt — lösbar, aber lästig.

**Nach der Sitzung**: Committen und pushen.

```bash
git add wiki/
git commit -m "ingest: Neue Quelle zu Retrieval-Architekturen"
git push
```

---

## Schritt 5: Branch-Workflow (empfohlen)

Für alle außer sehr kleinen Einzel-Änderungen lohnt sich ein Feature-Branch. Das Prinzip: jeder arbeitet in seinem eigenen Zweig, Änderungen werden erst nach Review zusammengeführt.

### Branch erstellen und arbeiten

```bash
# Alex erstellt einen Branch für seine Quelle
git checkout -b ralph/retrieval-studie

# Arbeit mit Claude Code...
# Commits wie gewohnt
git add wiki/
git commit -m "ingest: Retrieval-Studie aufgenommen"
git push -u origin ralph/retrieval-studie
```

### Pull Request öffnen

1. Geh auf GitHub → das Repo → **Pull requests** → **New pull request**
2. Base: `main`, Compare: `ralph/retrieval-studie`
3. Titel und kurze Beschreibung eintragen
4. **Create pull request**

Der andere schaut drüber, gibt Feedback oder mergt direkt. Bei kleinen Wikis reicht oft ein kurzer Blick.

### Wann Branches, wann direkt auf main?

| Situation | Empfehlung |
|---|---|
| Neue Quelle aufnehmen (5–15 Seiten) | Branch + PR |
| Kleine Korrektur, Tippfehler | Direkt auf main |
| Größere Umstrukturierung | Branch + PR, vorher kurz besprechen |
| Lint-Lauf | Direkt auf main |

---

## Schritt 6: Konflikte verstehen und lösen

Konflikte entstehen, wenn zwei Personen dieselbe Stelle in derselben Datei ändern, bevor sie synchronisiert haben. Im LLM-Wiki passiert das vor allem in zwei Dateien:

### wiki/index.md

Beide fügen neue Einträge hinzu — fast immer in verschiedenen Abschnitten, daher selten echte Konflikte. Trotzdem: erst pullen, dann arbeiten.

### wiki/log.md

Hier hängt jeder seinen Eintrag am Ende an. Wenn zwei Personen das gleichzeitig tun, entsteht ein Konflikt genau am letzten Eintrag. Git zeigt es so:

```
<<<<<<< HEAD
## [2026-05-15 14:30] ingest | Retrieval-Studie
=======
## [2026-05-15 14:28] query | Frage zu RAG
>>>>>>> ralph/retrieval-studie
```

Lösung: beide Einträge behalten, in chronologischer Reihenfolge sortieren, die Konflikt-Marker (`<<<<`, `====`, `>>>>`) entfernen, dann committen.

### Allgemeines Vorgehen bei Konflikten

```bash
git pull           # zeigt Konflikte an
# Dateien mit Konflikten öffnen und Marker manuell auflösen
git add wiki/index.md wiki/log.md
git commit -m "merge: Konflikte in index.md und log.md aufgelöst"
```

Oder mit einem Merge-Tool:

```bash
git mergetool      # öffnet ein visuelles Diff-Tool
```

---

## Schritt 7: Ownership klären (optional)

Für ein Zwei-Personen-Wiki reicht informelle Koordination: kurz absprechen, wer gerade an was arbeitet. Bei mehr Personen oder häufigen Konflikten lohnt sich das Ownership-Konzept aus der [Team-Vorlage](../vorlagen/claude-md-team.md):

Jede Wiki-Seite bekommt ein `owner:`-Feld im Frontmatter:

```yaml
---
date: 2026-05-15
type: konzept
owner: ralph
tags: [konzept]
status: active
---
```

Und in `CLAUDE.md` eine Regel:

```markdown
## Ownership

Ändere nur Seiten, bei denen du als `owner` eingetragen bist — oder bei denen `owner` fehlt.
Für Seiten anderer: Entwurf in `wiki/entwuerfe/` anlegen, Inhaber per Git-Commit-Nachricht informieren.
```

---

## Optional: Branch-Schutz

Wenn ihr wollt, dass niemand direkt auf `main` pushen kann ohne Review:

1. GitHub → **Settings** → **Branches** → **Add rule**
2. Branch name pattern: `main`
3. ✓ **Require a pull request before merging**
4. Optional: ✓ **Require approvals** (1 Approval)

Danach müssen alle Änderungen über PRs rein — auch deine eigenen. Das kostet etwas Overhead, erhöht aber die Qualität.

---

## Optional: GitHub Notifications einschalten

Damit Alex sofort sieht, wenn du neue Seiten pushst (und umgekehrt):

1. GitHub → das Repo → **Watch** → **All activity**

Alternativ reicht **Releases only** oder **Custom** (nur PRs), wenn ihr euch nicht mit Notifications überschütten wollt.

---

## Zusammenfassung: Der tägliche Rhythmus

```
Sitzung beginnen:
  git pull

Mit Claude Code arbeiten:
  neue Seiten, Quellen aufnehmen, Fragen stellen

Nach jedem Schritt:
  git add wiki/
  git commit -m "..."

Sitzung beenden:
  git push
```

Das reicht für 95% der Zusammenarbeit. Branches und PRs kommen hinzu, wenn ihr gleichzeitig an größeren Themen arbeitet.

---

## Verwandte Seiten

- [erste-schritte](erste-schritte.md) — Einrichtung von Null: Claude Code, Obsidian, erste Sitzung
- [claude-md-team](../vorlagen/claude-md-team.md) — CLAUDE.md-Vorlage für 2–8 Personen mit Ownership, Provenienz-Tags
- [enterprise-skalierung](../konzepte/enterprise-skalierung.md) — Warum das persönliche Muster bei größeren Teams bricht
- [claude-code](../werkzeuge/claude-code.md) — Claude Code: Was es ist und wie es das Wiki pflegt

---

[Wiki-Index](../index.md)
