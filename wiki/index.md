---
date: 2026-04-23
type: konzept
tags: [konzept]
status: active
---

# Wiki-Index

**Zusammenfassung**: Inhaltsverzeichnis und Übersicht aller Seiten im LLM-Wiki.
**Zuletzt aktualisiert**: 2026-05-15 (Ralph Wiggum Plugin-Seite hinzugefügt)

---

Dieses Wiki ist eine strukturierte, vernetzte Wissensdatenbank für das LLM-Wiki-Konzept. Claude pflegt das Wiki. Der Mensch kuratiert Quellen, stellt Fragen und leitet die Analyse.

## Konzepte

- [llm-wiki-muster](konzepte/llm-wiki-muster.md) — Das Kernkonzept: KI baut ein persistentes, wachsendes Wiki statt RAG
- [chat-problem](konzepte/chat-problem.md) — Das fundamentale Problem: Wissen verschwindet in Chats
- [statelessness](konzepte/statelessness.md) — Das technische Problem: KI-Agenten sind zustandslos
- [drei-ebenen-architektur](konzepte/drei-ebenen-architektur.md) — Rohquellen → Wiki → Schema
- [ingest-workflow](konzepte/ingest-workflow.md) — Der Prozess zur Aufnahme neuer Quellen (inkl. Tiefe-Auswahl)
- [query-workflow](konzepte/query-workflow.md) — Fragen gegen das Wiki stellen und Antworten zurückspeichern
- [lint-pruefung](konzepte/lint-pruefung.md) — Regelmäßige Gesundheitsprüfung des Wikis
- [rag-vs-wiki](konzepte/rag-vs-wiki.md) — Vergleich: RAG vs. persistentes Wiki
- [kompilierungs-metapher](konzepte/kompilierungs-metapher.md) — Rohquellen = Quellcode, Wiki = kompiliertes Binary
- [skalierungsgrenzen](konzepte/skalierungsgrenzen.md) — index.md-Bottleneck bei ~50–100K Token; Lösungsansätze
- [vault-trennung](konzepte/vault-trennung.md) — Sauberes Vault (menschlich) + Agenten-Vault (KI) trennen
- [kontaminierungsrisiko](konzepte/kontaminierungsrisiko.md) — Halluzinationen werden als persistente Fakten eingebacken
- [query-templates](konzepte/query-templates.md) — 6 Abfragetypen: Synthese, Lücken, Debatte, Output, Health, Anwendung
- [fortgeschrittene-architektur](konzepte/fortgeschrittene-architektur.md) — RAG über Wiki-Seiten, Routing, Prompt Caching, Feintuning
- [claude-md-design](konzepte/claude-md-design.md) — 6 Designprinzipien für effektive CLAUDE.md-Anweisungsdateien
- [seitenklassifikation](konzepte/seitenklassifikation.md) — Zeitlos / Gemischt / Zeitgenössisch: Pflichtfeld für zeitliche Relevanz von Wiki-Seiten
- [multimodale-quellen](konzepte/multimodale-quellen.md) — *(Konzeptskizze)* Audio, Bild und Video als Wiki-Quellen jenseits von Text
- [mece-prinzip](konzepte/mece-prinzip.md) — MECE-Ordnerstruktur für Vaults (keine Überlappungen, keine Lücken)
- [taeglicher-workflow](konzepte/taeglicher-workflow.md) — Sitzungseröffnung und -abschluss als tägliches Protokoll
- [modellunabhaengigkeit](konzepte/modellunabhaengigkeit.md) — Markdown = Unabhängigkeit vom KI-Anbieter
- [yaml-frontmatter](konzepte/yaml-frontmatter.md) — Standardisierte Metadaten für Wiki-Seiten
- [memex](konzepte/memex.md) — Vannevar Bushs Memex (1945) als geistiger Vorläufer
- [community-projekte](konzepte/community-projekte.md) — Swarm Vault, BrainDB, Menmo Vault: drei herausragende Implementierungen
- [llm-wiki-v2](konzepte/llm-wiki-v2.md) — Community-Erweiterung: typisierte Beziehungen, Lifecycle, Automation, Trust Score
- [enterprise-skalierung](konzepte/enterprise-skalierung.md) — Warum das persönliche Muster bei Unternehmen bricht und wie Enterprise-Wikis anders arbeiten müssen
- [exokortex](konzepte/exokortex.md) — Aaron Fulkersons „Exo" als externes kognitives Betriebssystem: 26 Skills, 14 MCP-Server, Auto-Enrichment
- [lernschleifen](konzepte/lernschleifen.md) — Drei formale Schleifen: täglich erfassen, wöchentlich reviewen, bewährte Muster in CLAUDE.md-Regeln graduieren
- [ralph-schleife](konzepte/ralph-schleife.md) — Geoffrey Huntleys deterministisches Agentenmuster: while-true-Loop für autonomen Ingest, $10,42/h Sonnet-Benchmark
- [usd-pro-wiki-seite](konzepte/usd-pro-wiki-seite.md) — Stückkostenmetrik US$/WP: ~$0,42/Seite Lifetime bei Sonnet 4.6 in einer Ralph-Schleife

