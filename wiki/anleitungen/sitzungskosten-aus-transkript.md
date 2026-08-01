---
date: 2026-08-01
type: anleitung
tags: [anleitung, kosten, metrik, automatisierung]
status: active
---

# Sitzungskosten aus dem Transkript messen

**Zusammenfassung**: Interaktive Abo-Sitzungen liefern keinen Konsolenwert — ihre Kosten wurden bisher über einen Zeilen-Proxy geschätzt. Das Sitzungstranskript von Claude Code enthält aber die echten Token-Zahlen. Diese Anleitung zeigt, wie man sie ausliest, die zwei Fallen umgeht und das Preismodell gegen einen bekannten Konsolenwert eicht.
**Quellen**: Eigene Messung der Sitzung vom 2026-08-01, Transkript `f634d61f-0a70-4904-8af7-7deb5fe4815d.jsonl` unter `~/.claude/projects/-home-max-Source-llm-wiki/`; Gegenprobe gegen zwei headless-Läufe mit bekanntem `total_cost_usd`; Ledger `website-maxron-de/.budget-ledger`
**Zuletzt aktualisiert**: 2026-08-01

---

## Das Problem

Headless-Läufe (Ralph, Harry, Marv, Frank, Axel) geben am Ende ein JSON mit
`total_cost_usd` aus — der [kostencounter](../konzepte/kostencounter.md) summiert
das einfach auf. **Der Architekt läuft interaktiv** und gibt nichts dergleichen
aus. Im Abo gibt es überhaupt keinen Konsolenwert.

Bisheriger Behelf im Feldprojekt: der **A2-Churn-Proxy** — geänderte Zeilen mal
einem Eichfaktor von `16/1045` USD je Zeile. Das ist eine Schätzung über ein
Artefakt, nicht über den tatsächlichen Verbrauch. Sie wurde jedes Mal mit
„Abo-Gegenwert, kein Konsolenwert" markiert.

Es geht genauer.

## Wo das Transkript liegt

```bash
ls ~/.claude/projects/-<pfad-mit-bindestrichen>/*.jsonl
```

Der Ordnername ist der Projektpfad mit `/` → `-`. Jede Zeile ist ein
JSON-Objekt; die Zeilen mit `"type":"assistant"` tragen unter
`message.usage` die Token-Zahlen.

> **Zeitzone**: Die `timestamp`-Felder sind **UTC** (`…Z`), die Datei-Zeiten
> lokal. Bei CEST sieht ein frisch geschriebenes Transkript deshalb zwei Stunden
> „alt" aus. Das ist kein Datenverlust.

## Falle 1 — dieselbe Usage steht mehrfach da

Eine Modellantwort mit Denkblock, Text und drei Werkzeugaufrufen erzeugt
**mehrere** `assistant`-Zeilen. Sie teilen sich eine `message.id` und tragen
alle **dieselbe** `usage`. Wer stumpf über alle Zeilen summiert, zählt doppelt
bis vierfach.

In der gemessenen Sitzung: 518 `assistant`-Zeilen, aber nur **280 eigenständige
Antworten**. Naives Summieren ergab in der Gegenprobe das **1,58- bis
1,72-fache** des echten Werts — und zwar uneinheitlich, also nicht mal durch
einen Korrekturfaktor zu retten.

**Lösung**: nach `message.id` deduplizieren.

## Falle 2 — der Cache-Write-Preis hängt an der TTL

Nach der Deduplizierung lag die Rechnung mit Listenpreisen konstant bei etwa
**0,74** des echten Werts — konstant, also ein Preisproblem, kein Zählproblem.

Ursache: Claude Code fährt hier eine **einstündige** Prompt-Cache-TTL. Der
Cache-Write kostet dann das Doppelte des Basis-Inputs, nicht das 1,25-fache.

## Das Preismodell eichen

Man muss nicht raten. Ein headless-Lauf liefert `total_cost_usd`; sein
Transkript liefert die Token. Zwei solche Läufe ergeben ein lösbares
Gleichungssystem für die beiden Cache-Preise:

```
Lauf A (Ralph):  in 3.567  out   983  cw 28.943  cr 241.704  →  0,2728 USD
Lauf B (Harry):  in 3.571  out 2.144  cw 58.347  cr 268.583  →  0,4751 USD

gelöst:  cache-write = 6,010 USD/Mio     cache-read = 0,304 USD/Mio
Liste:   cache-write = 6,00  (1 h TTL)   cache-read = 0,30
```

