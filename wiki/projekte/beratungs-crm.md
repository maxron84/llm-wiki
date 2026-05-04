---
date: 2026-05-04
type: konzept
tags: [konzept, projekt, crm, numerologie, astrologie]
status: draft
---

# Konzept: Beratungs-CRM mit esoterischer Profiltiefe

**Zusammenfassung**: Konzeptpapier für ein lokales, KI-gestütztes Klientenverwaltungssystem für eine selbständige psychologische Beraterin mit numerologischen und astrologischen Elementen — datenschutzkonform, offline-fähig, primär Markdown-basiert.
**Quellen**: Konzept erarbeitet in Besprechung, keine externen Quellen.
**Zuletzt aktualisiert**: 2026-05-04

---

## Ausgangslage und Ziel

Eine selbständige Beraterin bietet psychologische Einzelberatung an, ergänzt durch esoterische Diagnostik (Numerologie, Astrologie). Sie führt Klientenakten heute vermutlich in einem Mix aus Notizbuch, Word-Dokumenten und Kalender-App — ohne Zusammenhang, ohne Suchfunktion, ohne KI-Unterstützung.

Das System ersetzt diesen Mix durch eine einheitliche, dateibasierte Wissensbasis:

- **Klientenprofile** als Markdown-Dateien mit strukturiertem Frontmatter
- **Sitzungsnotizen** als chronologische Einträge, verknüpft mit dem Profil
- **Numerologische und astrologische Kennzahlen** automatisch berechnet, im Profil gespeichert
- **Rechnungen** als Markdown, auf Knopfdruck als PDF exportierbar
- **Lokales KI-Modell** (Qwen3 27B) für Sitzungsvorbereitung, Synthese und Formulierungshilfe

Nichts verlässt den Rechner. Kein Abo, kein Cloud-Dienst, keine Datenweitergabe.

---

## Zielgruppe

**Primär**: Selbständige Beraterin, Solo-Betrieb, 10–40 aktive Klienten, deutschsprachig.

**Technisches Niveau**: Grundlegende Computerkenntnisse. Die Beraterin soll Markdown-Dateien in einem Editor bearbeiten können — mehr nicht. Python-Skripte laufen per Doppelklick oder Terminal-Befehl, keine Programmierkenntnisse nötig.

**Nicht** für: Praxen mit mehreren Therapeuten, Kassenzulassung, Abrechnungspflicht gegenüber Krankenkassen.

---

## Ordnerstruktur

```
beratung/
  CLAUDE.md                        ← KI-Anweisung (Rolle, Regeln, Datenschutz)
  clients/
    maya-hoffmann.md               ← Klientenprofil mit Frontmatter
    jonas-brandt.md
  sessions/
    2026-04-10-maya-hoffmann.md    ← Sitzungsnotiz
    2026-04-17-jonas-brandt.md
  rechnungen/
    2026-017.md                    ← Rechnungsdatei
    2026-018.md
  knowledge/
    numerologie-pythagoräisch.md   ← Interpretationsbasis für KI
    astrologie-planeten.md
    astrologie-aspekte.md
    psychologische-ansätze.md      ← Methoden, Modelle, Interventionen
  vorlagen/
    rechnung-vorlage.md            ← Rechnungsvorlage (Pandoc-Template)
    sitzungsnotiz-vorlage.md
  scripts/
    berechne-profil.py             ← Numerologie + Astrologie berechnen
    woche.py                       ← Wochenübersicht und Vorbereitung
    rechnung-pdf.py                ← Rechnung als PDF exportieren
    offene-rechnungen.py           ← Unbezahlte Rechnungen auflisten
    monatsbericht.py               ← Umsatz, Auslastung, Statistik
    loesche-klient.py              ← DSGVO-konforme Löschung
```

---

## Datenmodell

### Klientenprofil (`clients/vorname-nachname.md`)

