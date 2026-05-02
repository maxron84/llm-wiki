---
date: 2026-05-02
type: konzept
tags: [konzept, produktion]
status: active
---

# Lernschleifen

**Zusammenfassung**: Aaron Fulkersons drei formale Lernschleifen machen den Unterschied zwischen einem Wiki das nur Wissen akkumuliert und einem System das Urteilsvermögen akkumuliert. Täglich erfassen, wöchentlich reviewen, bewährte Muster durch Graduation in permanente Regeln überführen.
**Quellen**: clippings/Karpathy's Pattern for an "LLM Wiki" in Production.md
**Zuletzt aktualisiert**: 2026-05-02

---

## Das Grundprinzip

Karpathys Muster sieht vor, dass gute Antworten als neue Wiki-Seiten zurückgespeichert werden (mehr: [query-workflow](query-workflow.md)). Fulkerson formalisiert diesen Gedanken in drei expliziten Schleifen:

> „The system doesn't just accumulate knowledge. It accumulates judgment." — Aaron Fulkerson (Quelle: clippings/Karpathy's Pattern for an "LLM Wiki" in Production.md)

Wissen ist das Was. Urteilsvermögen ist das Wie. Lernschleifen überführen einmalige Beobachtungen in generalisierte Regeln.

## Die drei Schleifen

### Schleife 1: Tägliche Beobachtungserfassung

**Frequenz**: täglich, als natürlicher Seiteneffekt der Arbeit

**Was erfasst wird**:
- Beobachtungen darüber, wie das System arbeitet
- Muster in Kundenkonversationen oder Arbeitsprozessen
- Fehler die gemacht wurden
- Erkenntnisse aus Lektüre oder Meetings

Bei Fulkerson läuft das automatisch: Ein **Post-Commit-Hook** erfasst Lernbeobachtungen bei jedem Code-Commit. Die Lernschleife ist ein Nebeneffekt des Arbeitens — keine separate Aufgabe.

### Schleife 2: Wöchentliches Review

**Frequenz**: wöchentlich, manuell ausgelöst

**Was passiert**:
1. Akkumulierte tägliche Beobachtungen werden gescannt
2. Schnittsessionale Muster werden identifiziert
3. Graduation-Kandidaten werden vorgeschlagen

Das Review-Prompt zieht alle Beobachtungen der Woche und fragt: Welche Muster wiederholen sich? Was hat sich oft genug bewahrheitet, um eine permanente Regel zu werden?

### Schleife 3: Graduation

**Was Graduation bedeutet**: Ein Muster hat genug Evidenz, um dauerhaft zu werden. Graduation bedeutet, es in eine von drei Formen zu überführen:

| Ziel | Inhalt | Wo |
|---|---|---|
| **CLAUDE.md-Regel** | Verhaltensanweisung die immer gilt | CLAUDE.md Instruktionen |
| **Skill-Verbesserung** | Workflow-Verfeinerung in einer Skill-Datei | Skill-Bibliothek |
| **Wissensbasis-Eintrag** | Neues Konzept oder validiertes Faktum | Wiki-Seite |

Das Ergebnis: Fulkersons ICP-Personas (Ideal Customer Profiles) starteten als leere Templates. Nach zwei Monaten Graduations aus echten Vertriebsgesprächen enthielten sie Erkenntnisse, die ein CISO in seinem Buying Committee wiedererkannte.

## Abgrenzung zum CLAUDE.md-Gradientenprinzip

Die Lernschleifen sind kein CLAUDE.md-Feature — sie sind ein Prozess. CLAUDE.md-Anweisungen beschreiben, wie das System arbeiten soll. Lernschleifen beschreiben, wie das System lernt, besser zu arbeiten. Beide brauchen einander:

- Ohne Lernschleifen: CLAUDE.md ist ein statisches Dokument
- Ohne CLAUDE.md: Lernschleifen haben keinen Ort, wohin Graduations fließen können

## Einstieg: Minimal-Implementierung

Man braucht keine 26 Skills um mit Lernschleifen anzufangen. Fulkersons Einstiegsempfehlung:

1. **Tägliche Notizpflege**: Am Tagesende eine kurze Beobachtung in eine `observations/YYYY-MM-DD.md` schreiben
2. **Wöchentlicher Review-Prompt**: „Welche Muster siehst du in meinen Beobachtungen der letzten Woche? Was sollte in die CLAUDE.md?"
3. **CLAUDE.md-Pflege**: Validierte Muster als Regeln eintragen

Das entspricht dem [taeglichen-workflow](taeglicher-workflow.md), ergänzt um die Graduation-Praxis.

## Verwandte Seiten

- [llm-wiki-fulkerson-exo](../quellen/llm-wiki-fulkerson-exo.md) — Quellartikel
- [exokortex](exokortex.md) — Das übergeordnete Systemkonzept
- [taeglicher-workflow](taeglicher-workflow.md) — Minimale tägliche Praxis
- [query-workflow](query-workflow.md) — Gute Antworten zurück ins Wiki
- [claude-md-design](claude-md-design.md) — Wohin Graduations fließen
- [aaron-fulkerson](../personen/aaron-fulkerson.md)

---

[Wiki-Index](../index.md)
