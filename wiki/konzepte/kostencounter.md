---
date: 2026-07-02
type: konzept
tags: [konzept, kosten, automatisierung, metrik]
status: active
---

# Kostencounter

**Zusammenfassung**: Einheitlicher Standard für das Tracking von KI-Kosten in CLAUDE.md-Vorlagen — modell- und plattformagnostisch, mit Pflichtabschluss für abgeschlossene Projekte und fortlaufendem Report für offene Projekte.
**Quellen**: Abgeleitet aus [usd-pro-wiki-seite](usd-pro-wiki-seite.md), [ralph-claude-code-llm-wiki-metrik](../quellen/ralph-claude-code-llm-wiki-metrik.md), [claude-md-token-sparen](claude-md-token-sparen.md)
**Zuletzt aktualisiert**: 2026-07-11

---

## Grundprinzip

KI-Kosten entstehen in jeder Session — unabhängig davon, ob das Modell über eine Cloud-API (Claude, GPT-4o) oder lokal (Ollama, LM Studio) läuft. Der Kostencounter macht diese Kosten sichtbar und vergleichbar:

- **Cloud-Modelle**: Token-Kosten in USD (Input × Preis/Mio. Token + Output × Preis/Mio. Token)
- **Lokale Modelle**: Strom- und Hardware-Abschreibungskosten, oder „kostenlos" wenn der Overhead nicht erfasst wird — dann zumindest Kontext-Budget in Token notieren
- **Agentische Loops** (z.B. ralph.sh): Kosten pro Iteration × Iterationsanzahl

Das Format ist **plattformagnostisch**: es funktioniert mit Claude Code, Zoo Code, Cursor, Cline — jeder Plattform, die Sessions führt. Es verlässt sich auf Schätzungen, nicht auf API-spezifische Abrufe.

---

## Zwei Varianten

### Abschluss-Variante (für abgeschlossene Projekte)

Für Projekte mit definiertem Ende: Software-Entwicklung, Legacy-Forensik, Team-Projekte.

**Pflicht**: Vor dem finalen Commit `wiki/kosten.md` mit Gesamtauswertung abschließen. Diese Auswertung ist Teil des Projekt-Abschluss-Artefakts.

Enthält in der CLAUDE.md:
- Budget-Limit pro Session + Gesamtprojekt
- Session-Tracking-Pflicht
- Token-Sparregeln
- Abschluss-Pflicht mit Gesamtsumme

### Fortlaufend-Variante (für offene Projekte)

Für Projekte ohne festes Ende: Lehrer, Nachhilfe, Rezepte-Wiki, YouTube-Verlauf, allgemeine Wikis.

**Rhythmus**: Am Ende jeder Session einen Eintrag; monatlich eine Zwischensumme.

Enthält in der CLAUDE.md:
- Budget-Limit pro Session
- Session-Tracking am Sitzungsende
- Token-Sparregeln
- Monatliche Zwischensummen

---

## Standard-Blöcke für CLAUDE.md

### Block A — Abschluss-Variante

```markdown
## Kostenkontrolle

**Modell / Plattform**: {{z.B. claude-sonnet-4-6 via Claude Code | qwen3:14b via Zoo Code lokal}}
**Budget-Limit**: {{N}} USD pro Session | {{M}} USD gesamt
**Warnschwelle**: {{N×0,8}} USD pro Session

Kosten werden in `wiki/kosten.md` protokolliert. Am Ende jeder Session eine Zeile anlegen.

**Token-Sparregeln (stets aktiv):**
- Dateien nur einmal pro Session lesen
- `offset` + `limit` bei Dateien > 100 Zeilen (max. 50 Zeilen pro Abruf)
- Pro Antwort max. 20 Zeilen, wenn nicht mehr gefragt
- Bei Erreichen der Warnschwelle: Benutzer informieren, bevor weitergemacht wird

**Projektabschluss-Pflicht:**
Vor dem letzten Commit `wiki/kosten.md` mit vollständiger Gesamtauswertung abschließen:
- Tabelle aller Sessions mit Kosten
- Gesamtsumme
- Kosteneffizienz-Notiz (z.B. Kosten pro Feature / Kosten pro Wiki-Seite)
```

