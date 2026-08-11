# Das Wiki als Datenbank: SQL-Betrieb eines LLM-Wikis mit schwachen lokalen Modellen

*Eine Architekturanalyse — warum der Umstieg auf SQL nicht am Wachstum des Wikis hängt, sondern an der Schwäche des Modells*

**Datum:** 2026-08-11
**Art:** Interne Analyse (Synthese aus Wiki-internen Seiten, Herstellerdokumentation und Modellarchitektur-Rechnung)
**Ausgangsfrage:** Lässt sich das LLM-Wiki-Muster mit einer klassischen SQL-Datenbank realisieren, betrieben von einem lokalen Modell bis ~24B auf einer 16-GB-Karte?

---

**TL;DR** — Die naheliegende Begründung für einen SQL-Umbau ist Wachstum: Das Wiki wird zu groß fürs Kontextfenster, also muss man es abfragen statt laden. Diese Begründung ist richtig, aber sie greift zu spät. Der stärkere Grund ist ein anderer: **SQL macht das große Kontextfenster überflüssig — und damit das schwache lokale Modell erst arbeitsfähig.** Ein 24B-Modell mit 100–200k Kontext passt auf 16 GB VRAM nicht einmal annähernd (Rechnung siehe Abschnitt 3). Aber es muss auch nicht passen, wenn jeder Modellaufruf statt eines geladenen Vaults nur drei Sektionen à 400 Token sieht. Der Umbau verschiebt Arbeit vom Modell in den Code: Referenzintegrität wird zum Foreign Key, die Lint-Prüfung zu acht SELECTs, der Routing-Schritt zu einer FTS-Query mit rekursivem CTE. Was bleibt, ist Bedeutungsarbeit — und nur die braucht ein Modell. Empfehlung: **SQLite als Einstieg, PostgreSQL als benannter Migrationspfad** (Grund: deutsche Volltextsuche), MariaDB nur bei Mehrbenutzerbetrieb. Die entscheidende Designregel: Das Modell schreibt niemals SQL.

---

## 1. Zwei Engpässe, die ständig verwechselt werden

Die Diskussion um Skalierungsgrenzen des LLM-Wiki-Musters vermischt zwei völlig verschiedene Probleme, weil beide sich als „Kontextfenster voll" äußern.

**Engpass A — Wiki-Größe.** Das Wiki wächst über das, was ein Modell in einem Rutsch lesen kann. Die dokumentierten Schwellen: unter 50K Token unauffällig, ab 50–100K wird `index.md` zum Navigationsflaschenhals, ab 200–300K beginnt Qualitätsdegradierung, jenseits davon ist das Wiki selbst ein neues Retrieval-Problem. Gravelles Formulierung — „Your wiki is not a document. It's a database. Stop loading it. Start querying it." — adressiert genau diesen Engpass.

**Engpass B — Session-Kapazität.** Das Modell schafft eine einzelne Operation nicht, unabhängig davon, wie groß das Wiki insgesamt ist. Ein Ingest braucht System-Prompt plus Schema-Datei plus Quelldokument plus gelesene Verweisseiten — dokumentiert sind 18–42k Token, bevor überhaupt eine Zeile geschrieben ist. Auf einem 14B-Modell mit nominell 40k Fenster beginnt die Latenz ab ~20–25k quadratisch zu steigen. Ein Wiki mit 30 Seiten überfordert das Modell dann genauso wie eines mit 3.000.

Der Unterschied ist entscheidend für die Frage, **wann** sich ein SQL-Umbau lohnt:

| | Engpass A (Größe) | Engpass B (Session) |
|---|---|---|
| Auslöser | Anzahl Seiten | Größe einer Operation |
| Tritt auf bei | Claude ab ~300 Seiten | Lokal ab Seite 1 |
| Lösung durch SQL | Ja — Abfrage statt Laden | Ja — kleine, dichte Kontexte |
| Alternative Lösung | Größeres Modell, mehr Kontext | Keine, außer besserem Modell |

Ein Wiki mit ~120 Seiten liegt nach den Schwellenwerten klar im Bereich „direkt laden". Wer nur Engpass A im Blick hat, kommt daher zu dem Schluss, der Umbau sei verfrüht. Das ist ein Fehlschluss: **Bei lokalem Betrieb ist Engpass B von Anfang an aktiv, und SQL ist die einzige Architektur, die ihn adressiert, ohne die Hardware zu wechseln.**

## 2. Was das Modell tatsächlich nicht kann

Bevor man eine Architektur um ein schwaches Modell herum baut, muss man präzise benennen, woran es scheitert. Die dokumentierten Fehlerbilder lokaler Modelle im agentischen Betrieb:

1. **Kontext zu klein für den System-Prompt.** Werkzeugdefinitionen werden abgeschnitten, das Modell sieht seine eigenen Werkzeuge nicht vollständig.
2. **Tool-Call-Format nicht getroffen.** Statt strukturierter `tool_calls` kommt Prosa oder JSON-als-Text. Ab Roo Code 3.54.0 ist der XML-Fallback entfernt, natives Function Calling also Pflicht.
3. **Fehler mit wachsendem Kontext.** Auch funktionierende Modelle produzieren mit steigender Iterationszahl falsche Dateipfade, fehlende Parameter, abgebrochene Diffs.
4. **Lange Ausgaben brechen ab.** `num_predict` schneidet Dateien mitten im Inhalt ab.

Punkte 3 und 4 sind für die Architekturfrage die wichtigsten, weil sie beide dieselbe Ursache haben: **Die Ausgabe ist zu lang und zu unstrukturiert.** Eine Markdown-Wiki-Seite mit 300 Zeilen komplett neu auszugeben, nur um einen Absatz zu ändern, ist genau die Operation, bei der ein 14B-Modell die Frontmatter verliert, einen Abschnitt vergisst oder mitten im Satz endet.

