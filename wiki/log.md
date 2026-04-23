---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Änderungsprotokoll

**Zusammenfassung**: Chronologisches Protokoll aller Vorgänge im Wiki.
**Zuletzt aktualisiert**: 2026-04-22

---

## [2026-04-23 00:00] ingest | 8 neue Clippings (Community-Reaktionen auf Karpathy)

Tiefe: **Mittel** — Quelltextzusammenfassungen + Konzeptseiten für wichtigste neue Ideen.

**8 neue Quelltextzusammenfassungen** in `wiki/quellen/`:
- `llm-wiki-mehul-gupta.md` — Mehul Gupta (Medium): Küchenmetapher, Rollenverschiebung
- `llm-wiki-urvil-joshi.md` — Urvil Joshi (Medium): Kompilierungs-Metapher, Tutorial-Demo
- `llm-wiki-plaban-nayak.md` — Plaban Nayak (Level Up Coding): 5-Schritt-Pipeline, RAG über Wiki
- `llm-wiki-jgravelle.md` — J. Gravelle (DEV.to): Skalierungsproblem + jDocMunch-Lösung
- `llm-wiki-venturebeat.md` — Carl Franzen (VentureBeat): Community-Reaktionen, Enterprise
- `llm-wiki-antigravity.md` — Antigravity.codes: Anwendungsfälle, Kompilierungsprofile
- `llm-wiki-analytics-vidhya.md` — Riya Bansal (Analytics Vidhya): Tool-Stack, Herausforderungen
- `llm-wiki-mindstudio.md` — MindStudio Team: Best Practices, Inbox-Muster

**6 neue Konzeptseiten** in `wiki/konzepte/`:
- `kompilierungs-metapher.md` — Rohquellen = Quellcode, Wiki = kompiliertes Binary
- `skalierungsgrenzen.md` — index.md-Bottleneck, Schwellenwerte, Lösungsansätze
- `vault-trennung.md` — Steph Angos Empfehlung: sauberes + Agenten-Vault trennen
- `kontaminierungsrisiko.md` — Halluzinationen als persistente Fakten; Minderungsstrategien
- `query-templates.md` — 6 Abfragetypen: Synthese, Lücken, Debatte, Output, Health, Anwendung
- `fortgeschrittene-architektur.md` — RAG über Wiki-Seiten, Routing, Prompt Caching, Feintuning

**2 neue Werkzeugseiten** in `wiki/werkzeuge/`:
- `jdocmunch.md` — Sektionsbasierte Abfrage, ~95% Token-Reduktion, MCP-Server
- `qmd.md` — Lokale semantische Suche (BM25/Vektor/Hybrid), Tobi Lütke

**2 neue Personenseiten** in `wiki/personen/`:
- `steph-ango.md` — Obsidian-CEO, Vault-Trennungs-Empfehlung
- `tobi-luetke.md` — Shopify-CEO, qmd-Entwickler

**3 bestehende Seiten aktualisiert**:
- `konzepte/rag-vs-wiki.md` — Kompilierungs-Metapher + Kontaminierungsrisiko ergänzt
- `konzepte/ingest-workflow.md` — Routing-Schritt + Skalierungsgrenzen ergänzt
- `werkzeuge/obsidian.md` — Vault-Trennung + Steph Ango ergänzt

`index.md` aktualisiert mit allen 18 neuen Seiten.

## [2026-04-22 22:45] ingest | CLAUDE.md-Vorlagen ins Wiki integriert

- `templates/`-Ordner aufgelöst — Vorlagen gehören als Wiki-Seiten ins Wiki (Karpathy-Prinzip: alles Wertvolle fließt zurück)
- 2 neue Wiki-Seiten unter `wiki/vorlagen/` erstellt:
  - `vorlagen/claude-md-software.md` — CLAUDE.md-Vorlage für Software-Projekte
  - `vorlagen/claude-md-legacy-forensik.md` — CLAUDE.md-Vorlage für Legacy-Forensik
- Beide Seiten mit Frontmatter, Konzeptbeschreibung und eingebetteter Vorlage zum Kopieren
- Mit Konzeptseiten verlinkt (`drei-ebenen-architektur`, `llm-wiki-muster`, `claude-code` etc.)
- `CLAUDE.md` korrigiert: `templates/` → `wiki/vorlagen/`
- `index.md` aktualisiert: neuer Abschnitt „Vorlagen"

## [2026-04-22 20:15] lint | Wiki-Gesundheitsprüfung

