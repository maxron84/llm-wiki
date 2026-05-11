# Die fehlende Metrik: Was kostet eine Seite in Karpathys LLM Wiki?

*Eine ökonomische Analyse mit Ralph als Maßstab*

---

**TL;DR** — Andrej Karpathys LLM Wiki und Geoffrey Huntleys Ralph-Schleife sind im April 2026 zwei der meistdiskutierten Muster der agentischen Praxis. Beide reden über Token-Throughput, aber keiner über die Stückkosten dessen, was am Ende auf der Festplatte landet. Dieser Text führt eine simple, aber überfällige Metrik ein: **Dollar pro Wiki-Seite (US$/WP)**. Mit Sonnet 4.6 auf einer Ralph-artigen Schleife liegt der Bestwert bei rund **0,42 US$ pro Seite**. Bei Opus 4.7 sind es eher 1,80 US$. Die Konsequenz ist nicht nur eine Rechnung, sondern eine Einordnung: ein 400-Artikel-Wiki à la Karpathy kostet ungefähr so viel wie ein gutes Abendessen — und das ändert alles, was wir bisher über persönliche Wissensbasen angenommen haben.

---

## 1. Das Loch in der Mitte

Zwei der einflussreichsten Posts der letzten Monate haben merkwürdig wenig miteinander geredet, obwohl sie thematisch direkt aneinandergrenzen.

Auf der einen Seite **Karpathys LLM Wiki** (April 2026): das LLM kompiliert kuratierte Rohquellen einmalig in ein lebendiges Markdown-Wiki und hält es per Lint-Pass konsistent. Drei Schichten — `raw/`, `wiki/`, `CLAUDE.md` —, drei Operationen — `ingest`, `query`, `lint`. Eleganter Bauplan, viral gegangen, über 16 Millionen Views auf X innerhalb von drei Wochen.

Auf der anderen Seite **Geoffrey Huntleys Ralph-Schleife** (benannt im Januar 2026, breit diskutiert seit Februar): eine deterministisch dumme `while true`-Schleife, die einem Coding-Agenten denselben Prompt wieder und wieder vorlegt, bis dieser eine Completion-Promise emittiert. Huntley nennt dafür eine konkrete Zahl, die in der Branche eingeschlagen ist wie eine Glocke: **10,42 US$ pro Stunde** Laufzeit, wenn Claude Sonnet 4.5 in einer solchen Schleife auf einer GCP-Instanz dreht. Y-Combinator-Teams haben damit über Nacht sechs Repos parallel gebaut; einer hat einen 50.000-Dollar-Vertrag für 297 Dollar API-Kosten ausgeliefert.

Was niemand öffentlich verbunden hat: **Ralph ist nicht nur ein Coding-Muster — es ist die effizienteste Ingest-Engine, die das LLM Wiki je hatte.** Und umgekehrt: das LLM Wiki ist eines der wenigen sinnvollen Anwendungsfelder für Ralph, das nicht „noch ein autonomes Coding-Experiment" ist.

