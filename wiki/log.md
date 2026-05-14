---
date: 2026-04-23
type: konzept
tags: [konzept]
status: active
---

# Änderungsprotokoll

**Zusammenfassung**: Chronologisches Protokoll aller Vorgänge im Wiki.
**Zuletzt aktualisiert**: 2026-05-14

---

## [2026-05-14 00:03] query | Designprinzip 8 (Git-Setup) dokumentiert + alle Vorlagen aktualisiert

- `wiki/konzepte/claude-md-design.md` — Designprinzip 8 hinzugefügt: Git-Repo beim ersten Start einrichten
  - Muster: git init + .gitignore + Erst-Commit wenn verfügbar; freundlicher Hinweis mit j/n-Wahl wenn nicht installiert
  - Commit-Rhythmus: am Ende jeder Sitzung `git add -A && git commit -m "..."`
- Zusammenfassung auf 8 Items erweitert
- Git-Setup als Schritt 2 in alle 9 Vorlagen eingebaut (direkt nach Platzhalter-Interview)
- Nachfolgende Schritte in allen Vorlagen um 1 hochgezählt
- Betroffene Vorlagen: laienlehrer, legacy-forensik, lehrer, nachhilfe, rezepte-ernaehrung, software-begleiter, software, team, youtube-verlauf

## [2026-05-14 00:01] query | KI-Lehrer-Vorlage: Zeitbudget und Zielgruppen-Sprache korrigiert

- „kindgerechte Erklärung" → „Tiefe und Sprache angepasst an Vorkenntnisse und Alter"
- Zeitbudget-Frage präzisiert: „typischerweise pro Sitzung"
- Phase 2: neuer Schritt 1 „Zeitcheck" — Claude fragt zu Sitzungsbeginn nach verfügbarer Zeit, weist auf Timer hin
- Ehrlicher Hinweis: Claude hat keine Uhr, kann Zeit nicht selbst messen
- Phase-2-Schritte umnummeriert (8 statt 7)
- Phase 1: „Alter" aus Lehrplan-Generierung entfernt (nicht relevant für Erwachsene)

## [2026-05-14 00:00] query | Platzhalter-Interview in alle 8 Vorlagen eingebaut

- claude-md-software.md: `## Initialisierung` eingefügt (PROJEKTNAME, Beschreibung, Sprache, Framework, Paketmanager)
- claude-md-software-begleiter.md: `## Initialisierung` eingefügt (PROJEKTNAME, Beschreibung, Sprache, Framework)
- claude-md-nachhilfe.md: `## Initialisierung` eingefügt (Fach, Klasse, Schulform, Name, Bundesland)
- claude-md-laienlehrer.md: `## Initialisierung` eingefügt (Fach, Klasse, Schulform, Kindname, Unterrichtender, Fachkenntnisse, Bundesland)
- claude-md-team.md: `## Initialisierung` eingefügt (Projektname, Beschreibung, Teammitglieder)
- claude-md-legacy-forensik.md: `### Initialisierung` eingefügt (Systemname, Organisation, Alter, Sprachen, Frameworks, Ziel)
- claude-md-youtube-verlauf.md: `## Initialisierung` eingefügt (Thematischer Fokus)
- claude-md-rezepte-ernaehrung.md: `## Initialisierung` eingefügt (Thematischer Fokus)
- Muster: claude-md-lehrer.md war bereits fertig und diente als Referenz
- Jeder Initialisierungsschritt: freundliche Einzelfragen pro Platzhalter, keine Listen

## [2026-05-13 00:14] query | KI-Lehrer-Vorlage: Platzhalter-Interview in Phase 1 explizit gemacht

- Phase 1 Schritt 1: explizite {{PLATZHALTER}}-Prüfung mit konkreten Fragen pro Feld
- Reihenfolge: erst Interview, dann Begrüßung (war vorher umgekehrt)
- Abdeckung vollständig: Name, Alter, Projekttitel, Vorkenntnisse, Zeitbudget

## [2026-05-13 00:13] query | KI-Lehrer-Vorlage: 14B-Tier und Designhinweise aktualisiert

- Modell-Kompatibilitätstabelle: 14B-Spalte ergänzt (qwen3:14b-40k, RTX 5080)
- 30B-Beschreibung präzisiert: benötigt 24+ GB VRAM
- Designhinweis 6: Code-Digest erst ab ~2000 Zeilen nötig, nicht bei 40K-Kontext-Modellen

## [2026-05-13 00:12] query | qwen3:14b-40k Praxismessung — 16 GB, 100% GPU, 40K Kontext

- Vergleichstabelle aktualisiert: qwen3:14b-40k als finale Spalte ergänzt
- Messdaten: 16 GB VRAM (9,3 GB Gewichte + 6,7 GB KV-Cache), 100% GPU, 40960 Kontext
- Erklärung: optimale VRAM-Ausnutzung auf RTX 5080

## [2026-05-13 00:11] query | Modelfile-Workflow für qwen3:14b-40k dokumentiert

- 40K-Kontext-Setup via Modelfile: Befehle für Docker und Direkt-Installation
- Continue-Config auf qwen3:14b-40k aktualisiert
- Hinweis: Doppelpunkt statt Bindestrich in ollama run

## [2026-05-13 00:10] query | Roocode als agentische Alternative zu Continue dokumentiert

- Neuer Abschnitt „Alternative zu Continue: Roocode" in lokale-modelle-fortgeschritten.md
- Vergleichstabelle Continue vs. Roocode: Dateischreiben, Wiki-Automatisierung
- Setup: Installation, Ollama-Verbindung, CLAUDE.md als Systemprompt
- Vorbehalt: Tool-Use-Stabilität bei 14B-Modellen — Praxistest empfohlen

## [2026-05-13 00:09] query | qwen3:14b Inferenzgeschwindigkeit + Kontext-Korrektur

- Praxismessung: 87,56 Token/s eval rate auf RTX 5080 — sehr schnell für 14B
- Kontexthinweis präzisiert: contextLength in Continue-Config reicht, kein manueller REPL-Befehl nötig

## [2026-05-13 00:08] query | qwen3:14b-Test: 100% GPU, Kontext-Default 4096 — Konfiguration nötig