Daraus folgt die Leitfrage für den Entwurf: **Wie macht man jede einzelne Schreiboperation klein genug, dass ein schwaches Modell sie zuverlässig schafft?** Die Antwort ist nicht „bessere Prompts", sondern eine Datenstruktur, in der kleine Schreiboperationen überhaupt möglich sind.

## 3. Die VRAM-Rechnung, ehrlich gemacht

Die Ausgangsfrage lautete: 24B-Modell, 100–200k Kontext. Das ist auf 16 GB nicht darstellbar, und die Rechnung dazu ist einfach genug, um sie einmal vollständig hinzuschreiben.

### Die Formel

Der KV-Cache — der Speicher, in dem die Attention-Zwischenergebnisse aller bisherigen Token liegen — wächst **linear mit der Kontextlänge**:

```
Bytes pro Token = 2 (K und V) × n_layers × n_kv_heads × head_dim × Bytes pro Element
```

Für ein typisches 24B-Modell der Mistral-Small-Klasse (40 Layer, 8 KV-Heads bei GQA, head_dim 128):

```
2 × 40 × 8 × 128 × 2 Byte (FP16) = 163.840 Byte = 160 KiB pro Token
```

Bemerkenswert: `qwen3:14b` hat dieselbe Geometrie (40 Layer, 8 KV-Heads, head_dim 128) und damit **denselben KV-Bedarf pro Token**. Der KV-Cache skaliert mit der Layer-Zahl und der GQA-Konfiguration, nicht mit der Parameterzahl. Ein kleineres Modell spart bei den Gewichten, nicht beim Kontext.

### Die Tabelle

| Kontext | FP16 | Q8-KV | Q4-KV |
|---|---|---|---|
| 40k | 6,4 GB | 3,2 GB | 1,6 GB |
| 100k | 16,0 GB | 8,0 GB | 4,0 GB |
| 200k | 32,0 GB | 16,0 GB | 8,0 GB |

Dazu die Gewichte (Richtwerte GGUF):

| Modell | Q3_K_M | Q4_K_M | Q5_K_M | Q8_0 |
|---|---|---|---|---|
| 14B | ~7,0 GB | ~9,0 GB | ~10,5 GB | ~15,7 GB |
| 24B | ~11,6 GB | ~14,3 GB | ~16,8 GB | ~25,0 GB |

### Das Ergebnis für 16 GB

Nutzbar sind auf einer 16-GB-Karte realistisch **14,5–15,0 GB** (Display, Treiberreserve, Fragmentierung).

| Konfiguration | Gewichte | Rest für KV | Max. Kontext |
|---|---|---|---|
| 24B Q4_K_M + Q8-KV | 14,3 GB | ~0,7 GB | **~9k Token** |
| 24B Q4_K_M + Q4-KV | 14,3 GB | ~0,7 GB | ~18k Token |
| 24B Q3_K_M + Q4-KV | 11,6 GB | ~3,4 GB | ~85k Token (starker Qualitätsverlust) |
| 14B Q4_K_M + Q8-KV | 9,0 GB | ~6,0 GB | **~75k Token** |
| 14B Q4_K_M + Q4-KV | 9,0 GB | ~6,0 GB | ~150k Token (theoretisch) |

**24B mit 100–200k Kontext auf 16 GB ist ausgeschlossen.** Es geht sich nicht knapp nicht aus, sondern um den Faktor 2–3.

### Und selbst die machbaren Zahlen sind Papierzahlen

Die 75k des 14B-Modells sind rechnerisch korrekt und praktisch irrelevant. Transformer-Attention ist quadratisch: Jedes neue Token muss gegen alle gecachten berechnet werden. Die vorliegende Messreihe mit `qwen3:14b-40k` zeigt das Muster deutlich — Request 1 mit ~13,9k Prompt-Token braucht 7,5 s, Request 3 mit ~19,6k braucht 23,9 s. Ein Sprung um Faktor 3 bei 40 % mehr Kontext.

**Die praktische Nutzungsgrenze liegt bei ~20–25k Token, unabhängig davon, wie viel VRAM man dem KV-Cache widmet.** Wer 150k Kontext konfiguriert, konfiguriert eine Zahl, die er nie erreicht, weil er vorher aufgibt.

### Prüfpunkt: eine Unstimmigkeit im bestehenden Wiki

Die vorhandene Angabe „`qwen3:14b-40k` (Q8, ~9,3 GB Gewichte + ~6,7 GB KV-Cache = ~16 GB)" ist in sich widersprüchlich. Der KV-Wert von 6,7 GB passt exakt zu 40k × 160 KiB bei **FP16**, nicht bei Q8-KV. Und 9,3 GB Gewichte entsprechen bei 14B einer Q4/Q5-Quantisierung, nicht Q8 (das wären ~15,7 GB). Die Messung von 15,1 GiB Gesamtbelegung ist plausibel, das Label „Q8" ist es nicht. **Zu korrigieren beim Ingest.**

### Praktische Konsequenzen für die Konfiguration

- KV-Quantisierung erfordert Flash Attention. Bei Ollama: `OLLAMA_FLASH_ATTENTION=1` und `OLLAMA_KV_CACHE_TYPE=q8_0`.
- Q8-KV kostet praktisch keine Qualität und halbiert den Cache. Q4-KV ist spürbar, besonders bei langen Kontexten und bei Zahlen/Code.
- `num_predict` ≥ 8.000 bleibt nötig, auch wenn im SQL-Betrieb die Einzelausgaben klein sind — abgeschnittene Ausgaben sind der teuerste Fehler, weil sie stillschweigend passieren.

## 4. Die Umkehrung: SQL macht das Kontextfenster überflüssig

