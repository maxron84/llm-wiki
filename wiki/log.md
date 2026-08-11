---
date: 2026-04-23
type: log
tags: [log]
status: active
---

# Änderungsprotokoll

**Zusammenfassung**: Chronologisches Protokoll aller Vorgänge im Wiki.
**Zuletzt aktualisiert**: 2026-08-11

---

> **Reihenfolge**: Neueste Einträge stehen **oben**. Die letzten Vorgänge liest man mit
> `grep "^## \[" wiki/log.md | head -5`.

## [2026-08-11 16:20] ingest | Das Wiki als Datenbank — SQL-Betrieb mit schwachen lokalen Modellen

Quelle: `raw/sqlwiki_lokalesmodell_architektur.md` (interne Architekturanalyse, 505 Zeilen). Tiefe: **vollständig**. Anders als die übrigen Quellen ist das kein externes Fundstück, sondern eine Analyse, die auf dem Wiki selbst aufsetzt — und an einer Stelle ausdrücklich eine Korrektur einfordert.

**Die These**: Die naheliegende Begründung für einen SQL-Umbau ist Wachstum. Sie ist richtig, greift aber zu spät. Der stärkere Grund ist die Umkehrung — **SQL macht das große Kontextfenster überflüssig und damit das schwache lokale Modell erst arbeitsfähig**. Damit wird aus einer Hardware-Kaufentscheidung eine Software-Entscheidung.

**Neu (14 Seiten):**