```markdown
---
name: Maya Hoffmann
geburtsname: Maya Brenner
geburtsdatum: 1988-03-14
geburtszeit: "09:45"          # optional — für Aszendent-Berechnung
geburtsort: München           # optional — für Astrologie
telefon: "+49 176 ..."
email: maya@beispiel.de
erstgespräch: 2026-01-10
letzter-kontakt: 2026-04-17
nächster-termin: 2026-05-20
status: aktiv                 # aktiv | pausiert | abgeschlossen
themen:
  - Selbstwert
  - Karriereblockaden
  - Partnerschaft

# Numerologie — berechnet von scripts/berechne-profil.py
lebenszahl: 8
ausdruckszahl: 6
seelenzahl: 3
persönlichkeitszahl: 3
schicksalszahl: 5

# Astrologie — berechnet von scripts/berechne-profil.py
sonne: Fische
mond: Skorpion
aszendent: Krebs           # nur wenn Geburtszeit vorhanden
dominantes-element: Wasser
dominante-qualität: Fix
---

## Hintergrund

Maya kam nach einem unfreiwilligen Jobverlust. Äußerlich ruhig, wirkt aber
innerlich angespannt. Hat Schwierigkeiten, eigene Bedürfnisse zu artikulieren.

## Numerologisches Profil

Lebenszahl 8 steht für Macht, materielle Verantwortung und Führung —
im Kontext des Karrierethemas besonders aussagekräftig. Die Diskrepanz
zwischen 8 (Außenwirkung/Karriere) und Seelenzahl 3 (Kreativität, Ausdruck)
deutet auf einen inneren Konflikt zwischen gesellschaftlichem Anspruch
und persönlichem Bedürfnis hin.

## Astrologisches Profil

Sonne in Fische / Mond in Skorpion: hohe Empathiefähigkeit, Tendenz zur
Selbstaufopferung, starke intuitive Wahrnehmung. Aszendent Krebs verstärkt
das Fürsorgliche nach außen. Der Wasser-Überhang erklärt die Schwierigkeiten
mit klarer Abgrenzung — ein zentrales Beratungsthema.

## Synthese

Numerologie und Astrologie zeigen konsistent: Maya ist jemand, der viel
für andere trägt (8, Skorpion, Krebs), aber wenig für sich selbst einfordert
(Fische-Sonne, Seelenzahl 3 unterdrückt). Das Karrierevent ist vermutlich
ein Katalysator für eine tiefere Identitätsfrage.
```

Der Synthese-Abschnitt wird beim ersten Anlegen von der KI vorgeschlagen — die Beraterin überarbeitet und ergänzt ihn mit ihrer eigenen Einschätzung.

---

### Sitzungsnotiz (`sessions/YYYY-MM-DD-nachname.md`)

```markdown
---
datum: 2026-04-17
client: maya-hoffmann
dauer-minuten: 90
art: folgegespräch          # erstgespräch | folgegespräch | online | präsenz
sitzung-nr: 4
rechnung: 2026-017          # Referenz auf Rechnungsdatei, leer wenn noch offen
---

## Themen dieser Sitzung

Selbstwert im beruflichen Kontext. Maya hat seit letzter Woche eine
Bewerbung abgeschickt — erster konkreter Schritt seit dem Jobverlust.

## Beobachtungen

Körperhaltung offener als in Sitzung 3. Spricht schneller, weniger Pausen.
Weint kurz beim Thema "Anerkennung durch Chef" — bisher nicht adressiert.

## Interventionen

- Übung: Tagesprotokoll "Was habe ich heute gut gemacht?" (10 Min. abends)
- Frage als Hausaufgabe: "Was würde ich tun, wenn niemand zuschaut?"

## Offene Punkte

- Thema Anerkennung vertiefen (Elternbezug prüfen)
- Nächste Sitzung: Ergebnis der Bewerbung besprechen
```

---

### Rechnung (`rechnungen/YYYY-NNN.md`)

```markdown
---
rechnungsnummer: 2026-017
client: maya-hoffmann
datum: 2026-04-30
fällig: 2026-05-14
positionen:
  - bezeichnung: "Psychologische Beratung (Sitzung 3, 90 Min.)"
    menge: 1
    einzelpreis: 130.00
  - bezeichnung: "Psychologische Beratung (Sitzung 4, 90 Min.)"
    menge: 1
    einzelpreis: 130.00
kleinunternehmer: true      # false = 19% MwSt. wird ausgewiesen
bezahlt: false
bezahlt-am: null
---
```

`scripts/rechnung-pdf.py` liest dieses Frontmatter, zieht Adresse und Name aus dem Client-Profil und rendert per Pandoc eine druckfertige PDF.

---

## Python-Skripte im Detail

### `berechne-profil.py`

Liest ein Client-Profil, berechnet alle Kennzahlen und schreibt sie zurück ins Frontmatter. Abhängigkeiten: `python-frontmatter`, `kerykeion` (Astrologie).

**Numerologie-Berechnung** (pythagoräisches System):
- **Lebenszahl**: Quersumme aus Geburtsdatum, iteriert bis einstellig (Ausnahme: 11, 22, 33)
- **Ausdruckszahl**: Zahlenwert aller Buchstaben des Vollnamens (A=1, B=2 ...)
- **Seelenzahl**: Nur Vokale des Namens
- **Persönlichkeitszahl**: Nur Konsonanten
- **Schicksalszahl**: Ausdruckszahl + Lebenszahl, reduziert

