---
date: 2026-05-04
type: anleitung
tags: [anleitung, setup, crm, numerologie, astrologie, linux]
status: active
---

# Beratungs-CRM einrichten: Linux, Obsidian, VS Codium

**Zusammenfassung**: Schritt-für-Schritt-Einrichtung des lokalen Beratungs-CRM auf Linux — von der Softwareinstallation bis zum ersten funktionierenden Klientenprofil mit Numerologie und Astrologie.
**Zuletzt aktualisiert**: 2026-05-04

---

> Konzept und Hintergrund → [beratungs-crm](../projekte/beratungs-crm.md)
> Technische Grundlagen (Ollama, Continue) → [lokale-modelle-fortgeschritten](lokale-modelle-fortgeschritten.md)

---

## Was am Ende läuft

- **Qwen3 27B** lokal via Ollama — keine Daten verlassen den Rechner
- **VS Codium + Continue** als Arbeitsumgebung mit Dateizugriff auf Klientenprofile
- **Python-Skripte**: Numerologie + Astrologie automatisch berechnet, Wochenvorschau, offene Rechnungen
- **Obsidian** als Lesewerkzeug für alle Markdown-Dateien

---

## Voraussetzungen

### Hardware

Mindestens 32 GB RAM für Qwen3 27B (CPU-Betrieb). Mit dedizierter GPU ab 24 GB VRAM deutlich schneller. Details → [lokale-modelle](lokale-modelle.md).

### Betriebssystem

Ubuntu 22.04 / 24.04 oder eine vergleichbare Debian-basierte Distribution. Alle Befehle sind für `bash` geschrieben.

---

## Schritt 1: Systemabhängigkeiten installieren

```bash
sudo apt update
sudo apt install -y git python3 python3-pip python3-venv pandoc curl
```

Versionen prüfen:

```bash
python3 --version    # mindestens 3.10
git --version
pandoc --version
```

---

## Schritt 2: Ollama und Qwen3 einrichten

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama pull qwen3:27b
```

Der Download dauert 15–45 Minuten (ca. 17 GB). Kurzer Test:

```bash
ollama run qwen3:27b "Antworte mit einem Satz auf Deutsch."
# Mit /bye beenden
```

Ollama läuft danach dauerhaft im Hintergrund auf `http://localhost:11434`.

---

## Schritt 3: VS Codium und Continue einrichten

**VS Codium installieren** (falls noch nicht vorhanden):

```bash
# Über das offizielle Repository
wget -qO - https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/master/pub.gpg \
  | gpg --dearmor | sudo dd of=/usr/share/keyrings/vscodium-archive-keyring.gpg

echo 'deb [ signed-by=/usr/share/keyrings/vscodium-archive-keyring.gpg ] https://download.vscodium.com/debs vscodium main' \
  | sudo tee /etc/apt/sources.list.d/vscodium.list

sudo apt update && sudo apt install -y codium
```

**Continue Extension installieren:**

1. VS Codium starten: `codium`
2. Extensions öffnen: `Ctrl+Shift+X`
3. Nach `Continue` suchen → Extension von `Continue.dev` installieren
4. VS Codium neu starten

**Continue mit Ollama verbinden** — `~/.continue/config.json` öffnen (Continue-Panel → Zahnrad-Icon) und eintragen:

```json
{
  "models": [
    {
      "title": "Qwen3 27B (lokal)",
      "provider": "ollama",
      "model": "qwen3:27b",
      "contextLength": 32768,
      "completionOptions": {
        "temperature": 0.3
      }
    }
  ]
}
```

Verbindung testen: Im Continue-Chat `Hallo` eingeben — Antwort sollte innerhalb weniger Sekunden erscheinen.

---

## Schritt 4: Projektordner anlegen

```bash
mkdir -p ~/beratung
cd ~/beratung
git init

mkdir -p clients sessions rechnungen knowledge vorlagen scripts
```

Vollständige Struktur nach diesem Schritt:

