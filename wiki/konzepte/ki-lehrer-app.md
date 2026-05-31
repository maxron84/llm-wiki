---
date: 2026-05-24
type: konzept
tags: [konzept, ux, desktop, ki-lehrer]
status: draft
---

# KI-Lehrer App

**Zusammenfassung**: Eine Tkinter-Desktop-Anwendung, die den KI-Lehrer-Ansatz für technisch unaffine Nutzer zugänglich macht — Kinder, Eltern, alle. GUI als Zugangslösung, nicht als Feature: die Komplexität dahinter (Git, Markdown, Heft-Muster) bleibt vollständig erhalten, wird aber unsichtbar.
**Quellen**: `raw/ki-lehrer_gui-entwurf_sehr-abstrakt.png`, `raw/CLAUDE-MD-Softwareprojekt-Battetested-Rookie.md`
**Zuletzt aktualisiert**: 2026-05-31

---

## Das Problem

Die bestehenden [KI-Lehrer-Vorlagen](../vorlagen/claude-md-lehrer.md) laufen in Claude Code — einem Terminal-Werkzeug. Wer die Vorlage nutzen will, muss:

- Ein Terminal öffnen und navigieren können
- Wissen was eine CLAUDE.md-Datei ist
- Markdown-Dateien bearbeiten
- Git-Commits verstehen (oder zumindest nicht fürchten)

Das ist für Entwickler kein Problem. Für ein Kind im Schulalter oder einen Elternteil ohne technischen Hintergrund ist das eine Wand. Das Konzept dahinter ist stark — der Zugang bisher nicht.

## Die Lösung: DAU-Prinzip als Designgrenze

**DAU-Prinzip** (Dümmster Anzunehmender User): Wenn jemand der gerade so ein Tablet bedienen kann die App starten und nutzen kann, dann kann es jeder. Das ist die Messlatte — keine Ausnahmen.

Konkret bedeutet das:
- Kein Terminal, keine Kommandozeilenbefehle sichtbar
- Keine Datei-Pfade, keine `.md`-Erweiterungen, keine Git-Begriffe
- Geführtes Setup beim ersten Start — eine Frage nach der anderen
- Klare, große Navigationspunkte statt technischer Menüs
- Fehlermeldungen in einfacher Sprache oder gar nicht sichtbar

Die GUI ist kein optionales Komfort-Layer. Sie ist die Bedingung dafür, dass das Konzept eine breitere Zielgruppe erreicht.

## GUI-Entwurf (Wireframe)

Der erste abstrakte Entwurf zeigt eine klassische Zweiteilung:

```
┌─────────────────────────────────────────────────────┐
│  KI Lehrer Programm                        [─][□][×] │
├──────────────┬──────────────────────────────────────┤
│ Navigation   │  Hauptbereich                         │
│              │                                       │
│ Lehrerpult   │  [Tab: Aufgaben] [Tab: Chat] [Tab: …] │
│ Aufgaben-    │                                       │
│  hefte       │  Inhalte je nach aktivem Tab          │
│ Fächer       │                                       │
│ Einstellungen│                                       │
│ …            │                                       │
└──────────────┴──────────────────────────────────────┘
```

**Linke Navigationsleiste**: Lehrerpult, Aufgabenhefte, Fächer, Einstellungen — erweiterbar.

**Hauptbereich mit Tabs**: „Aufgaben bearbeiten", „Mit Lehrer chatten", weitere nach Bedarf. Anzahl und Positionen der Tabs sind konfigurierbar — nicht statisch, modernes Layout.

**Ersteinrichtung**: Einmalig über geführtes Setup (wie in der Vorlage beschrieben), danach jederzeit über Einstellungen anpassbar.

## Was darunter läuft

Die GUI verbirgt Komplexität — sie entfernt sie nicht. Im Hintergrund laufen weiterhin:

- Der **[Obsidian Web Clipper](../werkzeuge/obsidian-web-clipper.md)** — der Lehrer sammelt Unterrichtsmaterial mit einem Klick im Browser, landet als Markdown in `clippings/`, die App liest es als Kontext
- Das Interview-basierte Setup aus den [KI-Lehrer-Vorlagen](../vorlagen/claude-md-lehrer.md) — adaptiert das System an Fach, Alter und Vorkenntnisse
- Das [Heft-Muster](heft-muster.md) — Schülerarbeit in lebenden Markdown-Dateien, Git als implizite Lernhistorie
- Git-Commits nach jeder Session — automatisch im Hintergrund, unsichtbar für den Nutzer
- Die [drei-Ebenen-Architektur](drei-ebenen-architektur.md) — Rohquellen, Wiki, Schema

Der Nutzer sieht: „Aufgabe bearbeiten", „Mit Lehrer chatten". Was tatsächlich passiert: Markdown wird gelesen und geschrieben, git committet, Kontext geladen.

## Zielgruppe

Primär: Kinder im Schulalter, technisch unerfahrene Erwachsene, Eltern die das System für ihre Kinder einrichten.

