# LLM Wiki

Ein persönliches Wissenswiki nach [Andrej Karpathys LLM-Wiki-Muster](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — aufgebaut und gepflegt von Claude Code.

---

## Inhaltsverzeichnis

- [Motivation](#motivation)
- [Funktionsweise](#funktionsweise)
- [Struktur](#struktur)
- [Vorlagen](#vorlagen)
- [Anleitungen](#anleitungen)
- [Hilfsskripte](#hilfsskripte)
- [Aktuelle Zahlen](#aktuelle-zahlen)

---

## Motivation

Wissen, das in Chats entsteht, verschwindet. Wissen, das einmal in einem strukturierten Wiki kompiliert wurde, bleibt — und jede neue Quelle macht es tiefer statt breiter. Die KI ist nicht Suchmaschine, sondern Wissensbasisarchitekt.

---

## Funktionsweise

Quellen (Artikel, Blogposts, Videos) landen als Markdown in `clippings/` oder `raw/`. Claude liest, extrahiert und vernetzt sie zu Konzept-, Personen- und Werkzeugseiten unter `wiki/`. Jede Quelle wird einmal kompiliert — Fragen danach sind günstig.

Das Wiki wächst durch drei Operationen:

| Operation | Befehl | Ergebnis |
|---|---|---|
| **Ingest** | „Nimm `clippings/X.md` auf" | Neue Seiten, aktualisierte Konzepte |
| **Query** | Frage stellen | Antwort aus Wiki + optionale neue Seite |
| **Lint** | `python3 ~/.claude/scripts/wiki_lint.py` | Bericht über tote Links, Waisen, Formatfehler |

---

## Struktur

```
clippings/          Quelldokumente (Markdown) — unveränderlich
raw/                Quelldokumente (Rohdaten) — unveränderlich
wiki/
├── index.md        Inhaltsverzeichnis aller Seiten
├── log.md          Chronologisches Änderungsprotokoll
├── konzepte/       Ideen, Muster, Architekturentscheidungen
├── quellen/        Zusammenfassungen der aufgenommenen Dokumente
├── werkzeuge/      Tools, Apps, Technologien
├── personen/       Autoren und relevante Personen
├── vorlagen/       CLAUDE.md-Vorlagen für andere Projekttypen
├── anleitungen/    Schritt-für-Schritt-Guides
└── projekte/       Laufende Anwendungsprojekte
```

---

## Vorlagen

Der eigentliche Output dieses Wikis: kopierbare `CLAUDE.md`-Vorlagen für spezialisierte LLM-Wikis in anderen Projekten. Jede Vorlage enthält Ordnerstruktur, Seitenformate, Extraktionsstrategien und Abfragetypen — einfach kopieren, Platzhalter füllen, loslegen.

| Vorlage | Einsatzgebiet |
|---|---|
| [Software-Projekte](wiki/vorlagen/claude-md-software.md) | Codebase-Dokumentation — Lean (nur CLAUDE.md) oder Dokumentiert (+ Projekt-Wiki) |
| [Software-Begleiter](wiki/vorlagen/claude-md-software-begleiter.md) | Mensch schreibt Code allein — KI übernimmt Dokumentation, Review und Ideenaustausch |
| [Team-Wiki](wiki/vorlagen/claude-md-team.md) | Gemeinsames Wiki für 2–8 Personen — mit Ownership, Provenienz-Tags und Entwurfs-Pufferzone |
| [Legacy-Forensik](wiki/vorlagen/claude-md-legacy-forensik.md) | Analyse und Kartierung gewachsener Codebases |
| [KI-Lehrer](wiki/vorlagen/claude-md-lehrer.md) | Proaktiver Lehrbegleiter — generiert Lehrplan, lehrt Schritt für Schritt (z.B. Coding-Projekte) |
| [Nachhilfe](wiki/vorlagen/claude-md-nachhilfe.md) | Fachlehrer für ein Schulfach — reaktiv, aufgabengetrieben, für Schüler bis Klasse 10 |
| [Laienlehrer](wiki/vorlagen/claude-md-laienlehrer.md) | Elternteil oder Laie unterrichtet — KI coacht den Erwachsenen, zweistufige Erklärungen |
| [YouTube-Verlauf](wiki/vorlagen/claude-md-youtube-verlauf.md) | Wissensbasis aus dem eigenen Video-Verlauf |
| [Rezepte & Ernährung](wiki/vorlagen/claude-md-rezepte-ernaehrung.md) | Vernetzte Rezept-, Technik- und Zutaten-Basis |
| [Roo Code + lokales Modell](wiki/vorlagen/roocode-llm-wiki-lokal.md) | LLM-Wiki mit Roo Code + Ollama + qwen3:14b-40k (RTX 5080), auf 40K-Kontextlimit abgestimmt |

---

## Anleitungen

Schritt-für-Schritt-Guides für Einstieg, Betrieb und Sonderfälle:

| Anleitung | Inhalt |
|---|---|
| [Erste Schritte](wiki/anleitungen/erste-schritte.md) | Setup von Null: Claude Code, Obsidian, Vorlage wählen, erste Sitzung starten |
| [Lokale Modelle](wiki/anleitungen/lokale-modelle.md) | Betrieb mit LM Studio (Anfänger) oder Ollama + Open WebUI + Continue (Fortgeschrittene) |
| [Roo Code + Ollama einrichten](wiki/anleitungen/roocode-llm-wiki-einrichten.md) | Schritt-für-Schritt: LLM-Wiki mit Roo Code + qwen3:14b-40k auf RTX 5080 (bestätigt Mai 2026) |
| [Vorlage einrichten](wiki/anleitungen/vorlage-einrichten.md) | Vorlage kopieren, Platzhalter füllen, Projekt starten — Schritt für Schritt am Pygame-Beispiel |
| [Lokale Modelle (Fortgeschritten)](wiki/anleitungen/lokale-modelle-fortgeschritten.md) | Pygame-Projekt mit Qwen3 27B/35B lokal: Ollama, Continue in VS Codium, halbautomatischer Wiki-Betrieb |
| [Beratungs-CRM einrichten](wiki/anleitungen/beratungs-crm-einrichtung.md) | Lokales CRM auf Linux: Ollama, VS Codium, Python-Skripte, Obsidian, erstes Klientenprofil |
| [Token sparen](wiki/anleitungen/token-sparen.md) | 4-stufige Strategie zur Token-Reduktion — von Sofortmaßnahmen bis fortgeschrittener Architektur |
| [Zusammenarbeit & Git](wiki/anleitungen/zusammenarbeit-git.md) | Gemeinsam am Wiki arbeiten: GitHub Collaborators, Branch-Workflow, Konflikte lösen |

---

## Hilfsskripte

| Skript | Zweck | Aufruf |
|---|---|---|
| `~/.claude/scripts/wiki_lint.py` | Prüft auf tote Links, verwaiste Seiten, fehlende Index-Einträge und Formatfehler | `python3 ~/.claude/scripts/wiki_lint.py` (aus dem Projekt-Root) |

Das Skript liegt global unter `~/.claude/scripts/` und braucht keine zusätzlichen Abhängigkeiten.

---

## Aktuelle Zahlen

| Bereich | Seiten |
|---|---|
| Konzepte | 34 |
| Quellen | 20 |
| Werkzeuge | 13 |
| Vorlagen | 10 |
| Anleitungen | 8 |
| Personen | 7 |
| Projekte | 1 |
| **Gesamt** | **93** |

- **37 Quelldokumente** aufgenommen (35 Clippings + 2 Raw)
- **20 Quellen** mit eigener Zusammenfassungsseite
- Keine toten Links, keine verwaisten Seiten (zuletzt geprüft 2026-05-15 mit `wiki_lint.py`)

---

> **Hinweis:** Dieses Repository — Wiki-Seiten, Vorlagen und Dokumentation — ist ausschließlich auf Deutsch verfügbar.