## Werkzeuge

- [obsidian](werkzeuge/obsidian.md) — Kostenlose Markdown-basierte Notizen-App, dient als Wiki-Viewer/IDE
- [claude-code](werkzeuge/claude-code.md) — Anthropics KI-Agent, der das Wiki schreibt und pflegt
- [obsidian-web-clipper](werkzeuge/obsidian-web-clipper.md) — Browser-Erweiterung zum schnellen Quellen-Sammeln
- [dataview](werkzeuge/dataview.md) — Obsidian-Plugin für Abfragen über YAML-Frontmatter-Felder
- [marp](werkzeuge/marp.md) — Obsidian-Plugin für Markdown-basierte Präsentationen
- [jdocmunch](werkzeuge/jdocmunch.md) — MCP-Server für sektionsbasierte Abfrage; ~95% Token-Reduktion
- [qmd](werkzeuge/qmd.md) — Lokale semantische Suchmaschine (BM25/Vektor/Hybrid) von Tobi Lütke
- [wiki-ralph-sh](werkzeuge/wiki-ralph-sh.md) — Bash-Skript: Ralph-Schleife für autonomen LLM-Wiki-Ingest aus inbox/
- [radeon-ai-pro-r9700](werkzeuge/radeon-ai-pro-r9700.md) — 32 GB GDDR6 KI-Karte: mehr VRAM als RTX 5080, aber Blower-Kühler macht sie für Desktop-Einsatz problematisch
- [phi-4](werkzeuge/phi-4.md) — Microsofts Phi-4-Familie (14B/3.8B): stark bei Math und Code, 16K Kontext ein Limit für Wiki-Betrieb
- [ralph-wiggum-plugin](werkzeuge/ralph-wiggum-plugin.md) — Offizielles Claude-Code-Plugin für autonome Iterationsschleifen: Stop-Hook, /ralph-loop, Anwendung auf Wiki-Ingest

## Personen

- [andrej-karpathy](personen/andrej-karpathy.md) — KI-Forscher, Urheber des LLM-Wiki-Musters
- [mario](personen/mario.md) — Webentwickler und YouTuber, hat das Konzept live demonstriert
- [pasquale-pillitteri](personen/pasquale-pillitteri.md) — IT-Berater, Obsidian + Claude Code als zweites Gehirn
- [steph-ango](personen/steph-ango.md) — Obsidian-CEO, prägte die Vault-Trennungs-Empfehlung
- [tobi-luetke](personen/tobi-luetke.md) — Shopify-CEO, Entwickler von qmd
- [aaron-fulkerson](personen/aaron-fulkerson.md) — Mitgründer MindTouch, Erbauer von Exo — reichhaltigste Produktionsquelle im Wiki
- [geoffrey-huntley](personen/geoffrey-huntley.md) — Erfinder der Ralph-Schleife, $10,42/h-Kostenbenchmark für agentische Arbeit

## Quellen

- [llm-wiki-karpathy](quellen/llm-wiki-karpathy.md) — Andrej Karpathys originales Konzeptdokument (GitHub Gist)
- [llm-wiki-tutorial-mit-mario](quellen/llm-wiki-tutorial-mit-mario.md) — YouTube-Video: Live-Aufbau eines Kochrezepte-Wikis
- [obsidian-claude-code-pillitteri](quellen/obsidian-claude-code-pillitteri.md) — Blogartikel: Obsidian + Claude Code als zweites Gehirn
- [llm-wiki-mehul-gupta](quellen/llm-wiki-mehul-gupta.md) — Medium: Küchenmetapher, Rollenverschiebung, Einstieg
- [llm-wiki-urvil-joshi](quellen/llm-wiki-urvil-joshi.md) — Medium: Kompilierungs-Metapher, Tutorial-Demo, RAG vs. Wiki ehrlich
- [llm-wiki-plaban-nayak](quellen/llm-wiki-plaban-nayak.md) — Level Up Coding: 5-Schritt-Pipeline, RAG über Wiki, Query-Templates
- [llm-wiki-jgravelle](quellen/llm-wiki-jgravelle.md) — DEV.to: Skalierungsproblem + jDocMunch (95% Token-Reduktion)
- [llm-wiki-venturebeat](quellen/llm-wiki-venturebeat.md) — VentureBeat: Community-Reaktionen, Enterprise, Feintuning
- [llm-wiki-antigravity](quellen/llm-wiki-antigravity.md) — Antigravity.codes: Anwendungsfälle, Kompilierungsprofile
- [llm-wiki-analytics-vidhya](quellen/llm-wiki-analytics-vidhya.md) — Analytics Vidhya: Tool-Stack (qmd, Git), Herausforderungen
- [llm-wiki-mindstudio](quellen/llm-wiki-mindstudio.md) — MindStudio: Best Practices, Inbox-Muster, Team-Skalierung
- [llm-wiki-fulkerson-exo](quellen/llm-wiki-fulkerson-exo.md) — Aaron Fulkerson: 2 Monate Produktionsbetrieb mit Exo (26 Skills, 14 MCP-Server, 3 Lernschleifen)
- [llm-wiki-tecadrise](quellen/llm-wiki-tecadrise.md) — TecAdRise.ai: Karpathys Idee als Bewegung, Community-Projekte, Constrained Pipelines
- [llm-wiki-v2-tamiltech](quellen/llm-wiki-v2-tamiltech.md) — Tamiltech: LLM Wiki v2 mit Produktionserfahrung — typisierte Beziehungen, Hooks, Trust Score
- [llm-wiki-enterprise-falconer](quellen/llm-wiki-enterprise-falconer.md) — Falconer: Enterprise-Skalierung — vier Eigenschaften, warum persönliche Muster nicht skalieren
- [ralph-claude-code-llm-wiki-metrik](quellen/ralph-claude-code-llm-wiki-metrik.md) — Ökonomische Analyse: Ralph + Karpathy, US$/WP-Metrik, drei Kostenszenarien

