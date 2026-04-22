---
date: 2026-04-22
type: konzept
tags: [konzept]
status: active
---

# Täglicher Workflow: Sitzungseröffnung und -abschluss

**Zusammenfassung**: Ein minimales tägliches Protokoll mit zwei Befehlen — Morgen und Abend —, das den Obsidian-Vault als „zweites Gehirn" stets aktuell hält.
**Quellen**: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md
**Zuletzt aktualisiert**: 2026-04-22

---

## Die Idee

Der tägliche Workflow besteht aus zwei Ritualen, die zusammen das [[statelessness|Zustandslosigkeits-Problem]] von [[claude-code|Claude Code]] lösen. Zwei Befehle reichen aus, um unbegrenzten Speicher zu erreichen — ohne monastische Disziplin. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Sitzungseröffnung: „Guten Morgen"

Der erste Prompt des Tages löst einen in der `CLAUDE.md` definierten Workflow aus. Claude:

1. Liest das Systemdatum
2. Erstellt oder öffnet die Tagesnotiz (z.B. `/Daily/2026-04-22.md`)
3. Ruft die gestrige Notiz ab — offene Aufgaben und getroffene Entscheidungen
4. Liest die langfristigen Ziele in `/Context`
5. Begrüßt dich, fasst offene Aufgaben zusammen und fragt nach den heutigen Prioritäten

In **20 Sekunden** weiß die KI, was du tust, was offen geblieben ist und wohin du unterwegs warst. Ohne das müsstest du jeden Morgen alles manuell erklären. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Sitzungsabschluss: „Für heute sind wir fertig"

Der Abschluss ist noch wichtiger. Claude stellt fünf kurze Fragen:

1. **Was haben wir heute abgeschlossen?**
2. **Was haben wir gelernt, das sich zu merken lohnt?**
3. **Was hat morgen Priorität?**
4. **Gibt es Kontext, den ich für die nächste Sitzung behalten muss?**
5. **Sollte etwas aus dem heutigen Tag zu einer SOP (wiederverwendbarer Skill) werden?**

Die Antworten werden in der Tagesnotiz gespeichert, mit relevanten Ordnern verlinkt und bei wiederkehrenden Mustern in einen neuen Skill verwandelt. (Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Was man davon hat

- **Kontinuierlicher Kontext** — Keine wiederholten Erklärungen, Wissen summiert sich
- **Audit-Trail** — Jede Entscheidung ist über die Tagesnotiz rekonstruierbar
- **Emergente Skills** — Workflows konsolidieren sich automatisch zu Mustern
- **Null kognitiver Overhead** — Du schreibst im Chat, das System archiviert

## Analogie

> „Das System wird mit der Zeit intelligenter, genau wie das menschliche Gehirn während des Schlafs Erinnerungen konsolidiert."

(Quelle: clippings/Obsidian + Claude Code Das zweite Gehirn, das KI-Agenten wirklich nützlich macht.md)

## Bezug zum LLM-Wiki

Dieser Workflow ergänzt das [[llm-wiki-muster]] um eine **tägliche Ebene**. Während das LLM-Wiki auf Quellen-Aufnahme und -Abfrage fokussiert ist, kümmert sich der tägliche Workflow um den **persönlichen Arbeitsalltag**. Beide Ansätze teilen die Kernidee: Wissen fließt in persistente Dateien statt in flüchtige Chats.

## Verwandte Seiten

- [[obsidian-claude-code-pillitteri]]
- [[statelessness]]
- [[claude-code]]
- [[mece-prinzip]]
- [[chat-problem]]

---

[Zurück zum Index](../index.md)
