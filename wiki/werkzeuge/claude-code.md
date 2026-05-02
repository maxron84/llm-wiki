---
date: 2026-04-22
type: werkzeug
tags: [werkzeug]
status: active
---

# Claude Code

**Zusammenfassung**: Anthropics KI-Agent mit Dateizugriff — schreibt und pflegt das Wiki, führt Aufgaben im Projektordner aus, und kann als geplanter Remote-Agent autonom laufen.
**Quellen**: clippings/LLM Wiki Tutorial - So baut KI dir eine Wissensbasis.md, clippings/llm-wiki.md, clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Was ist Claude Code?

Claude Code ist ein KI-Agent von Anthropic, der nativ mit lokalen Dateien arbeitet. Er kann Ordner lesen, Dateien schreiben, Strings suchen, Terminal-Befehle ausführen und Verknüpfungen erstellen — alles innerhalb eines Projektordners.

## Verfügbare Oberflächen

| Oberfläche | Für wen | Besonderheit |
|---|---|---|
| **Desktop App** (Windows/macOS) | Einsteiger, alle ohne Terminal-Kenntnisse | Einfachste Installation, kein Terminal nötig |
| **Web App** ([claude.ai/code](https://claude.ai/code)) | Alle | Im Browser, kein lokales Setup |
| **VS Code Extension** | Entwickler | Direkt in der IDE, mit Datei-Kontext |
| **JetBrains Extension** | Entwickler | Wie VS Code, für JetBrains-IDEs |
| **CLI** (Terminal) | Fortgeschrittene, Linux | Maximale Kontrolle, skriptbar |

Für die Einrichtung → [erste-schritte](../anleitungen/erste-schritte.md)

## Das Statelessness-Problem

Claude Code ist standardmäßig zustandslos. Jede Sitzung beginnt bei null — die KI erinnert sich an nichts aus früheren Gesprächen. Für Projekte über Wochen und Monate reicht das Kontextfenster allein nicht aus.

Das LLM-Wiki-Muster löst dieses Problem: Das Wiki ist der externe Speicher. Claude liest relevante Seiten zu Beginn jeder Sitzung und weiß damit, was bisher erarbeitet wurde — unabhängig davon, wie lange das Projekt schon läuft. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Rolle im LLM-Wiki

Im [llm-wiki-muster](../konzepte/llm-wiki-muster.md) ist Claude Code der „Programmierer":

- **Liest** Rohquellen und versteht den Inhalt
- **Schreibt** Wiki-Seiten in Markdown
- **Verknüpft** verwandte Konzepte über Links
- **Aktualisiert** bestehende Seiten wenn neue Quellen hinzukommen
- **Prüft** das Wiki auf Widersprüche und fehlende Links ([lint-pruefung](../konzepte/lint-pruefung.md))

Die Steuerung erfolgt über die `CLAUDE.md`-Datei im Projektordner — der dritten Ebene der [drei-ebenen-architektur](../konzepte/drei-ebenen-architektur.md).

## Geplante Agenten (Routinen)

Claude Code kann als **Remote-Agent** auf einem Cron-Zeitplan laufen — vollständig autonom, ohne dass jemand am Rechner sitzt. Der Agent läuft in Anthropics Cloud, klont das GitHub-Repo, führt die Aufgabe aus und pusht das Ergebnis zurück.

**Typische Anwendungsfälle:**
- Wiki-Seiten monatlich auf Aktualität prüfen und aktualisieren
- Regelmäßige Lint-Prüfungen automatisch durchführen
- Wiederkehrende Recherche-Aufgaben auf Zeitplan legen

**Einrichtung:**

1. GitHub-Repo verbinden: [claude.ai/code/onboarding](https://claude.ai/code/onboarding?magic=github-app-setup)
2. Routinen anlegen und verwalten: [claude.ai/code/routines](https://claude.ai/code/routines)
3. In Claude Code: `/schedule` aufrufen — ein geführter Assistent hilft beim Aufsetzen

**Wie eine Routine funktioniert:**

```
Zeitplan (z.B. 1. jeden Monats)
  → Agent startet in Anthropics Cloud
  → Klont das GitHub-Repo
  → Führt den definierten Prompt aus (lesen, recherchieren, schreiben)
  → Committet Änderungen
  → Pusht ins Repo
  → Fertig — kein menschliches Zutun nötig
```

**Voraussetzungen:**
- GitHub-Repo (öffentlich oder privat)
- GitHub-Verbindung in claude.ai eingerichtet
- Claude.ai-Abo (Pro oder Team)

> In diesem Wiki läuft bereits eine Routine: `lokale-modelle-aktualisierung` — prüft jeden 1. des Monats [lokale-modelle](../anleitungen/lokale-modelle.md) auf Aktualität und aktualisiert die Datei automatisch.

## Claude Code vs. Obsidian

| Werkzeug | Rolle |
|---|---|
| Claude Code | Schreibt und pflegt das Wiki |
| [Obsidian](obsidian.md) | Zeigt das Wiki an, ermöglicht Navigation |

Claude Code braucht weder ein Obsidian-Plugin noch einen MCP-Server. Es genügt, den Projektordner zu öffnen.

## Verwandte Seiten

- [llm-wiki-muster](../konzepte/llm-wiki-muster.md) — Das übergeordnete Wiki-Konzept
- [drei-ebenen-architektur](../konzepte/drei-ebenen-architektur.md) — Wo Claude Code in der Architektur steht
- [claude-md-design](../konzepte/claude-md-design.md) — 6 Designprinzipien für die CLAUDE.md
- [statelessness](../konzepte/statelessness.md) — Das Zustandslosigkeits-Problem
- [erste-schritte](../anleitungen/erste-schritte.md) — Einrichtungsanleitung
- [lokale-modelle](../anleitungen/lokale-modelle.md) — Betrieb ohne Claude Code mit lokalen Modellen
- [obsidian](obsidian.md)

---

[Wiki-Index](../index.md)