- qwen3:14b Messdaten: 10 GB, 100% GPU ✅, Kontext default 4096
- Vergleichstabelle: Kontext-Spalte aufgeteilt in Default vs. konfiguriert
- Hinweis ergänzt: contextLength in Continue-Config übergibt num_ctx an Ollama → 40K aktivieren
- Erklärung: Modelle >10 GB haben keinen VRAM-Puffer mehr für größeren KV-Cache → bleiben bei 4K

## [2026-05-13 00:07] query | nemotron3:33b-Test + finale Stack-Empfehlung RTX 5080

- nemotron3:33b Messdaten ergänzt: 30 GB, 50%/50% CPU/GPU, Kontext 4096 — für KI-Lehrer ungeeignet
- Vergleichstabelle auf drei Modelle erweitert (27B Distillat / nemotron3:33b / qwen3:14b)
- Erklärender Hinweis: alle Modelle >10 GB auf RTX 5080 → CPU-Offload → Kontext auf 4096 gedeckelt
- Empfohlener Stack dokumentiert: qwen3:14b lokal + Claude Sonnet als Backup

## [2026-05-13 00:06] query | ollama-ps-Messdaten: 27B-Distillat auf RTX 5080 — Kontext auf 4096 gedeckelt

- Praxismessung: 18 GB Modellgröße, 78% GPU / 22% CPU, Kontext automatisch auf 4096 begrenzt
- Hardware-Sektion: Vergleichstabelle 27B-Distillat vs. qwen3:14b mit gemessenen Werten
- Fazit dokumentiert: 4K Kontext für KI-Lehrer unzureichend → qwen3:14b bleibt Empfehlung für RTX 5080
- 27B-Distillat erst ab 32 GB VRAM (RTX 5090) sinnvoll

## [2026-05-13 00:05] query | Korrektur: 27B-Distillat funktioniert auf RTX 5080 — Docker-GPU-Setup war das Problem

- Community-Modell-Hinweis korrigiert: Modell ist geeignet, wenn Docker mit --gpus=all gestartet wird
- Prominenter Docker-GPU-Warnhinweis nach Schritt 1 ergänzt (--gpus=all, --restart=always, nvidia-smi-Check)
- Vorheriger Eintrag war falsch: Ursache war fehlendes GPU-Passthrough, nicht VRAM-Limit

## [2026-05-13 00:04] query | RTX-5080-Praxistest: 27B-Distillat auf 16 GB VRAM zu langsam

- Community-Modell-Hinweis in Hardware-Sektion präzisiert: Qwen3.5-27B Q3_K_M übersteigt 16 GB VRAM (Weights + KV-Cache), CPU-Offload macht es unbrauchbar
- Praxistest: RTX 5080 / Ryzen 7 9800X3D / 64 GB RAM — bestätigt nicht empfohlen für 16 GB VRAM

## [2026-05-13 00:03] query | lokale-modelle-fortgeschritten auf offizielle Ollama-Modelle aktualisiert

- Hardware-Tabelle: ersetzt durch tatsächliche Ollama-Modelle mit Größe, Kontext und VRAM
- RTX 5080 (16 GB): `qwen3:14b` als Empfehlung dokumentiert, `qwen3:30b` als CPU-Offload-Fall
- Hinweis auf Community-Reasoning-Distillate (Qwen3.5-27B) ergänzt
- Pull-Befehle: `qwen3:14b` / `qwen3:32b` / `qwen3:30b` nach Hardware-Tier
- Continue-Config, Temperatur-Config, Test-Befehl: alle auf `qwen3:14b` aktualisiert
- Thinking Mode: „14B und größer" statt „27B und größer"
- Kontextfenster-Tipp: 40K statt 32K, Code-Digest erst ab 2000+ Zeilen nötig
- Häufige Probleme: qwen3:30b-a3b und MLX-Referenzen entfernt, RTX 5080-Hinweis ergänzt

## [2026-05-13 00:02] lint | Wiki-Gesundheitsprüfung

- 0 Dead Links, 0 Orphans, 0 fehlende Index-Einträge, 0 Format-Fehler
- 78 Seiten — alles sauber

## [2026-05-13 00:01] query | lokale-modelle-fortgeschritten.md auf Code-Digest aktualisiert

- Projektstruktur: `wiki/code-stand.md` ergänzt
- Schritt 4: Vorlage-Auswahl auf KI-Lehrer als primäre Option umgeschrieben, Platzhalter-Beispiel angepasst
- Schritt 7: Neuer Abschnitt „Code-Digest" mit Prompt-Beispiel und Folgesitzungs-Syntax
- Kontextfenster-Tipp: Verweis auf Code-Digest statt manueller Dateiauswahl

## [2026-05-13 00:00] query | Code-Digest-Option in KI-Lehrer-Vorlage ergänzt

- `wiki/vorlagen/claude-md-lehrer.md`: Code-Digest für lokale 32k-Modelle eingebaut
- Neuer Abschnitt `## Code-Digest` im Vorlagen-Fence: Aktivierung, Ladestrategie, Regel
- Ordnerstruktur: `wiki/code-stand.md` ergänzt
- Phase 2 Schritt 7: code-stand.md-Update bei aktivem Digest
- Neues Seitenformat `code-stand.md` mit Modul-Tabelle und Offene-Punkte-Sektion
- Designhinweis 6: Code-Digest nur bei ≤ 32k, nicht bei Cloud-Modellen

## [2026-05-11 02:00] query | Kostenhinweis in erste-schritte.md ergänzt

- Neuer Abschnitt `## Was kostet das?` in `wiki/anleitungen/erste-schritte.md`
- $51-Richtwert für Karpathys Use Case (100 Seiten, 3 Monate, Sonnet 4.6)
- Kosten-Tabelle: Haiku/Sonnet/Team-Szenarien
- Verweis auf `usd-pro-wiki-seite.md` und `token-sparen.md`
- Verwandte Seiten um token-sparen und usd-pro-wiki-seite erweitert

## [2026-05-04 14:00] query | Vorlage claude-md-laienlehrer erstellt

- Neue Seite `vorlagen/claude-md-laienlehrer.md`
- Zielgruppe: Eltern und Laien als Unterrichtende (Fernunterricht, Heimunterricht)
- Kernprinzip: zweistufige Ausgabe — „Für dich" (vollständig) + „Formulierung fürs Kind" (altersgerecht)
- 3 Workflows: Stundenvorbereitung, Hausaufgabenkorrektur, Schnellerklärung
- 4 Seitenformate: Lektions-Seite, Korrektur-Seite, Kind-Profil, Fortschritt
- Explizite Warnpflicht: KI sagt klar, wenn ein Thema einen echten Fachlehrer braucht
- Modell-Kompatibilitätstabelle analog zur Nachhilfe-Vorlage