Der Brückenschlag ist überfällig. Und sobald man ihn macht, fällt das eigentliche Loch auf: niemand spricht über Stückkosten. Karpathy nennt seine Wiki-Größe in Worten („~100 Artikel, ~400.000 Wörter"), aber nicht in Dollar. Huntley nennt Stundenraten, aber keine Output-Stückzahlen. Wir wissen also nicht, was eine *Seite* kostet — und das ist genau die Größe, die zählt, sobald man entscheidet, ob man ein Wiki überhaupt anlegt.

## 2. Warum Karpathy die Kostenfrage offenlässt

Karpathys Gist ist eine *Pattern-Beschreibung*, kein Betriebshandbuch. Er schreibt explizit, dass die konkrete Implementierung — Verzeichnisstruktur, Schema-Konventionen, Seitenformate, Tooling — vom Anwender und seinem Agenten gemeinsam ausgearbeitet werden soll. Das ist intellektuell ehrlich, aber ökonomisch lückenhaft. Drei Faktoren fallen unter den Tisch:

**Erstens: Ingest ist nicht atomar.** Eine einzelne Quelle berührt im Schnitt 10 bis 15 Seiten im Wiki. Das ist nicht „eine API-Roundtrip pro Seite", sondern: lesen, mit dem Index abgleichen, mehrere Seiten holen, je nach Ergebnis editieren, Backlinks aktualisieren, Log anhängen. Wer mit Pauschalen wie „Sonnet kostet 3 US$ pro Mio. Input-Tokens" kalkuliert, übersieht den multiplikativen Effekt der Cross-Referenzen.

**Zweitens: Der Lint-Pass ist regelmäßig, nicht einmalig.** Ein gesundes Wiki braucht periodische Konsistenzprüfung — Karpathys eigene Empfehlung. Bei 100 Artikeln und 400.000 Wörtern bedeutet das wöchentlich einen Lesedurchgang von ungefähr 530.000 Tokens, plus Schreiblast für die Korrekturen. Das ist eine *laufende* Kostenstelle, kein Einmalaufwand.

**Drittens: Die „Query-back-fill"-Schleife.** Karpathy empfiehlt, gute Antworten zurück ins Wiki zu schreiben — das ist genau der Compound-Loop, der das Wiki schlauer macht. Aber jede solche Frage ist auch eine Schreiboperation, die ihrerseits Tokens verbraucht.

Im Aggregat heißt das: das Wiki ist *nicht* die billige Alternative zu RAG, als die es oft verkauft wird. Es verschiebt Kosten von der Query-Zeit in die Ingest-Zeit. Ob das günstiger ist, hängt vom Verhältnis Quellen-zu-Queries ab — und genau diese Rechnung führt niemand vor.

## 3. Ralph als ökonomischer Benchmark

Huntleys eigentliche Pointe ist nicht die Bash-Schleife — das ist die Mechanik. Sein eigentlicher Beitrag ist eine **Kostenbenchmark, an der sich agentische Arbeit messen lässt**. Sonnet 4.5 in einer Ralph-Schleife: 10,42 US$ pro Stunde dauerhafter Aktivität, bei vollem Token-Burn ohne Caching-Tricks.

Die Zahl hat zwei Eigenschaften, die sie nützlich machen:

1. **Sie ist konservativ.** Sie nimmt an, dass der Agent ohne Pause schreibt und denkt. In Wirklichkeit warten viele Iterationen auf Tool-Returns, sodass die effektive Rate niedriger liegt. Aber als Obergrenze ist sie sauber.

2. **Sie ist modellunabhängig parametrisierbar.** Mit Opus 4.7 (~3× teurer als Sonnet 4.6) sind es eher 30 US$/h, mit Haiku 4.5 (~⅓ von Sonnet) eher 3,50 US$/h. Die Schleifenmechanik bleibt, nur die Stückkosten ändern sich.

Was an Ralph relevant fürs LLM Wiki ist, sind nicht die Tasks — Migrationen, Refactorings, Test-Generierungen —, sondern die *Architektur*: zustandslose Iterationen, frischer Kontext bei jedem Durchgang, Persistenz über das Dateisystem statt über das Kontextfenster. Genau die Eigenschaften, die Karpathy für `ingest` und `lint` implizit fordert, aber nicht operationalisiert.

Wer ehrlich rechnet, muss zugeben: ohne eine Ralph-artige Schleife ist Karpathys Pattern nur halb implementiert. Die andere Hälfte ist manuelles Anstoßen jeder Ingest-Operation — und das ist menschliche Wartung, also genau das, was das Wiki nicht haben soll.

## 4. Die Synthese: Ralph als Wiki-Worker

Konkret: ein Ralph-Loop, der das `inbox/`-Verzeichnis pollt und neue Quellen autonom ins Wiki einbaut. Hier der minimale Bauplan:

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

Das ist die Mechanik. Die Schleife ist absichtlich primitiv: kein State-Tracking außer dem Dateisystem, kein Retry, kein Branch. Jede Iteration startet mit frischem Kontext, was Huntleys Punkt zur Kontext-Hygiene direkt adressiert — die Wiki-Schemadatei plus die zu ingestierende Quelle plus relevante Index-Einträge passen in jedes moderne Kontextfenster, ohne dass die berüchtigte „Dumb Zone" jenseits 60–70 % Auslastung erreicht wird.

Die ökonomisch entscheidende Eigenschaft: **jede Iteration ist eine messbare Einheit**. Wir wissen, wie viele Tokens reingehen, wir wissen, wie viele rausgehen, und wir wissen, was im Wiki dazukommt. Damit ist die Stückkostenrechnung erstmals möglich.

## 5. Die Metrik: US$ pro Wiki-Seite

Hier die Definition, die ich zur Diskussion stelle:

> **US$/WP = (Input-Token-Kosten + Output-Token-Kosten) / Anzahl neu entstandener oder substanziell geänderter Wiki-Seiten**
>
> wobei „substanziell geändert" ≥ 100 Wörter Delta gegenüber der Vorversion bedeutet (linter-prüfbar via `git diff --stat`).

Die zugrundeliegenden Größen für eine durchschnittliche Ingest-Iteration:

| Komponente | Tokens (Input) | Tokens (Output) |
|---|---|---|
| CLAUDE.md (Schema) | 2.500 | — |
| Quelle (PDF-extrahiert, ~5.000 Wörter) | 7.500 | — |
| Index.md + 12 betroffene Seiten | 18.000 | — |
| LLM-Reasoning + Tool-Calls | 4.000 | 6.000 |
| Updates auf 10–15 Seiten | — | 8.500 |
| Log-Eintrag | — | 500 |
| **Summe pro Iteration** | **32.000** | **15.000** |

Bei Claude Sonnet 4.6 (3 US$/Mio. Input, 15 US$/Mio. Output):

```python
def cost_per_iteration(input_tok, output_tok, model="sonnet-4.6"):
    rates = {
        "sonnet-4.6": (3.00, 15.00),   # USD per million tokens
        "opus-4.7":   (15.00, 75.00),
        "haiku-4.5":  (1.00, 5.00),
    }
    in_rate, out_rate = rates[model]
    cost_in  = (input_tok  / 1_000_000) * in_rate
    cost_out = (output_tok / 1_000_000) * out_rate
    return cost_in + cost_out

# Mit den Werten oben:
sonnet = cost_per_iteration(32000, 15000, "sonnet-4.6")
opus   = cost_per_iteration(32000, 15000, "opus-4.7")
haiku  = cost_per_iteration(32000, 15000, "haiku-4.5")

print(f"Sonnet: ${sonnet:.4f}")  # $0.3210
print(f"Opus:   ${opus:.4f}")    # $1.6050
print(f"Haiku:  ${haiku:.4f}")   # $0.1070
```

Eine Iteration berührt im Karpathy-Schnitt 10–15 Seiten, von denen 7–8 substanziell sind (die Quelle selbst plus 6–7 verlinkte Konzeptseiten). Damit ergibt sich:

| Modell | $/Iteration | Seiten/Iteration | **$/WP** |
|---|---:|---:|---:|
| Haiku 4.5 | $0,11 | 7,5 | **$0,014** |
| Sonnet 4.6 | $0,32 | 7,5 | **$0,043** |
| Opus 4.7 | $1,61 | 7,5 | **$0,214** |

Das sind die nackten Token-Kosten. Was hier *fehlt*, ist die Lint-Last über die Lebenszeit. Karpathys Empfehlung — wöchentlicher Lint-Pass — bei einem 100-Artikel-Wiki bedeutet zusätzlich:

```python
# Wöchentlicher Lint-Pass über 100 Artikel à 4.000 Wörter (~5.300 Tokens)
LINT_INPUT = 100 * 5_300 + 2_500       # alle Seiten + Schema
LINT_OUTPUT = 12_000                    # Korrekturen + Report
weekly_lint_sonnet = cost_per_iteration(LINT_INPUT, LINT_OUTPUT, "sonnet-4.6")
# = $0,000 + $0,18 + $0,000 = $1,78 pro Woche
# ≈ $7,12 pro Monat oder $85/Jahr
```

Wenn man Lint amortisiert auf die durchschnittlich vorhandenen Seiten — sagen wir, 200 stabile Wiki-Pages über ein Jahr —, kommt ein **Lifetime-$/WP** dazu von etwa **0,42 US$**. Bei Sonnet 4.6 ist das die ehrlichste Zahl, die ich derzeit verteidigen würde:

> **0,42 US$ pro substanziell gepflegter Wiki-Seite über ein Jahr Lebenszyklus, bei Sonnet 4.6 in einer Ralph-Schleife.**

Bei Opus 4.7 ist es das Vierfache (~1,80 US$/WP), bei Haiku 4.5 ein Sechstel (~0,07 US$/WP). Das ist die Spanne, in der jede Diskussion über LLM-Wiki-Tauglichkeit geführt werden sollte — nicht in „viel" oder „wenig", sondern in messbaren Stückkosten.

## 6. Drei Szenarien durchgerechnet

Mit der Metrik im Werkzeugkasten lassen sich konkrete Entscheidungen treffen. Drei Beispiele:

### Szenario A: Persönliches Forschungs-Wiki (Karpathys eigener Use-Case)

100 Artikel, 400.000 Wörter, gespeist aus ~70 Quellen über drei Monate.

| Posten | Berechnung | Kosten |
|---|---|---:|
| Initiale Ingestion (Sonnet 4.6) | 70 Quellen × $0,32 | $22,40 |
| Wöchentlicher Lint × 12 Wochen | 12 × $1,78 | $21,36 |
| Query-back-fill (~50 Antworten) | 50 × $0,15 | $7,50 |
| **Gesamt für 3 Monate** | | **$51,26** |

Das ist *weniger als ein Monatsabo* gängiger Notetaking-Tools. Für einen Forscher, der mit dem Wiki tatsächlich arbeitet, ist das selbst auf Stundenlohnbasis vernachlässigbar — die ersten zehn Minuten gesparter Quellensuche zahlen das schon.

### Szenario B: Kleines Team-Wiki (Slack-Threads, Meeting-Transkripte, Tickets)

Ingest aus drei Quellen-Streams, ca. 200 Quellen pro Monat, 6 Monate Laufzeit, 500 Wiki-Seiten am Ende.

| Posten | Berechnung | Kosten |
|---|---|---:|
| Ingest (Sonnet 4.6, 1.200 Quellen) | 1.200 × $0,32 | $384 |
| Lint × 26 Wochen (Wiki wächst) | ~26 × $5 (Durchschnitt) | $130 |
| Queries (~10/Tag × 180 Tage) | 1.800 × $0,08 | $144 |
| **Gesamt für 6 Monate** | | **$658** |

Bei 500 Seiten am Ende ergibt das **~1,32 US$/WP** über die Lebenszeit. Vergleichbar mit den Lizenzkosten einer Confluence-Seat-Lizenz, aber mit dem Unterschied, dass die Wiki-Wartung im Confluence-Fall *zusätzlich* anfällt — als menschliche Arbeitszeit, die hier komplett wegfällt.

### Szenario C: Enterprise-Onboarding-Wiki, Opus-Niveau

5.000 interne Dokumente, hohe Genauigkeitsanforderung (Compliance), Opus 4.7 als Default, Mensch-im-Loop für jede Page-Approval.

| Posten | Berechnung | Kosten |
|---|---|---:|
| Ingest (Opus 4.7, 5.000 Quellen) | 5.000 × $1,61 | $8.050 |
| Lint × 52 Wochen | 52 × $35 | $1.820 |
| Queries (~100/Tag × 250 Werktage) | 25.000 × $0,40 | $10.000 |
| **Jahresgesamt** | | **$19.870** |

Bei geschätzt 3.000 Wiki-Seiten ergibt das **~6,60 US$/WP** über ein Jahr. Das klingt erstmal nach viel — bis man es mit den Kosten eines einzigen menschlichen Knowledge-Managers vergleicht, der in Vollzeit ein Wiki dieser Größe pflegt (≥ 80.000 US$ p.a. fully loaded). Selbst wenn die Genauigkeit nicht gleich ist, ist das Verhältnis ungefähr 1:4.

## 7. Wo das Modell zerfällt

Die Metrik ist nützlich, aber nicht universell. Drei Bruchstellen, die man kennen sollte:

**Hallucination-Cost.** Das US$/WP-Modell zählt produzierte Seiten — nicht *korrekte* Seiten. Wenn 5 % der ingestierten Behauptungen falsch sind und einmal pro Quartal eine teure Halluzination einen Mitarbeiter eine Stunde kostet, kippt die Rechnung. Dagegen helfen explizite Confidence-Scores (siehe LLM Wiki v2 von Rohit Ghumare) und eine harte Lint-Regel, die jede Behauptung an genau eine Quelle bindet. Aber das ist Mehraufwand, der wieder eingerechnet werden muss.

**Context-Window-Klippen.** Sobald das Wiki größer als rund 200.000 Wörter wird, passt der Index nicht mehr trivial in den Schemakontext. Karpathys Pattern setzt implizit voraus, dass das LLM den ganzen Index in einem Rutsch lesen kann. Jenseits dieser Schwelle muss man auf hybride Retrieval-Strategien (BM25 + Vektor + Graph) umsteigen — was die Stückkosten nicht-linear erhöht und die Metrik komplizierter macht.

**Modellwechsel.** Die Kostenrechnung ist auf Modellpreise vom Mai 2026 geeicht. Anthropic hat Sonnet zwischen 4.5 und 4.6 effektiv im Preis halbiert (gemessen am Aufwand pro Task), Google liefert Gemini 3.1 Pro inzwischen mit aggressivem Prompt-Caching, und DeepSeek V4-Flash kommt auf $0,14/$0,28. In zwölf Monaten ist die hier genannte 0,42-US$/WP-Zahl vermutlich um Faktor 3–5 zu hoch. Was sich aber *nicht* ändert, ist die *Form* der Rechnung — und genau deshalb lohnt es sich, die Metrik einzuführen, bevor die Zahlen verfallen.

## 8. Was die Metrik ändert

Wenn das Argument trägt, dann ist US$/WP zur LLM-Wiki-Diskussion das, was Cost-per-Click zur Online-Werbung war: keine besonders tiefe Idee, aber eine, die Streitgespräche von „gut/schlecht" zu „bei welchen Stückkosten lohnt es sich" verschiebt.

Drei praktische Folgerungen:

**Erstens — Wikis als Investitionsentscheidung.** Bisher wurde das LLM Wiki überwiegend als Lifestyle-Tool diskutiert („mein zweites Gehirn"). Mit US$/WP wird es zu einer ROI-Frage: bei welchen Kosten pro Seite ist die Wartung günstiger als das Vergessen? Die Antwort hängt nicht vom Modell ab, sondern vom Marginalwert der Wiederabfrage.

**Zweitens — Ralph wird zur Standardarchitektur.** Eine 0,42-US$/WP-Zahl ist nur erreichbar, wenn die Ingest-Operationen automatisiert ablaufen. Ein Mensch, der jede Quelle einzeln in Claude Code wirft, hat denselben Token-Verbrauch plus die eigene Arbeitszeit. Damit ist die Ralph-Schleife (oder ein Äquivalent: Cron-Job, Anthropic-Plugin, dedizierter Worker) nicht mehr optional, sondern Voraussetzung für die Wirtschaftlichkeit des Patterns.

**Drittens — die Wahl des Modells wird zur Strategiefrage.** Haiku 4.5 schreibt das Wiki für 0,07 US$ pro Seite, aber mit niedrigerer Synthese-Qualität. Opus 4.7 schreibt für 1,80 US$ pro Seite, aber mit deutlich besserer Cross-Reference-Qualität. Dazwischen liegt Sonnet 4.6 als der vermutlich dominante Sweet Spot — aber das ist eine Hypothese, die sich erst empirisch klären lässt, sobald genug Leute die Metrik tatsächlich messen.

Genau das ist der Vorschlag, mit dem ich diesen Text beenden möchte: **wer ein LLM Wiki betreibt, sollte US$/WP loggen.** Das ist nicht aufwendig — der Wert lässt sich aus den jsonl-Logs einer Ralph-Schleife trivial extrahieren —, und es ist die einzige Größe, mit der die nächste Welle der Diskussion geführt werden kann.

Karpathy hat das Muster geliefert. Huntley hat den Stundenbenchmark geliefert. Was fehlt, ist die Stückkostenmetrik. Sie ist nicht spektakulär, aber sie ist überfällig — und sie ist der Punkt, an dem das LLM Wiki aufhört, ein hübsches Konzept zu sein, und anfängt, eine messbare Engineering-Praxis zu werden.

---

*Berechnungen basieren auf Modellpreisen vom Mai 2026 und Karpathys eigenen Größenangaben im Original-Gist. Code und Zahlen sind als Diskussionsgrundlage gedacht, nicht als Produktionsempfehlung. Wer eigene Messwerte sammelt: ich höre zu.*