Die Zielgruppe bestimmt Designentscheidungen konkret:
- Schriftgröße und Kontrast: großzügig
- Sprache in der Oberfläche: einfach, kein Fachjargon
- Fehlerbehandlung: entweder unsichtbar oder in Klartext
- Konfigurierbarkeit: vorhanden, aber nicht im Weg

Technisch affine Nutzer können dieselbe App nutzen — sie verlieren nichts.

## Abgrenzung zu den bestehenden Vorlagen

Die [KI-Lehrer-Vorlagen](../vorlagen/claude-md-lehrer.md) sind CLAUDE.md-Dateien: sie definieren, *wie* Claude sich verhält. Sie setzen einen menschlichen Operator voraus, der Claude Code bedient.

Die KI-Lehrer App ist ein eigenständiges Programm: sie *ruft* die KI auf, verwaltet den Kontext, und zeigt Ergebnisse in einer GUI. Kein Claude Code im Terminal nötig.

| | KI-Lehrer Vorlage | KI-Lehrer App |
|---|---|---|
| Zugang | Claude Code CLI | Tkinter Desktop-App |
| Zielgruppe | Entwickler, Techniker | Alle, inkl. Kinder |
| Setup | CLAUDE.md kopieren | Geführtes Interview |
| Git | Manuell oder Claude | Automatisch im Hintergrund |
| Erweiterbarkeit | CLAUDE.md editieren | Einstellungsmenü |

## Architektur: Cloud oder Lokal?

Die App kommuniziert über eine API — entweder mit Anthropic (Cloud) oder mit einem lokalen Modell über eine OpenAI-kompatible Schnittstelle (Ollama, LM Studio). Die GUI selbst merkt keinen Unterschied — nur die Einstellungen zeigen, was hinten dran hängt.

### Option A: Cloud (Anthropic API)

Der technisch einfachste Weg für die App selbst. Der Nutzer trägt einmalig einen API-Key ein, danach läuft alles.

**Vorteile:**
- Beste Qualität: Erklärungen, Ton, adaptives Lehren — Sonnet/Opus sind hier klar überlegen
- Kein Hardware-Aufwand, läuft auf jedem Rechner
- Kurze Antwortzeiten

**Nachteile:**
- API-Key = Registrierung bei Anthropic, Kreditkarte, laufende Kosten
- Internetverbindung zwingend nötig
- Datenschutz: Gespräche gehen über Anthropic-Server

**Realistisch für DAU-Setup:** Der Elternteil oder Administrator richtet den Key einmalig über das geführte Setup ein. Das Kind sieht danach nichts davon. Machbar, aber nicht kostenfrei.

---

### Option B: Lokal (Ollama / LM Studio)

Kein API-Key, kein Internet, volle Datenkontrolle. Dafür: Hardware-Voraussetzungen und einmalige technische Einrichtung durch jemanden der weiß was er tut.

**Was die App vom Modell verlangt (anders als Wiki-Pflege):**
- Kein Multi-File-Tool-Use — die App übernimmt Dateizugriffe selbst
- Gesprächslänge: überschaubar (eine Lektion, kein 200K-Kontext)
- Dafür: pädagogischer Ton, klares Deutsch, adaptives Reagieren auf Schülerantworten

Das senkt die Hardware-Latte gegenüber der Wiki-Pflege — etwas.

**Mindestvoraussetzungen lokal (realistisch):**

| Stufe | Hardware | Modell | Für wen geeignet |
|---|---|---|---|
| Minimum | 16 GB VRAM (z.B. RTX 5080) | `qwen3:14b@Q8` | Ältere Schüler mit Vorkenntnissen, einfache Fächer |
| Empfohlen | 32 GB VRAM (z.B. R9700) | `qwen3:32b@Q4` | Die meisten Lernszenarien, inkl. jüngere Schüler |
| Komfortabel | 32 GB VRAM + Apple Silicon | `qwen3:32b@Q8` oder 70B | Kinder, Erstlerner, anspruchsvolle Fächer |

**Warum 14B das Limit ist:**
14B-Modelle halten bei einfachen Aufgaben guten Ton und klares Deutsch. Bei jüngeren Kindern, Einsteigern ohne Vorkenntnisse oder Frustrationsmomenten (wo adaptive Reaktion wichtig wird) sind sie grenzwertig. Für Kinder unter 12 oder Fächer mit komplexen Erklärungen ist 32B die ehrliche Mindestempfehlung.

**Preisvergleich gebraucht (eBay.de, Mai 2026):**

| Karte | VRAM | Preis | Backend | Kühler |
|---|---|---|---|---|
| RTX 3090 gebraucht | 24 GB | ~€900–1.100 | CUDA (stabil) | Axial |
| R9700 neu | 32 GB | ~€1.410–1.575 | ROCm (⚠️ reift) | Blower |
| RTX 4090 gebraucht | 24 GB | ~€2.100–2.500 | CUDA (stabil) | Axial |