## [2026-05-04 13:00] query | Einrichtungsanleitung Beratungs-CRM erstellt

- Neue Seite `anleitungen/beratungs-crm-einrichtung.md`
- Vollständige Einrichtung auf Linux: Systemabhängigkeiten, Ollama + Qwen3, VS Codium + Continue, Python-venv
- 3 vollständige Python-Skripte: berechne-profil.py (Numerologie + Astrologie via kerykeion), woche.py (Wochenvorschau mit offenen Punkten), offene-rechnungen.py
- CLAUDE.md-Template für das Projekt, Knowledge-Base-Aufbau
- Obsidian-Einrichtung (Gesamtprojektordner als Vault, Sync deaktivieren)
- Typischer Tagesablauf: Vorbereitung, Nachbereitung, monatliche Rechnungen
- Git-Backup-Hinweis: kein GitHub/GitLab, nur eigene Infrastruktur

## [2026-05-04 12:00] query | Konzeptpapier Beratungs-CRM erstellt

- Neue Seite `projekte/beratungs-crm.md` angelegt (neuer Bereich `projekte/`)
- Vollständiges Konzept für lokales CRM: psychologische Beraterin + Numerologie + Astrologie
- Datenmodell: Klientenprofil, Sitzungsnotiz, Rechnung (YAML-Frontmatter-Beispiele)
- 5 Python-Skripte beschrieben: berechne-profil.py (kerykeion), woche.py, rechnung-pdf.py, monatsbericht.py, loesche-klient.py
- KI-Integration: CLAUDE.md-Kern, 3 typische Arbeitsabläufe
- 3 Ausbaustufen: Kern (V1), Erweiterung (V2), Optional (V3)
- DSGVO-Hinweise: Art. 9, Aufbewahrungspflichten, Backup-Empfehlungen
- Offene Entscheidungen vor Projektstart tabellarisch festgehalten

## [2026-05-03 15:00] query | Anleitung „Lokale Modelle Fortgeschritten" erstellt

- Neue Seite `anleitungen/lokale-modelle-fortgeschritten.md` angelegt
- Zielgruppe: erfahrene Programmierer, Pygame-Beispielprojekt
- Stack: Ollama + Qwen3 27B/35B (inkl. MoE-Variante) + Continue in VS Codium
- Hardware-Tabelle mit RAM/VRAM-Anforderungen für 27B, 30B MoE, 32B
- Continue-Konfiguration: config.json, @-Referenzen für Dateizugriff, System-Prompt-Strategien
- Qwen3-Tipps: Thinking Mode, Kontextfenstermanagement, Temperatur für Code
- Halbautomatischer Wiki-Betrieb: Sitzungsnotizen manuell anlegen, fortschritt.md als Sitzungsanker
- Obsidian-Integration für Vault-Betrieb
- Häufige Probleme: langsame CPU-Inference, MLX-Empfehlung, Kontextverlust bei langen Sitzungen

## [2026-05-02 00:02] lint | werkzeuge/claude-code.md aktualisiert

- [[wiki-links]] → Standard-Markdown-Links
- Inhalt auf aktuellen Stand gebracht: Desktop App, Web App, VS Code/JetBrains Extension, CLI
- Neuer Abschnitt „Geplante Agenten (Routinen)": Funktionsweise, Einrichtung, Voraussetzungen
- Hinweis auf laufende Routine `lokale-modelle-aktualisierung`
- Verwandte Seiten erweitert (erste-schritte, lokale-modelle)

## [2026-05-02 00:01] query | Anleitung „Lokale Modelle" erstellt

- Neue Seite `anleitungen/lokale-modelle.md` angelegt
- Recherche via WebSearch: aktueller Stand lokaler LLM-Tools 2025/2026
- **Anfänger-Pfad**: LM Studio (5/5 DAU) — Installation, Modell-Download, System-Prompt-Setup, erste Sitzung; Jan.ai als Alternative
- **Fortgeschrittener Pfad**: Ollama, Open WebUI, Continue in VS Code (einzige Option mit Dateizugriff)
- Hardware-Tabelle: RAM-Anforderungen nach Modellgröße; Apple MLX-Hinweis für M-Chips
- Modell-Empfehlungen 2026: Qwen3 (14b/27b), Llama 3.3, Mistral Small 3, Phi-4-mini
- Ehrliche Einschränkungs-Tabelle vorangestellt: kein automatisches Wiki-Wachstum ohne Dateizugriff
- `index.md` aktualisiert

## [2026-05-02 00:00] query | Anleitung „Erste Schritte" erstellt

- Neue Seite `anleitungen/erste-schritte.md` angelegt (neuer Ordner `wiki/anleitungen/`)
- Zielgruppe: DAU — keine Vorkenntnisse vorausgesetzt
- **Hauptpfad** (Windows/macOS): Claude Code Desktop App + Claude.ai-Abo, 6 Schritte von Installation bis erster Sitzung
- **Fortgeschrittener Pfad**: VS Code / VS Codium (inkl. Linux-Hinweis), API-Key-Einrichtung, Git-Backup
- Vorlage-Auswahl-Tabelle mit allen 7 Vorlagen
- `index.md` aktualisiert: neuer Abschnitt `## Anleitungen`

## [2026-05-01 00:00] lint | [[wiki-links]] → Standard-Markdown in index.md

- `wiki/index.md`: Alle 54 Einträge von `[[wiki-link]]` auf `[name](kategorie/seitenname.md)` umgestellt
- Datum `Zuletzt aktualisiert` auf 2026-05-01 gesetzt

## [2026-04-29 00:01] query | Vorlage KI-Lehrer erstellt

- Neue Vorlage `vorlagen/claude-md-lehrer.md` angelegt
- Variabel einsetzbar: CLAUDE.md definiert Lehrmethode (fest), Lehrplan wird beim ersten Start generiert (dynamisch)
- Enthält: Aufnahme-Workflow (Phase 1, einmalig), Sitzungsstruktur (Phase 2, wiederholt),
  Frustrations-Protokoll, Code-Regeln (Konzept-Beispiele erlaubt, Projektcode verboten),
  Lehrplan-/Fortschritts-/Sitzungsformat, Modell-Kompatibilitäts-Tabelle