**Astrologie-Berechnung** (`kerykeion`):
- Geburtsdatum + Uhrzeit + Koordinaten des Geburtsorts → vollständiges Horoskop
- Ohne Geburtszeit: Sonne, Mond und Planeten ohne Häuser und Aszendent
- Ergebnis: Sonne, Mond, Aszendent, dominantes Element und Qualität

Aufruf:
```bash
python3 scripts/berechne-profil.py clients/maya-hoffmann.md
```

### `woche.py`

Gibt eine strukturierte Wochenvorschau aus:

```
Woche 20. – 24. Mai 2026
━━━━━━━━━━━━━━━━━━━━━━━━
Mo 20.05  Maya Hoffmann      10:00  Sitzung 5  (letzte: 17.04, 33 Tage)
Di 21.05  Jonas Brandt       14:30  Erstgespräch
Do 23.05  —

Vorbereitung ausstehend:
  → Maya Hoffmann: Offener Punkt aus Sitzung 4: Anerkennungsthema
  → Jonas Brandt:  Erstkontakt, kein Profil vorhanden
```

Das Skript liest `nächster-termin` aus allen Client-Profilen und prüft, ob Sitzungsnotizen offene Punkte enthalten.

### `rechnung-pdf.py`

Kombiniert Rechnungs-Frontmatter + Client-Adresse + Vorlage und erzeugt eine PDF:

```bash
python3 scripts/rechnung-pdf.py rechnungen/2026-017.md
# → rechnungen/2026-017.pdf
```

Pandoc rendert die Rechnung aus einer Jinja2-Vorlage. Kleinunternehmerregelung und Regelbesteuerung als Flag konfigurierbar.

### `loesche-klient.py`

DSGVO-konforme Löschung: Entfernt alle Dateien eines Klienten (Profil + alle Sitzungsnotizen), lässt aber Rechnungen für die gesetzliche Aufbewahrungsfrist (10 Jahre, §147 AO) stehen und pseudonymisiert sie.

```bash
python3 scripts/loesche-klient.py clients/maya-hoffmann.md
# Bestätigung erforderlich: "Alle Daten für Maya Hoffmann löschen? [j/N]"
```

---

## KI-Integration

### CLAUDE.md — Kernregeln

```markdown
# CLAUDE — Beratungsassistentin

Du bist Assistentin einer psychologischen Beraterin.
Du arbeitest ausschließlich mit den Dateien in diesem Projektordner.

## Rolle
- Sitzungsvorbereitung: Zusammenfassung der letzten Sitzungen, offene Themen
- Profilsynthese: Numerologie und Astrologie im psychologischen Kontext verbinden
- Formulierungshilfe: Rohmitschriften in strukturierte Sitzungsnotizen umwandeln
- Wissensrecherche: in knowledge/ nach passenden Methoden suchen

## Grenzen
- Du interpretierst numerologische und astrologische Profile nur als Angebote,
  nicht als Urteile. Die Beraterin entscheidet, was sie verwendet.
- Du diagnostizierst nicht. Du beschreibst Muster, keine Befunde.
- Du gibst keine medizinischen oder rechtlichen Empfehlungen.

## Datenschutz
- Gib in Zusammenfassungen nie Klarnamen aus, wenn nicht explizit gefragt.
- Rechnungsdaten nur auf ausdrückliche Anfrage zeigen.
```

### Typische Arbeitsabläufe

**Sitzungsvorbereitung:**
```
@clients/maya-hoffmann.md @sessions/

Ich habe morgen Sitzung 5 mit Maya. Fasse die letzten drei Sitzungen zusammen,
hebe offene Punkte hervor und schlage einen möglichen Einstieg vor.
```

**Profilanlage:**
```
@clients/jonas-brandt.md @knowledge/numerologie-pythagoräisch.md @knowledge/astrologie-planeten.md

Jonas hat heute das Erstgespräch. Schreibe einen ersten Synthese-Abschnitt
für sein Profil basierend auf den berechneten Kennzahlen.
```

**Sitzungsnachbereitung:**
```
@vorlagen/sitzungsnotiz-vorlage.md

Wandle diese Rohmitschrift in eine strukturierte Sitzungsnotiz um:
[Mitschrift einfügen]
```

---

## Ausbaustufen

### Version 1 — Kern (empfohlener Start)