```
~/beratung/
  clients/        ← Klientenprofile
  sessions/       ← Sitzungsnotizen
  rechnungen/     ← Rechnungsdateien
  knowledge/      ← Interpretationsbasis für die KI
  vorlagen/       ← Vorlagen für Notizen und Rechnungen
  scripts/        ← Python-Skripte
  CLAUDE.md       ← KI-Anweisung (gleich anlegen)
```

---

## Schritt 5: Python-Umgebung einrichten

Eine virtuelle Umgebung hält die Abhängigkeiten sauber und verhindert Konflikte mit dem System-Python.

```bash
cd ~/beratung
python3 -m venv .venv
source .venv/bin/activate
```

Abhängigkeiten installieren:

```bash
pip install python-frontmatter kerykeion jinja2 weasyprint
```

Die Liste als Datei speichern, damit sie später reproduzierbar ist:

```bash
pip freeze > requirements.txt
```

> **Venv aktivieren**: Jedes Mal, wenn ein neues Terminal geöffnet wird, muss die Umgebung neu aktiviert werden: `source ~/beratung/.venv/bin/activate`. Alternativ den folgenden Alias in `~/.bashrc` eintragen: `alias beratung='cd ~/beratung && source .venv/bin/activate'`

---

## Schritt 6: Python-Skripte anlegen

### `scripts/berechne-profil.py`

Liest ein Klientenprofil, berechnet alle Numerologie- und Astrologiekennzahlen und schreibt sie zurück ins Frontmatter.