- Zielgruppe: primär Kinder/Einsteiger, generell für jedes Lernprojekt einsetzbar
- `index.md` aktualisiert

## [2026-04-29 00:00] query | Vorlage Software-Begleiter erstellt

- Neue Vorlage `vorlagen/claude-md-software-begleiter.md` angelegt
- Dritte eigenständige Software-Vorlage neben Lean/Dokumentiert
- Kernkonzept: Mensch schreibt und besitzt Code vollständig — KI liest, dokumentiert, reviewt, tauscht Ideen aus
- Enthält: Dokumentations-Workflow, Review-Workflow, Ideen-Workflow, ADR-Format,
  Modul-Seitenformat, Ideen-Seitenformat, Modell-Kompatibilitäts-Tabelle
- Zentrale unveränderliche Regel prominent platziert: „Du schreibst keinen Code"
- `index.md` aktualisiert

## [2026-04-27 00:02] lint | Wiki-Gesundheitsprüfung + Vorlagen-Korrekturen

Befunde:
1. **Echter toter Link**: `vorlagen/claude-md-software.md` Footer `wiki/index.md` → korrigiert zu `../index.md`
2. **Falsche Modell-Beispiele**: 30B-Tier verwendete 70B/72B-Modelle (Llama 3.3 70B, Qwen 2.5 72B) — in allen 4 Vorlagen ersetzt durch Qwen3.6:27b / Qwen3.6:35b (tatsächlich ~27–35B, 256K Kontext)
3. **Kontextbudget-Tabelle** (Software-Vorlage): 128K → bis 256K (Qwen3.6) aktualisiert; Empfehlungstabelle angepasst

Fehlalarme (keine Aktion nötig):
- Alle Waisen/Fehlende-Index/Format-Warnungen: Lint-Skript erkennt `[[wiki-links]]` nicht als Links
- `pfad.md`-Warnungen: Inline-Code-Beispiele in Backticks, keine echten Links

## [2026-04-27 00:01] query | Modell-Tier-Sektion in alle Vorlagen eingebaut

- Alle 4 Vorlagen um einheitliche `## Modell-Kompatibilität`-Sektion erweitert
  - Tabelle: 7B / 30B / Cloud — welche Features funktionieren, welche nicht
  - Klare Empfehlung je Tier mit Beispielmodellen
- `vorlagen/claude-md-youtube-verlauf.md`: Workflow-Schritte 10–11 mit `*(Cloud)*` markiert
- `vorlagen/claude-md-legacy-forensik.md`: 7B explizit als nicht empfohlen markiert
- `vorlagen/claude-md-rezepte-ernaehrung.md`: 7B für Rezept-Extraktion als Sonderfall geeignet
- `vorlagen/claude-md-software.md`: Neue Sektion vor bestehender Kontextbudget-Tabelle ergänzt

## [2026-04-27 00:00] query | Konzeptskizze multimodale Quellen erstellt

- Neue Konzeptseite `konzepte/multimodale-quellen.md` angelegt (status: draft)
- Beschreibt theoretische Erweiterung des LLM-Wiki-Musters auf Audio, Bilder und Video
- Enthält: Zwei-Stufen-Pipeline, drei Eingabeklassen, Speicherproblem-Analyse,
  Verlusttoleranz-Einschätzung, Implikationen für künftige Vorlagen, offene Fragen
- Explizit als Konzeptskizze markiert — noch nicht in der Praxis erprobt
- `index.md` aktualisiert

## [2026-04-25 10:30] ingest | YouTube-Verlauf-CLAUDE.md aufgenommen

Quelle: `raw/CLAUDE-2026-04-25_youtube-video-verlauf.md` — gelebte, weiterentwickelte CLAUDE.md eines realen YouTube-Wiki-Projekts.
Tiefe: **Vollständig**

**Neue Konzeptseite**:
- `konzepte/seitenklassifikation.md` — Zeitlos/Gemischt/Zeitgenössisch: Pflichtfeld zur Bewertung der zeitlichen Relevanz von Wiki-Seiten

**YouTube-Vorlage grundlegend überarbeitet** (`vorlagen/claude-md-youtube-verlauf.md`):
- **Klassifikations-System**: Pflichtfeld auf allen Inhaltsseiten (außer Output-Dokumente)
- **Output-Dokumente**: `wiki/output/betrachter.md`, `zitate.md`, `offene-fragen.md` als neue Ordnerstruktur
- **Aufnahme-Workflow**: Schritte 10–11 ergänzt — `betrachter.md`-Pflege verpflichtend nach jeder Aufnahme
- **Vernetzung & Konsistenzpflege**: Neue Sektion — bidirektionale Links als Standard, regelmäßige Audits
- **Designhinweis 6**: `betrachter.md` ist Sonderfall — kein Klassifikationsfeld

**Weitere Vorlagen mit Klassifikations-Hinweis versehen** (optional):
- `vorlagen/claude-md-legacy-forensik.md` — Designhinweis 6: Klassifikation als zweite Dimension neben Konfidenzmodell
- `vorlagen/claude-md-software.md` — Designhinweis 6: Klassifikation optional für ADRs/Modulseiten im Dokumentiert-Modus

`index.md` aktualisiert.

## [2026-04-25 09:50] query | Software-Vorlage mit Zwei-Modi-System überarbeitet

- `vorlagen/claude-md-software.md` grundlegend überarbeitet:
  - **Modus "Lean"** (Standard): Reines Coding-Tool, kein Wiki. Funktioniert mit jedem Modell.
  - **Modus "Dokumentiert"**: Coding + fortlaufendes Projekt-Wiki (Architektur, Module, ADRs, Schnittstellen)
  - **Kontextbudget-Tabelle**: Ehrliche Token-Rechnung für Cloud-APIs vs. lokale 30B-Modelle (8K–128K Kontext)
  - **Empfehlungen nach Modellgröße**: Wann Lean, wann Dokumentiert, wann qmd/jdocmunch nötig
  - Vorlage selbst enthält optionalen "Projekt-Wiki"-Abschnitt mit Ordnerstruktur, Seitenformat und Prüfregeln
  - Verwandte Seiten um `skalierungsgrenzen`, `kompilierungs-metapher`, `qmd`, `jdocmunch`, `claude-md-rezepte-ernaehrung` erweitert