### Block B — Fortlaufend-Variante

```markdown
## Kostenkontrolle

**Modell / Plattform**: {{z.B. claude-sonnet-4-6 via Claude Code | qwen3:14b via Zoo Code lokal}}
**Budget-Limit**: {{N}} USD pro Session

Kosten werden in `wiki/kosten.md` protokolliert. Am Ende jeder Session einen Eintrag anlegen. Monatlich eine Zwischensumme hinzufügen.

**Token-Sparregeln (stets aktiv):**
- Dateien nur einmal pro Session lesen
- `offset` + `limit` bei Dateien > 100 Zeilen (max. 50 Zeilen pro Abruf)
- Pro Antwort max. 20 Zeilen, wenn nicht mehr gefragt
- Bei Erreichen des Budget-Limits: Benutzer informieren, bevor weitergemacht wird
```

---

## Format: kosten.md

Jedes Projekt bekommt eine `wiki/kosten.md`. Format ist identisch für beide Varianten:

```markdown
# Projektkosten

| Datum | Plattform / Modell | Aufgabe | Input (ca.) | Output (ca.) | Kosten (ca.) |
|---|---|---|---|---|---|
| YYYY-MM-DD | claude-sonnet-4-6 (Claude Code) | Ingest: Quellenname | ~10k Token | ~3k Token | ~$0,05 |
| YYYY-MM-DD | qwen3:14b-40k (Zoo Code, lokal) | Ingest: Quellenname | ~15k Token | ~4k Token | kostenlos |
| YYYY-MM-DD | claude-sonnet-4-6 (Ralph-Loop, 4 It.) | Feature X bauen | ~40k Token | ~12k Token | ~$0,22 |

---

**Gesamtkosten bis dato**: ~$X,XX

## Methodik

Token-Zahlen sind Schätzungen aus dem Tool-Interface oder manuell geschätzt.
Preisreferenz: Zum Zeitpunkt der Session gelten Listenpreise des Anbieters (Marktpreise ändern sich).
Lokale Modelle: Strom-/Hardware-Kosten werden nicht erfasst, sofern nicht relevant.

<!-- Monatliche Zwischensumme (nur Fortlaufend-Variante) -->
## Zwischensumme Juli 2026

Sessions: X | Tokens Input gesamt: ~Xk | Tokens Output gesamt: ~Xk | Kosten gesamt: ~$X,XX
```

### Abschluss-Auswertung (nur Abschluss-Variante)

Am Projektende wird `wiki/kosten.md` um diesen Block ergänzt:

```markdown
## Projektabschluss-Auswertung

**Projektlaufzeit**: YYYY-MM-DD bis YYYY-MM-DD
**Sessions gesamt**: X
**Tokens Input gesamt**: ~Xk
**Tokens Output gesamt**: ~Xk
**Gesamtkosten**: ~$X,XX

**Kosteneffizienz**:
- Kosten pro Feature / Stufe / Befund: ~$X,XX
- Kosten pro Wiki-Seite (falls relevant): ~$X,XX (→ Vergleich: [usd-pro-wiki-seite](usd-pro-wiki-seite.md))

**Erkenntnisse**:
{{Was war überraschend teuer? Was war günstiger als erwartet? Welche Aufgaben haben das Budget dominiert?}}
```

---

## Vorlagen-Zuordnung

| Vorlage | Variante |
|---|---|
| claude-md-software | Abschluss |
| claude-md-software-begleiter | Abschluss |
| claude-md-softwareprojekt-rookie | Abschluss (ergänzt ralph.sh-Budget) |
| claude-md-legacy-forensik | Abschluss |
| claude-md-team | Abschluss |
| claude-md-ki-team | Abschluss (Budget-Cap pro Stufe/Fall, `RALPH_BUDGET_USD`) — Ausnahmen ggü. dem Standard: (a) Token-Sparregeln optional/Default AUS statt „stets aktiv"; (b) **optionale Budget-Governance** (Feldprojekt-Erweiterung 2026-07-11): committete `.budget-ledger` als historische Basis, `./team-status.sh --budget`-Kontostand (Abo/API getrennt), `BUDGET_EMPFEHLUNG_USD=…`-Zeile je Plan mit Nur-Anheben-Automatik — **Pro-Lauf-Deckel = operative Grenze, Gesamtstand nur dokumentiert** (kein starrer wandernder Gesamtdeckel). Siehe [claude-md-ki-team](../vorlagen/claude-md-ki-team.md), Anhang A.7 |
| claude-md-lehrer | Fortlaufend |
| claude-md-nachhilfe | Fortlaufend |
| claude-md-laienlehrer | Fortlaufend |
| claude-md-youtube-verlauf | Fortlaufend |
| claude-md-rezepte-ernaehrung | Fortlaufend |
| zoocode-llm-wiki-lokal | Fortlaufend (Kontext-Budget statt USD) |