Hier liegt der eigentliche Kern der Analyse. Die übliche Denkrichtung lautet: *„Ich brauche ein Modell mit großem Kontext, um mit meinem Wiki zu arbeiten."* Das führt zu einer Hardware-Kaufentscheidung.

Die Umkehrung lautet: *„Wenn jede Operation nur die Daten sieht, die sie tatsächlich braucht, brauche ich das große Kontextfenster nicht."* Das führt zu einer Software-Entscheidung.

| Operation | Markdown-Vault | SQL-Betrieb |
|---|---|---|
| Frage beantworten | `index.md` (5–15k) + 3–8 Seiten (je 2–4k) = **20–45k** | 5 Sektionen à 400 Token + Seitenzusammenfassungen = **3–5k** |
| Absatz aktualisieren | Seite lesen (3k) + Seite komplett neu schreiben (3k) = **6k** | Sektion lesen (0,4k) + Sektion neu schreiben (0,4k) = **1k** |
| Betroffene Seiten finden | Modell liest Index und rät | FTS-Query + Graph-Traversal, **0 Token** |
| Lint | Vault lesen = **100k+** | Acht SELECTs, **0 Token** |

Ein Kontextfenster von 32k ist im SQL-Betrieb nicht knapp, sondern großzügig. Das 14B-Modell arbeitet dann im Bereich, in dem es schnell und zuverlässig ist — unterhalb der 20k-Latenzschwelle, mit dichtem, relevantem Kontext statt eines halb gelesenen Index.

Zwei Nebeneffekte, die oft übersehen werden:

**Lost in the middle entfällt.** Modelle verlieren Information in der Mitte langer Kontexte. Ein 40k-Kontext mit 90 % Irrelevantem ist nicht nur langsam, er ist auch qualitativ schlechter als ein 4k-Kontext mit 100 % Relevantem. Bei schwachen Modellen ist dieser Effekt ausgeprägter als bei starken.

**Die Latenz wird berechenbar.** Wenn jede Operation zwischen 2k und 8k Token liegt, ist die Antwortzeit konstant statt mit dem Gesprächsverlauf wachsend. Das ändert die Bedienbarkeit mehr als jede Tokenzahl.

## 5. Die Architekturgabel — und warum Schreiben entscheidet

Es gibt zwei ernsthafte Bauformen, und sie unterscheiden sich weniger im Lesen als im Schreiben.

**Variante A — SQL als Index über Markdown.** Der Vault bleibt Quelle der Wahrheit. Ein Indexer parst die Dateien und schreibt Seiten, Sektionen, Links und Tags in eine Datenbank, die nur für Suche und Lint dient. Das Modell liest über die Datenbank, schreibt aber weiterhin Markdown-Dateien.

**Variante B — SQL als Quelle der Wahrheit.** Die Datenbank hält den Inhalt. Ein Export-Skript erzeugt daraus jederzeit den vollständigen Markdown-Vault für Obsidian und Git. Das Modell liest *und* schreibt ausschließlich über die Datenbank.

Variante A ist verlockend, weil sie nichts kaputt macht. Sie löst aber nur die halbe Aufgabe:

| | Variante A | Variante B |
|---|---|---|
| Leseoperation | klein ✅ | klein ✅ |
| Schreiboperation | **ganze Datei neu ausgeben** ❌ | einzelne Sektion ✅ |
| Tote Links | Lint findet sie nachträglich | **strukturell unmöglich** (FK) |
| Index/Log-Pflege | Modellaufgabe, fehleranfällig | Nebeneffekt der Transaktion |
| Frontmatter-Konsistenz | Modell muss sie reproduzieren | Spalten mit CHECK-Constraints |
| Obsidian | direkt ✅ | über Export ⚠️ |
| Git-Diff | direkt ✅ | über Export ⚠️ |
| Invasivität | gering | hoch |

**Beim Schreiben liegt der Fehler, und beim Schreiben skaliert das Problem mit der Wiki-Größe.** Je reifer eine Seite, desto länger, desto größer die Ausgabe, desto wahrscheinlicher der Abbruch. Variante A wird mit wachsendem Wiki *schlechter*, obwohl sie das Leseproblem gelöst hat.

Die beiden Nachteile von Variante B — Obsidian und Git — lösen sich mit demselben Mittel: einem Export-Skript, das nach jeder Transaktion oder vor jedem Commit den kompletten Vault schreibt. Der Markdown-Vault verliert seinen Status als Original und wird zum **Rendering**. Für Obsidian, für die Graphansicht, für Lesbarkeit ohne Werkzeuge, für die Modellunabhängigkeit — alles bleibt erhalten, nur die Schreibrichtung dreht sich um.

Damit ist die Gabel entschieden: **Variante B, mit verpflichtendem Markdown-Export.** Ohne den Export wäre der Umbau ein Rückschritt, weil er die Eigenschaft aufgäbe, die das Muster überhaupt robust macht — dass alles am Ende lesbares Markdown ist.

## 6. Granularität: die Sektion als Atom

Die folgenreichste Schemaentscheidung ist nicht die Tabellenwahl, sondern die **Granularität der kleinsten schreibbaren Einheit**.

- **Seite als Atom:** Ein `UPDATE` ersetzt 2.000–4.000 Token Text. Das ist Variante A mit anderen Mitteln — der Fehler bleibt.
- **Absatz als Atom:** Zu fein. Bedeutungszusammenhänge zerfallen, die Zahl der Objekte explodiert, das Modell verliert den Überblick.
- **Sektion (H2-Abschnitt) als Atom:** 200–600 Token. Groß genug, dass ein Abschnitt für sich Sinn ergibt; klein genug, dass ein 14B-Modell ihn fehlerfrei neu schreibt.

