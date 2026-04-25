---
date: 2026-04-23
type: konzept
tags: [konzept]
status: active
---

# Wiki-Index

**Zusammenfassung**: Inhaltsverzeichnis und Übersicht aller Seiten im LLM-Wiki.
**Zuletzt aktualisiert**: 2026-04-25

---

Dieses Wiki ist eine strukturierte, vernetzte Wissensdatenbank für das LLM-Wiki-Konzept. Claude pflegt das Wiki. Der Mensch kuratiert Quellen, stellt Fragen und leitet die Analyse.

## Konzepte

- [[llm-wiki-muster]] — Das Kernkonzept: KI baut ein persistentes, wachsendes Wiki statt RAG
- [[chat-problem]] — Das fundamentale Problem: Wissen verschwindet in Chats
- [[statelessness]] — Das technische Problem: KI-Agenten sind zustandslos
- [[drei-ebenen-architektur]] — Rohquellen → Wiki → Schema
- [[ingest-workflow]] — Der Prozess zur Aufnahme neuer Quellen (inkl. Tiefe-Auswahl)
- [[query-workflow]] — Fragen gegen das Wiki stellen und Antworten zurückspeichern
- [[lint-pruefung]] — Regelmäßige Gesundheitsprüfung des Wikis
- [[rag-vs-wiki]] — Vergleich: RAG vs. persistentes Wiki
- [[kompilierungs-metapher]] — Rohquellen = Quellcode, Wiki = kompiliertes Binary
- [[skalierungsgrenzen]] — index.md-Bottleneck bei ~50–100K Token; Lösungsansätze
- [[vault-trennung]] — Sauberes Vault (menschlich) + Agenten-Vault (KI) trennen
- [[kontaminierungsrisiko]] — Halluzinationen werden als persistente Fakten eingebacken
- [[query-templates]] — 6 Abfragetypen: Synthese, Lücken, Debatte, Output, Health, Anwendung
- [[fortgeschrittene-architektur]] — RAG über Wiki-Seiten, Routing, Prompt Caching, Feintuning
- [[claude-md-design]] — 6 Designprinzipien für effektive CLAUDE.md-Anweisungsdateien
- [[seitenklassifikation]] — Zeitlos / Gemischt / Zeitgenössisch: Pflichtfeld für zeitliche Relevanz von Wiki-Seiten
- [[mece-prinzip]] — MECE-Ordnerstruktur für Vaults (keine Überlappungen, keine Lücken)
- [[taeglicher-workflow]] — Sitzungseröffnung und -abschluss als tägliches Protokoll
- [[modellunabhaengigkeit]] — Markdown = Unabhängigkeit vom KI-Anbieter
- [[yaml-frontmatter]] — Standardisierte Metadaten für Wiki-Seiten
- [[memex]] — Vannevar Bushs Memex (1945) als geistiger Vorläufer

## Werkzeuge

- [[obsidian]] — Kostenlose Markdown-basierte Notizen-App, dient als Wiki-Viewer/IDE
- [[claude-code]] — Anthropics KI-Agent, der das Wiki schreibt und pflegt
- [[obsidian-web-clipper]] — Browser-Erweiterung zum schnellen Quellen-Sammeln
- [[dataview]] — Obsidian-Plugin für Abfragen über YAML-Frontmatter-Felder
- [[marp]] — Obsidian-Plugin für Markdown-basierte Präsentationen
- [[jdocmunch]] — MCP-Server für sektionsbasierte Abfrage; ~95% Token-Reduktion
- [[qmd]] — Lokale semantische Suchmaschine (BM25/Vektor/Hybrid) von Tobi Lütke

## Personen

- [[andrej-karpathy]] — KI-Forscher, Urheber des LLM-Wiki-Musters
- [[mario]] — Webentwickler und YouTuber, hat das Konzept live demonstriert
- [[pasquale-pillitteri]] — IT-Berater, Obsidian + Claude Code als zweites Gehirn
- [[steph-ango]] — Obsidian-CEO, prägte die Vault-Trennungs-Empfehlung
- [[tobi-luetke]] — Shopify-CEO, Entwickler von qmd

## Quellen

- [[llm-wiki-karpathy]] — Andrej Karpathys originales Konzeptdokument (GitHub Gist)
- [[llm-wiki-tutorial-mit-mario]] — YouTube-Video: Live-Aufbau eines Kochrezepte-Wikis
- [[obsidian-claude-code-pillitteri]] — Blogartikel: Obsidian + Claude Code als zweites Gehirn
- [[llm-wiki-mehul-gupta]] — Medium: Küchenmetapher, Rollenverschiebung, Einstieg
- [[llm-wiki-urvil-joshi]] — Medium: Kompilierungs-Metapher, Tutorial-Demo, RAG vs. Wiki ehrlich
- [[llm-wiki-plaban-nayak]] — Level Up Coding: 5-Schritt-Pipeline, RAG über Wiki, Query-Templates
- [[llm-wiki-jgravelle]] — DEV.to: Skalierungsproblem + jDocMunch (95% Token-Reduktion)
- [[llm-wiki-venturebeat]] — VentureBeat: Community-Reaktionen, Enterprise, Feintuning
- [[llm-wiki-antigravity]] — Antigravity.codes: Anwendungsfälle, Kompilierungsprofile
- [[llm-wiki-analytics-vidhya]] — Analytics Vidhya: Tool-Stack (qmd, Git), Herausforderungen
- [[llm-wiki-mindstudio]] — MindStudio: Best Practices, Inbox-Muster, Team-Skalierung

## Vorlagen

- [[claude-md-software]] — CLAUDE.md-Vorlage für Software-Projekte (Lean oder Dokumentiert mit Projekt-Wiki)
- [[claude-md-legacy-forensik]] — CLAUDE.md-Vorlage für forensische Legacy-Codebase-Analyse
- [[claude-md-youtube-verlauf]] — CLAUDE.md-Vorlage für YouTube-Verlauf-Wikis
- [[claude-md-rezepte-ernaehrung]] — CLAUDE.md-Vorlage für Rezepte- und Ernährungs-Wikis

## Log

- [[log]] — Chronologisches Protokoll aller Änderungen

---