---

## Messen statt schätzen (2026-08-01)

Der Kostencounter erfasst headless-Läufe exakt (`total_cost_usd` aus der
Konsole), interaktive Sitzungen dagegen nur geschätzt — im Abo gibt es keinen
Konsolenwert. Das Feldprojekt behalf sich mit einem **Zeilen-Proxy**
(A2-Churn, Eichfaktor `16/1045` USD je geänderter Zeile).

Das Transkript von Claude Code enthält die echten Token-Zahlen, also lässt sich
auch eine interaktive Sitzung nachträglich **messen**. Wie das geht, welche zwei
Fallen dabei stecken und wie man das Preismodell gegen einen bekannten
Konsolenwert eicht, steht in
[sitzungskosten-aus-transkript](../anleitungen/sitzungskosten-aus-transkript.md).

Die erste so gemessene Sitzung ergab zwei Dinge:

- **Der Zeilen-Proxy lag nur 1,5 % daneben** (168,09 geschätzt / 165,58
  gemessen) — als Notbehelf also brauchbar, bei `n = 1` aber nicht bestätigt.
- **Drei Viertel der Kosten waren Cache-Reads**, Verhältnis 276 : 1 zum Output.
  Bezahlt wird überwiegend das erneute Vorlegen des gewachsenen Kontexts, nicht
  der erzeugte Text. Ein Kostencounter, der nur Ausgabe-Volumen betrachtet,
  misst am Haupttreiber vorbei.

---

## Verhältnis zu anderen Kostenkonzepten

- **[sitzungskosten-aus-transkript](../anleitungen/sitzungskosten-aus-transkript.md)**: Liefert die Messmethode für genau die Lücke, die der Kostencounter offenlässt — interaktive Sitzungen ohne Konsolenwert.
- **[usd-pro-wiki-seite](usd-pro-wiki-seite.md)**: Die US$/WP-Metrik ist eine spezifische Kosteneffizienz-Kennzahl für Wiki-Seiten. Der Kostencounter ist der allgemeinere Standard, der US$/WP als optionale Vergleichszahl einbetten kann.
- **[ralph-schleife](ralph-schleife.md)**: Ralph läuft mit `RALPH_BUDGET_USD`-Env-Variable. Das Kostencounter-Format in `wiki/kosten.md` aggregiert Ralph-Läufe zusammen mit manuellen Sessions.
- **[claude-md-token-sparen](claude-md-token-sparen.md)**: Die Token-Sparregeln im Kostencounter-Block sind eine kompakte Version der vollständigen Token-Sparstrategie. Bei Projekten, die stark auf Token-Effizienz angewiesen sind, die vollständige Seite als Referenz lesen.

---

## Verwandte Seiten

- [sitzungskosten-aus-transkript](../anleitungen/sitzungskosten-aus-transkript.md) — Interaktive Sitzungen aus dem Transkript messen statt schätzen
- [usd-pro-wiki-seite](usd-pro-wiki-seite.md) — US$/WP-Metrik für Wiki-Seiten
- [ralph-schleife](ralph-schleife.md) — Agentische Loop: Kostenstruktur und Budget-Kontrolle
- [claude-md-token-sparen](claude-md-token-sparen.md) — Vollständige Token-Sparstrategie
- [claude-md-design](claude-md-design.md) — 6 Designprinzipien für CLAUDE.md-Dateien

---

[Wiki-Index](../index.md)