```python
#!/usr/bin/env python3
"""Berechnet Numerologie und Astrologie für ein Klientenprofil."""

import sys
from pathlib import Path
import frontmatter

# ── Numerologie ──────────────────────────────────────────────────────────────

PYTHAGORAEISCH = {
    'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5, 'f': 6, 'g': 7, 'h': 8, 'i': 9,
    'j': 1, 'k': 2, 'l': 3, 'm': 4, 'n': 5, 'o': 6, 'p': 7, 'q': 8, 'r': 9,
    's': 1, 't': 2, 'u': 3, 'v': 4, 'w': 5, 'x': 6, 'y': 7, 'z': 8
}
VOKALE = set('aeiou')
MEISTERZAHLEN = {11, 22, 33}

def normalisiere(text):
    """Umlaute ersetzen, Kleinbuchstaben."""
    return (text.lower()
            .replace('ä', 'ae').replace('ö', 'oe').replace('ü', 'ue')
            .replace('ß', 'ss'))

def reduziere(n):
    while n > 9 and n not in MEISTERZAHLEN:
        n = sum(int(z) for z in str(n))
    return n

def lebenszahl(geburtsdatum):
    """Quersumme aus YYYY-MM-DD, iteriert bis einstellig (Meisterzahlen bleiben)."""
    ziffern = geburtsdatum.replace('-', '')
    return reduziere(sum(int(z) for z in ziffern))

def ausdruckszahl(name):
    """Alle Buchstaben des vollständigen Namens."""
    n = normalisiere(name)
    return reduziere(sum(PYTHAGORAEISCH.get(c, 0) for c in n if c.isalpha()))

def seelenzahl(name):
    """Nur Vokale."""
    n = normalisiere(name)
    return reduziere(sum(PYTHAGORAEISCH.get(c, 0) for c in n if c in VOKALE))

def persoenlichkeitszahl(name):
    """Nur Konsonanten."""
    n = normalisiere(name)
    return reduziere(sum(PYTHAGORAEISCH.get(c, 0) for c in n
                         if c.isalpha() and c not in VOKALE))

# ── Astrologie ───────────────────────────────────────────────────────────────

TIERKREIS_DE = {
    'Ari': 'Widder', 'Tau': 'Stier', 'Gem': 'Zwillinge', 'Can': 'Krebs',
    'Leo': 'Löwe', 'Vir': 'Jungfrau', 'Lib': 'Waage', 'Sco': 'Skorpion',
    'Sag': 'Schütze', 'Cap': 'Steinbock', 'Aqu': 'Wassermann', 'Pis': 'Fische'
}

def berechne_astrologie(geburtsdatum, geburtszeit=None, geburtsort=None):
    try:
        from kerykeion import AstrologicalSubject
        teile = geburtsdatum.split('-')
        jahr, monat, tag = int(teile[0]), int(teile[1]), int(teile[2])

        if geburtszeit:
            h, m = (int(x) for x in str(geburtszeit).split(':'))
        else:
            h, m = 12, 0  # Mittagszeit als Fallback — kein Aszendent möglich

        ort = geburtsort if geburtsort else 'Berlin'
        subjekt = AstrologicalSubject('Klient', jahr, monat, tag, h, m, ort)

        def sign(planet):
            return TIERKREIS_DE.get(planet['sign'][:3], planet['sign'])

        ergebnis = {
            'sonne': sign(subjekt.sun),
            'mond':  sign(subjekt.moon),
        }
        if geburtszeit:
            ergebnis['aszendent'] = TIERKREIS_DE.get(
                subjekt.first_house['sign'][:3], subjekt.first_house['sign'])

        return ergebnis

    except ImportError:
        print("  Hinweis: kerykeion nicht installiert — Astrologie übersprungen.")
        return {}
    except Exception as e:
        print(f"  Astrologie-Fehler: {e}")
        return {}

# ── Hauptprogramm ─────────────────────────────────────────────────────────────

def main():
    if len(sys.argv) < 2:
        print("Aufruf: python3 scripts/berechne-profil.py clients/name.md")
        sys.exit(1)

    pfad = Path(sys.argv[1])
    if not pfad.exists():
        print(f"Datei nicht gefunden: {pfad}")
        sys.exit(1)

    post = frontmatter.load(str(pfad))
    meta = post.metadata

    geburtsdatum = str(meta.get('geburtsdatum', ''))
    geburtsname  = str(meta.get('geburtsname', meta.get('name', '')))

    if not geburtsdatum:
        print("Fehler: kein 'geburtsdatum' im Profil.")
        sys.exit(1)

    lz = lebenszahl(geburtsdatum)
    az = ausdruckszahl(geburtsname)

    meta['lebenszahl']        = lz
    meta['ausdruckszahl']     = az
    meta['seelenzahl']        = seelenzahl(geburtsname)
    meta['persoenlichkeitszahl'] = persoenlichkeitszahl(geburtsname)
    meta['schicksalszahl']    = reduziere(lz + az)

    astro = berechne_astrologie(
        geburtsdatum,
        meta.get('geburtszeit'),
        meta.get('geburtsort')
    )
    meta.update(astro)

    with open(pfad, 'wb') as f:
        frontmatter.dump(post, f)

    print(f"\n✓  {pfad.name} aktualisiert")
    print(f"   Lebenszahl {meta['lebenszahl']}  |  "
          f"Ausdruckszahl {meta['ausdruckszahl']}  |  "
          f"Seelenzahl {meta['seelenzahl']}")
    if 'sonne' in astro:
        asz = f"  |  Aszendent {astro['aszendent']}" if 'aszendent' in astro else ''
        print(f"   Sonne {astro['sonne']}  |  Mond {astro['mond']}{asz}")

if __name__ == '__main__':
    main()
```

### `scripts/woche.py`

Gibt eine Wochenvorschau mit allen anstehenden Terminen aus.