Die Sektion ist zugleich die natürliche Einheit für **Retrieval**: Auf eine Frage antwortet selten eine ganze Seite, aber fast immer ein Abschnitt. Und sie ist die natürliche Einheit für **Frischheit**: Der Abschnitt „VRAM-Bedarf" veraltet, während „Die Grundidee" auf derselben Seite gültig bleibt. Frischheitsmetadaten auf Seitenebene sind zu grob, um nützlich zu sein.

## 7. Schemaentwurf

Portabel gehalten — kein Dialekt-Exotik, damit ein Wechsel von SQLite zu PostgreSQL später wenig kostet. Notiert in SQLite-Syntax.

### Ebene 1 — Quellen (unveränderlich)

```sql
CREATE TABLE sources (
  id          INTEGER PRIMARY KEY,
  slug        TEXT NOT NULL UNIQUE,
  path        TEXT NOT NULL UNIQUE,        -- raw/… oder clippings/…
  title       TEXT NOT NULL,
  kind        TEXT NOT NULL CHECK (kind IN ('raw','clipping')),
  sha256      TEXT NOT NULL,               -- erkennt stille Änderungen
  added_at    TEXT NOT NULL,
  ingested_at TEXT                          -- NULL = noch nicht aufgenommen
);
```

Die Unveränderlichkeitsregel der Rohquellen wird hier **strukturell** durchgesetzt: Es existiert schlicht kein Werkzeug, das in `sources` schreibt — der Import läuft über ein separates Skript, nicht über die Modellschicht. Der `sha256` macht nachträgliche Änderungen an einer Quelle sichtbar, was der Dateisystem-Variante bisher fehlt.

### Ebene 2 — Wiki

```sql
CREATE TABLE pages (
  id         INTEGER PRIMARY KEY,
  slug       TEXT NOT NULL UNIQUE,
  title      TEXT NOT NULL,
  type       TEXT NOT NULL CHECK (type IN
             ('konzept','quelle','person','werkzeug',
              'vergleich','vorlage','anleitung','projekt')),
  summary    TEXT NOT NULL,                 -- die eine Zeile für den Index
  status     TEXT NOT NULL DEFAULT 'active'
             CHECK (status IN ('active','draft','archived')),
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL
);

CREATE TABLE sections (
  id         INTEGER PRIMARY KEY,
  page_id    INTEGER NOT NULL REFERENCES pages(id) ON DELETE CASCADE,
  ord        INTEGER NOT NULL,
  heading    TEXT NOT NULL,
  body       TEXT NOT NULL,
  tokens     INTEGER,                       -- beim Schreiben gemessen
  updated_at TEXT NOT NULL,
  UNIQUE (page_id, ord)
);

CREATE TABLE tags (
  id   INTEGER PRIMARY KEY,
  name TEXT NOT NULL UNIQUE
);

CREATE TABLE page_tags (
  page_id INTEGER NOT NULL REFERENCES pages(id) ON DELETE CASCADE,
  tag_id  INTEGER NOT NULL REFERENCES tags(id)  ON DELETE CASCADE,
  PRIMARY KEY (page_id, tag_id)
);
```

Der `type`-CHECK ersetzt eine komplette Lint-Prüfung. Der Ordner ist im SQL-Betrieb keine eigene Information mehr — er wird beim Export aus `type` abgeleitet. Damit kann die Prüfung „`type` passt nicht zum Ordner" per Konstruktion nicht mehr fehlschlagen.

### Kanten — typisiert von Anfang an

```sql
CREATE TABLE links (
  from_page INTEGER NOT NULL REFERENCES pages(id) ON DELETE CASCADE,
  to_page   INTEGER NOT NULL REFERENCES pages(id) ON DELETE RESTRICT,
  rel       TEXT NOT NULL DEFAULT 'related' CHECK (rel IN
            ('related','depends-on','supersedes','implements',
             'owned-by','contradicts')),
  note      TEXT,
  PRIMARY KEY (from_page, to_page, rel)
);
```

Das ist die `relationships.json` aus dem v2-Muster — nur als das, was sie ohnehin ist: eine Kantentabelle. Der Unterschied zur JSON-Datei ist nicht kosmetisch. `ON DELETE RESTRICT` auf `to_page` bedeutet: Eine Seite, auf die verlinkt wird, kann nicht gelöscht werden. Tote Links entstehen nicht mehr — nicht „werden gefunden", sondern **entstehen nicht**.

Die Kante `contradicts` ist eine Ergänzung: Widersprüche zwischen Seiten werden damit erfasste Daten statt eines Absatzes im Fließtext, den beim nächsten Umschreiben niemand mehr findet.

### Behauptungen — Provenienz als Pflichtfeld

```sql
CREATE TABLE claims (
  id              INTEGER PRIMARY KEY,
  page_id         INTEGER NOT NULL REFERENCES pages(id) ON DELETE CASCADE,
  section_id      INTEGER REFERENCES sections(id) ON DELETE SET NULL,
  subject         TEXT NOT NULL,            -- normalisiert, z.B. 'kv-cache-bedarf-24b'
  statement       TEXT NOT NULL,
  source_id       INTEGER REFERENCES sources(id),
  confidence      TEXT NOT NULL DEFAULT 'sourced' CHECK (confidence IN
                  ('sourced','derived','unverified')),
  last_verified   TEXT,
  against_version TEXT
);
```

Diese Tabelle ist die direkte Antwort auf das Kontaminierungsrisiko — den schwerwiegendsten dokumentierten Nachteil des Wiki-Musters gegenüber RAG: Eine Halluzination beim Ingest wird als persistenter Fakt eingebacken und verbreitet sich über Querverweise.

