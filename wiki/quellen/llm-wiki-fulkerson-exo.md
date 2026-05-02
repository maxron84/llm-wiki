---
date: 2026-05-02
type: quelle
tags: [quelle, produktion, exokortex]
status: active
---

# Karpathy's Pattern in Production: Exo

**Zusammenfassung**: Aaron Fulkerson beschreibt zwei Monate produktiven Betriebs seines „Exo"-Systems — ein über Karpathys Grundmuster hinausgehender Exokortex mit 26 Skills, 14 MCP-Servern, 8 Hooks und drei formalen Lernschleifen.
**Quellen**: clippings/Karpathy's Pattern for an "LLM Wiki" in Production.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Kernaussage

Fulkerson zeigt, was passiert wenn man Karpathys Muster nicht als Konzept, sondern als tägliches Betriebssystem führt. Fünf Stellen, an denen die Theorie Erweiterungen braucht:

1. **Live-Datenfeeds statt statischer Dateien** — 14 MCP-Server ersetzen den `raw/`-Ordner. Kalender, E-Mail, CRM, Biometrie, Meeting-Transkripte fließen kontinuierlich ein.
2. **Skills statt Ad-hoc-Prompts** — 26 kodierte Workflows mit natürlichsprachlichen Triggern. „Prep Sarah" feuert einen vollständigen Meeting-Brief; keine Prompt-Improvisation bei 15 Meetings pro Woche.
3. **Drei Lernschleifen** — täglich Beobachtungen erfassen, wöchentlich reviewen, bewährte Muster in permanente CLAUDE.md-Regeln überführen (Graduation). Das System akkumuliert nicht nur Wissen, sondern Urteilsvermögen.
4. **Hooks erzwingen Verhalten** — CLAUDE.md-Anweisungen sind Vorschläge; Hook-Skripte sind deterministisch. Post-Commit-Hook: lernt als Seiteneffekt jeder Code-Änderung. Post-Compact-Hook: re-injiziert kritischen Zustand nach Kontext-Komprimierung.
5. **Auto-Enrichment als Seiteneffekt** — Jeder Skill, der eine Datei liest, schreibt beim Abschluss zurück. Meeting-Brief liest People-File → Meeting-Debrief aktualisiert es. Keine separaten Pflegesitzungen.

## Was Karpathy richtig lag, das Fulkerson übersehen hatte

Karpathys Gist brachte Fulkerson dazu, vier Lücken in seinem produktiven System zu entdecken:
- Kein Vault-weites Lint — erste Prüfung fand 23 verwaiste Dateien und 11 kaputte Links
- Keine INDEX.md — LLM suchte jedes Mal das Dateisystem durch
- Kein Änderungslog — kein Überblick über Vault-Evolution
- Keine Quell-Provenienz-Tags im Frontmatter (menschlich / LLM-generiert / LLM-generiert+menschlich geprüft)

## Relevanz

Reichhaltigste Praxisquelle im gesamten Wiki. Fulkerson war Mitgründer von MindTouch (Enterprise-Wiki, top-5 Open Source 2006–2010) — er denkt das Problem aus zwei Jahrzehnten Wissensmanagement-Erfahrung. Seine Mustersammlung: [github.com/AaronRoeF/claude-code-patterns](https://github.com/AaronRoeF/claude-code-patterns) (153 Muster, 13 spezifisch zum Wiki-Pattern).

## Verwandte Seiten

- [exokortex](../konzepte/exokortex.md) — Das System-Konzept hinter Exo
- [lernschleifen](../konzepte/lernschleifen.md) — Die drei formalen Lernschleifen
- [fortgeschrittene-architektur](../konzepte/fortgeschrittene-architektur.md) — Hooks, MCP, Routing
- [aaron-fulkerson](../personen/aaron-fulkerson.md)
- [llm-wiki-karpathy](llm-wiki-karpathy.md) — Das Grundmuster

---

[Wiki-Index](../index.md)