## Vorlagen

- [claude-md-software](vorlagen/claude-md-software.md) — CLAUDE.md-Vorlage für Software-Projekte (Lean oder Dokumentiert mit Projekt-Wiki)
- [claude-md-software-begleiter](vorlagen/claude-md-software-begleiter.md) — CLAUDE.md-Vorlage: KI als Begleiter — Mensch schreibt Code, KI dokumentiert und reviewt
- [claude-md-lehrer](vorlagen/claude-md-lehrer.md) — CLAUDE.md-Vorlage: KI als Lehrbegleiter — adaptiver Unterricht mit dynamisch generiertem Lehrplan
- [claude-md-legacy-forensik](vorlagen/claude-md-legacy-forensik.md) — CLAUDE.md-Vorlage für forensische Legacy-Codebase-Analyse
- [claude-md-youtube-verlauf](vorlagen/claude-md-youtube-verlauf.md) — CLAUDE.md-Vorlage für YouTube-Verlauf-Wikis
- [claude-md-rezepte-ernaehrung](vorlagen/claude-md-rezepte-ernaehrung.md) — CLAUDE.md-Vorlage für Rezepte- und Ernährungs-Wikis
- [claude-md-nachhilfe](vorlagen/claude-md-nachhilfe.md) — CLAUDE.md-Vorlage: KI als Fachlehrer — reaktiv, aufgabengetrieben, für Schüler bis Klasse 10
- [claude-md-laienlehrer](vorlagen/claude-md-laienlehrer.md) — CLAUDE.md-Vorlage: KI als Unterrichtscoach für Eltern und Laien — zweistufige Erklärungen, Stundenvorbereitung, Korrektur
- [claude-md-team](vorlagen/claude-md-team.md) — CLAUDE.md-Vorlage: Team-Wiki für 2–8 Personen mit Ownership, Provenienz-Tags und Entwurfs-Pufferzone

## Anleitungen

- [erste-schritte](anleitungen/erste-schritte.md) — Setup von Null: Claude Code, Obsidian, Vorlage wählen, erste Sitzung starten
- [lokale-modelle](anleitungen/lokale-modelle.md) — Betrieb mit lokalen Modellen: LM Studio (Anfänger), Ollama + Open WebUI + Continue (Fortgeschrittene)
- [vorlage-einrichten](anleitungen/vorlage-einrichten.md) — Schritt für Schritt: Vorlage kopieren, Platzhalter füllen, Projekt starten (Beispiel: Pygame-Lernprojekt)
- [lokale-modelle-fortgeschritten](anleitungen/lokale-modelle-fortgeschritten.md) — Pygame-Projekt mit Qwen3 27B/35B lokal: Ollama, Continue in VS Codium, halbautomatischer Wiki-Betrieb
- [beratungs-crm-einrichtung](anleitungen/beratungs-crm-einrichtung.md) — Beratungs-CRM auf Linux einrichten: Ollama, VS Codium, Python-Skripte, Obsidian, erstes Klientenprofil
- [token-sparen](anleitungen/token-sparen.md) — Token sparen mit Claude Code: 4-stufige Strategie von Sofortmaßnahmen bis fortgeschrittener Architektur
- [zusammenarbeit-git](anleitungen/zusammenarbeit-git.md) — Gemeinsam am Wiki arbeiten: GitHub Collaborators, Branch-Workflow, Konflikte lösen

## Projekte

- [beratungs-crm](projekte/beratungs-crm.md) — Konzept: lokales CRM für psychologische Beraterin mit Numerologie, Astrologie, Rechnungsstellung und KI-Sitzungsvorbereitung

## Log

- [log](log.md) — Chronologisches Protokoll aller Änderungen

---
