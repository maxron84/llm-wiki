---
date: 2026-08-11
type: konzept
tags: [konzept, vergleich, schema]
status: active
---

# Datenbankwahl für ein LLM-Wiki

**Zusammenfassung**: SQLite, MariaDB, PostgreSQL und DuckDB im Vergleich für den SQL-Betrieb eines LLM-Wikis. Ergebnis: SQLite als Einstieg, PostgreSQL als benannter Migrationspfad wegen der deutschen Volltextsuche, MariaDB erst bei Mehrbenutzerbetrieb, DuckDB scheidet aus.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Der Vergleich

| | SQLite | MariaDB | PostgreSQL | DuckDB |
|---|---|---|---|---|
| Volltext | FTS5/BM25, **kein dt. Stemmer** | InnoDB FULLTEXT, schwächstes Ranking | **`german`-Konfiguration ab Werk** | schwach |
| Vektor (später) | `sqlite-vec` | nativ ab 11.7 | `pgvector` | VSS-Extension |
| Rekursive CTEs | ✅ | ✅ (ab 10.2) | ✅ | ✅ |
| Viele kleine UPDATEs | schnell | schnell | schnell | **langsam (OLAP)** |
| Betrieb | eine Datei | Serverdienst | Serverdienst | eine Datei |
| Backup | `cp wiki.db` | Dump | Dump | `cp` |
| Nebenläufigkeit | ein Schreiber (WAL) | voll | voll | ein Schreiber |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Das Lastprofil bestimmt die Wahl

Ein LLM-Wiki im [SQL-Betrieb](sql-wiki-architektur.md) hat ein sehr spezifisches Lastprofil: viele kleine Einzelupdates auf [Sektionsebene](sektion-als-atom.md), Volltextsuche über einige tausend Sektionen, rekursive Graph-Traversals über die Kantentabelle, genau ein Schreiber. Keine Aggregationen über Millionen Zeilen, keine Nebenläufigkeit, keine Rechteverwaltung.

**DuckDB scheidet aus.** Es ist eine analytische Engine — hervorragend für Aggregationen über Millionen Zeilen, schlechter als SQLite bei vielen kleinen Einzelupdates. Genau das ist aber das Lastprofil eines Wikis.

> „Wer ‚performanter als SQLite' sucht, landet hier auf dem falschen Werkzeug." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

**MariaDB ist die schwächste der drei relationalen Optionen für diesen Fall.** Serverbetrieb ohne kompensierenden Vorteil, und die Volltextsuche ist die schlechteste im Feld. Vertrautheit ist ein echtes Argument — aber SQLite hat so wenig Bedienoberfläche, dass es kaum etwas zu kennen gibt. MariaDB wird relevant, sobald mehrere Personen gleichzeitig schreiben, also beim Team-Wiki. → [claude-md-team](../vorlagen/claude-md-team.md)

**SQLite ist der richtige Einstieg.** Ein einbenutziges, lokales Wiki ist der Fall, für den SQLite gebaut wurde: kein Dienst, kein Port, kein Rechtekonzept, Backup ist eine Dateikopie, FTS5 mit BM25 antwortet bei einigen tausend Sektionen in unter einer Millisekunde. Die Einbenutzer-Schreibbeschränkung ist keine, wenn genau ein Agent schreibt. → [sqlite](../werkzeuge/sqlite.md)

**PostgreSQL ist der benannte Migrationspfad**, und der Auslöser ist die [deutsche Volltextsuche](deutsche-volltextsuche.md) — nicht Größe, nicht Geschwindigkeit. → [postgresql](../werkzeuge/postgresql.md)

## Ein benannter Auslöser statt eines Gefühls

Der bemerkenswerte Teil der Empfehlung ist nicht die Reihenfolge, sondern dass der Wechselpunkt konkret ist:

> „Solange BM25 über `unicode61` plus Trigram-Fallback ausreicht, bleibt SQLite. Sobald Suchtreffer erkennbar an Flexion und Komposita scheitern — und das ist bei einem rein deutschen Wiki eine Frage der Zeit, nicht des Ob —, ist der Wechsel begründet." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Das unterscheidet die Entscheidung von der üblichen Datenbankwahl, bei der „für später gerüstet sein" zur vorzeitigen Serverinstallation führt. Vorbereitung heißt hier nur: kein SQLite-Exotisches im [Schema](wiki-datenbankschema.md), `INTEGER PRIMARY KEY` statt `ROWID`-Tricks, Datumsangaben als ISO-8601-Text, keine dynamische Typisierung ausnutzen. Dann ist die Migration ein Dump plus Anpassung der FTS-Schicht.

## Verwandte Seiten

- [sql-wiki-architektur](sql-wiki-architektur.md) — Der Rahmen der Entscheidung
- [deutsche-volltextsuche](deutsche-volltextsuche.md) — Der benannte Auslöser für die Migration
- [wiki-datenbankschema](wiki-datenbankschema.md) — Portabel gehaltener Entwurf
- [sektion-als-atom](sektion-als-atom.md) — Das Lastprofil: viele kleine Updates
- [sqlite](../werkzeuge/sqlite.md) — Der Einstieg
- [postgresql](../werkzeuge/postgresql.md) — Das Migrationsziel
- [enterprise-skalierung](enterprise-skalierung.md) — Wo Mehrbenutzerbetrieb zum Thema wird
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