Im Dateisystem ist die Zitierregel `(Quelle: dateiname.md)` eine **Konvention** — sie kann vergessen werden, und ein Lint-Lauf fand nachweislich 50 maschinell nicht auflösbare Quellenangaben. In der Datenbank ist `source_id` ein Fremdschlüssel: Er zeigt auf eine existierende Quelle oder ist `NULL`, und `NULL` ist per Definition `confidence = 'unverified'` — eine Zeile in einem Report, keine unsichtbare Lücke im Fließtext.

`last_verified` und `against_version` übernehmen die Frischheitsmetadaten aus dem v2-Muster, aber auf der richtigen Granularität: pro Behauptung, nicht pro Seite.

### Protokoll — mit Kostenspalten

```sql
CREATE TABLE log (
  id        INTEGER PRIMARY KEY,
  at        TEXT NOT NULL,
  op        TEXT NOT NULL CHECK (op IN ('ingest','query','lint','update')),
  subject   TEXT NOT NULL,
  detail    TEXT,
  model     TEXT,
  tokens_in INTEGER,
  tokens_out INTEGER,
  seconds   REAL,
  wh        REAL,        -- Energie, für lokalen Betrieb
  cost_usd  REAL         -- API-Kosten, für Cloud-Betrieb
);
```

Damit wird die Metrik US$ pro Wiki-Seite von einer Schätzung zu einer Abfrage:

```sql
SELECT SUM(cost_usd) / (SELECT COUNT(*) FROM pages) AS usd_pro_seite FROM log;
```

Für lokalen Betrieb ist die Dollarspalte uninteressant — dort zählt `wh` und `seconds`. **Die Metrik muss für lokale Modelle neu gefasst werden: Wattstunden pro Wiki-Seite und Minuten pro Wiki-Seite statt Dollar pro Wiki-Seite.** Bei 300 W GPU-Last und 4 Minuten pro Seite sind das 20 Wh, also bei 0,30 €/kWh rund 0,006 € Strom pro Seite — zwei Größenordnungen unter dem Sonnet-Wert von ~0,42 US$. Der Vergleich ist unfair, solange die Qualität nicht gleichzieht, aber er benennt, worum man eigentlich spielt.

### Volltext

```sql
CREATE VIRTUAL TABLE sections_fts USING fts5(
  heading, body,
  content='sections', content_rowid='id',
  tokenize='unicode61 remove_diacritics 2'
);
```

Plus die drei üblichen Trigger für `INSERT`/`UPDATE`/`DELETE`, die den Index synchron halten.

**Hier liegt die ernsteste Schwäche der SQLite-Variante:** FTS5 bringt keinen deutschen Stemmer mit. `unicode61` tokenisiert nur, `porter` stemmt englisch. Für ein durchgehend deutschsprachiges Wiki bedeutet das: „Kontextfenster" findet nicht „Kontextfenstern", und Komposita werden nicht zerlegt — „Kontextfenster" findet nicht „Fenster". Drei Behelfe:

1. **Trigram-Tokenizer** (`tokenize='trigram'`) — findet Teilzeichenketten, löst das Kompositaproblem brauchbar, kostet Indexgröße und Präzision.
2. **Zweiter Index**: ein normalisiertes Schlüsselwortfeld pro Sektion, vom Modell beim Schreiben mitgeliefert.
3. **Query-Erweiterung**: das Modell generiert 3–5 Suchvarianten statt einer.

PostgreSQL löst das mit `to_tsvector('german', …)` ab Werk — Snowball-Stemmer und deutsche Stoppwortliste sind eingebaut. Das ist der konkreteste technische Grund, PostgreSQL überhaupt in Betracht zu ziehen (siehe Abschnitt 10).

## 8. Der Lint wird zum Schema

Die acht Prüfungen des bestehenden Lint-Skripts im SQL-Betrieb:

| Prüfung | Im SQL-Betrieb |
|---|---|
| `DEAD LINKS` | **Entfällt** — Foreign Key |
| `TYPE / FOLDER` | **Entfällt** — CHECK-Constraint |
| `LOG ORDER` | **Entfällt** — `ORDER BY at` |
| `PAGES NOT IN INDEX` | **Entfällt** — Index wird generiert |
| `ORPHANED PAGES` | Eine Query |
| `UNINGESTED SOURCES` | Eine Query |
| `SOURCE CITATIONS` | Eine Query |
| `FORMAT CHECK` | Teils Constraint, teils Query |

Vier von acht Prüfungen verschwinden nicht, weil sie schneller werden, sondern weil die Fehlerklasse nicht mehr existiert. Die restlichen:

```sql
-- Verwaiste Seiten
SELECT p.slug FROM pages p
LEFT JOIN links l ON l.to_page = p.id
WHERE l.to_page IS NULL AND p.type <> 'index';

-- Nicht aufgenommene Quellen
SELECT slug, path FROM sources WHERE ingested_at IS NULL;

-- Behauptungen ohne auflösbare Quelle
SELECT p.slug, c.statement FROM claims c
JOIN pages p ON p.id = c.page_id
WHERE c.source_id IS NULL AND c.confidence <> 'derived';

-- Veraltete Behauptungen (älter als 180 Tage)
SELECT p.slug, c.subject, c.last_verified FROM claims c
JOIN pages p ON p.id = c.page_id
WHERE c.last_verified IS NULL
   OR julianday('now') - julianday(c.last_verified) > 180;

-- Widerspruchskandidaten: gleiches Subjekt, verschiedene Quellen
SELECT c1.subject, c1.statement, c2.statement
FROM claims c1 JOIN claims c2
  ON c1.subject = c2.subject AND c1.id < c2.id
 AND c1.source_id IS NOT c2.source_id;
```