```python
#!/usr/bin/env python3
"""Wochenvorschau: anstehende Termine und Vorbereitungshinweise."""

import glob
from datetime import date, timedelta
from pathlib import Path
import frontmatter

HORIZONT_TAGE = 10

heute  = date.today()
bis    = heute + timedelta(days=HORIZONT_TAGE)

print(f"\nVorschau {heute.strftime('%d.%m.')} – {bis.strftime('%d.%m.%Y')}")
print("━" * 50)

termine = []
vorbereitung = []

for pfad in sorted(Path('clients').glob('*.md')):
    post = frontmatter.load(str(pfad))
    meta = post.metadata
    name = meta.get('name', pfad.stem)

    termin_str = str(meta.get('nächster-termin', ''))
    if termin_str:
        try:
            termin = date.fromisoformat(termin_str)
            if heute <= termin <= bis:
                letzter = str(meta.get('letzter-kontakt', '—'))
                termine.append((termin, name, letzter))
        except ValueError:
            pass

    # Offene Punkte aus letzter Sitzungsnotiz suchen
    sitzungen = sorted(Path('sessions').glob(f'*-{pfad.stem}.md'))
    if sitzungen:
        letzte = frontmatter.load(str(sitzungen[-1]))
        inhalt = letzte.content
        if '## Offene Punkte' in inhalt:
            block = inhalt.split('## Offene Punkte')[1].split('##')[0].strip()
            if block:
                vorbereitung.append((name, block))

if termine:
    for termin, name, letzter in sorted(termine):
        print(f"  {termin.strftime('%a %d.%m.')}  {name:<28} letzter Kontakt: {letzter}")
else:
    print("  Keine Termine in den nächsten 10 Tagen.")

if vorbereitung:
    print(f"\nOffene Punkte aus letzten Sitzungen:")
    for name, punkte in vorbereitung:
        print(f"\n  {name}:")
        for zeile in punkte.splitlines():
            if zeile.strip():
                print(f"    {zeile}")

print()
```

### `scripts/offene-rechnungen.py`

```python
#!/usr/bin/env python3
"""Listet alle unbezahlten Rechnungen."""

from pathlib import Path
from datetime import date
import frontmatter

heute = date.today()
print("\nOffene Rechnungen")
print("━" * 40)

offen = []
for pfad in sorted(Path('rechnungen').glob('*.md')):
    post = frontmatter.load(str(pfad))
    meta = post.metadata
    if not meta.get('bezahlt', False):
        fällig_str = str(meta.get('fällig', ''))
        try:
            fällig = date.fromisoformat(fällig_str)
            überfällig = (heute - fällig).days if fällig < heute else 0
        except ValueError:
            fällig = None
            überfällig = 0
        offen.append((fällig, meta.get('rechnungsnummer', pfad.stem),
                      meta.get('client', '—'), überfällig))

if offen:
    for fällig, nr, client, überfällig in sorted(offen):
        hinweis = f"  ⚠ {überfällig} Tage überfällig" if überfällig > 0 else ''
        print(f"  {nr}   {client:<25} fällig: {fällig}{hinweis}")
else:
    print("  Keine offenen Rechnungen.")

print()
```

---

## Schritt 7: CLAUDE.md anlegen

Die CLAUDE.md erklärt dem lokalen Modell seine Rolle und die Projektregeln.

```bash
nano ~/beratung/CLAUDE.md
```

Inhalt:

```markdown
# CLAUDE — Beratungsassistentin

Du bist Assistentin einer selbständigen psychologischen Beraterin.
Du arbeitest ausschließlich mit den Dateien in diesem Projektordner.
Alle Daten sind vertraulich und verbleiben auf diesem Rechner.

## Rolle

- **Sitzungsvorbereitung**: Letzte Sitzungen zusammenfassen, offene Themen benennen, Einstieg vorschlagen
- **Profilsynthese**: Numerologie und Astrologie im psychologischen Kontext verbinden
- **Sitzungsnachbereitung**: Rohmitschriften in strukturierte Notizen umwandeln
- **Wissensrecherche**: In knowledge/ nach passenden Methoden und Interventionen suchen

## Grenzen

- Du interpretierst numerologische und astrologische Profile nur als Gesprächsangebote, nicht als Urteile.
- Du diagnostizierst nicht. Du beschreibst Muster, keine Befunde.
- Du gibst keine medizinischen oder rechtlichen Empfehlungen.
- Die Beraterin trifft alle inhaltlichen Entscheidungen selbst.

## Datenschutz

- Nenne in Zusammenfassungen keine Klarnamen, sofern nicht ausdrücklich gewünscht.
- Rechnungsdaten nur auf ausdrückliche Anfrage ausgeben.
- Bei Unsicherheit: lieber nachfragen als annehmen.

## Dateistruktur

- clients/     — Klientenprofile (YAML-Frontmatter + Fließtext)
- sessions/    — Sitzungsnotizen (chronologisch, verknüpft mit Client)
- rechnungen/  — Rechnungsdateien
- knowledge/   — Interpretationsbasis: Numerologie, Astrologie, psychologische Methoden
```