- `index.md` aktualisiert: Beschreibung der Software-Vorlage angepasst

## [2026-04-24 00:01] query | Vorlage Rezepte- und Ernährungs-Wiki erstellt

- Neue Vorlage `vorlagen/claude-md-rezepte-ernaehrung.md` angelegt
- Spezifisch für Rezepte, Kochbücher, Food-Science und eigene Küchenexperimente als Quellmaterial
- 5 Entitätstypen: Rezept, Zutat, Technik, Küche, Konzept (Food Science/Ernährung)
- Enthält: Extraktionsstrategien nach Quelltyp (Rezeptseite, Kochbuch, Food-Science, Eigenexperiment, Ernährungs-Artikel),
  vollständige Seitenformate (Rezept mit Küche/Diät/Saison-Frontmatter, Technik, Zutat mit Substitutions-Tabelle),
  geschlossene Diät-Tag-Liste, alle 6 Abfragetypen übersetzt für Kochkontext,
  Kontaminierungsrisiko-Warnung spezifisch für Ernährungsbehauptungen, Skalierungshinweise (qmd/jdocmunch)
- `index.md` aktualisiert

## [2026-04-24 00:00] lint | claude-md-design in bestehende Seiten verlinkt

4 Seiten mit [claude-md-design](konzepte/claude-md-design.md) verknüpft:
- `konzepte/drei-ebenen-architektur.md` — Inline-Hinweis in Ebene-3-Beschreibung + Verwandte Seiten
- `konzepte/yaml-frontmatter.md` — Verwandte Seiten (Designprinzip 4: Frontmatter-Konsistenz)
- `konzepte/lint-pruefung.md` — Verwandte Seiten (Designprinzip 3: keine toten Links)
- `werkzeuge/claude-code.md` — Verwandte Seiten (CLAUDE.md steuert Claude Code)

## [2026-04-23 00:02] query | Vorlage YouTube-Verlauf-Wiki erstellt

- Neue Vorlage `vorlagen/claude-md-youtube-verlauf.md` angelegt
- Spezifisch für YouTube-Verlauf als Quellmaterial: Transkripte, Beschreibungen, Notizen
- Enthält: Extraktionsstrategien nach Video-Typ, spezifisches Seitenformat für Videos/Kanäle/Serien,
  Transkript-Beschaffung (yt-dlp, youtube-transcript-api), alle 6 Abfragetypen,
  Kontaminierungsrisiko-Warnung, Skalierungshinweise (qmd/jdocmunch)
- index.md aktualisiert

## [2026-04-23 22:09] lint | Wiki-Gesundheitsprüfung

3 Befunde behoben:
1. **Toter Link**: `[[index-workflow]]` in `konzepte/skalierungsgrenzen.md:87` → korrigiert zu `[ingest-workflow](konzepte/ingest-workflow.md)`
2. **Veraltetes Datum**: `index.md` und `log.md` Frontmatter und Zuletzt-aktualisiert von 2026-04-22 → 2026-04-23
3. **Kein Befund** bei: `[[bitter-lesson]]` (steht in Inline-Code als Beispiel), Backslash in Pipe-Links (korrektes Tabellen-Escape)

Geprüft und in Ordnung:
- ✅ 46 Wiki-Seiten, alle mit YAML-Frontmatter
- ✅ 48 Index-Einträge = 46 Seiten + Index + Log
- ✅ Alle Seiten mit Fußzeile zum Index
- ✅ Keine echten verwaisten Links (Template-Platzhalter in 4-Backtick-Fences sind korrekt)

## [2026-04-23 21:45] ingest | CLAUDE.md-Designprinzipien aus YouTube-Verlauf-Projekt

- Neue Konzeptseite `konzepte/claude-md-design.md` erstellt: 6 Designprinzipien für effektive CLAUDE.md-Anweisungsdateien
  - Direkte Anweisungen statt Meta-Vorlagen
  - Link-Konsistenz zwischen Instruktion und Output
  - Keine Referenzen auf nicht-existierende Seiten
  - Frontmatter-Konsistenz
  - Wertvolle Ergänzungen als Top-Level-Sektionen
  - Regelmäßiges Review
- Alle 3 Vorlagen überarbeitet:
  - `vorlagen/claude-md-software.md` — Designhinweise-Abschnitt ergänzt, Verwandte Seiten erweitert
  - `vorlagen/claude-md-legacy-forensik.md` — Designhinweise-Abschnitt ergänzt, Verwandte Seiten erweitert
  - `vorlagen/claude-md-youtube-verlauf.md` — Designhinweise-Abschnitt ergänzt, Verwandte Seiten erweitert
- `index.md` aktualisiert: `claude-md-design` + `claude-md-youtube-verlauf` ergänzt

## [2026-04-23 00:01] query | Vorlagen auf neueste Datenlage aktualisiert

- `vorlagen/claude-md-legacy-forensik.md` aktualisiert:
  - **Benannte Abfragetypen** (Spezialberichte): 6 Query-Template-Typen als Forensik-Analysetypen eingebaut
  - **Kontaminierungsrisiko** (Regeln): Explizite Warnung + Pflicht zum Spot-Checking vor Entscheidungen
  - **Skalierung** (Regeln): qmd und jdocmunch als Skalierungstools für große Analyse-Wikis
  - Verwandte Seiten um kontaminierungsrisiko, query-templates, skalierungsgrenzen, qmd, jdocmunch erweitert
- `vorlagen/claude-md-software.md`: Keine Änderungen nötig — neue Konzepte betreffen das Wiki-Muster selbst, nicht allgemeine Softwareentwicklung

## [2026-04-23 00:00] ingest | 8 neue Clippings (Community-Reaktionen auf Karpathy)

Tiefe: **Mittel** — Quelltextzusammenfassungen + Konzeptseiten für wichtigste neue Ideen.