Die letzte Query ist die interessanteste. Widerspruchserkennung galt bisher als reine Bedeutungsfrage, die dem Modell vorbehalten bleibt. Sie bleibt es auch — aber die Datenbank liefert die **Kandidatenliste**. Statt dass ein Modell den gesamten Vault nach Widersprüchen durchsucht (auf 16 GB VRAM undurchführbar), bekommt es zehn Paare vorgelegt und entscheidet je Paar in 300 Token, ob ein echter Widerspruch vorliegt. Eine unmögliche Aufgabe wird zu zehn trivialen.

Dasselbe Prinzip lässt sich auf „fehlende Konzeptseiten" anwenden: Subjekte in `claims`, zu denen keine Seite existiert, sind Kandidaten für neue Seiten — auffindbar per Query, bewertbar per Modell.

## 9. Der Routing-Schritt wird deterministisch

Der dokumentierte Routing-Schritt zur Kostenkontrolle — erst eine kompakte Schema-Zusammenfassung lesen, dann entscheiden, welche Seiten relevant sind — ist im Dateibetrieb selbst eine Modellaufgabe und damit fehleranfällig. In SQL wird er eine Query:

```sql
WITH RECURSIVE
seeds AS (
  SELECT s.page_id AS id
  FROM sections_fts f
  JOIN sections s ON s.id = f.rowid
  WHERE sections_fts MATCH :q
  ORDER BY bm25(f)
  LIMIT 8
),
nb(id, dist) AS (
  SELECT id, 0 FROM seeds
  UNION
  SELECT l.to_page, nb.dist + 1
  FROM links l JOIN nb ON l.from_page = nb.id
  WHERE nb.dist < 2
)
SELECT DISTINCT p.slug, p.title, p.summary
FROM nb JOIN pages p ON p.id = nb.id;
```

BM25-Volltreffer plus zwei Schritte Graph-Traversal über typisierte Kanten. Ergebnis: eine Kandidatenliste mit je einer Zeile Zusammenfassung — 30 Seiten ergeben ~600 Token. Das Modell bekommt eine fertige Liste und muss nur noch auswählen, nicht suchen.

Das ist gleichzeitig die Antwort auf die dokumentierten Skalierungslösungen: Was jDocMunch (sektionsbasierter Zugriff) und qmd (BM25/Vektor/Hybrid) als externe Werkzeuge liefern, fällt hier als Eigenschaft des Schemas an. Ein Vektorindex lässt sich später ergänzen — `sqlite-vec` für SQLite, `pgvector` für PostgreSQL, natives `VECTOR` ab MariaDB 11.7 — aber er ist nicht der erste Schritt. BM25 über sauber geschnittene Sektionen deckt einen großen Teil ab, und Embeddings lokal zu erzeugen kostet erneut GPU-Zeit.

## 10. Datenbankwahl

| | SQLite | MariaDB | PostgreSQL | DuckDB |
|---|---|---|---|---|
| Volltext | FTS5/BM25, **kein dt. Stemmer** | InnoDB FULLTEXT, schwächstes Ranking | **`german`-Konfiguration ab Werk** | schwach |
| Vektor (später) | `sqlite-vec` | nativ ab 11.7 | `pgvector` | VSS-Extension |
| Rekursive CTEs | ✅ | ✅ (ab 10.2) | ✅ | ✅ |
| Viele kleine UPDATEs | schnell | schnell | schnell | **langsam (OLAP)** |
| Betrieb | eine Datei | Serverdienst | Serverdienst | eine Datei |
| Backup | `cp wiki.db` | Dump | Dump | `cp` |
| Nebenläufigkeit | ein Schreiber (WAL) | voll | voll | ein Schreiber |

**DuckDB scheidet aus.** Es ist eine analytische Engine — hervorragend für Aggregationen über Millionen Zeilen, schlechter als SQLite bei vielen kleinen Einzelupdates. Genau das ist aber das Lastprofil eines Wikis. Wer „performanter als SQLite" sucht, landet hier auf dem falschen Werkzeug.

**MariaDB ist die schwächste der drei relationalen Optionen für diesen Fall.** Serverbetrieb ohne kompensierenden Vorteil, und die Volltextsuche ist die schlechteste im Feld. Vertrautheit ist ein echtes Argument — aber SQLite hat so wenig Bedienoberfläche, dass es kaum etwas zu kennen gibt. MariaDB wird relevant, sobald mehrere Personen gleichzeitig schreiben, also beim Team-Wiki.

**SQLite ist der richtige Einstieg.** Ein einbenutziges, lokales Wiki ist der Fall, für den SQLite gebaut wurde: kein Dienst, kein Port, kein Rechtekonzept, Backup ist eine Dateikopie, FTS5 mit BM25 antwortet bei einigen tausend Sektionen in unter einer Millisekunde. Die Einbenutzer-Schreibbeschränkung ist keine, wenn genau ein Agent schreibt.

**PostgreSQL ist der benannte Migrationspfad, und der Auslöser ist die deutsche Volltextsuche.** Solange BM25 über `unicode61` plus Trigram-Fallback ausreicht, bleibt SQLite. Sobald Suchtreffer erkennbar an Flexion und Komposita scheitern — und das ist bei einem rein deutschen Wiki eine Frage der Zeit, nicht des Ob —, ist der Wechsel begründet. Vorbereitung: kein SQLite-Exotisches im Schema, `INTEGER PRIMARY KEY` statt `ROWID`-Tricks, Datumsangaben als ISO-8601-Text, keine dynamische Typisierung ausnutzen. Dann ist die Migration ein Dump plus Anpassung der FTS-Schicht.

## 11. Die eine Regel: das Modell schreibt niemals SQL

Alles bisher Beschriebene fällt in sich zusammen, wenn das Modell freien SQL-Zugriff bekommt. Ein Modell, das an Dateipfaden scheitert, schreibt kein korrektes SQL — und im Fehlerfall ist der Schaden nicht eine kaputte Datei, sondern ein `UPDATE` ohne `WHERE`.