---

## Schritt 8: Wissensbasis anlegen

Die KI benötigt eine Interpretationsbasis, um Numerologie- und Astrologieprofil mit dem psychologischen Kontext zu verbinden.

```bash
nano ~/beratung/knowledge/numerologie-pythagoräisch.md
```

Minimalinhalt (nach Bedarf erweitern):

```markdown
# Numerologie — Pythagoräisches System

## Lebenszahlen

**1** — Pionier, Unabhängigkeit, Führung. Lernaufgabe: Selbstvertrauen ohne Dominanz.
**2** — Diplomatie, Mitgefühl, Kooperation. Lernaufgabe: eigene Bedürfnisse einfordern.
**3** — Kreativität, Ausdruck, Freude. Lernaufgabe: Tiefe statt Ablenkung.
**4** — Stabilität, Ordnung, Disziplin. Lernaufgabe: Flexibilität zulassen.
**5** — Freiheit, Wandel, Abenteuer. Lernaufgabe: Verbindlichkeit halten.
**6** — Fürsorge, Harmonie, Verantwortung. Lernaufgabe: Selbstfürsorge.
**7** — Analyse, Tiefe, Spiritualität. Lernaufgabe: Vertrauen in andere.
**8** — Macht, Materialität, Führung. Lernaufgabe: Kontrolle loslassen.
**9** — Weisheit, Mitgefühl, Vollendung. Lernaufgabe: Loslassen.
**11** — Meisterzahl: intuitive Führung, hohe Sensibilität.
**22** — Meisterzahl: Visionär, praktische Umsetzung großer Ideen.
**33** — Meisterzahl: Hochstes Mitgefühl, Lehrer der Menschheit.
```

Ebenso anlegen: `knowledge/astrologie-planeten.md` und `knowledge/psychologische-ansätze.md` (zunächst leer oder mit eigenem Inhalt der Beraterin füllen).

---

## Schritt 9: Obsidian einrichten

Obsidian wird als Lesewerkzeug für alle Dateien im Projekt genutzt.