Restfehler 0,2 %. Damit ist das Modell **belegt**, nicht angenommen:

| Posten | Sonnet-Klasse | Opus-Klasse |
|---|---|---|
| Input | 3,00 | 15,00 |
| Output | 15,00 | 75,00 |
| Cache-Write (1 h) | 6,00 | 30,00 |
| Cache-Read | 0,30 | 1,50 |

*(USD je Mio. Token. Die Opus-Spalte ist das übliche 5:1-Verhältnis — bei den
Sonnet-Werten gegen echte Konsolenwerte geeicht, bei den Opus-Werten
hergeleitet.)*

## Das Skript

```python
import json
P = {'input_tokens': 15.0, 'output_tokens': 75.0,
     'cache_creation_input_tokens': 30.0, 'cache_read_input_tokens': 1.50}

gesehen, summe = set(), {}
for zeile in open(TRANSKRIPT):
    d = json.loads(zeile)
    if d.get('type') != 'assistant':
        continue
    m = d.get('message', {})
    if m.get('id') in gesehen:          # Falle 1
        continue
    gesehen.add(m.get('id'))
    for k, v in (m.get('usage') or {}).items():
        if isinstance(v, int):
            summe[k] = summe.get(k, 0) + v

print(sum(summe.get(k, 0) * p / 1e6 for k, p in P.items()))
```

Mit einem Zeitfenster auf `d['timestamp']` lässt sich die Sitzung in Phasen
schneiden — praktisch, wenn eine Sitzung mehrere Domänen berührt und der Ledger
sie getrennt führen soll.

## Was die Messung zeigte

Sitzung vom 2026-08-01, Opus 5, interaktiv, rund 3¼ Stunden:

| Phase | Output | Cache-Read | USD |
|---|---:|---:|---:|
| Wiki-Audit und Umsetzung | 75.800 | 11,7 Mio | 29,57 |
| Feldinspektion (read-only) | 21.368 | 6,2 Mio | 12,36 |
| Starterkit-Bau 1.0.0 → 2.2.0 | 184.016 | 64,5 Mio | 119,09 |
| **Summe** | **301.542** | **83,3 Mio** | **165,58** |

Die eigentliche Erkenntnis steht in der Aufteilung:

| Posten | Anteil |
|---|---:|
| Cache-Read | **75,5 %** |
| Output | 13,7 % |
| Cache-Write | 10,9 % |
| Input | 0,0 % |

**Das Verhältnis Cache-Read zu Output ist 276 : 1.** Bezahlt wird nicht, was das
Modell schreibt, sondern dass es bei jedem Werkzeugaufruf den gewachsenen
Kontext erneut vorgelegt bekommt. Die Kosten einer langen Sitzung skalieren
darum eher mit der **Zahl der Schritte mal der Kontextgröße** als mit dem
erzeugten Text — was die Regel der [T.E.A.M.-Vorlage](../vorlagen/claude-md-ki-team.md),
Prosa-Arbeit aus dem Loop herauszuhalten, von der anderen Seite bestätigt.

## Gegenprobe gegen den alten Proxy

| Methode | Wert |
|---|---:|
| A2-Churn: 10.979 Zeilen × 16/1045 | 168,09 USD |
| Token-Messung | 165,58 USD |

**1,5 % Abweichung.** Der im Feld erratene Eichfaktor trifft erstaunlich gut.
Das ist allerdings `n = 1`, und in dieser Sitzung heben sich zwei Fehler
womöglich gegenseitig auf: der Churn ist durch **kopierte** Dateien aufgebläht
(die kosten keine Token), während der Proxy den dominierenden Cache-Read gar
nicht sieht. Als Notbehelf bleibt der Proxy brauchbar — wo ein Transkript
vorliegt, ist die Messung vorzuziehen.

## Verwandte Seiten

- [kostencounter](../konzepte/kostencounter.md)
- [usd-pro-wiki-seite](../konzepte/usd-pro-wiki-seite.md)
- [team-starter-kit](../werkzeuge/team-starter-kit.md)
- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md)
- [token-sparen](token-sparen.md)

---

[Wiki-Index](../index.md)