7 Befunde behoben:
1. **YAML-Frontmatter** auf allen 22 Wiki-Seiten ergänzt (`date`, `type`, `tags`, `status`)
2. **Personenseiten** `personen/mario.md` und `personen/pasquale-pillitteri.md` angelegt; in Quellseiten und Index verlinkt
3. **Link ergänzt**: `[[andrej-karpathy]]` in `quellen/llm-wiki-karpathy.md` (Fließtext + Verwandte Seiten)
4. **Link ergänzt**: `[[obsidian-web-clipper]]` in `konzepte/ingest-workflow.md` (Verwandte Seiten)
5. **Werkzeugseiten** `werkzeuge/dataview.md` und `werkzeuge/marp.md` angelegt; in `obsidian.md`, `yaml-frontmatter.md` und Index verlinkt
6. **Zitationslücke** in `konzepte/rag-vs-wiki.md`: Behauptung über NotebookLM/ChatGPT als `(überprüfungsbedürftig)` markiert
7. **Selbstreferenz** in `index.md`-Fußzeile entfernt

## [2026-04-22 19:36] Quelle aufgenommen: Obsidian + Claude Code (Pillitteri)

- Quelle gelesen und Erkenntnisse besprochen
- 6 neue Wiki-Seiten erstellt:
  - `quellen/obsidian-claude-code-pillitteri.md` — Zusammenfassung des Artikels
  - `konzepte/mece-prinzip.md` — MECE-Ordnerstruktur für Vaults
  - `konzepte/taeglicher-workflow.md` — Sitzungseröffnung und -abschluss
  - `konzepte/statelessness.md` — Das Zustandslosigkeits-Problem
  - `konzepte/modellunabhaengigkeit.md` — Markdown = Anbieterunabhängigkeit
  - `konzepte/yaml-frontmatter.md` — Standardisierte Metadaten
- 4 bestehende Seiten aktualisiert:
  - `werkzeuge/obsidian.md` — MECE, Graph-Test, Quelle 3
  - `werkzeuge/claude-code.md` — Statelessness, Quelle 3
  - `konzepte/drei-ebenen-architektur.md` — CLAUDE.md als Orchestrator
  - `konzepte/llm-wiki-muster.md` — Modellunabhängigkeit, Quelle 3
- `index.md` aktualisiert mit allen neuen Seiten

## [2026-04-22 19:31] Quelle aufgenommen: LLM Wiki Tutorial (Mit Mario)

- Quelle gelesen und Erkenntnisse besprochen
- 5 neue Wiki-Seiten erstellt:
  - `quellen/llm-wiki-tutorial-mit-mario.md` — Zusammenfassung des Videos
  - `konzepte/chat-problem.md` — Das fundamentale Problem: Wissen verschwindet in Chats
  - `personen/andrej-karpathy.md` — Urheber des LLM-Wiki-Konzepts
  - `werkzeuge/claude-code.md` — Claude Code/Cowork als Agent
  - `werkzeuge/obsidian-web-clipper.md` — Browser-Erweiterung zum Quellen-Sammeln
- 3 bestehende Seiten aktualisiert:
  - `werkzeuge/obsidian.md` — Quelle 2 als Referenz, neue verwandte Seiten
  - `konzepte/ingest-workflow.md` — Tiefe-Auswahl ergänzt
  - `konzepte/llm-wiki-muster.md` — Quelle 2 als Referenz, neue verwandte Seiten
- `index.md` aktualisiert mit allen neuen Seiten

## [2026-04-22 19:26] Quelle aufgenommen: llm-wiki.md (Karpathy Gist)

- Quelle gelesen und Erkenntnisse besprochen
- 9 Wiki-Seiten erstellt:
  - `quellen/llm-wiki-karpathy.md` — Zusammenfassung der Quelle
  - `konzepte/llm-wiki-muster.md` — Das Kernkonzept
  - `konzepte/drei-ebenen-architektur.md` — Rohquellen → Wiki → Schema
  - `konzepte/ingest-workflow.md` — Aufnahme-Prozess
  - `konzepte/query-workflow.md` — Fragen ans Wiki
  - `konzepte/lint-pruefung.md` — Wiki-Gesundheitsprüfung
  - `konzepte/rag-vs-wiki.md` — Vergleich RAG vs. Wiki
  - `konzepte/memex.md` — Vannevar Bushs Memex (1945)
  - `werkzeuge/obsidian.md` — Obsidian als IDE
- `index.md` aktualisiert mit allen neuen Seiten
- Alle Seiten untereinander verlinkt

## [2026-04-22 19:21] Initialisierung

- Wiki-Grundgerüst angelegt
- `index.md` erstellt (Inhaltsverzeichnis)
- `log.md` erstellt (dieses Protokoll)
- Drei Quellen in `clippings/` identifiziert, Aufnahme steht bevor

---

[Zurück zum Index](index.md)