**8 neue Quelltextzusammenfassungen** in `wiki/quellen/`:
- `llm-wiki-mehul-gupta.md` — Mehul Gupta (Medium): Küchenmetapher, Rollenverschiebung
- `llm-wiki-urvil-joshi.md` — Urvil Joshi (Medium): Kompilierungs-Metapher, Tutorial-Demo
- `llm-wiki-plaban-nayak.md` — Plaban Nayak (Level Up Coding): 5-Schritt-Pipeline, RAG über Wiki
- `llm-wiki-jgravelle.md` — J. Gravelle (DEV.to): Skalierungsproblem + jDocMunch-Lösung
- `llm-wiki-venturebeat.md` — Carl Franzen (VentureBeat): Community-Reaktionen, Enterprise
- `llm-wiki-antigravity.md` — Antigravity.codes: Anwendungsfälle, Kompilierungsprofile
- `llm-wiki-analytics-vidhya.md` — Riya Bansal (Analytics Vidhya): Tool-Stack, Herausforderungen
- `llm-wiki-mindstudio.md` — MindStudio Team: Best Practices, Inbox-Muster

**6 neue Konzeptseiten** in `wiki/konzepte/`:
- `kompilierungs-metapher.md` — Rohquellen = Quellcode, Wiki = kompiliertes Binary
- `skalierungsgrenzen.md` — index.md-Bottleneck, Schwellenwerte, Lösungsansätze
- `vault-trennung.md` — Steph Angos Empfehlung: sauberes + Agenten-Vault trennen
- `kontaminierungsrisiko.md` — Halluzinationen als persistente Fakten; Minderungsstrategien
- `query-templates.md` — 6 Abfragetypen: Synthese, Lücken, Debatte, Output, Health, Anwendung
- `fortgeschrittene-architektur.md` — RAG über Wiki-Seiten, Routing, Prompt Caching, Feintuning

**2 neue Werkzeugseiten** in `wiki/werkzeuge/`:
- `jdocmunch.md` — Sektionsbasierte Abfrage, ~95% Token-Reduktion, MCP-Server
- `qmd.md` — Lokale semantische Suche (BM25/Vektor/Hybrid), Tobi Lütke

**2 neue Personenseiten** in `wiki/personen/`:
- `steph-ango.md` — Obsidian-CEO, Vault-Trennungs-Empfehlung
- `tobi-luetke.md` — Shopify-CEO, qmd-Entwickler

**3 bestehende Seiten aktualisiert**:
- `konzepte/rag-vs-wiki.md` — Kompilierungs-Metapher + Kontaminierungsrisiko ergänzt
- `konzepte/ingest-workflow.md` — Routing-Schritt + Skalierungsgrenzen ergänzt
- `werkzeuge/obsidian.md` — Vault-Trennung + Steph Ango ergänzt

`index.md` aktualisiert mit allen 18 neuen Seiten.

## [2026-04-22 22:45] ingest | CLAUDE.md-Vorlagen ins Wiki integriert

- `templates/`-Ordner aufgelöst — Vorlagen gehören als Wiki-Seiten ins Wiki (Karpathy-Prinzip: alles Wertvolle fließt zurück)
- 2 neue Wiki-Seiten unter `wiki/vorlagen/` erstellt:
  - `vorlagen/claude-md-software.md` — CLAUDE.md-Vorlage für Software-Projekte
  - `vorlagen/claude-md-legacy-forensik.md` — CLAUDE.md-Vorlage für Legacy-Forensik
- Beide Seiten mit Frontmatter, Konzeptbeschreibung und eingebetteter Vorlage zum Kopieren
- Mit Konzeptseiten verlinkt (`drei-ebenen-architektur`, `llm-wiki-muster`, `claude-code` etc.)
- `CLAUDE.md` korrigiert: `templates/` → `wiki/vorlagen/`
- `index.md` aktualisiert: neuer Abschnitt „Vorlagen"

## [2026-04-22 20:15] lint | Wiki-Gesundheitsprüfung

7 Befunde behoben:
1. **YAML-Frontmatter** auf allen 22 Wiki-Seiten ergänzt (`date`, `type`, `tags`, `status`)
2. **Personenseiten** `personen/mario.md` und `personen/pasquale-pillitteri.md` angelegt; in Quellseiten und Index verlinkt
3. **Link ergänzt**: `[andrej-karpathy](personen/andrej-karpathy.md)` in `quellen/llm-wiki-karpathy.md` (Fließtext + Verwandte Seiten)
4. **Link ergänzt**: `[obsidian-web-clipper](werkzeuge/obsidian-web-clipper.md)` in `konzepte/ingest-workflow.md` (Verwandte Seiten)
5. **Werkzeugseiten** `werkzeuge/dataview.md` und `werkzeuge/marp.md` angelegt; in `obsidian.md`, `yaml-frontmatter.md` und Index verlinkt
6. **Zitationslücke** in `konzepte/rag-vs-wiki.md`: Behauptung über NotebookLM/ChatGPT als `(überprüfungsbedürftig)` markiert
7. **Selbstreferenz** in `index.md`-Fußzeile entfernt

## [2026-04-22 19:36] Quelle aufgenommen: Obsidian + Claude Code (Pillitteri)

- Quelle gelesen und Erkenntnisse besprochen
- 6 neue Wiki-Seiten erstellt:
  - `quellen/obsidian-claude-code-pillitteri.md` — Zusammenfassung des Artikels
  - `konzepte/mece-prinzip.md` — MECE-Ordnerstruktur für Vaults
  - `konzepte/taeglicher-workflow.md` — Sitzungseröffnung und -abschluss
  - `konzepte/statelessness.md` — Das Zustandslosigkeits-Problem
  - `konzepte/modellunabhaengigkeit.md` — Markdown = Anbieterunabhängigkeit
  - `konzepte/yaml-frontmatter.md` — Standardisierte Metadaten
- 4 bestehende Seiten aktualisiert:
  - `werkzeuge/obsidian.md` — MECE, Graph-Test, Quelle 3
  - `werkzeuge/claude-code.md` — Statelessness, Quelle 3
  - `konzepte/drei-ebenen-architektur.md` — CLAUDE.md als Orchestrator
  - `konzepte/llm-wiki-muster.md` — Modellunabhängigkeit, Quelle 3
- `index.md` aktualisiert mit allen neuen Seiten

## [2026-04-22 19:31] Quelle aufgenommen: LLM Wiki Tutorial (Mit Mario)