- Client-Profile mit Frontmatter
- Sitzungsnotizen
- Numerologie-Berechnung (`berechne-profil.py` — Astrologie optional deaktivierbar)
- Wochenübersicht (`woche.py`)
- KI-Sitzungsvorbereitung via Continue + Qwen3 27B
- `knowledge/` mit Numerologie-Interpretationsbasis

**Aufwand**: 2–3 Tage Entwicklung, danach sofort nutzbar.

### Version 2 — Erweiterung

- Astrologie vollständig (`kerykeion` + Interpretationsbasis in `knowledge/`)
- Rechnungsstellung mit PDF-Export
- Monatsbericht (Umsatz, Auslastung)
- DSGVO-Löschskript

### Version 3 — Optional

- Geburtstags-Erinnerungen (automatisch aus Frontmatter)
- Exportfunktion „Klientenmappe" (alle Sitzungen als PDF für Übergabe)
- Zweites esoterisches System (Human Design, Tarot-Affinität) als weiteres Modul

---

## Technischer Stack

| Komponente | Werkzeug | Zweck |
|---|---|---|
| Dateiformat | Markdown + YAML Frontmatter | Primäres Datenformat |
| Frontmatter-Lesen | `python-frontmatter` | Python-Zugriff auf YAML |
| Numerologie | Custom Python | Lebenszahl, Ausdruckszahl etc. |
| Astrologie | `kerykeion` | Geburtshoroskop, Aspekte |
| PDF-Export | Pandoc + Jinja2 | Rechnungen, Ausdrucke |
| KI | Qwen3 27B via Ollama | Synthese, Vorbereitung, Formulierung |
| KI-Interface | Continue in VS Codium | `@`-Dateireferenzen, System-Prompt |
| Wiki-Betrachter | Obsidian | Alle Dateien durchsuchen und lesen |
| Versionierung | Git | Backup, Änderungsverlauf |

---

## DSGVO-Hinweise

Psychologische Beratungsnotizen fallen unter **Art. 9 DSGVO** — besondere Kategorien personenbezogener Daten. Das ist die höchste Schutzklasse.

**Konsequenzen für das System:**

- Keine Cloud-Synchronisation (kein iCloud, kein Google Drive, kein Dropbox)
- Git-Backup nur auf eigenes, verschlüsseltes Repository (kein öffentliches GitHub)
- Verschlüsselung der Festplatte empfohlen (FileVault auf Mac, BitLocker auf Windows)
- Rechnungen: 10 Jahre Aufbewahrungspflicht (§147 AO) — auch nach Klientenlöschung
- Beratungsnotizen: Aufbewahrungsdauer individuell prüfen; Faustregel: solange die Beziehung aktiv ist, danach auf Anfrage löschen

**Was das System erleichtert:**
- Löschen auf Anfrage ist durch `loesche-klient.py` dokumentiert und nachvollziehbar
- Klar getrennte Datenarten (Rechnungen vs. Beratungsinhalte) vereinfachen die Abwägung

---

## Offene Entscheidungen

Vor Projektstart zu klären:

| Frage | Optionen |
|---|---|
| Steuermodell | Kleinunternehmerregelung (kein MwSt-Ausweis) oder Regelbesteuerung (19%) |
| Pseudonymisierung | Immer (Klient-ID statt Name in Notizen) / Optional / Nicht nötig |
| Backup-Strategie | Verschlüsseltes lokales Backup / Eigener NAS / Verschlüsselter Git-Remote |
| Weitere esoterische Systeme | Nur Numerologie + Astrologie, oder auch Human Design / Tarot? |
| Geburtszeit-Pflichtfeld | Optional lassen oder als Pflichtfeld (schlechtere Datenlage aber vollständigeres Profil) |
| Sitzungsnotizen-Sprache | Deutsch durchgehend, oder englische Schlüsselbegriffe (z.B. für Fachbegriffe) |

---

## Verwandte Seiten

- [lokale-modelle-fortgeschritten](../anleitungen/lokale-modelle-fortgeschritten.md) — Technische Einrichtung: Ollama, Qwen3, Continue
- [claude-md-software](../vorlagen/claude-md-software.md) — Basis für die CLAUDE.md dieses Projekts
- [kontaminierungsrisiko](../konzepte/kontaminierungsrisiko.md) — Warum KI-Vorschläge immer menschlich überprüft werden müssen
- [vault-trennung](../konzepte/vault-trennung.md) — Empfehlung für den Obsidian-Vault-Aufbau

---

[Wiki-Index](../index.md)
