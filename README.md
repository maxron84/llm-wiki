# LLM Wiki

Ein persönliches Wissenswiki nach [Andrej Karpathys LLM-Wiki-Muster](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — aufgebaut und gepflegt von Claude Code.

## Motivation

Wissen, das in Chats entsteht, verschwindet. Wissen, das einmal in einem strukturierten Wiki kompiliert wurde, bleibt — und jede neue Quelle macht es tiefer statt breiter. Die KI ist nicht Suchmaschine, sondern Wissensbasisarchitekt.

## Datenquellen & Verarbeitung

Quellen (Artikel, Blogposts, Videos) landen als Markdown in `clippings/` oder `raw/`. Claude liest, extrahiert und vernetzt sie zu Konzept-, Personen- und Werkzeugseiten unter `wiki/`. Jede Quelle wird einmal kompiliert — Fragen danach sind günstig.

## Vorlagen

Der eigentliche Output dieses Wikis: kopierbare `CLAUDE.md`-Vorlagen für spezialisierte LLM-Wikis in anderen Projekten.

| Vorlage | Einsatzgebiet |
|---|---|
| [Software-Projekte](wiki/vorlagen/claude-md-software.md) | Codebase-Dokumentation — Lean (nur CLAUDE.md) oder Dokumentiert (+ Projekt-Wiki) |
| [Software-Begleiter](wiki/vorlagen/claude-md-software-begleiter.md) | Mensch schreibt Code allein — KI übernimmt Dokumentation, Review und Ideenaustausch |
| [Team-Wiki](wiki/vorlagen/claude-md-team.md) | Gemeinsames Wiki für 2–8 Personen — mit Ownership, Provenienz-Tags und Entwurfs-Pufferzone |
| [Legacy-Forensik](wiki/vorlagen/claude-md-legacy-forensik.md) | Analyse und Kartierung gewachsener Codebases |
| [KI-Lehrer](wiki/vorlagen/claude-md-lehrer.md) | Proaktiver Lehrbegleiter — generiert Lehrplan, lehrt Schritt für Schritt (z.B. Coding-Projekte) |
| [Nachhilfe](wiki/vorlagen/claude-md-nachhilfe.md) | Fachlehrer für ein Schulfach — reaktiv, aufgabengetrieben, für Schüler bis Klasse 10 |
| [YouTube-Verlauf](wiki/vorlagen/claude-md-youtube-verlauf.md) | Wissensbasis aus dem eigenen Video-Verlauf |
| [Rezepte & Ernährung](wiki/vorlagen/claude-md-rezepte-ernaehrung.md) | Vernetzte Rezept-, Technik- und Zutaten-Basis |

Jede Vorlage enthält Ordnerstruktur, Seitenformate, Extraktionsstrategien und Abfragetypen — einfach kopieren, Platzhalter füllen, loslegen.

---

> **Note:** This repository — wiki pages, templates, and documentation — is currently available in German only.