Stattdessen eine dünne Vermittlungsschicht mit **sechs engen Werkzeugen**, flach und typisiert:

| Werkzeug | Parameter | Rückgabe |
|---|---|---|
| `search` | `query`, `limit` | Sektions-IDs + Seitenzusammenfassungen |
| `get_section` | `section_id` | Überschrift + Text |
| `get_page_outline` | `page_slug` | Sektionsüberschriften + Nachbarseiten |
| `upsert_section` | `page_slug`, `heading`, `body`, `source_slug` | neue Sektions-ID |
| `add_link` | `from_slug`, `to_slug`, `rel` | ok |
| `add_claim` | `page_slug`, `subject`, `statement`, `source_slug` | Claim-ID |

Die Schicht verantwortet alles Mechanische: Transaktion, `updated_at`, Tokenzählung, Log-Eintrag, Slug-Normalisierung, Validierung gegen die CHECK-Werte, Ablehnung mit verständlicher Fehlermeldung. Das Modell trifft ausschließlich Bedeutungsentscheidungen.

Vier Konsequenzen:

1. **Der System-Prompt wird klein.** Sechs flache Werkzeuge statt eines Dateisystem-Agenten mit Lese-, Schreib-, Diff- und Suchwerkzeugen. Der dokumentierte System-Prompt-Umfang von 33–51 KB ist selbst ein Hauptgrund, warum lokale Modelle scheitern — er lässt sich auf einen Bruchteil drücken.
2. **Ungültige Zustände sind nicht erreichbar.** Das Modell *kann* keinen toten Link erzeugen, weil `add_link` gegen existierende Slugs prüft und der FK das ohnehin erzwingt.
3. **Fehler sind lokal und rückgängig.** Eine misslungene Sektion ist eine Zeile in einer Transaktion, kein halb überschriebenes Dokument.
4. **Der Freiheitsgrad, an dem schwache Modelle scheitern, wird ihnen genommen.** Das ist kein Kompromiss, sondern der Zweck der Übung.

Voraussetzung bleibt natives Function Calling über den OpenAI-kompatiblen `/v1`-Endpoint — mit dem nativen Ollama-Endpoint kommt kein `tool_calls`-Format zurück. Reasoning-Modelle mit `<think>`-Blöcken bleiben ungeeignet.

## 12. Ingest bleibt hart — aber zerlegbar

Der Ingest ist die Operation, bei der die Datenbank am wenigsten hilft, weil er echte Bedeutungsarbeit über ein ganzes Quelldokument ist: lesen, verstehen, zerlegen, mit Bestehendem abgleichen, Widersprüche erkennen, vernetzen. Das ist genau das, was ein 14B-Modell nicht in einem Zug kann.

Es lässt sich aber in ein Fließband zerlegen, dessen Zustand in der Datenbank liegt statt im Kontextfenster:

| Schritt | Ausführung | Kontextbedarf |
|---|---|---|
| 1. `split` | Code — Quelle in ~1.500-Token-Abschnitte | 0 |
| 2. `extract` | Modell — pro Abschnitt: Behauptungen als JSON, mit `subject` | ~2,5k je Abschnitt |
| 3. `route` | Code — `subject` → FTS + Graph → Kandidatenseiten | 0 |
| 4. `merge` | Modell — pro betroffener Sektion: alter Text + neue Behauptungen → neuer Text | ~2k je Sektion |
| 5. `link` | Modell — Kantenvorschläge aus der Kandidatenliste | ~1k |
| 6. `verify` | Code — Lint-Queries; Fehler → zurück zu Schritt 4 | 0 |

Kein Schritt übersteigt 3k Token. Ein Clipping von 8.000 Wörtern wird zu etwa 20 kleinen Modellaufrufen statt einem unmöglichen großen. Bei ~10 s pro Aufruf sind das drei bis vier Minuten — langsam gegenüber einer Cloud-API, aber durchführbar, und das ist der relevante Unterschied.

Die Struktur ist die der Ralph-Schleife: zustandslose Iterationen, frischer Kontext bei jedem Durchgang, Persistenz über einen externen Speicher statt über das Kontextfenster. Nur liegt der externe Speicher hier in einer Datenbank mit Constraints statt in einem Dateisystem ohne. Schritt 6 ist die Abbruchbedingung — die Schleife läuft, bis alle Lint-Queries leer zurückkommen.

**Was das Fließband nicht liefert:** Die Tiefe-Auswahl (minimal/mittel/vollständig) und die Beurteilung, was an einer Quelle *wichtig* ist, bleiben Qualitätsfragen, bei denen ein 14B-Modell messbar schlechter urteilt. Es wird mehr Triviales extrahieren und weniger Verbindungen sehen. Realistisch: **`query` und `update` lokal, `ingest` neuer Quellen weiterhin bevorzugt mit einem starken Modell** — aber gegen dasselbe Schema und dieselben Werkzeuge, sodass die Wahl des Modells eine Kostenentscheidung pro Operation wird statt einer Architekturentscheidung.

## 13. Markdown-Export: was gerettet werden muss

Der Export ist kein Nebenprodukt, sondern Teil der Architektur. Er stellt drei Eigenschaften wieder her, die sonst verloren gingen:

1. **Obsidian** — Graphansicht, Backlinks, Dataview, Lesen ohne Werkzeugkette.
2. **Git** — lesbare Diffs. Eine SQLite-Datei ist ein Binärblob; ein Commit über 40 geänderte Markdown-Dateien ist nachvollziehbar. Praktikabel: `wiki.db` in `.gitignore`, der exportierte Vault ist das, was versioniert wird. Zusätzlich ein `.sql`-Dump als Textdatei, falls die Datenbank selbst rekonstruierbar bleiben soll.
3. **Modellunabhängigkeit** — die Eigenschaft, dass das Wiki jeden Agenten überlebt. Sie hängt daran, dass am Ende lesbares Markdown steht, nicht an der Speicherform dazwischen.