1. Obsidian starten (falls noch nicht installiert: [obsidian.md](https://obsidian.md) → `.AppImage` oder `.deb`)
2. **„Ordner als Vault öffnen"** → `~/beratung` wählen (den gesamten Projektordner, nicht einen Unterordner)
3. Obsidian zeigt jetzt alle Ordner in der linken Leiste

**Nützliche Einstellungen für dieses Projekt:**

- `Einstellungen → Dateien & Verknüpfungen → Standardspeicherort für neue Notizen` → `sessions/` (neue Sitzungsnotizen landen automatisch dort)
- Graph-Ansicht (`Ctrl+G`): Verbindungen zwischen Klientenprofilen und Sitzungsnotizen sichtbar

> **Sicherheitshinweis**: Obsidian-Sync und iCloud-Sync deaktivieren. Alle Daten müssen lokal bleiben. Einstellungen → Core Plugins → Sync → **nicht** aktivieren.

---

## Schritt 10: Erstes Klientenprofil anlegen und testen

### Profil erstellen

```bash
cd ~/beratung
nano clients/beispiel-klient.md
```

Inhalt:

```markdown
---
name: Anna Beispiel
geburtsname: Anna Müller
geburtsdatum: 1990-06-15
geburtszeit: "08:30"
geburtsort: Hamburg
telefon: "+49 176 000000"
email: anna@beispiel.de
erstgespräch: 2026-05-04
letzter-kontakt: 2026-05-04
nächster-termin: 2026-05-18
status: aktiv
themen:
  - Orientierung
  - Beziehungsthemen
---

## Hintergrund

Erstkontakt per E-Mail. Sucht Orientierung nach Trennung.
```

### Numerologie und Astrologie berechnen

```bash
cd ~/beratung
source .venv/bin/activate
python3 scripts/berechne-profil.py clients/beispiel-klient.md
```

Ausgabe sollte in etwa so aussehen:

```
✓  beispiel-klient.md aktualisiert
   Lebenszahl 4  |  Ausdruckszahl 6  |  Seelenzahl 9
   Sonne Zwillinge  |  Mond ...  |  Aszendent Krebs
```

Das Profil jetzt in Obsidian öffnen — die Kennzahlen sind ins Frontmatter eingetragen.

### Wochenvorschau testen

```bash
python3 scripts/woche.py
```

---

## Schritt 11: VS Codium für das Projekt konfigurieren

VS Codium mit dem Projektordner starten:

```bash
codium ~/beratung
```

**Continue für dieses Projekt einrichten:**

Im Continue-Chat die erste Sitzungsvorbereitung testen:

```
@CLAUDE.md @clients/beispiel-klient.md

Ich habe morgen Erstgespräch mit dieser Klientin.
Fasse das Profil zusammen und schlage einen möglichen Einstieg vor.
```

Das Modell liest CLAUDE.md (Rolle und Regeln) sowie das Klientenprofil und gibt einen kontextualisierten Vorbereitungshinweis aus.

---

## Typischer Tagesablauf

### Morgens — Vorbereitung

```bash
cd ~/beratung && source .venv/bin/activate
python3 scripts/woche.py
```

Im Continue-Chat für jeden heutigen Termin:

```
@CLAUDE.md @clients/name.md @sessions/

Bereite mich auf die heutige Sitzung mit [Name] vor.
```

### Nach der Sitzung — Nachbereitung

Rohmitschrift im Continue-Chat übergeben:

```
@CLAUDE.md @vorlagen/sitzungsnotiz-vorlage.md

Wandle diese Mitschrift in eine strukturierte Sitzungsnotiz um:

[hier die Mitschrift einfügen]
```

Ergebnis kopieren und als `sessions/YYYY-MM-DD-nachname.md` speichern.

Danach `letzter-kontakt` und `nächster-termin` im Klientenprofil aktualisieren.

### Monatlich — Rechnungen und Überblick

```bash
python3 scripts/offene-rechnungen.py
```

---

## Häufige Probleme

**„kerykeion kann den Geburtsort nicht finden"**
→ Englischen Ortsnamen verwenden: `Hamburg` statt Ortsteil, `Munich` statt `München`. Alternativ im Profil `geburtsort` leer lassen — Aszendent wird dann nicht berechnet, Sonne und Mond funktionieren trotzdem.

**„`source .venv/bin/activate` — Datei nicht gefunden"**
→ Virtualenv wurde nicht im richtigen Verzeichnis angelegt. Prüfen: `ls ~/beratung/.venv/`. Falls nicht vorhanden: `cd ~/beratung && python3 -m venv .venv`

**„Continue findet @CLAUDE.md nicht"**
→ VS Codium muss mit `codium ~/beratung` (dem Projektordner) gestartet sein, nicht mit einem Unterordner.

**„Das Modell antwortet sehr langsam"**
→ Bei CPU-only-Betrieb mit Qwen3 27B sind 1–3 Token/s normal. Für Sitzungsvorbereitung (kurze Anfragen, nicht zeitkritisch) ist das akzeptabel. Für flüssigere Arbeit: `qwen3:30b-a3b` (MoE-Variante, weniger RAM-Druck).

---

## Git-Backup einrichten

```bash
cd ~/beratung
git add .
git commit -m "Initialer Commit: Projektstruktur"
```

Für regelmäßiges Backup auf einen eigenen Server oder verschlüsselten Remote:

```bash
# Beispiel: eigener NAS oder Hetzner Storage Box
git remote add backup ssh://user@mein-server.de/~/backup/beratung.git
git push backup main
```

> **Kein GitHub oder GitLab** für dieses Projekt — Klientendaten dürfen nicht auf fremde Server. Nur eigene, kontrollierte Infrastruktur.

---

## Verwandte Seiten

- [beratungs-crm](../projekte/beratungs-crm.md) — Konzept, Datenmodell, Ausbaustufen
- [lokale-modelle-fortgeschritten](lokale-modelle-fortgeschritten.md) — Technische Grundlagen: Ollama, Continue, Qwen3
- [obsidian](../werkzeuge/obsidian.md) — Obsidian-Funktionen im Detail

---

[Wiki-Index](../index.md)
