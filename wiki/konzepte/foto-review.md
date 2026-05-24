---
date: 2026-05-24
type: konzept
tags: [konzept, multimodale-quellen, ki-lehrer, fernschule]
status: active
---

# Foto-Review

**Zusammenfassung**: Nicht jede Schülerarbeit lässt sich digital bearbeiten. Für Aufgaben die zwingend auf Papier entstehen — Geometrie-Konstruktionen, beschriftete Diagramme, Zeichnungen — liest die KI ein Foto der Schülerarbeit und gibt Feedback. Optional, flexibel, Cloud-only.
**Quellen**: Entwickelt aus der Praxis der [KI-Lehrer-Vorlagen](../vorlagen/claude-md-nachhilfe.md); kein einzelnes Quelldokument.
**Zuletzt aktualisiert**: 2026-05-24

---

## Das Problem

Die [Nachhilfe-Vorlage](../vorlagen/claude-md-nachhilfe.md) geht davon aus, dass der Schüler seine Lösungen digital in `heft/` schreibt und dann auf Papier abschreibt. Das funktioniert für Textaufgaben, Rechenaufgaben, Aufsätze.

Für bestimmte Aufgabentypen ist das umgekehrt oder schlicht unmöglich:

- **Geometrie-Konstruktionen**: Zirkel, Lineal, Geodreieck — das Werkzeug ist analog, die Zeichnung ist das Ergebnis
- **Beschriftete Diagramme**: Pflanzenzelle, Kreislauf, Landkarte — die räumliche Struktur entsteht auf Papier
- **Freihandzeichnungen**: Kunst, Sachkunde, Technik
- **Schreibschrift-Übungen**: Form und Duktus sind nur auf Papier beurteilbar

Besonders für **Fernschüler** ist das relevant: die Einsendungsaufgabe muss auf Papier eingereicht werden. Das Kind muss also auf Papier arbeiten — und sollte die Möglichkeit haben, die Lösung vor dem Einschicken reviewen zu lassen.

---

## Der Workflow

```
Aufgabe liegt vor
      ↓
Kind arbeitet auf Papier (Zirkel, Lineal, Stift)
      ↓
Foto → raw/foto_YYYY-MM-DD_thema.jpg
      ↓
KI liest das Foto
      ↓
KI erkennt Aufgabentyp und passt Review an
      ↓
Feedback: was stimmt, was fehlt, was ist falsch
      ↓
(ggf. korrigieren → neues Foto → nochmal)
      ↓
„Das kannst du so einschicken" — oder offene Punkte benennen
```

---

## Flexibilität: die KI passt sich an

Es gibt kein festes Review-Schema. Was die KI prüft hängt davon ab, was auf dem Foto ist. Die KI erkennt den Aufgabentyp selbst und reagiert entsprechend:

| Aufgabentyp | Was die KI prüft |
|---|---|
| Geometrie-Konstruktion | Schritte korrekt? Winkel stimmen? Abstände plausibel? Beschriftung vorhanden? |
| Rechenweg handschriftlich | Rechenfehler? Zwischenschritte korrekt? Endergebnis richtig? |
| Beschriftetes Diagramm | Alle Pflichtteile vorhanden? Beschriftungen korrekt? Pfeile/Linien logisch? |
| Aufsatz / Fließtext | Inhaltlich vollständig? Struktur erkennbar? Grobe Fehler? |
| Freihandzeichnung | Aufgabenstellung erfüllt? Was fehlt oder stimmt nicht? |

Die Rückmeldung ist immer konkret: nicht „da ist ein Fehler", sondern „der Winkel an Punkt B sollte 60° sein, nicht 90°."

---

## Optionalität

Der Foto-Review-Workflow ist **nicht** Standard in jeder Session. Er wird nur aktiviert wenn:

- Die Aufgabe auf Papier bearbeitet wurde (weil sie nicht digital geht)
- Das Kind ein Foto einwirft und um Feedback bittet
- Oder der Aufgabentyp es nahelegt (Geometrie, Diagramme)

Für reine Textaufgaben, Rechenaufgaben ohne Konstruktion, Deutschaufsätze am Computer — bleibt der normale digitale Workflow.

---

## Grenzen

- **Cloud-only**: Nur Vision-fähige Modelle lesen Fotos zuverlässig (Claude Sonnet/Opus). Kein lokales Modell macht das sinnvoll.
- **Fotoqualität entscheidend**: Unlesbares, unscharfes oder schlecht beleuchtetes Foto → KI bittet sofort um ein neues. Nie raten.
- **Geometrie-Präzision begrenzt**: Die KI kann grobe Fehler erkennen (falscher Winkel, fehlende Linie), aber keine Millimeter-genaue Messung. Der pädagogische Nutzen — war das Prinzip verstanden? — ist trotzdem hoch.
- **Kein Ersatz für einen Fachlehrer**: Wenn eine Konstruktion komplex und die Beurteilung grenzwertig ist, sagt die KI das klar.

---

## Verbindung zur Fernschule

Für **Fernschüler** ist der Foto-Review besonders wertvoll: die Einsendungsaufgabe wird benotet, es gibt keine Rückfragemöglichkeit beim Lehrer vor dem Einschicken. Das Kind kann die Lösung vor dem Abschicken von der KI prüfen lassen — und Fehler korrigieren, bevor die Note feststeht.

Das ist keine Prüfungstäuschung, sondern das Äquivalent davon, dass ein Mitschüler oder Elternteil nochmal drüberschaut.

---

## Verwandte Seiten

- [claude-md-nachhilfe](../vorlagen/claude-md-nachhilfe.md) — Vorlage die diesen Workflow als optionalen Block enthält
- [multimodale-quellen](multimodale-quellen.md) — Fotos und Bilder als Quellen: allgemeine Überlegungen
- [ki-lehrer-app](ki-lehrer-app.md) — In der App: eigener Upload-Button für Schülerfotos
- [ki-lehrer-datenmodell](ki-lehrer-datenmodell.md) — Ablageort: `raw/foto_YYYY-MM-DD_thema.jpg`

---

[Wiki-Index](../index.md)