Der Export ist deterministisch: `type` bestimmt den Ordner, `slug` den Dateinamen, Frontmatter wird aus den Spalten erzeugt, Sektionen nach `ord` konkateniert, „Verwandte Seiten" aus `links`, der Index aus `pages.summary`, das Log aus `log`. Es gibt keinen Entscheidungsspielraum und damit nichts, was das Modell falsch machen könnte — der Import zurück ist bewusst *nicht* vorgesehen (siehe offene Fragen).

## 14. Migrationspfad

Ein Umbau in einem Zug ist unnötig riskant. Vier Stufen, jede für sich nützlich:

**Stufe 1 — Spiegel, schreibgeschützt.** Ein Importer liest den bestehenden Vault und füllt die Datenbank. Nichts ändert sich am Arbeitsablauf. Gewinn: Der Lint läuft als SQL-Queries, `search` wird als Werkzeug verfügbar. Risiko: null, die Datenbank ist wegwerfbar.

**Stufe 2 — Lesebetrieb.** Das lokale Modell beantwortet Fragen ausschließlich über `search` und `get_section`. Geschrieben wird weiterhin Markdown von Hand oder per starkem Modell. Hier zeigt sich, ob die Sektionsgranularität stimmt und ob die deutsche Volltextsuche trägt.

**Stufe 3 — Schreibbetrieb, Umkehrung der Richtung.** Die Datenbank wird Quelle der Wahrheit, der Export erzeugt den Vault. `upsert_section` geht scharf. Ab hier ist der Vault ein Rendering. Der Rückweg bleibt offen, solange der Export vollständig ist.

**Stufe 4 — Ingest-Fließband.** Der zerlegte Ingest aus Abschnitt 12, zunächst halbautomatisch mit Bestätigung je Schritt.

Zwischen Stufe 2 und 3 liegt die einzige wirklich schwer umkehrbare Entscheidung. Sie sollte erst fallen, wenn Stufe 2 im Alltag getragen hat.

## 15. Was das Muster nicht löst

- **Qualität der Synthese.** Ein 14B-Modell schreibt schlechtere Zusammenfassungen als Sonnet, in jeder Architektur. SQL macht es arbeitsfähig, nicht klüger. Der Hauptgewinn liegt bei mechanischer Zuverlässigkeit, nicht bei inhaltlicher Tiefe.
- **Deutsche Volltextsuche in SQLite.** Siehe Abschnitt 7 — ein offener Punkt mit drei Behelfen und einem benannten Ausweg.
- **Bidirektionales Editieren.** Wer im Export-Vault eine Datei in Obsidian ändert, verliert die Änderung beim nächsten Export. Ein Rückimport ist technisch möglich (Datei parsen, Sektionen vergleichen, Konflikte melden), aber Konfliktauflösung ist der aufwendigste Teil jedes Sync-Systems. Vorerst: Der Export-Vault ist schreibgeschützt zu behandeln.
- **Mehrere Geräte.** SQLite über Cloud-Ordner zu synchronisieren, ist ein bekannter Weg zu beschädigten Datenbanken. Bei Bedarf: Der exportierte Vault synchronisiert, die Datenbank bleibt auf einem Rechner — oder es wird doch PostgreSQL/MariaDB mit Netzwerkzugriff.
- **Sektionsgranularität und Bedeutungszusammenhang.** Ob sich ein Wiki gut in 200–600-Token-Blöcke schneiden lässt, ohne dass Argumentationsbögen zerfallen, ist eine empirische Frage. Stufe 2 des Migrationspfads beantwortet sie, bevor sie teuer wird.
- **Die Metrik für lokalen Betrieb.** Wattstunden und Minuten pro Wiki-Seite sind vorgeschlagen, aber nicht gemessen. Ohne Messung bleibt der Vergleich lokal-gegen-Cloud eine Behauptung.

## 16. Fazit

Der Umbau auf SQL wird üblicherweise mit Wachstum begründet und deshalb aufgeschoben, bis das Wiki groß genug ist. Für den lokalen Betrieb ist das die falsche Reihenfolge: Dort ist der Engpass nicht die Zahl der Seiten, sondern die Größe der einzelnen Operation — und der besteht ab der ersten Seite.

Die Rechnung dazu ist eindeutig: Ein 24B-Modell mit 100–200k Kontext braucht 22–31 GB VRAM und ist auf 16 GB nicht darstellbar. Die richtige Reaktion darauf ist nicht, eine größere Karte zu kaufen, sondern die Anforderung verschwinden zu lassen. Wenn ein Modellaufruf 3k statt 40k Token sieht, ist ein 14B-Modell mit 32k Fenster nicht knapp, sondern reichlich — und es antwortet in Sekunden statt in halben Minuten.

Was dabei entsteht, ist mehr als eine Optimierung. Vier von acht Lint-Prüfungen verschwinden ersatzlos, weil ihre Fehlerklasse strukturell unmöglich wird. Provenienz wird von einer Konvention zu einem Fremdschlüssel. Widerspruchserkennung wird von einer unmöglichen Aufgabe zu zehn trivialen. Der Routing-Schritt wird von einer Modellentscheidung zu einer Query. Das Muster verliert dabei nichts von dem, was es ausmacht — solange der Markdown-Export nicht als Nebensache behandelt wird, sondern als das, was das Wiki jeden Agenten und jede Datenbank überleben lässt.

Der ehrliche Rest: Der Ingest neuer Quellen bleibt die Operation, bei der ein starkes Modell deutlich besser ist. Die Datenbank verschiebt diese Grenze spürbar nach unten — sie hebt sie nicht auf.
