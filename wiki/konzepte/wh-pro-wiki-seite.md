---
date: 2026-08-11
type: konzept
tags: [konzept, metrik, kosten, lokale-modelle]
status: active
---

# Wh/WP — Wattstunden pro Wiki-Seite

**Zusammenfassung**: Die Stückkostenmetrik des LLM-Wikis rechnet in Dollar und wird damit für lokalen Betrieb sinnlos, weil dort keine Token-Rechnung anfällt. Vorgeschlagener Ersatz: Wattstunden und Minuten pro Wiki-Seite. Größenordnung ~0,006 € Strom pro Seite — zwei Größenordnungen unter Sonnet, aber noch ungemessen.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Warum US$/WP lokal nicht funktioniert

Die Metrik [US$/WP](usd-pro-wiki-seite.md) misst (Input- + Output-Token-Kosten) geteilt durch substanziell gepflegte Wiki-Seiten. Bei lokalem Betrieb ist der Zähler null — es gibt keine Token-Rechnung. Die Metrik meldet damit „kostenlos", was falsch ist: Strom, Zeit und Hardware-Abschreibung fallen weiterhin an, und die Zeit ist bei lokalen Modellen die knappste Größe.

> „Die Metrik muss für lokale Modelle neu gefasst werden: Wattstunden pro Wiki-Seite und Minuten pro Wiki-Seite statt Dollar pro Wiki-Seite." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Die Rechnung

| Größe | Wert |
|---|---|
| GPU-Last | 300 W |
| Zeit pro Seite | 4 Minuten |
| Energie pro Seite | **20 Wh** |
| Strompreis | 0,30 €/kWh |
| **Kosten pro Seite** | **~0,006 €** |

Zum Vergleich: ~0,42 US$ pro Seite bei Sonnet 4.6 in einer [Ralph-Schleife](ralph-schleife.md) — zwei Größenordnungen mehr. (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Die Quelle relativiert das im selben Atemzug:

> „Der Vergleich ist unfair, solange die Qualität nicht gleichzieht, aber er benennt, worum man eigentlich spielt."

Das ist die entscheidende Einschränkung. Ein 14B-Modell schreibt schlechtere Zusammenfassungen als Sonnet, extrahiert mehr Triviales und sieht weniger Verbindungen. → [hardware-vergleich-sonnet-vs-lokal](hardware-vergleich-sonnet-vs-lokal.md), [ingest-fliessband](ingest-fliessband.md)

## Warum Minuten die wichtigere Hälfte sind

Von den beiden vorgeschlagenen Einheiten ist **Minuten/WP** praktisch die relevantere. 0,006 € gegen 0,42 US$ ist ein Unterschied, der bei jedem realistischen Wiki-Umfang unter der Wahrnehmungsschwelle bleibt — bei 200 Seiten sind das 1,20 € gegen 84 US$, und beides ist für ein persönliches Wiki ohne Belang.

Die Zeit ist es nicht. Vier Minuten pro Seite bedeuten bei einem Ingest über 12 betroffene Seiten knapp eine Stunde, in der die Karte belegt ist. Das ist die Größe, die über die Bedienbarkeit entscheidet — und die Größe, die der [SQL-Umbau](sql-wiki-architektur.md) tatsächlich angreift, indem er die Latenz von „mit dem Gesprächsverlauf wachsend" auf „konstant" umstellt. → [kv-cache-rechnung](kv-cache-rechnung.md)

## Messbar durch die Log-Tabelle

Im [Wiki-Datenbankschema](wiki-datenbankschema.md) hat die `log`-Tabelle die Spalten `seconds`, `wh` und `cost_usd` nebeneinander. Damit wird die Metrik von einer Schätzung zu einer Abfrage — und zwar für beide Betriebsarten gleichzeitig:

```sql
SELECT SUM(cost_usd) / (SELECT COUNT(*) FROM pages) AS usd_pro_seite FROM log;
SELECT SUM(wh)       / (SELECT COUNT(*) FROM pages) AS wh_pro_seite  FROM log;
SELECT SUM(seconds)  / 60.0 / (SELECT COUNT(*) FROM pages) AS min_pro_seite FROM log;
```

Der praktische Nutzen liegt im Nebeneinander: Wenn `ingest` mit einem starken Modell läuft und `query`/`update` lokal, steht in einer Tabelle, was welcher Weg gekostet hat. Die Modellwahl wird damit zu einer Entscheidung pro Operation. → [kostencounter](kostencounter.md)

## Der offene Punkt

Die Quelle führt die Metrik selbst unter „Was das Muster nicht löst" auf:

> „Wattstunden und Minuten pro Wiki-Seite sind vorgeschlagen, aber nicht gemessen. Ohne Messung bleibt der Vergleich lokal-gegen-Cloud eine Behauptung." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Die 300 W und 4 Minuten sind Annahmen, keine Messwerte (überprüfungsbedürftig). Eine belastbare Zahl bräuchte `nvidia-smi`-Sampling über einen echten Ingest-Lauf und eine Zählung der tatsächlich geänderten Seiten — dieselbe Disziplin, die die [Sitzungskostenmessung](../anleitungen/sitzungskosten-aus-transkript.md) für Abo-Sitzungen etabliert hat.

## Verwandte Seiten

- [usd-pro-wiki-seite](usd-pro-wiki-seite.md) — Die Metrik, die hier neu gefasst wird
- [wiki-datenbankschema](wiki-datenbankschema.md) — Die Log-Spalten, die sie messbar machen
- [kv-cache-rechnung](kv-cache-rechnung.md) — Woher die Latenz kommt
- [sql-wiki-architektur](sql-wiki-architektur.md) — Was die Minuten pro Seite senkt
- [hardware-vergleich-sonnet-vs-lokal](hardware-vergleich-sonnet-vs-lokal.md) — Die Qualitätsseite des Vergleichs
- [ingest-fliessband](ingest-fliessband.md) — Modellwahl als Kostenentscheidung pro Operation
- [kostencounter](kostencounter.md) — Kosten-Tracking-Standard
- [ralph-schleife](ralph-schleife.md) — Der Sonnet-Benchmark
- [sitzungskosten-aus-transkript](../anleitungen/sitzungskosten-aus-transkript.md) — Messen statt schätzen
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