- [sql-wiki-architektur](konzepte/sql-wiki-architektur.md) — Das Kernkonzept. Die Umkehrung, die Architekturgabel (SQL als Index über Markdown vs. SQL als Quelle der Wahrheit) und die Entscheidung für Variante B mit verpflichtendem Export. Der Grund liegt beim Schreiben, nicht beim Lesen: Variante A wird mit wachsendem Wiki *schlechter*, weil das Modell weiter ganze Dateien neu ausgeben muss.
- [engpass-groesse-vs-session](konzepte/engpass-groesse-vs-session.md) — Die Unterscheidung, die alles trägt. Engpass A (Wiki-Größe) tritt bei Claude ab ~300 Seiten auf und lässt sich mit einem größeren Modell erschlagen. Engpass B (Session-Kapazität) tritt lokal ab Seite 1 auf und lässt sich *nicht* mit mehr VRAM erschlagen. Beide melden sich als „Kontextfenster voll", deshalb die ständige Verwechslung.
- [kv-cache-rechnung](konzepte/kv-cache-rechnung.md) — Die Rechnung vollständig hingeschrieben. 160 KiB pro Token bei FP16 für ein 24B-Modell der Mistral-Small-Klasse; 24B mit 100–200k Kontext auf 16 GB scheitert um Faktor 2–3. Der bemerkenswerteste Befund: `qwen3:14b` hat dieselbe Geometrie und damit denselben KV-Bedarf pro Token — ein kleineres Modell spart bei den Gewichten, nicht beim Kontext.
- [sektion-als-atom](konzepte/sektion-als-atom.md) — Die folgenreichste Schemaentscheidung. Nicht die Tabellenwahl, sondern die Granularität: H2-Abschnitt mit 200–600 Token, zugleich natürliche Einheit für Retrieval und für Frischheit.
- [wiki-datenbankschema](konzepte/wiki-datenbankschema.md) — Der Tabellenentwurf: `sources` (mit `sha256` gegen stille Änderungen), `pages`/`sections`/`tags`, `links` mit typisierten Kanten, `claims` mit Provenienz als Pflichtfeld, `log` mit Kostenspalten, FTS5.
- [werkzeugschicht](konzepte/werkzeugschicht.md) — Die eine Regel: das Modell schreibt niemals SQL. Sechs enge, flache Werkzeuge; alles Mechanische verantwortet die Vermittlungsschicht.
- [ingest-fliessband](konzepte/ingest-fliessband.md) — Der Ingest in sechs Schritten, drei davon ohne Modell. Ein Clipping von 8.000 Wörtern wird zu ~20 kleinen Aufrufen statt einem unmöglichen großen.
- [markdown-als-rendering](konzepte/markdown-als-rendering.md) — Der Export als Architekturbestandteil. Der Vault verliert seinen Status als Original; der Rückweg ist bewusst nicht vorgesehen.
- [deutsche-volltextsuche](konzepte/deutsche-volltextsuche.md) — Die ernsteste Schwäche der SQLite-Variante: FTS5 hat keinen deutschen Stemmer, „Kontextfenster" findet weder „Kontextfenstern" noch „Fenster".
- [datenbankwahl-wiki](konzepte/datenbankwahl-wiki.md) — SQLite, MariaDB, PostgreSQL, DuckDB im Vergleich. DuckDB scheidet aus (OLAP-Engine bei einem OLTP-Lastprofil), MariaDB erst beim Team-Wiki.
- [wh-pro-wiki-seite](konzepte/wh-pro-wiki-seite.md) — Die Metrik für lokalen Betrieb. ~0,006 € Strom pro Seite gegen ~0,42 US$ bei Sonnet — zwei Größenordnungen, aber ungemessen, und die Quelle nennt den Vergleich selbst unfair, solange die Qualität nicht gleichzieht.
- [sqlite](werkzeuge/sqlite.md), [postgresql](werkzeuge/postgresql.md) — Die beiden Datenbanken als Werkzeugseiten.
- [sqlwiki-lokalesmodell-architektur](quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quellenzusammenfassung.

**Korrigiert — ein Widerspruch, den die Quelle ausdrücklich einforderte:**

Die Angabe „`qwen3:14b-40k` (Q8, ~9,3 GB Gewichte + ~6,7 GB KV-Cache = ~16 GB)" stand an **drei** Stellen und ist in sich falsch. 6,7 GB KV entsprechen exakt 40k × 160 KiB bei **FP16**, nicht bei Q8-KV (das wären ~3,3 GB); und 9,3 GB Gewichte entsprechen bei 14B einer **Q4_K_M**-Quantisierung — Q8_0 wären ~15,7 GB, damit wäre die Karte allein durch die Gewichte voll. Die gemessene Gesamtbelegung von 15,1 GiB ist plausibel und bleibt stehen; falsch war nur das Label. Korrigiert in [hardware-vergleich-sonnet-vs-lokal](konzepte/hardware-vergleich-sonnet-vs-lokal.md), [quantisierung](konzepte/quantisierung.md) und [lokale-modelle-fortgeschritten](anleitungen/lokale-modelle-fortgeschritten.md), jeweils mit dem Rechenweg statt nur der neuen Zahl.

Nebenbefund: Die Qualitätsvergleichstabelle in `hardware-vergleich` führt eine Spalte `14B@Q8`, während Tier 1 (RTX 5080) tatsächlich Q4_K_M fährt. Die Bewertungen dort sind also eher optimistisch — vermerkt statt stillschweigend nachgezogen.

**Aktualisiert:**

- [skalierungsgrenzen](konzepte/skalierungsgrenzen.md) — Der Abschnitt „Session-Limits bei lokalen Modellen" beschrieb Engpass B bereits, ohne ihn von A zu trennen. Jetzt benannt, plus die Einschränkung an der Empfehlungstabelle: „< 50–100 Seiten → direkt laden" gilt nur für Cloud-Betrieb.
- [lint-pruefung](konzepte/lint-pruefung.md) — Neuer Abschnitt: Vier von acht Prüfungen entfallen im SQL-Betrieb **ersatzlos**, weil ihre Fehlerklasse nicht mehr existiert (FK, CHECK, `ORDER BY`, generierter Index). Interessanter noch: Die drei Bedeutungsfragen, die das Skript bisher gar nicht kann, werden zerlegbar — die Datenbank liefert die Kandidatenliste, das Modell entscheidet je Paar in 300 Token.
- [kontaminierungsrisiko](konzepte/kontaminierungsrisiko.md) — Neunte Minderungsstrategie: Provenienz als Fremdschlüssel statt als Konvention. Die acht bisherigen sind alle Konventionen, und wie wenig das trägt, zeigte der eigene Lint-Lauf mit 50 maschinell nicht auflösbaren Quellenangaben. Ausdrücklich mitvermerkt, was das *nicht* löst: Ein `source_id` erzwingt, dass eine Quelle existiert, nicht dass sie die Behauptung stützt.
- [usd-pro-wiki-seite](konzepte/usd-pro-wiki-seite.md) — Vierter Punkt unter „Wo das Modell zerfällt": Bei lokalem Betrieb ist der Zähler null, die Metrik meldet „kostenlos". Dazu der Weg von der Schätzung zur Abfrage über die Log-Spalten.
- [hardware-vergleich-sonnet-vs-lokal](konzepte/hardware-vergleich-sonnet-vs-lokal.md) — Neben der Korrektur ein neuer Abschnitt „Die dritte Option": Alle vier Hardware-Tiers beantworten dieselbe Kauffrage; die Architekturlösung beantwortet sie nicht, sondern lässt sie verschwinden.
- [llm-wiki-v2](konzepte/llm-wiki-v2.md) — Drei der vier v2-Ergänzungen sind Datenbankkonzepte, als Dateien nachgebaut. `relationships.json` ist eine Kantentabelle; der Unterschied ist `ON DELETE RESTRICT`.
- [ralph-schleife](konzepte/ralph-schleife.md) — Das Fließband ist strukturell Ralph, nur mit Constraints. Zwei Gewinne, beide an der Achillesferse des Musters: Die Abbruchbedingung wird maschinell prüfbar, und jede Iteration wird klein genug für ein schwaches Modell.
- [modellunabhaengigkeit](konzepte/modellunabhaengigkeit.md) — Der naheliegende Einwand („Datenbank = Unabhängigkeit weg") greift nur halb. Die Eigenschaft hängt am deterministischen Export, nicht an der Speicherform. Der Preis ist ehrlich benannt: Der Export-Vault wird schreibgeschützt.
- [fortgeschrittene-architektur](konzepte/fortgeschrittene-architektur.md) — Der Routing-Schritt war dort als Kostenhebel beschrieben, ist im Dateibetrieb aber selbst eine Modellaufgabe. In SQL wird er eine Query.
- [ollama-kontextfenster](konzepte/ollama-kontextfenster.md) — Neuer Abschnitt zur KV-Quantisierung (`OLLAMA_FLASH_ATTENTION=1`, `OLLAMA_KV_CACHE_TYPE=q8_0`): halber Cache, praktisch kein Qualitätsverlust. Dazu der Hinweis, dass die Latenzgrenze davon unberührt bleibt.
- [quantisierung](konzepte/quantisierung.md), [lokale-modelle-fortgeschritten](anleitungen/lokale-modelle-fortgeschritten.md), [tool-use-lokale-modelle](konzepte/tool-use-lokale-modelle.md), [jdocmunch](werkzeuge/jdocmunch.md), [qmd](werkzeuge/qmd.md) — Korrektur bzw. Querverweise nachgezogen.
- [index](index.md) — 14 neue Einträge.

**Offen geblieben** (die Quelle benennt es selbst): Die Sektionsgranularität ist eine empirische Frage, die erst Stufe 2 des Migrationspfads beantwortet. Und Wh/WP sind vorgeschlagen, nicht gemessen — die 300 W und 4 Minuten sind Annahmen. Ohne Messung bleibt der Vergleich lokal-gegen-Cloud eine Behauptung.

## [2026-08-02 13:15] update | Starterkit 2.4.1 — die Ledger-Prüfung traf zum ersten Mal ein fremdes Ledger

Nachschlag zum Eintrag von 11:30. Das Kit steht jetzt auf **2.4.1**. Neu ist ein Vorgang, der die gestern angelegten Konzeptseiten von einer unerwarteten Seite bestätigt: Die frisch gebaute Ledger-Prüfung lief erstmals gegen ein **fremdes, gewachsenes** Ledger — 67 Zeilen aus 22 Kaskaden im Ursprungsprojekt — und meldete **drei Warnungen, von denen keine echt und keine je auflösbar war**. Und das mit 176 grünen Tests im Rücken.

Zurückgespielt wurde das Verallgemeinerbare, nicht die Fehlerliste:

**Aktualisiert:**

- [gegenprobe-zweite-quelle](konzepte/gegenprobe-zweite-quelle.md) — zwei neue Abschnitte. Erstens: **Die Gegenprobe braucht selbst eine Gegenprobe.** Testdaten aus dem eigenen Projekt sind dieselbe Quelle wie das eigene Projekt — sie bestätigen nur, was der Autor ohnehin annahm. Zwei Annahmen fielen: `BL-13` verdrahtete eine Zuordnung (Log-Ordner ↔ genau eine Rolle), die das Werkzeug **selbst zu brechen erlaubt** — der Befehl `akteur-abschluss --rolle <X>` existiert ausdrücklich dafür. Der Fix verlängert die Liste nicht, sondern schafft sie ab und leitet die Rollenmenge aus dem Ledger ab. Zweitens die Regel, dass eine **geschätzte** Zahl nicht in den Vergleichstopf darf: Die Architekten-Zeile ist eine aus dem Transkript gemessene Schätzung ohne Rohlog und hätte mit 275 USD jede echte Untergebuchung maskiert — die Prüfung wäre dauerhaft grün und damit wertlos gewesen. Dazu: Der Befund nennt jetzt die gezählten Rollen, weil `BL-1`/`BL-4`/`BL-5` kein Werkzeug fand, sondern ein nachrechnender Mensch. Die Automatik ist sein Zubringer, nicht sein Ersatz.
- [alarmmuedigkeit](konzepte/alarmmuedigkeit.md) — der bemerkenswerteste Teil: **Das Werkzeug, das aus Rücksicht auf Alarmmüdigkeit bewusst kein hartes Gate wurde, war selbst bei jedem Aufruf rot.** Ursache bei `BL-14`: eine Regel, die im Normalfall stimmt, deren Ausnahme aber ständig eintritt (benannte Kaskaden sind Out-of-Loop-Fixserien, dort fehlt die Bau-Zeile zu Recht). Behoben auf Weg 2 der Rangfolge — herabstufen von Warnung auf erklärten Hinweis. Neu präzisiert ist die Trennlinie zum verbotenen vierten Weg: Eine Herabstufung ist nur dann keine Vertuschung, wenn ein Test die **Gegenrichtung** festnagelt. Hier: Bei nummerierter Kaskade bleibt es eine Warnung, `BL-4` wird nicht mit entschärft.
- [rueckkanal-feld-kit](konzepte/rueckkanal-feld-kit.md) — **die dritte Richtung.** Der Kanal war als Feld → Werkzeug geregelt; übersehen wurde das Projekt, aus dem das Werkzeug ursprünglich herausgelöst wurde. Der 12-USD-Fehler `BL-11` lag dort **bis zuletzt**, drei Fixe fehlten insgesamt. Erschwerend: Der Ahnherr trägt noch das Vor-Kit-Layout und nimmt `install.sh --update` gar nicht an. Zwei Regeln daraus — wer ein Werkzeug herauslöst, erbt **drei** Kanäle statt einem; und wenn der automatische Weg für ein Projekt nicht gilt, muss das ein benannter Entscheid sein, mit beziffertem Aufwand (hier: 531 Pfadverweise in 61 Dateien, bewusst nicht migriert) statt „wäre viel Arbeit".
- [team-starter-kit](werkzeuge/team-starter-kit.md) — auf 2.4.1, 182 Testfälle, neuer Abschnitt „Der erste Lauf gegen ein fremdes Ledger" mit beiden Fehlern und dem Nebenbefund zum Ursprungsprojekt.
- [index](index.md) — Version und Testzahl nachgezogen.

Keine neue Seite: Der Fund ist kein neues Muster, sondern dasselbe eine Ebene höher. Genau deshalb steht er in [gegenprobe-zweite-quelle](konzepte/gegenprobe-zweite-quelle.md) statt daneben.

Kein Kosteneintrag: Wiki-Pflege, der Kit-Bau ist im Feld-Ledger unter `team` verbucht.

---

## [2026-08-02 11:30] update | Starterkit 2.3.0–2.4.0 — Erntelauf des ersten Feldprojekts zurückgespielt

Das Kit ist seit dem letzten Wiki-Stand (2.2.0) auf **2.4.0** gelaufen. Auslöser war der erste scharfe Einsatz in einem fremden Projekt (`team-kit_project_platformer`, Kaskaden 1–2): zwölf Backlog-Einträge, davon sechs aus dem Feld und zwei selbst verursacht beim Beheben. Zurückgespielt wurde nicht die Fehlerliste, sondern das, was sich verallgemeinern lässt.

**Drei neue Konzeptseiten**, alle aus demselben Erntelauf:

- [gegenprobe-zweite-quelle](konzepte/gegenprobe-zweite-quelle.md) — der tragende Fund. Drei Kit-Fehler (`BL-1`, `BL-4`, `BL-5`) passierten 149 grüne Tests, einen Live-Kontostand und ein committetes Ledger unbemerkt und fielen erst auf, als ein **Mensch zwei Dokumente nebeneinander hielt**. Gemeinsame Ursache: Der Bericht las dieselbe Quelle, die der Fehler bereits verfälscht hatte. Eine fehlende Datei galt als „keine Funde", eine fehlende Ledger-Zeile als „nichts angefallen". Regel: Mindestens eine Prüfung muss ihre Kennzahl aus einer anderen Quelle ziehen als das Geprüfte. Die Seite ordnet das Muster ein und zeigt, dass [lint-pruefung](konzepte/lint-pruefung.md) dieselbe Idee schon umsetzt — Quellenangaben gegen `raw/` prüfen statt gegen sich selbst.
- [alarmmuedigkeit](konzepte/alarmmuedigkeit.md) — drei Formen desselben Fehlers, in wenigen Stunden nebeneinander aufgetreten: die Domänenzeile, die strukturell immer `0.0000` zeigte (`BL-9`); das Gate, das man regelmäßig umgehen müsste und deshalb bewusst zur mitlaufenden Warnung herabgestuft wurde (Skizze D); der Testlauf, der erwartet rot ist und deshalb niemand mehr fuhr (`BL-6`). Drei Auswege in Rangfolge: wegnehmen, herabstufen, danebenstellen — **nicht**: die Schwelle verschieben, bis es grün wird.
- [rueckkanal-feld-kit](konzepte/rueckkanal-feld-kit.md) — genau der Vorgang dieses Log-Eintrags, verallgemeinert. Der teure Beleg: `BL-11` (12,00 USD an einem einzigen Befund verbrannt) lag **zwei Kaskaden lang** nur im Feldprojekt, und das erste Update nahm den Fix dann wortlos zurück (`BL-12`). Gelöst als Konvention, nicht als Werkzeug — bei einem Menschen und zwei Repos wäre Automatisierung teurer als das Problem. Neu bewertet ab dem dritten Feldprojekt. Gilt genauso für die CLAUDE.md-Vorlagen dieses Wikis und für `~/.claude/scripts/`.

**Aktualisiert:**

- [team-starter-kit](werkzeuge/team-starter-kit.md) — auf 2.4.0: der Erntelauf mit allen sechs Fehlern, `kit-test.sh` als Selbstverifikation, `install.sh --update` als sicherer Weg nach vorn (bis 2.3.0 war die einzige dokumentierte Update-Option datenvernichtend), die Ledger-Selbstprüfung. 176 Tests statt 127. Frank ist inzwischen scharf gelaufen, Axel weiterhin nicht.
- [claude-md-ki-team](vorlagen/claude-md-ki-team.md) — die drei geänderten Regeln im kopierbaren Block: Kostenabschluss schreibt zwei Zeilen statt einer (`BL-4`), der Nachlauf überschreibt nicht mehr (`BL-5`), eine Domäne ist der Normalfall (`BL-9`), und der Abschluss wird geprüft statt geglaubt.
- [kostencounter](konzepte/kostencounter.md) — Abschnitt „Prüfen statt glauben": Eine Log-Quelle ohne Aufrufer und Ersetzen-statt-Addieren bei disjunkten Mengen. Bemerkenswert daran: Aufgefallen ist das Fehlen nur, **weil** Bau- und Sweep-Kosten getrennt ausgewiesen werden. Eine Sammelzeile hätte den Fehler verdeckt — getrennte Zeilen sind nicht nur feineres Reporting, sie sind die Bedingung dafür, ein Fehlen zu bemerken.
- [read-only-guard](konzepte/read-only-guard.md) — `BL-10` als drittes Feldbeispiel, diesmal ein **positives**: Der Guard rollte ein Installer-Update zurück, das mitten in einen laufenden Betrieb schrieb. Er hatte recht, das Werkzeug hatte unrecht. Zwei Lehren: Ein korrekt anschlagender Guard sieht von außen wie ein Fehler aus — wer ihn dann entschärft, entfernt die Sicherung statt der Ursache. Und: Jedes Werkzeug, das neben dem Loop in dessen Arbeitsbaum schreibt, braucht dieselbe Sperre wie der Loop.

Kein Kosteneintrag: Diese Sitzung ist Wiki-Pflege, der Kit-Bau selbst ist im Feld-Ledger unter `team` verbucht.

## [2026-08-02 01:45] lint | Abschlussprüfung — Kostenmessung in Vorlage und Kit zurückgespielt

Letzter Pflegedurchgang. `wiki_lint.py` meldet alle acht Prüfungen sauber: 122 Seiten, 45 Quelldokumente (43 namentlich zitiert), 0 tote Links, keine Waisen, keine fehlenden Index-Einträge, Typ und Ordner konsistent, Log chronologisch.

Die formalen Prüfungen waren grün — zwei inhaltliche Funde blieben trotzdem:

**1. Die Vorlage behauptete etwas, das seit heute falsch ist.** In [claude-md-ki-team](vorlagen/claude-md-ki-team.md) stand zum Kostenabschluss im Abo: die A2-Schätzung sei „die belastbarste Zahl, die das Abo zulässt". Genau das hat die heutige Messung widerlegt. Die Stelle nennt jetzt zwei Wege in klarer Rangfolge — **messen** (mit beiden Fallen benannt: Deduplizierung über die Nachrichten-ID, Eichung des Preismodells an einem headless-Lauf) und die Schätzung nur als Notbehelf ohne Transkript. Dazu die Erwartungshaltung, dass der Löwenanteil auf das erneute Vorlegen des Kontexts entfällt.

Bewusst **ohne Wiki-Link** formuliert: Der Abschnitt liegt im kopierbaren Block und landet in fremden Projekten, wo ein Verweis auf `../anleitungen/…` ins Leere zeigen würde. Die Methode steht deshalb selbsttragend dort. Dieselbe Korrektur im Starterkit (`bootstrap/CLAUDE.md.vorlage`) und zusätzlich im Architekten-Briefing an der Stelle, wo er den Betrag tatsächlich einträgt.

**2. Die Dateizahl stimmte seit Version 2.1.0 nirgends.** Vier Stellen nannten „53 Dateien"; der Installer schreibt tatsächlich **55**. Nachgezogen in [team-starter-kit](werkzeuge/team-starter-kit.md), [index](index.md), der globalen `~/.claude/CLAUDE.md` und der Kit-`README.md`. Maßgeblich ist die Zählung des Installers selbst, nicht die Erinnerung an sie.

**Gegenprobe zur Sicherheit**: Die geänderten Kit-Artefakte in ein Wegwerf-Projekt installiert — 132 Regressionstests grün, keine offenen Platzhalter, der neue Text kommt gefüllt an. (Im Kit-Repo selbst schlagen 17 Tests fehl; das ist ein Layout-Artefakt, weil die Entrypoints dort unter `entry/` liegen, und war schon vor dieser Änderung so.)

Beide Funde gehören zur bekannten Klasse: **eine Aussage, die einmal stimmte, altert still weiter.** Der Lint findet so etwas nicht — er prüft Form, nicht Wahrheit.

---

## [2026-08-02 01:15] update | Sitzungskosten gemessen statt geschätzt — Kit-Herstellung im Feld-Ledger verbucht

Zwei Aufträge: das Wiki pflegen und die Herstellungskosten des Starterkits so festhalten, dass die Projektkarte der Website sie ziehen kann.

**Die Lücke**: Headless-Rollen geben `total_cost_usd` aus, der Architekt läuft interaktiv — im Abo ohne jeden Konsolenwert. Das Feldprojekt behalf sich bisher mit dem A2-Churn-Proxy (geänderte Zeilen × `16/1045` USD). Das Transkript von Claude Code enthält aber die echten Token-Zahlen.

**Zwei Fallen, beide belegt:**

1. Eine Modellantwort erzeugt mehrere `assistant`-Zeilen, die sich eine `message.id` und dieselbe `usage` teilen. Stumpfes Summieren ergab das 1,58- bis 1,72-fache des echten Werts — uneinheitlich, also nicht per Korrekturfaktor zu retten. 518 Zeilen entsprachen 280 eigenständigen Antworten.
2. Nach dem Deduplizieren blieb ein konstanter Faktor 0,74 — ein Preis-, kein Zählproblem. Ursache: einstündige Prompt-Cache-TTL, Cache-Write kostet dann das Doppelte des Basis-Inputs.

**Geeicht statt geraten**: Aus zwei headless-Läufen mit bekanntem `total_cost_usd` (Ralph 0,2728 / Harry 0,4751) ließ sich das Preismodell auflösen — Cache-Write 6,010 und Cache-Read 0,304 USD/Mio gegen die Liste 6,00 (1 h) und 0,30. Restfehler 0,2 %.

**Ergebnis der Sitzung** (Opus 5, ~3¼ h): Wiki-Pflege 29,57 · Feldinspektion 12,36 · Kit-Bau 119,09 · Verifikationsläufe 0,75 → **161,77 USD** Abo-Gegenwert.

**Die eigentliche Erkenntnis**: 75,5 % der Kosten waren Cache-Reads, Verhältnis zum Output 276 : 1. Bezahlt wird nicht, was das Modell schreibt, sondern dass es bei jedem Werkzeugaufruf den gewachsenen Kontext erneut vorgelegt bekommt. Ein Kostencounter, der nur Ausgabevolumen betrachtet, misst am Haupttreiber vorbei. Das bestätigt die Vorlagen-Regel „Prosa-Arbeit gehört nicht in den Loop" von der anderen Seite.

**Gegenprobe**: A2-Churn hätte 168,09 USD geschätzt, gemessen waren 165,58 — 1,5 % Abweichung. Als Notbehelf brauchbar, bei `n = 1` aber nicht bestätigt; in dieser Sitzung heben sich womöglich zwei Fehler auf (kopierte Dateien blähen den Churn, der Proxy sieht den Cache-Read nicht).

**Neu**: [sitzungskosten-aus-transkript](anleitungen/sitzungskosten-aus-transkript.md) — Fundort des Transkripts, beide Fallen, Eichverfahren, Preistabelle, Skript.
**Aktualisiert**: [kostencounter](konzepte/kostencounter.md) (Abschnitt „Messen statt schätzen"), [team-starter-kit](werkzeuge/team-starter-kit.md) (Herstellungskosten), [index](index.md).
**Außerhalb des Wikis**: sechs Zeilen in `website-maxron-de/.budget-ledger` (Domäne `team`, Kaskade `post-22`), von dort über `update_projekt_kosten.py` auf die T.E.A.M.-Projektkarte.

**Nachtrag — was die Verbuchung sichtbar machte**: Der „Wert-Hebel" der Projektkarte fiel von 102× auf 49×. Nach Formel korrekt, in der Sache nicht: der Zähler (Engineering-Gegenwert) zählt Commit-Tage **des Feld-Repos**, der Nenner nimmt seit dieser Buchung auch Kosten aus fremden Repos auf. Der Kit-Bau hinterließ dort keine Commits — Zähler stand, Nenner wuchs. Kosten werden repo-übergreifend gemessen, Wert repo-lokal. Auf Einwand des Strippenziehers als `BL-56` im Feldprojekt vermerkt (Backlog **und** Roadmap-Skizzen, Prio hoch, vier Lösungswege), zur Überarbeitung mit dem dortigen Architekten. Warnung an beiden Stellen: `update_projekt_kosten.py` läuft bei jedem Deploy — vor der Klärung nicht ausrollen.

---

## [2026-08-02 00:30] query | Starterkit 2.2.0 — bedienbar und scharf gelaufen

Zwei Anforderungen des Strippenziehers: „sicherstellen, dass es endlich läuft" **und** „bedienbar ist".

**Bedienbar — die letzte Lücke.** Die Prüfung eines frisch installierten Projekts zeigte: keine `README`, und die teuerste Warnung des Kits („vor dem ersten Guard-Lauf committen") stand **nirgends** im Projekt — nur in der Terminal-Ausgabe des Installers, die wegscrollt. Dieselbe Fehlerklasse wie `BL-55` und Planungsregel 5: flüchtige Ausgabe statt committetes Dokument. Ich hatte die Erstlauf-Regeln für den *Architekten* in die Artefakte geschrieben und dabei den *Menschen* vergessen.

Neu: **`TEAM.md`** im Projekt — Guard-Warnung ganz oben, Rollenübersicht, Kaskaden-Ablauf, Befehls- und Exit-Code-Tabelle (`42` ist eine Pause, kein Absturz), Ablageübersicht, Fehlersuche. Fünf Regressionstests sichern sie ab; der Installer verweist zuerst darauf.

**Läuft — erstmals mit echten CLI-Aufrufen** statt Fixtures, in einem Wegwerf-Projekt (Mini-Python-Projekt, eigener Smoke-Test, Ein-Stufen-Kaskade):

| Rolle | Ergebnis | Kosten |
|---|---|---|
| Ralph | Auth (abo) → realer Aufruf → Code gebaut → Smoke-Test grün → ein `feat(stufe1)`-Commit → Promise erkannt → State auf 2 → Cap respektiert → Exit 0 | 0,2728 USD |
| Harry | realer Sweep, Exit 0, State auf HEAD, Produktivcode nachweislich unangetastet | 0,4751 USD |

**Der Read-Only-Guard ist damit scharf belegt**: zwei `permission_denials` im Log — die `--allowedTools`-Allowlist verweigerte zwei Bash-Aufrufe, ohne dass der Aufruf als Fehler zählte. Die Kostenerfassung wies 0,7479 USD korrekt als **Abo-Gegenwert** aus, getrennt von API-Kosten.

Damit ist die Kette Konfiguration → Briefing → `team_claude` → Auth → Promise → Budget-Check → State → Guard → Kostenlog **durchgängig verifiziert**. Gesamtkosten des Beweises: **0,75 USD**.

**Weiterhin offen**: eine komplette `vollautomatik.sh`-Kaskade über alle vier Phasen. Frank und Axel sind einzeln geprüft (Exit 3 ohne Arbeitsvorrat), aber nie an einem echten Fund.

**Aktualisierte Seiten:**
- `werkzeuge/team-starter-kit.md` — Version 2.2.0, neuer Abschnitt „Bedienbarkeit: TEAM.md", Verifikationsstand durch den scharfen Lauf ersetzt.

## [2026-08-01 23:45] update | Starterkit 2.1.0 — Erstlauf-Regeln in die Artefakte

Auslöser war eine Rückfrage des Strippenziehers: „Deine Empfehlung für den ersten Lauf wird der Architekt ausführen, richtig?" — Antwort: **nein, nur teilweise.** Die Prüfung der Artefakte zeigte, dass zwei der sechs Empfehlungen (Smoke-Test hat Vorrang, erste Kaskade kurz halten) **nirgends** standen. Sie existierten nur als Zusage in einem Gespräch; ein kalt startender Architekt in einem frischen Projekt hätte sie nicht gekannt.

Dieselbe Fehlerklasse wie schon zweimal zuvor in diesem Vorhaben — und sie ist genau das, was Planungsregel 5 der Vorlage für den Terminal-Abschlussbericht regelt: **Was nicht im Git steht, existiert für die nächste Instanz nicht.**

**Wer führt was aus** (jetzt vollständig verortet):

| Empfehlung | Ausführender | Steht in |
|---|---|---|
| Kleines Projekt wählen | Strippenzieher | — (Entscheidung vor der Installation) |
| Smoke-Test als Stufe 1 | Architekt plant, Ralph baut | Briefing + Bootstrap-Roadmap |
| Erste Kaskade kurz halten | Architekt | Briefing |
| Budget-Deckel | Architekt schreibt `BUDGET_EMPFEHLUNG_USD`, Strippenzieher setzt `TEAM_BUDGET_USD` | Briefing + Installer-Meldung |
| Committen vor dem Guard | Strippenzieher | Installer-Meldung |
| Closeout nach dem Lauf | Architekt schreibt und ledgert, Strippenzieher committet | Briefing |

**Geändert im Kit** (`~/Source/team-kit`, 2.1.0): Architekten-Briefing um den Abschnitt „Die erste Kaskade eines Projekts" ergänzt; die vormals leere Bootstrap-Roadmap bringt „Skizze 1: Verifikationsfähigkeit herstellen" mit, die sich über den gefüllten Smoke-Test-Platzhalter selbst als erledigt kennzeichnet; Installer-Abschlussmeldung nennt `TEAM_BUDGET_USD=15` und verweist ohne Smoke-Test ausdrücklich auf Skizze 1.

**Vorabprüfung der CLI** (Anhang A.5, Faktencheck-Pflicht) gegen Claude Code 2.1.206: `-p`, `--model`, `--output-format`, `--permission-mode`, `--allowedTools` alle vorhanden; `git`, `flock`, `python3`, `pytest`, `claude` installiert; Auth im Abo-Modus mit API-Fallback, kein störender Key in der Umgebung. **Zwischenzeitlich fälschlich als Blocker gemeldet**: `--permission-mode default` fehlt in der Auswahlliste von `claude --help` — der diskriminierende Test zeigt aber, dass die CLI den Wert akzeptiert. Als Risikohinweis im Kit festgehalten, falls eine künftige Fassung ihn entfernt (es träfe genau die beiden Read-Only-Rollen).

**Aktualisierte Seiten:**
- `werkzeuge/team-starter-kit.md` — Version 2.1.0, neuer Abschnitt zu den Erstlauf-Regeln.

## [2026-08-01 23:00] update | Starterkit 2.0.0 — sprach- und stackagnostisch

Nachbesserung auf Anforderung: Das Kit soll in **jedem** Projekt laufen, unabhängig von Sprache und Stack. Version 1.0.0 setzte an mehreren Stellen still den Stack des Ursprungsprojekts voraus.

**Zwei Defekte, die ich in 1.0.0 selbst eingebaut bzw. übersehen hatte:**

1. **Die Rollen-Briefings waren nicht parametrisiert.** Ich hatte die vier Rollen-*Skripte* parametrisiert, die fünf *Briefings* aber wörtlich kopiert — obwohl sie selbst Prompts sind und als erstes gelesen werden. In einem Go-Projekt bekam Harry damit `site/**` als Guard-Grenze genannt und Ralph `python3 scripts/smoke_test.py` als Smoke-Test. Falsche Grenze, nicht existenter Befehl.
2. **`kosten.py` erzwang die Domänen `website` und `team`** an drei Stellen. Meine frühere Aussage „kosten.py ist projektfrei und wurde wörtlich übernommen" war falsch — ich hatte nach `site/` gegrept, nicht nach `website`.

**Umbau:**

| Vorher | Nachher |
|---|---|
| Skripte flach in der Wurzel, Werkzeuge in `scripts/`, Briefings in `prompts/`, Tests im `tests/` des Projekts | Entrypoints in der Wurzel, alles Aufgerufene unter `team/` (lib · redteam · tools · prompts · tests) |
| `.gitignore` brachte `__pycache__/` und `.pytest_cache/` global mit | auf `team/**` eingegrenzt |
| Team-Tests liefen im Test-Ordner des Projekts | eigener Aufruf `./team-test.sh` |
| Domänen `website|team` hart validiert | `TEAM_DOMAENEN` projektdefiniert; **Lesepfad validiert nicht mehr**, damit historische Ledger-Zeilen filterbar bleiben |
| fünf Interview-Fragen | sieben (Domänen, Commit-Regel des Architekten) |

**Neu**: `team/prompts/rolle-architekt.md` — das sechste Briefing fehlte, weil der Architekt interaktiv läuft und `team_briefing` nie braucht. Für den Trigger „Du bist unser Architekt" gab es damit nichts Kompaktes. Dazu `team-test.sh` und der Kostenabschluss-Hinweis in der Abschlussmeldung.

**Verifiziert in drei fremden Stacks** (Go, Rust, PHP), je 15 Prüfungen: keine stack-fremden Dateien in Projektordnern, kein `scripts/`/`prompts/` angelegt, eigene Dateien unangetastet, Briefings mit den **richtigen** Pfaden, eigene Domänen akzeptiert und fremde abgelehnt, **127 Regressionstests grün**, chirurgischer Guard-Rollback, perfekte Idempotenz.

**Eigener Fehler beim Umbau**, hier festgehalten: Beim Umschreiben auf `$TEAM_KOSTEN_TOOL` hatte ich die Variable **in Anführungszeichen** gesetzt — bash suchte dann ein Kommando namens `python3 team/tools/kosten.py`. Die Wort-Trennung ist hier gewollt; 27 Stellen korrigiert. Aufgefallen ist es nur, weil die Regressionstests es gefunden haben.

**Aktualisierte Seiten:**
- `werkzeuge/team-starter-kit.md` — Layout, sieben Fragen, alle vier Defekte, Stack-Verifikation.
- `index.md` — Beschreibung auf sprach-/stackagnostisch.

## [2026-08-01 21:00] query | T.E.A.M.-Starterkit gebaut — Rollenteam per Konsolenbefehl

Auftrag: eine komplette, auf Knopfdruck transferierbare Vorbereitung für jedes neue Software-Projekt. Ergebnis: eigenes Git-Repo `~/Source/team-kit` (Version 1.0.0, 55 Dateien) plus Launcher `~/.claude/scripts/team-init.sh`.

```
bash ~/.claude/scripts/team-init.sh ~/Source/mein-neues-projekt
```

**Entscheidungen des Strippenziehers**: eigenes Repo (statt `kit/` im Wiki oder rein global), zentrale `team.config.sh` (statt sed beim Kopieren), interaktives Interview mit fünf Fragen (statt Platzhalter oder Config-Datei).

**Zentraler Befund der Code-Analyse**: Die harten Projektbezüge waren stark konzentriert. `team-lib.sh` (821 Z), `kosten.py` (952 Z), `beutebuch.py` (275 Z), `vollautomatik.sh`, `halbautomatik.sh` und `team-status.sh` (643 Z) waren **bereits projektfrei** und wurden wörtlich übernommen. Nur 32 Stellen in den vier Rollen-Skripten mussten parametrisiert werden. Damit blieben ~3.200 Zeilen über 22 Kaskaden gehärteter Code unangetastet — genau das Risiko, das eine Neugenerierung aus Prosa erzeugt hätte.

**Zwei echte Defekte gefunden** — beide treten ausschließlich in einem frischen Projekt auf: `ralph.sh` und `team_plan_datei()` lasen `.ralph-plan` per `head`. Fehlt die Datei, liefert `head` RC≠0 und riss unter `set -e -o pipefail` den Loop weg, **bevor** die erklärende Fehlermeldung kam — blanker Exit 1 ohne Hinweis. Im Feldprojekt existiert die Zeiger-Datei seit Kaskade 1; beim allerersten Start eines neuen Projekts ist ihr Fehlen der Normalfall. Dieselbe Fehlerklasse wie bei der Feldinspektion: was zur Selbstverständlichkeit wird, meldet niemand zurück.

**Verifiziert im Wegwerf-Repo**: Installation (51 Dateien, Platzhalter und abgeleitete Whitelists korrekt), Shell-Syntax, Python-Kompilierung, **127 Regressionstests grün**, chirurgischer Guard-Rollback (Verletzer weg, erlaubte Datei erhalten, Produktivcode unangetastet), Verhalten aller Rollen ohne Arbeitsvorrat, **perfekte Idempotenz** (zweiter Lauf: 0 Dateien geschrieben, eigene Änderungen bewahrt). **Nicht verifiziert**: ein scharfer Vollautomatik-Lauf — der kostet echtes Geld und braucht einen echten Plan.

Zwei Testanpassungen für den generischen Einsatz, beide im Kit-CHANGELOG begründet: Die Ledger-Prüfung „Summe > 0" überspringt ein leeres Ledger; die BL-55-Regelprüfung testet die Substanz statt eines wörtlichen Satzes der Feldprojekt-CLAUDE.md.

**Das Feldprojekt `website-maxron-de` wurde ausschließlich gelesen**, nicht verändert.

**Neue Seiten:**
- `werkzeuge/team-starter-kit.md` — Aufruf, Herkunft, was wörtlich übernommen wurde, die fünf Fragen, die zwei Erstlauf-Defekte, Verifikationsstand und Grenzen.

**Aktualisierte Seiten:**
- `vorlagen/claude-md-ki-team.md` — „Schnellweg"-Kasten in der Benutzung; „Kein fertiges Skript-Bundle" um den Kit-Hinweis ergänzt.
- `anleitungen/team-skripte-generieren.md` — Verweis, dass die Anleitung nur noch für fremde CLI/Repo-Konventionen nötig ist.
- `index.md`, `README.md` — neue Werkzeugseite und Hilfsskript-Zeile.
- `~/.claude/CLAUDE.md` (außerhalb des Repos) — `team-init.sh` in die globale Skript-Tabelle.

## [2026-08-01 19:30] update | T.E.A.M.-Vorlage: Erstlauf-Lücken aus Feldinspektion geschlossen

Erste **direkte Inspektion** des Feldprojekts `website-maxron-de` (read-only, keine Änderung dort) statt Rückspielung per Bericht. Anlass: die Frage, ob ein neues Projekt mit der Vorlage genauso läuft wie das Feldprojekt. Antwort: prozessual ja, aber sechs Bausteine fehlten, die im Feld über 22 Kaskaden nebenbei entstanden sind und deshalb in keinem Rückspielbericht auftauchten.

**Was gefunden wurde und wo es jetzt steht:**

| Lücke | Belegt im Feldprojekt durch | Jetzt in |
|---|---|---|
| `## Projekt-Spezifika` fehlte im kopierbaren Block | reale `CLAUDE.md` beginnt damit; Regel-Inventar `R-1`…`R-8` | Vorlage, neuer erster Abschnitt + 4 neue Platzhalter |
| Kein `{{Smoke-Test-Befehl}}` | `prompts/rolle-ralph.md`: „muss grün sein, bevor die Stufe fertig ist" | Vorlage, Interview-Frage 2b als wichtigste Frage markiert |
| Bootstrap-Dateiliste fehlte ganz | 9 Dateien/Ordner, die die Skripte voraussetzen | Anleitung, neues **A.0** |
| Beutebuch-Fundformat nur beschrieben | `## Vorlage`-Block in `plans/beutebuch.md` | Vorlage, Harry-&-Marv-Sektion |
| `.gitignore` unvollständig | `.ralph-plan`, `.frank-attempts`, `.budget-ledger.lock` fehlten | Anleitung A.2, Punkt 8 |
| Briefing-Helfer ohne Code/Muster | `team_briefing()` in `team-lib.sh` + 5 Briefings | Anleitung, neues **A.10.1** |
| Abschluss-Protokoll ohne Gliederung | `plans/kaskade-22-abschluss.md`, 7 Abschnitte | Vorlage, Planungsregel 5 |

Dazu die **Test-Namenskonvention** `test_<fundnummer>_<stichwort>.py` (im Feld ~30 Regressionstests eindeutig ihren Funden zugeordnet, ohne Extra-Liste) und der Hinweis, dass Herleitung/Historie in ein Projekt-`wiki/` mit Rückverweis-Satz wandern.

**Methodische Lehre (auf der Quellenseite festgehalten):** Rückspielung per Bericht findet, was der Berichtende für bemerkenswert hält — nicht, was ihm zur Selbstverständlichkeit geworden ist. Für eine Vorlage, die fremde Projekte aufsetzen soll, ist das die gefährlichere Lücke: Der Erstlauf scheitert nicht an den spannenden Regeln, sondern an einer fehlenden leeren Datei.

**Aktualisierte Seiten:**
- `vorlagen/claude-md-ki-team.md` — 41,1 → 44,6 KB (Projekt-Spezifika, Fundformat, Test-Konvention, Abschluss-Gliederung, 4 Platzhalter, 3 Interview-Fragen).
- `anleitungen/team-skripte-generieren.md` — 32,8 → 38,2 KB (A.0 Bootstrap, vollständige `.gitignore`, A.10.1 Bauformen).
- `quellen/claude-md-ki-team.md` — Nachtrag „Feldinspektion statt Rückspielung" mit allen sechs Lücken.

## [2026-08-01 18:00] lint | wiki_lint.py um vier Prüfungen erweitert + Typ-Felder korrigiert

Das Skript sah bisher vier Fehlerklassen nicht, die der Wiki-Audit an diesem Tag gefunden hat. Vier neue Prüfungen ergänzt (jetzt acht insgesamt), jede mit einem Negativtest in einer Repo-Kopie verifiziert:

| Neue Prüfung | Findet | Hätte gefunden |
|---|---|---|
| `SOURCE CITATIONS` | Quellenangaben, die nicht exakt auf `raw/`/`clippings/` zeigen | die 50 abweichenden Angaben (Apostrophe, Doppel-Leerzeichen, `...`-Abkürzungen) |
| `UNINGESTED SOURCES` | Quelldokumente ohne Erwähnung auf einer Wiki-Seite | die Mysore-Quelle, drei Monate übersehen |
| `TYPE / FOLDER` | `type:` passt nicht zum Ordner | `token-sparen.md` und `beratungs-crm.md` |
| `LOG ORDER` | Einträge außer der Reihe, unbekannte Präfixe | die gemischte Log-Sortierung |

**Typ-Felder korrigiert** (die neue Prüfung meldete sie sofort):
- `anleitungen/token-sparen.md` — `konzept` → `anleitung`
- `projekte/beratungs-crm.md` — `konzept` → `projekt`
- `index.md` → `index`, `log.md` → `log` (waren beide als `konzept` geführt)

**Aktualisierte Seiten:**
- `konzepte/lint-pruefung.md` — Prüftabelle mit Zuordnung zu den sieben inhaltlichen Prüfpunkten; ausdrücklich vermerkt, was das Skript **nicht** kann (Widersprüche, veraltete Behauptungen, fehlende Konzeptseiten bleiben KI-Arbeit).
- `CLAUDE.md` — Typ muss zum Ordner passen; Sonderfälle `index`/`log` dokumentiert.
- `README.md` — Prüftabelle im Hilfsskripte-Abschnitt.
- `~/.claude/CLAUDE.md` (außerhalb des Repos) — Skript-Tabelle nachgezogen.

## [2026-08-01 17:10] update | T.E.A.M.-Vorlage aufgeteilt — Anhang A als eigene Anleitung

Die T.E.A.M.-Vorlage war mit **69,8 KB** die größte Datei im Repo — größer als das Log, dreimal so groß wie die nächstgrößte Vorlage. Aufgeteilt entlang der Nutzungsgrenze:

| | vorher | nachher |
|---|---|---|
| `vorlagen/claude-md-ki-team.md` | 69,8 KB | **41,1 KB** |
| `anleitungen/team-skripte-generieren.md` | — | **32,8 KB** |

**Trennlinie**: Betriebsregeln (was das Team befolgt, bei jeder Vorlagen-Änderung gelesen) bleiben in der Vorlage; die Bau-Anleitung Anhang A.1–A.10 (Einrichtungsarbeit, einmal beim Aufsetzen gelesen) wandert in die neue Anleitung. Das ist A.10 der Vorlage — „Regeldatei schichten, kürzt Text, nie Geltung" — auf die Vorlage selbst angewandt.

**Abschnittsnummern A.1–A.10 blieben unverändert**, damit alle bestehenden Verweise („siehe Anhang A.7") weiter stimmen. Betroffene Verweise in `konzepte/kostencounter.md`, `konzepte/ki-team-forensik.md` und `quellen/claude-md-ki-team.md` funktionieren unverändert.

**Korrektur zur Audit-Schätzung**: Der Audit-Bericht nannte „~20K Token in jeder Session" für die Vorlage als CLAUDE.md. Das war zu hoch — der tatsächlich in ein Zielprojekt kopierte Vorlagenblock ist **28,5 KB (~8K Token)**; die restlichen 41 KB waren Wiki-Meta und Anhang A, die nie in die Ziel-`CLAUDE.md` wandern. Die Aufteilung entlastet also nicht den Zielprojekt-Betrieb, sondern die Arbeit **an** der Vorlage in diesem Wiki.

**Neue Seiten:**
- `anleitungen/team-skripte-generieren.md` — A.1 Vorbedingungs-Check, A.2 Generierungs-Reihenfolge, A.3 Auth-Fallback, A.4 Read-Only-Guard (3 Linien), A.5 Faktencheck-Pflicht, A.6 Parallelität, A.7 Budget-Governance + acht Feld-Betriebslehren, A.8 Session-Limit-Robustheit (429), A.9 Interaktive Akteur-Kosten, A.10 Doku-Konsolidierung.

**Aktualisierte Seiten:**
- `vorlagen/claude-md-ki-team.md` — Anhang A durch Zeiger-Abschnitt ersetzt; Benutzung Schritt 3 und neuer Designhinweis 9 verweisen auf die Anleitung.
- `index.md`, `README.md` — neue Anleitung aufgenommen, Zahlen auf 120 Seiten.

## [2026-08-01 16:20] update | Seitenklassifikation als Konzeptskizze eingeordnet

Beim Audit aufgefallen: `konzepte/seitenklassifikation.md` beschrieb ein **Pflichtfeld** `**Klassifikation**:`, das auf **0 von 118 Seiten** existierte und in der `CLAUDE.md` nicht gefordert wird — das Wiki stellte eine Regel über sich selbst auf und brach sie.

Entscheidung: **nicht einführen**, Seite als Konzeptskizze kennzeichnen. Drei Gründe (ausführlich auf der Seite): Der Nutzen ist hier klein, weil fast alle Seiten „Zeitlos"/„Gemischt" wären; die zeitgebundenen Fälle sind bereits über Datumsangaben im Text und ⚠️/`status: archived` gelöst; und eine Halbeinführung wäre schlechter als keine, weil der Lint sie nicht durchsetzen könnte.

**Aktualisierte Seiten:**
- `konzepte/seitenklassifikation.md` — `status: draft`, Geltungsbereichs-Kasten oben (Feld gilt in der YouTube-Vorlage, nicht hier), neuer Abschnitt „Warum hier nicht eingeführt", Lint-Abschnitt relativiert.
- `index.md` — Beschreibung auf *(Konzeptskizze)* umgestellt.

## [2026-08-01 16:00] ingest | What Is Andrej Karpathy's LLM Wiki — And How Can You Extend It (Vishal Mysore)

Beim Audit als **einzige nie aufgenommene Quelle** gefunden — lag seit dem 2026-05-02 in `clippings/`.
Tiefe: **Mittel** (Quellenseite + eine Konzeptseite)

**Neue Seiten:**
- `quellen/llm-wiki-wikizz-mysore.md` — Medium-Artikel vom 2026-04-18. Referiert das Grundmuster und erweitert es um einen **5W1H-Kontextrahmen**; umgesetzt im quelloffenen Browser-Werkzeug **LLM WikiZZ** (Zero-Server, lokales Parsen, Cloudflare-Worker als CORS-Proxy zu NVIDIA NIM/Anthropic/Gemini). Drei Mechanismen: autonomes Gerüst, Kontrast-Maschine (Plain vs. WikiZZ nebeneinander), LLM-Jury (Bewerter-Modell benennt die Differenz). Neue Zahl fürs Wiki: 5.000 Stars / 4.400 Forks binnen zwei Wochen — als Sekundärangabe und Momentwert markiert.
- `konzepte/kontextrahmen-5w1h.md` — Die sechs Felder (Wer/Was/Wann/Wo/Warum/Wie), Mysores Diagnose der **Kontextschuld**, Beispieltabelle am Karpathy-Gist, Vorschlag für einen optionalen Block auf Quellenseiten. Bewertung: übernehmenswert ist der Rahmen, nicht das Werkzeug; Nutzen ist unbelegt (der Messaufbau existiert, Ergebnisse fehlen), und sechs autonom gefüllte Interpretationsfelder sind sechs neue Angriffsflächen fürs Kontaminierungsrisiko.

**Aktualisierte Seiten:**
- `konzepte/llm-wiki-v2.md` — Querverweis: zwei Wege zum selben Wissensgraphen (typisierte Kanten vs. gemeinsame Rahmenfelder).
- `konzepte/query-templates.md` — Querverweis: 5W1H als Gegenstück (Rahmenbedingungen statt Analyseart).
- `konzepte/community-projekte.md` — LLM WikiZZ als vierte unabhängige Implementierung ergänzt.
- `index.md` — beide neuen Seiten aufgenommen.

## [2026-08-01 14:30] update | T.E.A.M.-Vorlage: Feldstand Kaskaden 16–22 zurückgespielt

Sechster Rückfluss aus dem Feldprojekt (`website-projekt`, Zeitraum 2026-07-12 bis 2026-08-01). Sieben Erkenntnisse, davon **zwei Korrekturen überholter Vorlagen-Aussagen** statt reiner Ergänzungen — das erste Mal, dass der Rückfluss eine bestehende Pflichtregel als Bug entlarvt.
Tiefe: **Mittel**

**Aktualisierte Seiten:**
- `vorlagen/claude-md-ki-team.md` — Feldstand 15 → **22 Kaskaden** (HM-1…HM-53). **(1)** Keine Rolle ist mehr fest „API": auch **Der Architekt** läuft Abo-first (Entscheid 2026-07-13); im Abo persistiert der A1-Abschluss die Live-Schätzung als **Abo-Gegenwert**, weil es keinen Konsolenwert gibt. **(2)** `BL-55`: Die bisherige Pflicht „Ledger + Archivierung in jeder Kaskaden-Abschluss-Stufe" war **selbst ein Bug** — sie machte die Pro-Lauf-Durchsetzung blind (Bericht druckte **6,16 statt 26,42 USD**, −77 %, Deckel faktisch zurückgesetzt vor der offenen Fixphase). Neu: Kostenabschluss nur im **Closeout nach dem Lauf**, Durchsetzung zählt Archivpfade mit, Aufrufkosten = Summe **aller** Versuchs-Logs (sonst ist der Pro-Stufe-Cap umgehbar). **(3)** Planungsregel 5: Abschluss-Doc-Pflicht je gebauter Kaskade (Terminalbericht ist flüchtig und gitignored). **(4)** Neuer **Anhang A.10** — Doku-Konsolidierung als eigenes Muster (`BL-54`): Regeldatei schichten (859 → 320 Z), Rollen-Briefings statt Volltext-Verweis (859 → ~20 Z je Aufruf), Fundliste archivieren (3075 → 46 Z) mit archiv-bewusster Nummernvergabe — abgesichert durch ein **Regel-Inventar** (`NORM`/`HERLEITUNG`/`HISTORIE`) plus Regressionstest, Leitplanke „kürzt Text, nie Geltung". **(5)** Betriebslehre: Prosa-Arbeit gehört nicht in den Bau-Loop (Textumbau-Stufen 3,23–4,68 USD vs. 2,16–2,35 USD für Code-Stufen derselben Kaskade — der Loop zahlt je Stufe einen Kaltstart). **(6)** Kaskadenscharfe Rollen-Kosten, dritter Auth-Bucket `gemischt` statt geratener Aufteilung, Sanitisierung **aller** interpolierten Ledger-Felder (`HM-36`). **(7)** Reifegrad-Marken durchgängig nachgezogen.
- `quellen/claude-md-ki-team.md` — Update-Absatz „2026-08-01 (Kaskaden 16–22)" ergänzt; der 07-12-Absatz auf Vergangenheitsform gesetzt, da sein Feldstand überholt ist.

**Nachtrag 2026-08-01**: Dieser Eintrag wurde nachträglich erstellt — der zugehörige Commit `510a903` hatte `log.md` und `index.md` nicht mitgeführt.
## [2026-07-12 23:11] query | T.E.A.M.-Forensik-Konzeptskizze

**Anlass**: Brainstorming des Strippenziehers — kann die ausgehärtete T.E.A.M.-Vorlage für Legacy-Forensik angepasst werden? Kandidat: reale Enterprise-Produktionsumgebung.

**Entscheide**: (1) Konzeptskizze statt Vollvorlage (🟡 bis zum Feldlauf), (2) Quartett ohne Frank — Architekt, Ralph, Auditor (Harry⊕Marv), Axel; Auditor findet, Ralph fixt, (3) neuer Spezialist **Der Lotse** (Wissensträger-Interviewer) als strukturierter Mensch-Eskalationspunkt.

**Neue Seite**:
- `wiki/konzepte/ki-team-forensik.md` — Kern-Inversion (raw/ universell tabu, Wiki = Produkt, wiki_lint als Smoke-Test), Kader-Tabelle, Lotsen-Handwerk, 4 neue Design-Regeln (Konfidenz-Deckel 🟡 für Automatik, Halbautomatik-Default, zurückhaltende Token-Sparregeln, DSGVO-Guard-Linie), offene Fragen vor Aushärtung

**Updates**: Index (+1 Eintrag), Rückverlinkung aus beiden Mutter-Vorlagen (`claude-md-ki-team.md`, `claude-md-legacy-forensik.md`)

---

[Zurück zum Index](index.md)
## [2026-07-12 17:45] ingest | T.E.A.M.-Vorlage — Feldstand Kaskaden 9–15 zurückgespielt

Feldprojekt `website-maxron-de` ist von Kaskade 7 (bisheriger Vorlagenstand) auf Kaskade 15 gewachsen; die relevanten Neuerungen wurden in die Vorlage übernommen.

- `wiki/vorlagen/claude-md-ki-team.md`:
  - **Planungsregel 4** ergänzt — Scharfschalt-Sequenz als Pflicht-Ausgabe des Architekten (Zeiger umlegen → Konsistenz-Check → Budget → ggf. Red-Team-Fokus → Start)
  - **Session-Limit (429), Strategie A+B** (`BL-20`/`BL-25`) — dritte Fehlerklasse, Exit-42-Pausen-Mechanik, Auto-Retry mit Deckel, Auslauf-Bremse `TEAM_FIX_MAX_STAGNATION` (neuer Block in „Loop-Mechanik & Auth" + Anhang A.8)
  - **Aktive Auth-Startwarnung** (`BL-27`) — Warnung bei `ANTHROPIC_API_KEY` in der Env trotz Abo-Modus
  - **Zwei-Schwellen-Budgetmodell** (`BL-30`) — zentraler Soft-Cap (Hinweis) + Hard-Cap (Abbruch) für Frank/Axel; Auslöser HM-32 (zu tiefer Cap vervielfacht Kosten via Rollback); Axel-Sektion + Kostenkontrolle angepasst
  - **Interaktive Akteur-Kostenerfassung** (`BL-28`/`BL-29`/`BL-33`) — A2-Live-Schätzung (Architekt-Churn) + rollen-agnostischer A1-Abschluss, domänengetrenntes Ledger-Schema (neuer Block in Kostenkontrolle + Anhang A.9)
  - **Ablage-Konvention** Top-Level `*.sh` vs. `scripts/` in Anhang A.2 ergänzt; team-lib-Helferliste + Orchestrator-/Kostenwerkzeug-Beschreibung erweitert; Aufnahme-Interview um Budget-/Ledger-Platzhalter (Fragen 10–11) ergänzt
  - Reifegrad-Legende, Zusammenfassung, Quellen-Feld und Frontmatter auf Feldstand Kaskade 15 aktualisiert
- `wiki/quellen/claude-md-ki-team.md` — „Update 2026-07-12"-Absatz (Kaskaden 9–15), Planungsregel 4 in der Zusammenfassung, Frontmatter/Tags aktualisiert
## [2026-07-11 16:30] update | T.E.A.M.-Vorlage: Kaskade-7-Erkenntnisse zurückgespielt

Vierter Rückfluss aus `website-maxron-de` — die verallgemeinerbaren Erkenntnisse aus **Kaskade 7 + den Frank-Fixes BL-14…BL-19** (Vorlage kannte bislang nur bis Kaskade 6). Sechs Deltas: zwei vormalige A.7-*Ideen* sind jetzt **gebaut/erprobt**, zwei teuer gelernte Budget-Bugs und eine Guard-Lektion sind neu, plus die sprechende Entrypoint-Benennung.
Tiefe: **Mittel**

**Aktualisierte Seiten:**
- `vorlagen/claude-md-ki-team.md` — Reifegrad-Zeile + Quellen auf **sieben Kaskaden** (HM-1…HM-13); `TEAM_REDTEAM_FOCUS` und „success ohne Promise"-Härtung als ✅ aufgenommen. **A.7** erweitert: neuer Pflicht-Baustein **Log-Rotation/Archivierung** (sonst Doppelzählung der Budget-Ledger), **A/B-Kennzahlen-Trennung** (Pro-Lauf-Kosten für Durchsetzung vs. Gesamt-Kontostand nur Anzeige); Betriebslehren #2/#3 von *Idee* auf ✅ **gebaut** umgeschrieben, #4 (Log-Rotation) + #5 (A/B-Trennung) neu. **A.4** um Staging-Lektion ergänzt (datei-genau statt ordner-weit, weil der interaktive Architekt außerhalb des `flock` uncommittete Whitelist-Dateien liegen haben kann). **Designhinweis 7** + A.2-Punkt-6 + Wording: sprechende Entrypoint-Namen (`vollautomatik.sh`/`halbautomatik.sh` statt `wache.sh`/`pock.sh`).
- `konzepte/read-only-guard.md` — zweite ⚠️-Warnbox: Whitelist-Prüfung ≠ datei-genaues Staging; interaktiver Nicht-Loop-Akteur außerhalb des `flock` als Kollisions-Ursache.
- `quellen/claude-md-ki-team.md` — Reifegrad-Update-Absatz auf sieben Kaskaden + die zwei gebauten Bausteine, zwei neuen Betriebslehren und die Guard-Lektion gehoben.
- `konzepte/kostencounter.md` — Vorlagen-Zuordnungstabelle: Zeile `claude-md-ki-team` um den neuen Anhang-A.7-Hinweis (optionale Budget-Governance) ergänzt.
## [2026-07-10 17:00] lint | Wikiwartung + README-Aktualisierung

`wiki_lint.py` gelaufen: 1 verwaiste Seite gefunden (`wiki/konzepte/ki-lehrer-prototyp.md`, keine eingehenden Wiki-Links). Behoben durch Rückverweis in `wiki/konzepte/ki-lehrer-app.md` (Verwandte Seiten). Erneuter Lauf: keine toten Links, keine Waisen, keine fehlenden Index-Einträge, keine Formatfehler (116 Seiten gesamt).

`README.md` war seit 95 Seiten (2026-05-24) nicht mehr aktualisiert:
- Vorlagen-Tabelle um 3 fehlende Einträge ergänzt (`claude-md-softwareprojekt-rookie`, `claude-md-ki-team`, `zoocode-llm-wiki-lokal`), `roocode-llm-wiki-lokal` als archiviert markiert
- Anleitungen-Tabelle um `flutter-claude-md-anpassung` ergänzt
- Zahlen-Tabelle aktualisiert: 46 Konzepte, 24 Quellen, 14 Werkzeuge, 13 Vorlagen, 9 Anleitungen, 7 Personen, 1 Projekt — 114 gesamt; 45 Quelldokumente (37 Clippings + 8 Raw)
## [2026-07-10 16:15] update | T.E.A.M.-Vorlage: Erkenntnisse aus dem ersten scharfen Feldlauf

Dritter Rückfluss aus `website-maxron-de` — diesmal aus dem **ersten echten** Red-Team→Frank-Durchlauf (Harry/Marv/Frank via Abo). Zwei reusable Erkenntnisse in die Vorlage nachgetragen.
Tiefe: **Mittel**

**Aktualisierte Seiten:**
- `vorlagen/claude-md-ki-team.md` — **A.5 Tool-Permission-Format** von „zu verifizieren" auf ✅ **verifiziert** (headless `--permission-mode default` + `--allowedTools` greift real; Sweep ließ Produktivcode unangetastet). **Frank-Variante** korrigiert: Dreisatz-Verifikation darf **nicht** verlangen, dass HEAD selbst der `{{fix-präfix}}`-Commit ist — der Fixer darf einen `docs:`-Folgecommit anhängen; korrekt ist `git log START_HASH..HEAD | grep {{fix-präfix}}`. Der ursprüngliche „letzter Commit"-Check rollte im Feld korrekt gefixte Arbeit fälschlich zurück (Warnbox ergänzt).
## [2026-07-10 15:30] update | T.E.A.M.-Vorlage: Vollautomatik implementiert + Guard-Härtungs-Lektion

Zweiter Rückfluss aus `website-maxron-de` am selben Tag: Die komplette **Voll-Automatik** (Orchestrator-Wache + `redteam.sh`/`frank.sh`/`axel.sh` + `flock` + Beutebuch-Zustandsmaschine + Monitoring) wurde dort implementiert und ohne LLM-Aufrufe getestet. Dabei **teuer gelernt**: Der Read-Only-Guard darf nur **chirurgisch** zurückrollen — ein blindes `git reset --hard` + `git clean -fd` löschte im Bau die gesamte noch uncommittete Team-Infrastruktur.
Tiefe: **Mittel**

**Aktualisierte Seiten:**
- `konzepte/read-only-guard.md` — Linie 3 auf chirurgischen Per-Pfad-Rollback umgestellt; Warnbox mit der Footgun-Lektion (reset --hard/clean -fd) und drei Betriebsregeln (nur Verletzer-Pfade; Infrastruktur vor Guard committen; Guard-Tests nur im Wegwerf-Repo).
- `vorlagen/claude-md-ki-team.md` — A.4 von 🟡 auf „✅ erprobt" mit chirurgischem Guard + Härtungs-Warnbox; A.3-Auth-Hinweis: Axel-Ausnahme ist Strippenzieher-Entscheidung (im Feldprojekt bewusst in Abo-first aufgenommen); Reifegrad-Zeile: Vollautomatik-Mechanik ✅, erster echter `wache.sh`-Lauf noch 🟡.
## [2026-07-10 14:30] update | T.E.A.M.-Vorlage: Auth-Mechanik feldgetestet (Abo Prio 1 + API-Fallback)

Rückfluss aus dem Zweitprojekt `website-maxron-de` (erste Anwendung der Vorlage außerhalb des Ursprungsprojekts): Der bisher als 🟡-Zielbild geführte **Abo-Default mit stufen-lokalem API-Fallback** ist dort für Ralph implementiert und verifiziert; zwei offene Fragen aus Anhang A sind beantwortet.
Tiefe: **Mittel**

**Aktualisierte Seiten:**
- `vorlagen/claude-md-ki-team.md` — Auth-Modi-Block im Vorlagen-Fence neu gefasst (Auflösungskette Env → `~/.config/claude-team/auth-mode` → Rollen-Default; Key-Datei `~/.config/claude-team/api-key` statt `export` in `.bashrc`; **Verdrängungsfalle** dokumentiert: exportierter `ANTHROPIC_API_KEY` hat Vorrang vor dem Abo-Login, geerbte Env in offenen Terminals/IDEs hält sich bis `unset`); A.3 von 🟡 auf „✅ bei Ralph erprobt" mit konkretem Rezept (frische Auflösung pro Stufe, genau ein API-Retry); A.5-Faktencheck beantwortet: Fehlersignal = Exit-Code ≠ 0 oder `is_error` im JSON; Hinweis auf maschinenweites Einrichtungsskript `~/.claude/scripts/team-auth-setup.sh` (neu, liegt außerhalb des Wikis); Reifegrad-Zeile und Quellen entsprechend nachgeführt.
## [2026-07-10 12:20] ingest | CLAUDE.md T.E.A.M. v2 (Verfeinerung)

Zweite Fassung `raw/claude-md-team-v2.md` nachgereicht — entstanden in Zusammenarbeit mit dem Architekten des KI-Lehrer-App-Projekts (Modell Opus 4.8). In `wiki/vorlagen/claude-md-ki-team.md` übernommen:

- Kostenkontrolle-Block überarbeitet: Token-Sparregeln jetzt optional/Default AUS (Axel braucht ungekürztes Lesen für Root-Cause-Analysen), neue „Grundregeln (stets aktiv)" trennen Kosten-Cap von Lese-Sparmaßnahmen — bewusste Abweichung vom generischen [kostencounter](konzepte/kostencounter.md)-Standard
- Designhinweis 8 neu: Kostenkontrolle-Block liegt bewusst innerhalb des kopierbaren Vorlagenblocks
- Smoke-Test-Hinweis präzisiert (Python-Beispiel als solches kennzeichnen)
- „R4-Zielbild" → „Zielbild" (Rückbezug auf Ursprungsprojekt-Roadmap entfernt)
- `wiki/quellen/claude-md-ki-team.md` — Abschnitt „Verfeinerung (v2)" ergänzt, Quellen-Feld erweitert
- `wiki/konzepte/kostencounter.md` — Ausnahme-Hinweis in der Vorlagen-Zuordnungstabelle ergänzt
## [2026-07-10 12:00] ingest | CLAUDE.md T.E.A.M. (KI-Rollenteam)

Neue Quelle `raw/claude-md-team.md`: eine CLAUDE.md-Vorlage für ein Team aus sechs KI-Rollen (Ralph, Der Architekt, Frank, Harry, Marv, Axel) unter einem menschlichen „Strippenzieher", abgeleitet aus dem realen KI-Lehrer-App-Projekt. Tiefe: Vollständig.

- **Namenskonflikt entdeckt und aufgelöst**: Die Quelle heißt intern „claude-md-team", kollidiert aber mit der bestehenden Vorlage `wiki/vorlagen/claude-md-team.md` (Team-Wiki für 2–8 Menschen, anderes Thema). Nutzer entschied: neue Seite als `claude-md-ki-team` aufnehmen, bestehende Seite bleibt unangetastet.
- `wiki/quellen/claude-md-ki-team.md` — neu: Zusammenfassungsseite mit Einordnung, Namenskonflikt-Historie, Übersicht der sechs Rollen
- `wiki/vorlagen/claude-md-ki-team.md` — neu: vollständige Vorlage (Rollentabelle, drei Dreisätze, Status-Kette, Kaskaden-Planungsregeln, Auth-Modi, Anhang A Skript-Generierung, Aufnahme-Interview). Kostenkontrolle-Block nach [kostencounter](konzepte/kostencounter.md)-Standard ergänzt (Abschluss-Variante, war in der Quelle nicht enthalten). Externe Links auf Dateien des Ursprungsprojekts (`plans/roadmap-skizzen.md`, `ralph.sh` etc.) auf Klartext umgestellt, da diese Dateien nicht in diesem Wiki-Repo liegen
- `wiki/konzepte/finder-fixer-prinzip.md` — neu: Gewaltenteilungsregel (wer findet, fixt nicht selbst)
- `wiki/konzepte/read-only-guard.md` — neu: 3-Linien-Verteidigung (Prompt + Tool-Permissions + Post-Hook) zur technischen Durchsetzung von Read-Only-Rollen
- `wiki/konzepte/ki-lehrer-app.md`, `wiki/konzepte/ralph-schleife.md`, `wiki/konzepte/kostencounter.md` — Verwandte-Seiten/Tabellen um Rückverweise ergänzt
- `wiki/index.md` aktualisiert

---
## [2026-07-10 00:00] ingest | Guide: Flutter-Integration in die CLAUDE.md-Vorlage

Quelle: `raw/guide_flutter_integration.md` — Anleitung, wie die Vorlage `vorlagen/claude-md-software.md` für ein Flutter-Projekt über 5 Plattformen (Linux, Android, macOS, iOS, Windows) angepasst wird. Kein fertiges Template, sondern Anpassungsanleitung für die Produktions-KI.
Tiefe: **Mittel**

**Neue Seiten:**
- `quellen/guide-flutter-integration.md` — Zusammenfassung der 10 Anpassungspunkte
- `anleitungen/flutter-claude-md-anpassung.md` — vollständige Schritt-für-Schritt-Anleitung (Moduswahl, Platzhalter, Plattform-Build-Befehle, Flutter-Projektstruktur, State-Management-Entscheidung, Zielplattformen-Block, Dart-Konventionen, Tests, `wiki/plattformen/`-Unterordner, Proxy-/VS-Codium-Umgebungshinweise)

**Aktualisierte Seiten:**
- `vorlagen/claude-md-software.md` — Verwandte Seiten um Verweis auf die neue Flutter-Anleitung ergänzt
- `index.md` — 2 neue Einträge (Anleitungen, Quellen)
## [2026-07-02 00:00] update | Zoo Code + Kostencounter-Standard

**Zoo Code (Roocode-Nachfolger):**
- Neu: `werkzeuge/zoo-code.md` — Community-Fork von Roo Code (Apache 2.0, v3.64.0, aktiv entwickelt)
- Update: `werkzeuge/roo-code.md` — Status auf `archived`, Verweis auf Zoo Code
- Neu: `vorlagen/zoocode-llm-wiki-lokal.md` — Zoo-Code-Vorlage (portiert von Roocode-Variante)
- Update: `vorlagen/roocode-llm-wiki-lokal.md` — Status auf `archived`, Verweis auf neue Vorlage

**Kostencounter-Standard:**
- Neu: `konzepte/kostencounter.md` — Einheitliches Kosten-Tracking: Fortlaufend-Variante (offene Projekte) + Abschluss-Variante (abgeschlossene Projekte)
- Update: alle 10 Vorlagen in `vorlagen/` — `## Kostenkontrolle`-Block ergänzt (Fortlaufend: Lehrer, Nachhilfe, Laienlehrer, YouTube, Rezepte; Abschluss: Software, Software-Begleiter, Softwareprojekt-Rookie, Legacy-Forensik, Team)
## [2026-05-31 12:00] query | KI-Lehrer App erweitert aus Rookie-CLAUDE.md

1 Update: `konzepte/ki-lehrer-app.md` — neue Abschnitte Tech-Stack, Architektur-Regeln, Umgebung & Betrieb, Kaskaden-Chronik (Kaskaden 1–10), Bekannte Regressions-Checks. Status von `draft` auf `active` gesetzt.
## [2026-05-31 00:00] ingest | CLAUDE.md Battle-tested Veteran + Rookie

5 neue Seiten: `quellen/claude-md-legacy-forensik-veteran`, `quellen/claude-md-softwareprojekt-rookie`, `vorlagen/claude-md-softwareprojekt-rookie`, `konzepte/feldtest-sperre`, `konzepte/claude-md-token-sparen`.
3 Updates: `vorlagen/claude-md-legacy-forensik` (Battle-tested-Abschnitt), `konzepte/ki-lehrer-app` (V1.9 Feldtest), `index.md`.
Neue Konzepte: FELDTEST-SPERRE (Release-Freeze während Nutzertests), In-CLAUDE.md-Token-Sparregeln.
## [2026-05-24 19:00] query | KI-Lehrer App — Prototyp-Konzept

Neue Seite `konzepte/ki-lehrer-prototyp.md`: Tech-Stack-Entscheidungen (Tkinter, anthropic SDK, openai-compat. für lokal, gitpython), vollständige Projektstruktur mit Modulaufteilung (gui/ + core/), 8-stufige Build-Reihenfolge von Grundgerüst bis vollständiger App. Architektur-Prinzipien: Screens als eigenständige Frame-Klassen, zentraler AppState, core/ ohne Tkinter, Streaming im Thread via root.after().

**Neue Seiten:**
- `wiki/konzepte/ki-lehrer-prototyp.md`

**Geänderte Seiten:**
- `wiki/index.md` aktualisiert

---
## [2026-05-24 18:00] query | KI-Lehrer App — Setup-Wizard

Neue Seite `konzepte/ki-lehrer-setup-wizard.md`: vollständiger Wizard-Entwurf mit 9 Schritten (Willkommen, Name, Klasse, Fächer, Anrede, Erklärtiefe, KI-Verbindung, API-Key/lokale URL + Verbindungstest, Fertig-Zusammenfassung). Jeder Dialog als ASCII-Wireframe mit konkreten Formulierungen und DAU-Begründungen. Fehlerbehandlung für Verbindungstest in Klartext. Tabelle der bewussten Weglassungen. Verkürzter Wizard für zweite Schülerprofile dokumentiert.

**Neue Seiten:**
- `konzepte/ki-lehrer-setup-wizard.md`

**Aktualisierte Seiten:**
- `wiki/index.md`: Eintrag ergänzt
## [2026-05-24 17:00] query | KI-Lehrer App — Die vier Screens

Neue Seite `konzepte/ki-lehrer-screens.md`: vollständiger Entwurf aller vier Navigations-Screens mit ASCII-Wireframes und DAU-Begründungen. Lehrerpult (Fachkarten, Wochenfortschritt, Schüler-Begrüßung), Aufgabenhefte (Heft/Sitzungen/Chat-Tabs mit automatischem KI-Eröffnungsgruß und Session-beenden-Flow), Fächer (Checkbox-Liste, Materialien-Manager), Einstellungen (KI-Verbindung, Schülerprofil in Klarsprache, App). Flussdiagramm zeigt 2-Klick-Weg zur laufenden Session.

**Neue Seiten:**
- `konzepte/ki-lehrer-screens.md`

**Aktualisierte Seiten:**
- `wiki/index.md`: Eintrag ergänzt
## [2026-05-24 16:00] query | Foto-Review — Vision-Workflow für nicht-digitalisierbare Schülerarbeit

Neues Konzept `konzepte/foto-review.md`: optionaler Vision-Workflow für Aufgaben die zwingend auf Papier entstehen (Geometrie-Konstruktionen, Diagramme, Zeichnungen). Kernprinzip: KI erkennt den Aufgabentyp selbst und passt das Review flexibel an — kein festes Schema. Cloud-only. Besonders relevant für Fernschüler vor der Einsendungsaufgabe. `vorlagen/claude-md-nachhilfe.md` um optionalen Foto-Review-Workflow-Block erweitert (Designhinweis #8 + eigener Workflow-Abschnitt in der Vorlage).

**Aktualisierte Seiten:**
- `konzepte/foto-review.md`: neu angelegt
- `vorlagen/claude-md-nachhilfe.md`: Designhinweis #8 + Foto-Review-Workflow-Block
- `index.md`: neuer Eintrag
## [2026-05-24 15:00] query | KI-Lehrer App — Datenmodell & Ordnerstruktur

Neue Seite `konzepte/ki-lehrer-datenmodell.md`: vollständige Ordnerstruktur unter `~/KI-Lehrer/`, JSON-Schemas für `config.json` und `profil.json`, Git-Strategie (ein Repo pro Schüler), Kontext-Ladestrategie (4 Quellen pro Session), Dateiformat für Heft und Sitzungs-Logs, Backup-Empfehlung für DAUs. Vorwärts-Link auf `ki-lehrer-screens.md` als nächste geplante Seite.
## [2026-05-24 14:00] lint | Wartung — verwaiste Seite verlinkt

Wiki-Lint: 0 tote Links, 0 Format-Fehler, 97 Seiten. Eine verwaiste Seite behoben: `konzepte/ki-lehrer-app.md` hatte keine eingehenden Links. Rück-Links in `vorlagen/claude-md-lehrer.md` und `konzepte/heft-muster.md` ergänzt.
## [2026-05-24 12:00] query | Web Clipper als Clipping-Werkzeug in KI-Lehrer-App verankert

Klärung: Obsidian selbst ist kein Pflicht-Bestandteil der Vorlagen — der praktische Mehrwert liegt im Obsidian Web Clipper (Browser-Plugin → clippings/ → Ingest). `obsidian.md` wieder auf ursprüngliche Rolle als Viewer gestutzt. `ki-lehrer-app.md`: Web Clipper als Quellkanal für Unterrichtsmaterial dokumentiert.

**Aktualisierte Seiten:**
- `werkzeuge/obsidian.md`: Übertriebene Backbone-Abschnitte entfernt
- `konzepte/ki-lehrer-app.md`: Web Clipper → clippings/ als Kernworkflow
## [2026-05-24 00:00] ingest | KI-Lehrer App — GUI-Wireframe als Konzeptseite

Bild `raw/ki-lehrer_gui-entwurf_sehr-abstrakt.png` aufgenommen. Kern: Tkinter-Desktop-App die den KI-Lehrer-Ansatz für technisch unaffine Nutzer (DAU-Prinzip) zugänglich macht. GUI als Zugangslösung — Komplexität (Git, Markdown, Heft-Muster) läuft im Hintergrund, unsichtbar. Flexible Interview-Logik bleibt erhalten. Projektstatus: Konzeptphase, wird ausgelagert.

**Neue Seiten:**
- `konzepte/ki-lehrer-app.md`: Konzept, DAU-Prinzip, Wireframe-Beschreibung, Abgrenzung zu Vorlagen
## [2026-05-18 00:00] query | Heft-Konzept: zeitliche Dimension für KI-Lehrer-Vorlagen

`heft/`-Ordner als Schüler-Arbeitsraum eingeführt: ein lebendes `.md`-Dokument pro Thema/Konzept. Schüler schreibt eigene Lösungen hinein (in Obsidian), Claude liest und reviewed, committet danach. Git-Historie = Bewertungshistorie. Session-Start-Scan prüft welche Konzepte >14 Tage nicht reviewed wurden → Spaced-Repetition-Signal ohne Overhead. Laienlehrer ohne `heft/` (Modell B), aber mit Themen-Scan über `wiki/lektionen/`.

**Aktualisierte Seiten:**
- `vorlagen/claude-md-lehrer.md`: heft/, Heft-Scan in Phase 2, Heft-Seitenformat, Commit-Regel
- `vorlagen/claude-md-nachhilfe.md`: heft/, Session-Start-Scan, Heft-Schritt im Aufgaben-Workflow, Heft-Seitenformat, Commit-Regel
- `vorlagen/claude-md-laienlehrer.md`: Session-Start-Themen-Scan über lektionen/

---
## [2026-05-17 00:00] query | Docs-Ordner-Feature für Lehr-Vorlage

Feature-Erweiterung der KI-Lehrer-Vorlage: optionaler `docs/`-Ordner für Referenzdokumente (PDFs, gescrapte Dokumentationen, Bücher).

- Phase 1 bekommt neuen Schritt 3 (Dok-Ingest): Claude liest Dokumente beim ersten Start und erstellt `docs/index.md` als Navigationsindex
- „Was du tust" ergänzt: Faktenfragen gegen docs/ nachschlagen statt halluzinieren
- Neues Seitenformat für `docs/index.md` (Typ, Umfang, Abschnittsliste)
- Neue Regeln: docs/ unveränderlich, Zitierformat `(Quelle: dateiname, Kap. X / S. Y)`
- Neuer Designhinweis 7 erklärt Zweck und Optionalität des Docs-Ordners
- `wiki/vorlagen/claude-md-lehrer.md` aktualisiert

---
## [2026-05-16 20:00] query | Hardware-Vergleich Sonnet 4.6 vs. lokale Modelle

Neue Konzeptseite: welche GPU-Tiers für vergleichbare Wiki-Qualität nötig sind. Vier Tiers (16 GB bis 192 GB), Aufgaben-Qualitätsmatrix, Empfehlungen.

**Neue Seiten:**
- `konzepte/hardware-vergleich-sonnet-vs-lokal.md`

**Aktualisierte Seiten:**
- `index.md`: Neue Seite eingetragen

---
## [2026-05-16 20:00] ingest | Anleitung: LLM-Wiki mit Roo Code + lokalem Modell einrichten

Vollständige Schritt-für-Schritt-Anleitung für bestätigtes Setup (RTX 5080, qwen3:14b-40k, Ollama, Roo Code 3.54.0). Deckt Ein- und Zwei-Maschinen-Setup ab, Modelfile-Einrichtung, Provider-Konfiguration, Projektstruktur, ersten Start, laufenden Betrieb und Troubleshooting.

**Neue Seiten:**
- `anleitungen/roocode-llm-wiki-einrichten.md`

**Aktualisierte Seiten:**
- `wiki/index.md`: Neue Anleitung eingetragen
- `anleitungen/erste-schritte.md`: Link auf neue Anleitung ergänzt
- `anleitungen/lokale-modelle.md`: Link auf neue Anleitung ergänzt

---
## [2026-05-16 19:30] ingest | Vorlage: LLM-Wiki mit Roo Code + qwen3:14b-40k

System-Prompt-Vorlage für lokalen LLM-Wiki-Betrieb erstellt. Auf 40K-Kontextlimit abgestimmt: max. 1 Quelle pro Session, max. 3–5 neue Seiten, kein Lint. Enthält Provider-Setup, Token-Budget-Tabelle und kompakte Workflow-Anleitung (~800–1.000 Tokens).

**Neue Seiten:**
- `vorlagen/roocode-llm-wiki-lokal.md`: Vorlage mit Einrichtungsanleitung und Template-Block

**Aktualisierte Seiten:**
- `wiki/index.md`: Neue Vorlage eingetragen

---
## [2026-05-16 19:00] query | RTX 5090 in Hardware-Vergleiche aufgenommen

32 GB GDDR7, ~1.792 GB/s Bandbreite, CUDA — gleiche VRAM-Kapazität wie R9700 aber ~2,8× höhere Bandbreite. Marktpreise Mai 2026 massiv überhöht. In drei Seiten ergänzt.

**Aktualisierte Seiten:**
- `werkzeuge/radeon-ai-pro-r9700.md`: Vergleichstabelle um RTX 5090 erweitert
- `konzepte/quantisierung.md`: 32-GB-Fazit um 5090 ergänzt
- `anleitungen/lokale-modelle-fortgeschritten.md`: Kommentar bei 32B-Empfehlung aktualisiert

---
## [2026-05-16 18:30] query | Hypothetisches R9700-Setup: 32B + Roo Code analysiert

32B Q4_K_M + 40K passt knapp in 32 GB VRAM (~29 GB), 32K ist komfortabler. Qualitäts-Kompromiss (Q4) und ROCm-Reife (RDNA 4, Stand Mai 2026) als offene Fragen. Kein klarer Vorteil gegenüber RTX 5080 + qwen3:14b-40k.

**Aktualisierte Seiten:**
- `werkzeuge/radeon-ai-pro-r9700.md`: Neuer Abschnitt mit VRAM-Tabelle, Qualitäts- und ROCm-Analyse, Empfehlung

---
## [2026-05-16 18:00] query | 32B-Modelle mit 40K Kontext auf RTX 5080: nicht realisierbar

KV-Cache für 32B bei 40K Kontext beträgt ~10-11 GB unabhängig von Gewichts-Quantisierung. Selbst Q2_K (~8 GB Gewichte) übersteigt 16 GB VRAM. Optimum für RTX 5080 bleibt qwen3:14b-40k.

**Aktualisierte Seiten:**
- `konzepte/quantisierung.md`: Neuer Unterabschnitt mit Tabelle und Erklärung

---
## [2026-05-16 17:30] query | Lokale Modelle für LLM-Wiki-Ingest: Session-Limits dokumentiert

Praktische Grenze für lokale 14B-Modelle beim LLM-Wiki-Ingest: Session-Kontext (System-Prompt + Clipping + Gesprächsverlauf) summiert sich auf 18–42k Tokens — zu viel für qwen3:14b-40k im regulären Betrieb. Lokale Modelle nur für einfache Queries geeignet, nicht für Ingest oder Lint.

**Aktualisierte Seiten:**
- `anleitungen/lokale-modelle.md`: Neuer Abschnitt „LLM-Wiki-Ingest mit lokalen Modellen" mit Token-Aufschlüsselung und Empfehlungstabelle
- `konzepte/ingest-workflow.md`: Hinweis auf lokale Modell-Limits im Abschnitt „Arbeitsweise"
- `konzepte/skalierungsgrenzen.md`: Neuer Abschnitt „Session-Limits bei lokalen Modellen"

---
## [2026-05-16 17:00] query | qwen3:14b-40k: Latenzdegradation bei wachsendem Kontext

Beobachtung aus Ollama-Debug-Logs: Antwortzeit steigt quadratisch mit wachsendem Gesprächsverlauf, lange vor dem 40k-Token-Limit. Bei ~19,6k Tokens dauerte Request 3 bereits 23,9s (vs. 7,5s bei ~13,9k Tokens). Praktische Nutzungsgrenze: ~20–25k Tokens.

**Aktualisierte Seiten:**
- `konzepte/ollama-kontextfenster.md`: Abschnitt „Latenzdegradation bei wachsendem Kontext" mit Messtabelle und Empfehlung ergänzt

---
## [2026-05-16 16:00] query | Roo Code 3.54.0 + Ollama: bestätigte Konfiguration

Roo Code 3.54.0 mit lokalem Modell via Ollama auf Zwei-Maschinen-Setup in Betrieb genommen (RTX 5080, 16 GB VRAM).

**Bestätigte Konfiguration:**
- Modell: `qwen3:14b-40k` (40K Kontext eingebaut)
- Provider in Roo Code: OpenAI Compatible
- Endpoint: `http://<server>:11434/v1` (nicht `/api/chat`)
- GPU-Auslastung: 93%, CPU-Offload: 7%

**Warum der native `ollama`-Provider nicht funktioniert:** Ollamas `/api/chat`-Endpoint gibt vereinfachtes JSON zurück (`{"name": ..., "arguments": ...}`), das Roo Code 3.54.0 nicht parsen kann. Der `/v1`-Endpoint liefert das korrekte OpenAI-`tool_calls`-Format.

**Aktualisierte Seiten:**
- `werkzeuge/roo-code.md`: Tool-Calling-Abschnitt von XML auf natives Function Calling korrigiert; Anbieter-Tabelle überarbeitet; bestätigte Konfiguration + Negativliste ergänzt
- `konzepte/ollama-kontextfenster.md`: Checkliste um Provider-Hinweis erweitert; Tabelle mit Modellen mit eingebautem Kontext (qwen3:14b-40k) ergänzt
## [2026-05-15 18:00] ingest | Roo Code + Lokale Modelle (10 Clippings)

10 neue Clippings aufgenommen — alle zum Thema Roo Code mit lokalen Modellen via Ollama / LM Studio.

**Neue Seiten (10):**
- `werkzeuge/roo-code.md` — Roo Code: Modi, Tool-Nutzung, lokale Optimierungen
- `werkzeuge/lm-studio.md` — LM Studio: GGUF-Modelle, Inferenz-Server, Vergleich zu Ollama
- `konzepte/ollama-kontextfenster.md` — 2K-Standardlimit, Modelfile-Lösung, num_ctx/num_predict
- `konzepte/tool-use-lokale-modelle.md` — XML-Werkzeugaufrufe, Fine-tuned Modelle, Ehrliche Einschätzung
- `konzepte/quantisierung.md` — Q4/Q8, VRAM-Kalkulation, Parameter vs. Präzision
- `konzepte/roocode-system-prompt-optimierung.md` — MCP, Foot Gun Prompt, drei Optimierungsstufen
- `quellen/roocode-lokale-doku.md` — Offizielle Roo Code Docs (3 Clippings)
- `quellen/roocode-lokale-community-reddit.md` — Reddit r/RooCode (2 Threads)
- `quellen/roocode-local-evaluation-olilanz.md` — GitHub-Evaluation olilanz (1 Clipping)
- `quellen/roocode-lokale-mychen76.md` — mychen76 Modelle + Artikel (4 Clippings)
## [2026-05-15 14:30] query | Lehrer-Vorlage: Domain-Erweiterung dynamisch

- Optionaler Coding-Block aus der Vorlage entfernt
- Phase 1 um Schritt „Domain-Erweiterung" ergänzt: Claude generiert domänenspezifische CLAUDE.md-Abschnitte nach dem Aufnahmegespräch, bevor der erste Commit entsteht
- Designhinweis 6 entsprechend aktualisiert
## [2026-05-15 14:00] query | Lehrer-Vorlage überarbeitet

- `vorlagen/claude-md-lehrer.md`: Vorlage fachunabhängig gemacht — Coding-spezifische Abschnitte (Code-Regeln, Code-Digest, Skalierung) in optionalen Block verschoben
- `vorlagen/claude-md-lehrer.md`: Sitzungsstruktur von zeitbasiert auf kriterienbasiert umgestellt — Phasen enden wenn das Lernziel erreicht ist, nicht wenn eine Zeitspanne abläuft
- Alle 9 Vorlagen: `.gitignore`-Anweisung um `.claude/` ergänzt
## [2026-05-15 12:30] lint | Nachbesserungen aus Wiki-Prüfung

- `CLAUDE.md`: Wiki-Link-Syntax von `[[…]]` auf Standard-Markdown `[text](pfad.md)` korrigiert (Seitenformat + Workflow-Schritt)
- `CLAUDE.md`: Tag-Regel von starrer Liste auf Zweistufen-System erweitert (Typ-Tag + Themen-Tags)
- `konzepte/llm-wiki-muster.md` ↔ `konzepte/llm-wiki-v2.md`: gegenseitige Querverlinkung hinzugefügt
- `konzepte/enterprise-skalierung.md`: Link zu `kompilierungs-metapher.md` ergänzt
## [2026-05-15 12:00] lint | Wiki-Prüfung

- `werkzeuge/phi-4.md`: fehlendes `**Quellen**`-Feld hinzugefügt
- `werkzeuge/phi-4.md`: Obsidian-Link `[[radeon-ai-pro-r9700]]` → Standard-Markdown `[Radeon AI PRO R9700](radeon-ai-pro-r9700.md)` korrigiert
- Systembefund (nicht auto-behoben): Tags in vielen Dateien weichen von den erlaubten Werten (`konzept, quelle, person, werkzeug, vergleich`) ab — Klärung ausstehend
- Systembefund: CLAUDE.md-Instruktion empfiehlt `[[wiki-links]]`, alle Seiten verwenden aber Standard-Markdown — minimale Inkonsistenz
## [2026-05-15 10:15] query | Ralph Wiggum Plugin für Claude Code

- Neue Werkzeugseite `wiki/werkzeuge/ralph-wiggum-plugin.md` erstellt
- Thema: offizielles Plugin für autonome Iterationsschleifen; Installation, /ralph-loop-Befehle, Wiki-Ingest-Anwendungsfall, Kostentabelle
- `wiki/index.md` aktualisiert

---
## [2026-05-15 10:00] query | Wiki-Zusammenarbeit mit Git und Claude Code

- Neue Anleitungsseite `wiki/anleitungen/zusammenarbeit-git.md` erstellt
- Thema: zwei Personen (GitHub Collaborators), Branch-Workflow, Konflikte in index.md und log.md, Ownership-Konzept
- `wiki/index.md` aktualisiert

---
## [2026-05-15 00:03] ingest | Radeon AI Pro R9700 — Geizhals-Clippings aller Dritthersteller verarbeitet

- Variantenübersicht ergänzt: ASRock (€1.410), PowerColor (€1.419), GIGABYTE (€1.427), Sapphire (€1.475), ASUS Turbo (€1.486), XFX (€1.575)
- Alle Varianten verwenden Blower-Kühler — Klassen-Design, keine Ausnahme
- Verfügbarkeit aktualisiert: Karten sind in Österreich lieferbar
- Hinweis präzisiert: kein axial-gekühltes Modell existiert
## [2026-05-15 00:02] ingest | AMD Radeon AI PRO R9700 — zwei AMD-Clippings verarbeitet

- Specs korrigiert: FP16 Vector 47,8 TFLOPS (war 95 — Geizhals-Fehler), Bandbreite 640 GB/s, Infinity Cache 64 MB ergänzt
- Windows 10/11 offiziell unterstützt (war als experimentell eingetragen)
- AMD-Benchmark-Tabelle ergänzt: R9700 vs. RTX 5080 für 24–32B Modelle (3,6–5× schneller, da 5080 CPU-Offloading benötigt)
## [2026-05-15 00:01] query | Radeon AI Pro R9700 dokumentiert

- Neue Seite `wiki/werkzeuge/radeon-ai-pro-r9700.md` — Specs, Vergleich mit RTX 5080, Eignung für LLM-Inferenz
- Hauptwarnung: Blower-Kühler ungeeignet für Desktop direkt am Schreibtisch
- Fazit: technisch interessant für 33B-Modelle, aber Axial-Variante abwarten
## [2026-05-15] query | Phi-4 für lokalen Einsatz

- Neue Werkzeugseite `werkzeuge/phi-4.md` angelegt: Phi-4-Familie (14B, mini, reasoning, mini-reasoning, multimodal), VRAM-Bedarf, Vergleich mit Qwen3 14B, Ollama-Einrichtung
- `anleitungen/lokale-modelle.md`: Modellübersicht um Phi-4 14B und aktualisierte Phi-4-mini-Zeile erweitert
- `wiki/index.md` aktualisiert
## [2026-05-15 00:00] query | Lehrer-Vorlage: Visualisierungssektion ergänzt

- Neue Sektion `## Visualisierung` in der Lehrer-Vorlage — instruiert die KI, Konzepte aktiv mit ASCII/Unicode-Diagrammen und optional Mermaid zu illustrieren
- Mehrstufig: ASCII/Unicode als Basis (überall), Mermaid als optionale Erweiterung bei unterstützenden Clients (VS Code, Roocode)
- Enthält Wann-Regeln: Programmfluss, Datenstrukturen, Projektstruktur, Zusammenhänge
## [2026-05-14 10:00] query | Zwei Bugs in allen Vorlagen behoben: CLAUDE.md-Rückschreiben + Pygame-Bias

- **Bug 1 (alle 7 Vorlagen)**: `gedanklich als Kontext eintragen` → KI schreibt Antworten aus dem Interview jetzt explizit zurück in CLAUDE.md (jeweils in den richtigen Abschnitt). Ohne diesen Fix gingen alle Interview-Antworten nach einem Neustart verloren.
- **Bug 2 (Lehrer-Vorlage)**: Platzhalter `{{z.B. "Mein erstes Jump'n'Run mit Pygame"}}` durch neutrale Beispiele ersetzt — verhindert, dass das Modell Pygame als Standard-Vorschlag gibt, bevor der Nutzer eine Richtung angegeben hat.
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
## [2026-05-02] ingest | TecAdRise, LLM Wiki v2 (Tamiltech), Enterprise-Wiki (Falconer)

- `wiki/quellen/llm-wiki-tecadrise.md` — TecAdRise.ai: Karpathys Idee als Bewegung, 3 Kritikpunkte (Halluzination, Konsistenz, Architektur), Constrained Pipelines, 3 Community-Projekte
- `wiki/quellen/llm-wiki-v2-tamiltech.md` — Tamiltech/LLM Wiki v2: Produktionserfahrung, typisierte Beziehungen (relationships.json), Git-Hooks, Trust Score
- `wiki/quellen/llm-wiki-enterprise-falconer.md` — Falconer: Enterprise-Skalierung, warum `raw/`-Ordner-Modell nicht skaliert, cross-tool Entity Resolution, automatische Drift-Erkennung
- `wiki/konzepte/community-projekte.md` — neu: Swarm Vault, BrainDB, Menmo Vault
- `wiki/konzepte/llm-wiki-v2.md` — neu: 4 Produktionserweiterungen, relationships.json, Lifecycle-Management
- `wiki/konzepte/enterprise-skalierung.md` — neu: 4 Eigenschaften (Capture/Link/Compound/Stay Current) auf Enterprise-Niveau
- `wiki/konzepte/kontaminierungsrisiko.md` — Constrained Pipelines (Community-Konsens) und Trust Score (v2) ergänzt
- `wiki/konzepte/rag-vs-wiki.md` — v2-Argument (RAG als 2023-Workaround) und Enterprise-Retrieval-Kritik ergänzt
## [2026-05-01 00:00] lint | [[wiki-links]] → Standard-Markdown in index.md

- `wiki/index.md`: Alle 54 Einträge von `[[wiki-link]]` auf `[name](kategorie/seitenname.md)` umgestellt
- Datum `Zuletzt aktualisiert` auf 2026-05-01 gesetzt
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
## [2026-04-23 00:02] query | Vorlage YouTube-Verlauf-Wiki erstellt

- Neue Vorlage `vorlagen/claude-md-youtube-verlauf.md` angelegt
- Spezifisch für YouTube-Verlauf als Quellmaterial: Transkripte, Beschreibungen, Notizen
- Enthält: Extraktionsstrategien nach Video-Typ, spezifisches Seitenformat für Videos/Kanäle/Serien,
  Transkript-Beschaffung (yt-dlp, youtube-transcript-api), alle 6 Abfragetypen,
  Kontaminierungsrisiko-Warnung, Skalierungshinweise (qmd/jdocmunch)
- index.md aktualisiert
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
## [2026-04-22 19:36] ingest | Obsidian + Claude Code (Pillitteri)

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
## [2026-04-22 19:31] ingest | LLM Wiki Tutorial (Mit Mario)

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
## [2026-04-22 19:26] ingest | llm-wiki.md (Karpathy Gist)

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
## [2026-04-22 19:21] init | Wiki-Initialisierung

- Wiki-Grundgerüst angelegt
- `index.md` erstellt (Inhaltsverzeichnis)
- `log.md` erstellt (dieses Protokoll)
- Drei Quellen in `clippings/` identifiziert, Aufnahme steht bevor
