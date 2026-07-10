---
date: 2026-05-24
type: konzept
tags: [konzept, ux, desktop, ki-lehrer, softwareprojekt]
status: active
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

## Tech-Stack

| Schicht | Technologie |
|---|---|
| GUI | Tkinter (Python-Stdlib) |
| KI Cloud | anthropic Python SDK |
| KI Lokal | openai SDK (OpenAI-kompatibel) |
| Git | gitpython (optional — App läuft auch ohne) |
| Datenhaltung | JSON + Markdown (Stdlib) |
| PDF-Export | reportlab ≥ 4.0 |
| Build | pyinstaller ≥ 6.0 (Dev-Tool) |

**Python-Mindestversion**: 3.10. Keine externen UI-Bibliotheken — Tkinter aus der Stdlib hält das Bundle schlank und vermeidet Plattform-Abhängigkeiten.

**Datenverzeichnis**: `~/KI-Lehrer/` — liegt außerhalb des Projekt-Repos. Beim ersten Wizard-Abschluss angelegt, danach erkennt die App `config.json` und überspringt den Wizard.

## Architektur-Regeln

Vier Regeln, die alle Screens und Module durchziehen:

1. **AppState** — zentrales State-Objekt, beim Start instanziiert, an alle Screens übergeben. Keine globalen Variablen.
2. **core/ kennt kein tkinter** — `data.py`, `api.py`, `git_manager.py`, `context.py` importieren kein Tkinter. Logik und Darstellung bleiben getrennt.
3. **Streaming im Thread** — API-Calls laufen in eigenem Thread, GUI-Updates via `root.after()`. So friert das Fenster nie ein.
4. **DAU-Prinzip** — keine Terminal-Begriffe, keine Dateipfade, keine Git-Begriffe in der UI-Sprache.

Über die Spec hinaus gewachsene Konventionen:

- **`AppState.navigate(name)`** — Callback für inter-Screen-Navigation (gesetzt in `app.py`). Lehrerpult nutzt ihn für „Starten" → Aufgabenhefte.
- **`AppState.active_fach` + `goto_chat_on_show`** — vom Lehrerpult beim „Starten"-Klick gesetzt, vom Aufgabenhefte-Screen in `on_show()` ausgewertet.
- **Statischer Eröffnungsgruß** — lokal aus `profil.json` + letztem Sitzungs-Log konstruiert, kein API-Call. Erscheint als KI-Nachricht in der Anzeige, fließt aber nicht in die API-Message-Liste ein — spart Token.
- **JSON-Schema-Versionierung** — `{"version": 1, ...}` in `config.json` und `profil.json` als Vorbereitung für Migrationen.

## Umgebung & Betrieb

**Tkinter im Flatpak-VSCodium nicht verfügbar** — `import tkinter` schlägt in der Sandbox fehl. Die App nur im nativen Terminal testen (GNOME Terminal, Konsole…). Auf Linux muss `python3-tk` (Debian/Ubuntu) bzw. `tk` (Arch) systemweit installiert sein.

**Logs**: Die App schreibt strukturierte Logs nach `~/KI-Lehrer/_log/app.jsonl` (JSON-Lines, eine Zeile = ein Eintrag). Rotation bei 1 MB, 5 Backups. Wichtige Events:

| event | Bedeutung |
|---|---|
| `stream_start` | KI-Antwort startet (Felder: `modus`, `modell`) |
| `stream_done` | KI-Antwort abgeschlossen (`chunks`, `dauer_ms`) |
| `sync_tick` | Material-Sync-Durchlauf (`neue_dateien`, `fehler`) |
| `git_commit` | Git-Commit erstellt (`hash`, `dateien`) |

Detailliertes Debug-Logging: `KI_LEHRER_DEBUG=1 python3 main.py`

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

## Kaskaden-Chronik

Die App wächst in Kaskaden — jede bringt eine abgeschlossene Erweiterung. Der Build selbst läuft in Stufen (1–78 und weiter), jede eigenständig lauffähig.

