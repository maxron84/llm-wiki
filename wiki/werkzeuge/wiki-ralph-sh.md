---
date: 2026-05-11
type: werkzeug
tags: [werkzeug, automatisierung]
status: active
---

# wiki-ralph.sh

**Zusammenfassung**: Eine Bash-Implementierung der [Ralph-Schleife](../konzepte/ralph-schleife.md) speziell für Karpathys LLM-Wiki — pollt ein `inbox/`-Verzeichnis, ingestiert neue Quellen autonom über die Claude CLI und schließt mit einem Lint-Pass ab.
**Quellen**: raw/ralph-claude-code-llm-wiki_metrik.md
**Zuletzt aktualisiert**: 2026-05-11

---

## Zweck

Karpathys Ingest-Workflow ist manuell: Quelle ablegen, Claude anstoßen, warten. `wiki-ralph.sh` macht daraus einen autonomen Hintergrundprozess: Quellen in `inbox/` ablegen, Skript starten, Wiki wächst von allein.

Das schließt die zweite Hälfte von Karpathys Muster, die bisher offen blieb: ohne Automatisierung ist das LLM-Wiki ein halbmanuelles System. Mit einer Ralph-Schleife ist es vollständig. (Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

## Das Skript

```bash
#!/usr/bin/env bash
# wiki-ralph.sh — Ralph-style ingest loop for Karpathy's LLM Wiki
# Run as: ./wiki-ralph.sh ~/my-wiki

WIKI_ROOT="${1:-$HOME/wiki}"
MAX_ITER=50
ITER=0

cd "$WIKI_ROOT" || exit 1

while [ $ITER -lt $MAX_ITER ]; do
  # 1. Stop condition: inbox empty?
  COUNT=$(find inbox/ -type f -name "*.md" | wc -l)
  if [ "$COUNT" -eq 0 ]; then
    echo "INBOX_EMPTY — exiting after $ITER iterations"
    break
  fi

  # 2. Pick oldest source
  SRC=$(find inbox/ -type f -name "*.md" -printf '%T+ %p\n' \
        | sort | head -1 | cut -d' ' -f2-)

  echo "[iter $ITER] ingesting: $SRC"

  # 3. Hand off to Claude Code with a deterministic prompt
  claude -p "Read the LLM Wiki schema in CLAUDE.md.
Ingest the source at $SRC into wiki/ following the schema strictly.
Touch every relevant page, update index.md, append to log.md.
When complete, move $SRC to raw/ and emit <promise>INGESTED</promise>." \
    --max-turns 30 \
    --output-format stream-json \
    >> log/ralph-$ITER.jsonl

  ITER=$((ITER + 1))
done

# 4. Final lint pass
echo "Running lint pass..."
claude -p "Run lint on the wiki. Find contradictions, orphans, broken links.
Fix what's safe; flag the rest in log/lint-$(date +%Y%m%d).md." \
  --max-turns 50
```

(Quelle: raw/ralph-claude-code-llm-wiki_metrik.md)

## Designentscheidungen

**Absichtlich primitiv** — Kein State-Tracking außer dem Dateisystem, kein Retry, kein Branch. Komplexität lebt im Agenten, nicht in der Schleife.

**Frischer Kontext pro Iteration** — Jede Ingest-Operation startet ohne Vorwissen aus früheren Läufen. Das hält den Kontext klein und die Antwortqualität hoch — keine „Dumb Zone" durch akkumulierten Kontext.

**FIFO-Reihenfolge** — Älteste Quelle zuerst (`sort` nach Timestamp). Einfach, deterministisch, kein Scheduling-Problem.

**jsonl-Logging** — Jede Iteration schreibt nach `log/ralph-$ITER.jsonl`. Das ermöglicht die nachträgliche Extraktion der [US$/WP-Metrik](../konzepte/usd-pro-wiki-seite.md) aus den Token-Usage-Feldern.

**Completion-Promise** — Der Agent muss `<promise>INGESTED</promise>` emittieren, damit die nächste Iteration startet. Kein stilles Scheitern.

**Lint am Ende** — Nach dem letzten Ingest ein abschließender Lint-Pass, der Widersprüche und Waisen findet.

## Verzeichnisstruktur (erweitert)

```
wiki-root/
├── inbox/          ← Neue Quellen hier ablegen (Eingangskorb)
├── raw/            ← Quellen nach Ingest (unveränderlich)
├── clippings/      ← Manuell gesammelte Quellen (unveränderlich)
├── wiki/           ← Von Claude gepflegte Seiten
├── log/
│   ├── ralph-0.jsonl
│   ├── ralph-1.jsonl
│   └── lint-20260511.md
└── CLAUDE.md
```

## US$/WP aus den Logs extrahieren

```python
import json, glob

total_in, total_out = 0, 0
for f in glob.glob("log/ralph-*.jsonl"):
    for line in open(f):
        ev = json.loads(line)
        if ev.get("type") == "usage":
            total_in  += ev["usage"]["input_tokens"]
            total_out += ev["usage"]["output_tokens"]

cost = (total_in / 1e6) * 3.0 + (total_out / 1e6) * 15.0
print(f"Gesamtkosten: ${cost:.4f}")
# Durch Anzahl substanziell geänderter Seiten teilen → US$/WP
```

## Wann einsetzen

`wiki-ralph.sh` ist sinnvoll, wenn:
- Regelmäßig neue Quellen aufgenommen werden (≥ 2–3 pro Woche)
- Manuelle Einzeloperationen den Ingest-Rhythmus stören
- Die [US$/WP-Metrik](../konzepte/usd-pro-wiki-seite.md) gemessen werden soll

Für gelegentliche Einzelquellen ist manuelles Anstoßen ausreichend.

## Verwandte Seiten

- [ralph-schleife](../konzepte/ralph-schleife.md) — Das zugrunde liegende Muster
- [usd-pro-wiki-seite](../konzepte/usd-pro-wiki-seite.md) — Kostenmetrik aus den Logs
- [ingest-workflow](../konzepte/ingest-workflow.md) — Was das Skript automatisiert
- [fortgeschrittene-architektur](../konzepte/fortgeschrittene-architektur.md) — Einbettung in größere Systeme
- [claude-code](claude-code.md) — Die CLI, die das Skript aufruft
- [geoffrey-huntley](../personen/geoffrey-huntley.md) — Erfinder des Musters

---

[Wiki-Index](../index.md)