- Quelle gelesen und Erkenntnisse besprochen
- 5 neue Wiki-Seiten erstellt:
  - `quellen/llm-wiki-tutorial-mit-mario.md` — Zusammenfassung des Videos
  - `konzepte/chat-problem.md` — Das fundamentale Problem: Wissen verschwindet in Chats
  - `personen/andrej-karpathy.md` — Urheber des LLM-Wiki-Konzepts
  - `werkzeuge/claude-code.md` — Claude Code/Cowork als Agent
  - `werkzeuge/obsidian-web-clipper.md` — Browser-Erweiterung zum Quellen-Sammeln
- 3 bestehende Seiten aktualisiert:
  - `werkzeuge/obsidian.md` — Quelle 2 als Referenz, neue verwandte Seiten
  - `konzepte/ingest-workflow.md` — Tiefe-Auswahl ergänzt
  - `konzepte/llm-wiki-muster.md` — Quelle 2 als Referenz, neue verwandte Seiten
- `index.md` aktualisiert mit allen neuen Seiten

## [2026-04-22 19:26] Quelle aufgenommen: llm-wiki.md (Karpathy Gist)

- Quelle gelesen und Erkenntnisse besprochen
- 9 Wiki-Seiten erstellt:
  - `quellen/llm-wiki-karpathy.md` — Zusammenfassung der Quelle
  - `konzepte/llm-wiki-muster.md` — Das Kernkonzept
  - `konzepte/drei-ebenen-architektur.md` — Rohquellen → Wiki → Schema
  - `konzepte/ingest-workflow.md` — Aufnahme-Prozess
  - `konzepte/query-workflow.md` — Fragen ans Wiki
  - `konzepte/lint-pruefung.md` — Wiki-Gesundheitsprüfung
  - `konzepte/rag-vs-wiki.md` — Vergleich RAG vs. Wiki
  - `konzepte/memex.md` — Vannevar Bushs Memex (1945)
  - `werkzeuge/obsidian.md` — Obsidian als IDE
- `index.md` aktualisiert mit allen neuen Seiten
- Alle Seiten untereinander verlinkt

## [2026-04-22 19:21] Initialisierung

- Wiki-Grundgerüst angelegt
- `index.md` erstellt (Inhaltsverzeichnis)
- `log.md` erstellt (dieses Protokoll)
- Drei Quellen in `clippings/` identifiziert, Aufnahme steht bevor

## [2026-04-30] query | Nachhilfe-Vorlage — projektübergreifendes Schülerprofil

- Neuer Designhinweis 7: Ein Profil pro Kind, Elternordner-Struktur
- Neuer Abschnitt außerhalb Fence: Elternordner-Setup mit Ordnerstruktur, Eltern-CLAUDE.md und profil.md-Format
- Inside Fence: profil.md zu Sessionbeginn lesen, nach Session aktualisieren (nur bei echten Neuigkeiten)
- Workflow Schritt 10 neu: Profil-Update nach Session
- Regel ergänzt: Profil nur bei echten Beobachtungen aktualisieren

## [2026-04-30] query | Nachhilfe-Vorlage — Foto-Workflow und Transkription

- Aufgaben-Workflow erweitert: Bild aus raw/ lesen → Aufgabentext transkribieren → Wiki-Seite autark
- Schritt 2 neu: explizite Transkriptions-Anweisung mit Autark-Prinzip
- Schritt 1: bei Unlesbarkeit neues Foto anfordern, nie raten; bei mehreren Aufgaben im Bild nachfragen
- Aufgaben-Seite: `## Aufgabe` Platzhalter erklärt das Autark-Prinzip
- Regel aktualisiert: Bildmaterial-Verhalten präzisiert

## [2026-04-30] query | Nachhilfe-Vorlage: jahrgangübergreifende Struktur

- Ein Projekt pro Fach, von Startklasse bis Abschluss (nicht nach Klassen aufteilen)
- Ordnerstruktur: `wiki/aufgaben/klasse-N/` für Aufgaben, `wiki/konzepte/` jahrgangübergreifend
- Fortschritt nach Klassen gegliedert: neuer Abschnitt pro Schuljahr
- CLAUDE.md: `**Aktuelle Klasse**` als aktualisierbares Feld (statt fixer Klasse)
- Konzept-Workflow: explizite Regel — eine Seite pro Thema, wird mit jeder Klasse tiefer
- Regeln: Jahresbeginn-Checkliste (Klasse aktualisieren, neuer Fortschritts-Abschnitt)

## [2026-04-30] lint | Vorlagen: [[wiki-links]] → Standard-Markdown

- Alle 7 Vorlagen-Dateien: `[[wiki-links]]` durch Standard-Markdown-Links ersetzt
- Außerhalb des Fences: Quellen, Verwandte Seiten, Designhinweise, Body-Referenzen
- Innerhalb des Fences: Beispiel-Seitenformate, Workflow-Anweisungen, Regeln
- Designhinweis 2 in allen Vorlagen: von "anpassen auf Markdown" zu "Standard ist Markdown"
- Explizite Markdown-Link-Regel in alle CLAUDE.md-Templates eingebaut
- Ziel: Vorlagen sind jetzt direkt im Browser/GitHub lesbar ohne Obsidian

## [2026-05-03] query | Anleitung „Vorlage einrichten"

- `wiki/anleitungen/vorlage-einrichten.md` — neu: DAU-Schritt-für-Schritt-Anleitung
  - Vorlagenauswahl-Tabelle (welche Vorlage für welchen Zweck)
  - Genaue Kopier-Anleitung (was zu kopieren ist, was nicht — Fence-Problem)
  - Platzhalter-Ausfüllen mit konkretem Pygame-Beispiel
  - Erste Sitzung und Folgesitzungen erklärt
  - Häufige Fehler mit direkten Lösungen

## [2026-05-03] lint | Wiki-Prüfung + Vorlagen-Update

- **Lint-Skript verbessert**: Anker-Links (`#heading`) nicht mehr als Dead Links gemeldet; Backtick-Code-Spans ausgenommen; `wiki/vorlagen/` übersprungen (Platzhalter-Inhalt); `type: anleitung` vom Quellen-Pflicht-Check befreit
- **3 fehlende Seiten für Fulkerson-Quelle nachgeliefert**: `exokortex.md`, `lernschleifen.md`, `personen/aaron-fulkerson.md`
- **7 Vorlagen aktualisiert** — Qualitätssicherung aus Community-Konsens (Constrained Pipelines) eingebaut:
  - `claude-md-software.md`: `last_verified`-Feld, Git-Hook-Hinweis, `(überprüfungsbedürftig)`-Praxis in Wiki-Prüfung
  - `claude-md-software-begleiter.md`: Staleness-Risiko-Meldung, `(überprüfungsbedürftig)` in Lint-Sektion
  - `claude-md-rezepte-ernaehrung.md`, `claude-md-youtube-verlauf.md`, `claude-md-legacy-forensik.md`, `claude-md-lehrer.md`, `claude-md-nachhilfe.md`: Jeweils Prüfung oder Regeln um Confidence-Marking und Human-in-the-Loop erweitert