| Kaskade | Version | Inhalt |
|---|---|---|
| 1–4 | V1.0–V1.3 | Grundgerüst, Setup-Wizard, Datenmodell, Chat-Kern |
| 5 | V1.4 | Chat ins Lehrerpult gezogen, Aufgabenhefte → „Mappe", dreistufiges Foto-Tag-System |
| 6 | V1.5 | Multi-Profil in Nav, Würde-konforme Schüler-Anmerkung im Heft, fachübergreifende Sitzungs-Suche |
| 7 | V1.6 | Installierbar: PyInstaller-Bundles für Linux/Windows/macOS, CI-Pipeline, Crash-Dialog, Update-Check |
| 8 | V1.7 | Lehrplan-Modus: `lehrplan.json` pro Fach, Fortschritts-Heuristik, KI-vorgeschlagener Wochenplan, „Heute lernen"-Karte |
| 9 | V1.8 | Eltern-/Lehrer-Cockpit: Wochenbericht, Heft-Diff, Foto-Übersicht, PDF-Export (reportlab) |
| 10 | V1.9 | Kosten-Transparenz: Token-Erfassung, Preis-Tabelle, Hybrid-Modell-System, Budget-Hinweis im Cockpit |

**Nächste geplante Themen (nach Feldtest):** Lokal-Token-Tracking, Auto-Switching zwischen Modellen, Klassenarbeit-Modus. Kaskaden 11–13 stehen bereit, sind aber eingefroren.

Den exakten Stand zeigt `git log --grep="feat(stufe"` im Projekt-Repo.

## Projektstatus

**V1.9 im Feldtest (ab 2026-05-31).** Die App hat Stufen 1 bis 78 durchlaufen — Kaskaden 1 bis 10 abgeschlossen — und ist am 2026-05-31 in den ersten echten Feldtest mit einem Kind gegangen.

Aktuelle Features: Lehrerpult mit Chat, Aufgabenhefte, Fächer, Eltern-Cockpit mit PDF-Bericht, Foto-Review, Lehrplan-Modus, Kosten-Transparenz mit Hybrid-Modell-System.

Während des Feldtests gilt eine [Feldtest-Sperre](feldtest-sperre.md): kein neues Feature, kein Refactoring, kein Ralph-Lauf bis zum expliziten Abschluss des Tests. Die Erkenntnisse fließen als Änderungswunschliste in Kaskade 11+.

Das Projekt läuft als eigenständiges Repo mit eigener [CLAUDE.md](../quellen/claude-md-softwareprojekt-rookie.md) und ralph.sh-Loop.

## Bekannte Regressions-Checks

Beim Test der App nach größeren Änderungen sind diese Szenarien kritisch:

- **Fach-Fokus**: Im Mathe-Chat eine Geschichtsfrage stellen → KI soll freundlich zum Geschichtsheft verweisen, nicht antworten.
- **Foto-Tag-Override**: Status manuell auf „verstanden" setzen → KI darf beim nächsten Session-Ende nicht überschreiben (`darf_setzen` prüft `status_quelle == "mensch"`).
- **Stream-Persistenz**: Stream starten, Bildschirm wechseln und zurück → Antwort vollständig, kein Crash. `chat_view.on_hide()` bricht keinen laufenden Stream ab.
- **Multi-Profil**: Profil wechseln → eigener Ordner, altes Profil bleibt erhalten.
- **Heft-Anmerkung**: Anmerkung anlegen → Blockquote sichtbar. Rechtsklick → Entfernen. KI-Blöcke niemals editierbar.
- **Cockpit-PIN**: Ohne PIN kein Cockpit-Eintrag in der Nav. Mit PIN → sofort navigierbar.
- **PDF-Export**: Cockpit → „Bericht als PDF" → Dateidialog → PDF enthält korrekte Wochendaten.
- **Verbrauch-Erfassung**: Lerngespräch führen → `_verbrauch.jsonl` erhält neue Zeile mit Modell, Pfad, Token-Zahlen.
- **Budget-Hinweis**: Budget auf 0,01 EUR setzen → nach erstem Cloud-Aufruf dezenter Banner. Erscheint nicht ohne PIN-Entsperrung.

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
- [claude-md-ki-team](../vorlagen/claude-md-ki-team.md) — Verallgemeinerte T.E.A.M.-Vorlage (Ralph/Architekt/Frank/Harry/Marv/Axel), abgeleitet aus diesem Projekt

---

[Wiki-Index](../index.md)
