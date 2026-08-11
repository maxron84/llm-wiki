---
date: 2026-08-11
type: konzept
tags: [konzept, skalierung, lokale-modelle]
status: active
---

# Engpass A vs. Engpass B: Wiki-Größe und Session-Kapazität

**Zusammenfassung**: Zwei völlig verschiedene Skalierungsprobleme äußern sich beide als „Kontextfenster voll" und werden deshalb ständig verwechselt. Wiki-Größe tritt bei Claude ab ~300 Seiten auf, Session-Kapazität lokal ab Seite 1. Die Unterscheidung entscheidet darüber, wann ein SQL-Umbau begründet ist.
**Quellen**: raw/sqlwiki_lokalesmodell_architektur.md
**Zuletzt aktualisiert**: 2026-08-11

---

## Die Verwechslung

Die Diskussion um Skalierungsgrenzen des LLM-Wiki-Musters vermischt zwei Probleme, weil beide sich mit demselben Symptom melden. (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

**Engpass A — Wiki-Größe.** Das Wiki wächst über das, was ein Modell in einem Rutsch lesen kann. Die dokumentierten Schwellen: unter 50K Token unauffällig, ab 50–100K wird `index.md` zum Navigationsflaschenhals, ab 200–300K beginnt Qualitätsdegradierung, jenseits davon ist das Wiki selbst ein neues Retrieval-Problem. → [skalierungsgrenzen](skalierungsgrenzen.md)

**Engpass B — Session-Kapazität.** Das Modell schafft eine einzelne Operation nicht, unabhängig davon, wie groß das Wiki insgesamt ist. Ein Ingest braucht System-Prompt plus Schema-Datei plus Quelldokument plus gelesene Verweisseiten — dokumentiert sind 18–42k Token, bevor überhaupt eine Zeile geschrieben ist. Auf einem 14B-Modell mit nominell 40k Fenster beginnt die Latenz ab ~20–25k quadratisch zu steigen.

> „Ein Wiki mit 30 Seiten überfordert das Modell dann genauso wie eines mit 3.000." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

## Gegenüberstellung

| | Engpass A (Größe) | Engpass B (Session) |
|---|---|---|
| Auslöser | Anzahl Seiten | Größe einer Operation |
| Tritt auf bei | Claude ab ~300 Seiten | Lokal ab Seite 1 |
| Lösung durch SQL | Ja — Abfrage statt Laden | Ja — kleine, dichte Kontexte |
| Alternative Lösung | Größeres Modell, mehr Kontext | Keine, außer besserem Modell |

(Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Der entscheidende Unterschied steht in der letzten Zeile. Gegen Engpass A hilft schlicht ein Modell mit größerem Kontextfenster — Claude löst ihn, indem es 200k Token liest. Gegen Engpass B gibt es keine Alternative, weil die Grenze nicht am nominellen Fenster hängt, sondern an der quadratischen Attention: Mehr VRAM verschiebt sie nicht. → [kv-cache-rechnung](kv-cache-rechnung.md)

## Der Fehlschluss

Ein Wiki mit ~120 Seiten liegt nach den Schwellenwerten von Engpass A klar im Bereich „direkt laden". Wer nur A im Blick hat, kommt daher zu dem Schluss, ein SQL-Umbau sei verfrüht.

> „Bei lokalem Betrieb ist Engpass B von Anfang an aktiv, und SQL ist die einzige Architektur, die ihn adressiert, ohne die Hardware zu wechseln." (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Das ist der Grund, warum die [SQL-Wiki-Architektur](sql-wiki-architektur.md) nicht mit Wachstum begründet wird. Wachstum ist eine richtige, aber zu spät greifende Begründung.

## Woran das Modell konkret scheitert

Bevor man eine Architektur um ein schwaches Modell herum baut, muss man präzise benennen, woran es scheitert. Die dokumentierten Fehlerbilder lokaler Modelle im agentischen Betrieb: (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

1. **Kontext zu klein für den System-Prompt.** Werkzeugdefinitionen werden abgeschnitten, das Modell sieht seine eigenen Werkzeuge nicht vollständig. → [roocode-system-prompt-optimierung](roocode-system-prompt-optimierung.md)
2. **Tool-Call-Format nicht getroffen.** Statt strukturierter `tool_calls` kommt Prosa oder JSON-als-Text. Ab Roo Code 3.54.0 ist der XML-Fallback entfernt, natives Function Calling also Pflicht. → [tool-use-lokale-modelle](tool-use-lokale-modelle.md)
3. **Fehler mit wachsendem Kontext.** Auch funktionierende Modelle produzieren mit steigender Iterationszahl falsche Dateipfade, fehlende Parameter, abgebrochene Diffs.
4. **Lange Ausgaben brechen ab.** `num_predict` schneidet Dateien mitten im Inhalt ab.

Punkte 3 und 4 sind für die Architekturfrage die wichtigsten, weil sie dieselbe Ursache haben: **Die Ausgabe ist zu lang und zu unstrukturiert.** Eine Markdown-Seite mit 300 Zeilen komplett neu auszugeben, nur um einen Absatz zu ändern, ist genau die Operation, bei der ein 14B-Modell die Frontmatter verliert, einen Abschnitt vergisst oder mitten im Satz endet.

Daraus folgt die Leitfrage für den Entwurf:

> „Wie macht man jede einzelne Schreiboperation klein genug, dass ein schwaches Modell sie zuverlässig schafft?" (Quelle: raw/sqlwiki_lokalesmodell_architektur.md)

Die Antwort ist nicht „bessere Prompts", sondern eine Datenstruktur, in der kleine Schreiboperationen überhaupt möglich sind. → [sektion-als-atom](sektion-als-atom.md)

## Verwandte Seiten

- [skalierungsgrenzen](skalierungsgrenzen.md) — Die Schwellenwerte von Engpass A
- [sql-wiki-architektur](sql-wiki-architektur.md) — Die Antwort auf Engpass B
- [kv-cache-rechnung](kv-cache-rechnung.md) — Warum mehr VRAM Engpass B nicht verschiebt
- [sektion-als-atom](sektion-als-atom.md) — Kleine Schreiboperationen als Datenstruktur
- [tool-use-lokale-modelle](tool-use-lokale-modelle.md) — Fehlerbilder beim Werkzeugaufruf
- [ollama-kontextfenster](ollama-kontextfenster.md) — Kontextlimits und Latenzdegradation
- [ingest-workflow](ingest-workflow.md) — Die Operation, die lokal am ehesten scheitert
- [sqlwiki-lokalesmodell-architektur](../quellen/sqlwiki-lokalesmodell-architektur.md) — Die Quelle

---

[Wiki-Index](../index.md)