- **Neue Vorlage**: `wiki/vorlagen/claude-md-team.md` — Team-Wiki für 2–8 Personen mit Ownership-Tabelle, Provenienz-Tags und `entwuerfe/`-Pufferzone
- **Lint-Ergebnis**: 0 Dead Links, 0 Orphans, 0 Format-Fehler (67 Seiten)

## [2026-05-02] ingest | TecAdRise, LLM Wiki v2 (Tamiltech), Enterprise-Wiki (Falconer)

- `wiki/quellen/llm-wiki-tecadrise.md` — TecAdRise.ai: Karpathys Idee als Bewegung, 3 Kritikpunkte (Halluzination, Konsistenz, Architektur), Constrained Pipelines, 3 Community-Projekte
- `wiki/quellen/llm-wiki-v2-tamiltech.md` — Tamiltech/LLM Wiki v2: Produktionserfahrung, typisierte Beziehungen (relationships.json), Git-Hooks, Trust Score
- `wiki/quellen/llm-wiki-enterprise-falconer.md` — Falconer: Enterprise-Skalierung, warum `raw/`-Ordner-Modell nicht skaliert, cross-tool Entity Resolution, automatische Drift-Erkennung
- `wiki/konzepte/community-projekte.md` — neu: Swarm Vault, BrainDB, Menmo Vault
- `wiki/konzepte/llm-wiki-v2.md` — neu: 4 Produktionserweiterungen, relationships.json, Lifecycle-Management
- `wiki/konzepte/enterprise-skalierung.md` — neu: 4 Eigenschaften (Capture/Link/Compound/Stay Current) auf Enterprise-Niveau
- `wiki/konzepte/kontaminierungsrisiko.md` — Constrained Pipelines (Community-Konsens) und Trust Score (v2) ergänzt
- `wiki/konzepte/rag-vs-wiki.md` — v2-Argument (RAG als 2023-Workaround) und Enterprise-Retrieval-Kritik ergänzt

## [2026-05-11 01:00] ingest | Die fehlende Metrik — Was kostet eine Seite im LLM Wiki?

**Neue Seiten (5):**
- `wiki/quellen/ralph-claude-code-llm-wiki-metrik.md` — Quellartikel: Ralph + Karpathy, US$/WP, drei Kostenszenarien
- `wiki/konzepte/ralph-schleife.md` — Deterministisches Agentenmuster, $10,42/h Sonnet-Benchmark, fehlende Ingest-Automatisierung
- `wiki/konzepte/usd-pro-wiki-seite.md` — Stückkostenmetrik: $0,043/WP roh, $0,42/WP Lifetime (Sonnet 4.6)
- `wiki/personen/geoffrey-huntley.md` — Erfinder der Ralph-Schleife
- `wiki/werkzeuge/wiki-ralph-sh.md` — Bash-Skript für autonomen Inbox-Ingest incl. Lint-Pass

**Aktualisierte Seiten (5):**
- `wiki/konzepte/fortgeschrittene-architektur.md` — Ralph als Ingest-Backbone, $0,32/Iteration Sonnet 4.6
- `wiki/konzepte/skalierungsgrenzen.md` — US$/WP-Tabelle, 200K-Wort-Klippe als harte Grenze
- `wiki/konzepte/enterprise-skalierung.md` — Szenario C: $19.870/Jahr, $6,60/WP bei Opus 4.7
- `wiki/konzepte/kontaminierungsrisiko.md` — Halluzinations-Kostenfaktor in der Wirtschaftlichkeitsrechnung
- `wiki/anleitungen/token-sparen.md` — US$/WP-Referenzwerte, Ralph-Schleife als Stufe 3.3

## [2026-05-11 00:00] query | Token sparen mit Claude Code

- `wiki/anleitungen/token-sparen.md` — neu: 4-stufiger Leitfaden zur Token-Reduktion beim LLM-Wiki-Betrieb
  - Stufe 1: Sofortmaßnahmen (Tiefenwahl, gezielte Fragen, schlanke CLAUDE.md)
  - Stufe 2: jDocMunch (95% Einsparung) + qmd (Semantische Suche ab 100 Seiten)
  - Stufe 3: Routing-Schritt beim Ingest, Prompt Caching (~90%), Ephemere Mini-Bases
  - Stufe 4: Lernschleifen + Graduation, täglicher Workflow
  - Schwellenwert-Tabelle: welche Strategie bei welcher Wiki-Größe
- `wiki/index.md` aktualisiert

## [2026-04-30] lint | Wiki-Prüfung

- 3 Dead Links gemeldet (`pfad.md`) — alle Falsch-Positive: Syntaxbeispiele in Backtick-Code-Spans
- Alle Orphan/Missing-Meldungen — Falsch-Positive: Skript erkennt `[[wiki-links]]` nicht
- Alle Format-Fehler (no H1, no Footer) — Falsch-Positive: Skript überspringt YAML-Frontmatter nicht
- Wiki ist sauber — keine echten Fehler
- `README.md` um Software-Begleiter, KI-Lehrer und Nachhilfe erweitert (7 Vorlagen gesamt)

## [2026-04-29 16:00] query | Vorlage Nachhilfe erstellt

- `wiki/vorlagen/claude-md-nachhilfe.md` — neue Vorlage: KI als Fachlehrer, reaktiv, aufgabengetrieben
  - Ein Fach pro Projekt, kein generierter Lehrplan
  - Workflow: Material in raw/clippings → Claude erklärt → Lösung vollständig zeigen → Wiki dokumentieren
  - Seitenformate: aufgaben/, konzepte/, fortschritt.md
  - Keine Lösungsbeschränkung (bewusste Designentscheidung)
  - Cloud-Modell empfohlen — einzige Option für Foto/Scan-Input (Vision)
- `wiki/index.md` aktualisiert

---

[Zurück zum Index](index.md)