Die RTX 3090 ist damit der günstigste Einstieg in 24 GB VRAM mit stabilem CUDA-Stack. Die R9700 bietet mehr VRAM für ~€300–500 Aufpreis, hat aber das ROCm-Risiko und den lauteren Blower-Kühler. Die RTX 4090 ist für diesen Anwendungsfall preislich nicht attraktiv. (Quellen: `clippings/Geforce Rtx 4090 online kaufen.md` und `clippings/Nvidia Geforce Rtx 3090 online kaufen.md`, 2026-05-24)

**Vorteile:**
- Keine Kosten pro Gespräch
- Offline-fähig
- Datenschutz: nichts verlässt den Rechner

**Nachteile:**
- Hardware kostet einmalig ~€1.400–1.600 (32 GB, neu)
- Einrichtung (Ollama + Modell-Download) ist nichts für DAU — einmalig durch technische Person nötig
- Antwortgeschwindigkeit langsamer als Cloud, spürbar bei längeren Ausgaben
- R9700: ROCm-Stack für RDNA 4 noch nicht vollständig ausgereift (Stand Mai 2026)

---

### Empfehlung für den Entwurf

Beide Optionen in der App vorsehen, wählbar im Setup. Standard-Pfad ist Cloud — weil er auf jedem Rechner funktioniert und die Qualität für Kinder entscheidend besser ist. Lokale Option für datenschutzbewusste Nutzer oder Offline-Einsatz — mit klarem Hinweis auf Hardware-Voraussetzungen im Setup-Dialog.

| | Cloud | Lokal (Minimum) | Lokal (Empfohlen) |
|---|---|---|---|
| Hardware | Beliebig | RTX 5080 / 16 GB VRAM | R9700 / 32 GB VRAM |
| Preis Hardware | — | ~€1.000 (neu) | ~€1.400–1.600 (neu) |
| Einrichtung | API-Key (einmalig) | Ollama + Modell-Download | Ollama + Modell-Download |
| Kosten laufend | Ja (API) | Nein | Nein |
| Qualität für Kinder | ✅✅ | ⚠️ grenzwertig | ✅ |
| Offline | ❌ | ✅ | ✅ |
| Datenschutz | Anthropic-Server | Lokal | Lokal |
| Backend-Risiko | — | CUDA (stabil) | ROCm/RDNA 4 (⚠️ reift noch) |

---

## Projektstatus

**V1.9 im Feldtest (ab 2026-05-31).** Die App hat Stufen 1 bis 78 durchlaufen — Kaskaden 1 bis 10 abgeschlossen — und ist am 2026-05-31 in den ersten echten Feldtest mit einem Kind gegangen.

Aktuelle Features: Lehrerpult mit Chat, Aufgabenhefte, Fächer, Eltern-Cockpit mit PDF-Bericht, Foto-Review, Lehrplan-Modus, Kosten-Transparenz mit Hybrid-Modell-System.

Während des Feldtests gilt eine [Feldtest-Sperre](feldtest-sperre.md): kein neues Feature, kein Refactoring, kein Ralph-Lauf bis zum expliziten Abschluss des Tests. Die Erkenntnisse fließen als Änderungswunschliste in Kaskade 11+.

Das Projekt läuft als eigenständiges Repo mit eigener [CLAUDE.md](../quellen/claude-md-softwareprojekt-rookie.md) und ralph.sh-Loop.

## Verwandte Seiten

- [obsidian-web-clipper](../werkzeuge/obsidian-web-clipper.md) — Unterrichtsmaterial mit einem Klick in clippings/ sammeln
- [claude-md-lehrer](../vorlagen/claude-md-lehrer.md) — Die inhaltliche Basis: Lehrstruktur, Interview, Phasen
- [claude-md-nachhilfe](../vorlagen/claude-md-nachhilfe.md) — Schwester-Vorlage: aufgabengetrieben, Kind sitzt selbst am Gerät
- [heft-muster](heft-muster.md) — Git-Commits als implizite Lernhistorie — läuft im Hintergrund der App
- [drei-ebenen-architektur](drei-ebenen-architektur.md) — Die Architektur die die App kapselt
- [hardware-vergleich-sonnet-vs-lokal](hardware-vergleich-sonnet-vs-lokal.md) — VRAM-Tiers, Modellempfehlungen, Qualitätsvergleich
- [quantisierung](quantisierung.md) — Q4 vs. Q8: Qualitätsverlust und VRAM-Kalkulation
- [feldtest-sperre](feldtest-sperre.md) — Das Freeze-Muster, das die App gerade schützt
- [claude-md-softwareprojekt-rookie](../quellen/claude-md-softwareprojekt-rookie.md) — Das CLAUDE.md des Projekts: Muster, Regressions-Checks, ralph.sh-Konfiguration
- [multimodale-quellen](multimodale-quellen.md) — Bild als Quelle: dieser Entwurf stammt aus einem PNG

---

[Wiki-Index](../index.md)
