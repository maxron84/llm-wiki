---
title: "J. Gravelle — DEV Community Profile"
source: "https://dev.to/jgravelle/a-radical-diet-for-karpathys-token-eating-llm-wiki-59ng"
author:
published: 2026-04-12
created: 2026-04-23
description: "Handsome, with the strength of ten men.  Sick, elderly men.  But still-- I mean, there's TEN of 'em..."
tags:
  - "clippings"
---
## By Friday, Your Token Bill Looks Like a Phone Number

You did everything right.

You read Karpathy’s post. It clicked immediately. Not because it was simple, but because you’ve lived the pain: spending the first 20 minutes of every session re-teaching a model what you already taught it yesterday.

The LLM Wiki idea felt like a jailbreak.

Compiled knowledge.  
Persistent artifacts.  
A second brain that compounds instead of resets.

So you built it.

You stood up the structure:

- `raw/`
- `wiki/`
- `index.md`
- `log.md`

You wrote a schema. You defined ingest. You defined lint. You fed it sources.

And it worked.

The model moved through your wiki like it had memory.  
Cross-references held. Synthesis stuck.  
For the first time in a while, you weren’t rebuilding context—you were building on top of it.

For a moment, you were ahead.

Then the wiki grew.

No alarms. No failure message. Just drag.

Queries got heavier.  
Answers got softer.  
The model started missing things you *knew* were there.

You linted it. Structurally clean.

But something had shifted.

The context window was getting crowded, and `index.md` was quietly becoming the bottleneck.

Here’s the part nobody says out loud in the “RAG is dead” takes:

> **The LLM Wiki doesn’t eliminate token cost. It moves it.**

You traded:

- per-query retrieval cost

for:

- per-session compilation cost

That’s a fantastic trade…

**until the wiki outgrows the window.**

---

## The Shape of the Problem (Before You Even See the Numbers)

You don’t need a benchmark to understand what’s happening.

You just need to see the curve.

[![ ](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fzvyuuhdk13skwxbud43z.png)](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fzvyuuhdk13skwxbud43z.png)

**One approach scales with size.  
The other scales with the answer.**

That’s the entire story.

---

## What Karpathy Actually Proposed (And Why It Hit So Hard)

Most takes flattened this into “RAG killer.”

That’s not what it is.

RAG is:

- stateless
- query-time
- recompute everything, every time

The LLM Wiki flips that:

- do the expensive thinking once (ingest)
- resolve contradictions early
- build cross-references up front
- store the result

Then query the artifact.

That’s not retrieval.

That’s compilation.

> The wiki is a **persistent, compounding artifact**.

And that idea landed because it mirrors how developers already think.

You don’t recompile your entire codebase on every function call.

You compile once.  
You run cheap.

---

## The Architecture (Why This Works at All)

Three layers:

### Raw Sources

Immutable truth:

- PDFs
- repos
- transcripts
- research

### Wiki

LLM-owned markdown:

- synthesized
- structured
- cross-linked

This is what you paid to build.

### Schema

The discipline layer:

- ingest rules
- page structure
- lint behavior

Without it, you don’t have a system.  
You have a pile of files.

---

## The Three Operations That Matter

### Ingest

New source → updates multiple pages (often 10–15)

Expensive? Yes.  
Correct? Also yes.

You’re building connections up front instead of rediscovering them forever.

---

### Query

Ask → route through wiki → optionally write back

Every query improves the system.

---

### Lint

Detect:

- orphan pages
- stale knowledge
- contradictions

Karpathy’s observation holds:

> “The tedious part is the bookkeeping.”

LLMs are extremely good at bookkeeping.

---

## Where It Starts to Crack

The failure mode isn’t theoretical. It’s structural.

### 1\. index.md Becomes a Liability

Navigation assumes:

> load the index → navigate

That works until it doesn’t.

In practice:

- ~50K–100K tokens → starts breaking
- beyond that → unreliable navigation

You either:

- truncate it (lose coverage), or
- load it (lose quality)

---

### 2\. Long Context Isn’t Actually Long

Marketing says million-token windows.

Reality:

- ~200K–300K → quality degradation begins

Symptoms:

- missed links
- weaker synthesis
- subtle drift

Nothing crashes. It just gets worse.

---

### 3\. Maintenance Cost Compounds Too

Each ingest touches multiple pages.

That’s correct behavior.

It also means:

- more tokens
- more updates
- more cost

You built a compounding asset.

You also built a compounding bill.

---

### 4\. The Irony

> You didn’t eliminate retrieval.  
> You postponed it.

At scale, your wiki becomes the corpus.

---

## The Numbers (This Is Where It Gets Real)

From the jDocMunch Wiki benchmark:

- Corpus: 7 pages, 7,449 tokens

## Results -- Full Wiki Baseline (Realistic)

| Query | Baseline | jDocMunch | Saved | Reduction | Ratio |
| --- | --- | --- | --- | --- | --- |
| cross repository dependency tracking | 7,449 | 599 | 6,850 | 92.0% | 12.4x |
| benchmark token reduction measurement | 7,449 | 314 | 7,135 | 95.8% | 23.7x |
| search scoring ranking debug | 7,449 | 344 | 7,105 | 95.4% | 21.7x |
| incremental indexing blob SHA performance | 7,449 | 313 | 7,136 | 95.8% | 23.8x |
| context bundle symbol imports | 7,449 | 304 | 7,145 | 95.9% | 24.5x |
| **Total (5 queries)** | **37,245** | **1,874** | **35,371** | **95.0%** | **19.9x** |

---

## Results -- Single File Baseline (Conservative)

| Query | File | jDocMunch | Saved | Reduction | Ratio |
| --- | --- | --- | --- | --- | --- |
| cross repository dependency tracking | 1,700 | 599 | 1,101 | 64.8% | 2.8x |
| benchmark token reduction measurement | 1,022 | 314 | 708 | 69.3% | 3.3x |
| search scoring ranking debug | 899 | 344 | 555 | 61.7% | 2.6x |
| incremental indexing blob SHA performance | 812 | 313 | 499 | 61.5% | 2.6x |
| context bundle symbol imports | 914 | 304 | 610 | 66.7% | 3.0x |
| **Total (5 queries)** | **5,347** | **1,874** | **3,473** | **65.0%** | **2.9x** |

---

### Visual Reality

```
Full Wiki: █████████████████████████████████████
jDocMunch: ██
```

Same questions.  
Same corpus.

**~95% less context.**

---

## The Money Shot

```
FULL WIKI:     37,245 tokens
jDocMunch:      1,874 tokens

SAVED:         35,371 tokens
REDUCTION:     95.0%
EFFICIENCY:    19.9×
```

That’s not optimization.

That’s a different class of system.

---

## Why This Happens (Not Magic, Just Mechanics)

Baseline A (full wiki): 37,245 tokens across 5 queries  
jDocMunch workflow: 1,874 tokens across 5 queries  
─────────────────────────────  
Saved: 35,371 tokens (95.0%)  
Average ratio: 19.9x

Baseline B (target file): 5,347 tokens across 5 queries  
jDocMunch workflow: 1,874 tokens across 5 queries  
─────────────────────────────  
Saved: 3,473 tokens (65.0%)  
Average ratio: 2.9x

You’re not compressing data.

You’re avoiding loading irrelevant data.

That’s it.

---

## The Structural Shift

Old world:

> cost ∝ wiki size

New world:

> cost ∝ answer complexity

That’s the entire advantage.

---

## The Fix: Stop Loading the Wiki

The mistake isn’t your structure.

It’s your access pattern.

You’re treating the wiki like a document.

It’s not.

It’s a dataset.

---

## Enter jDocMunch (Right Where You Need It)

jDocMunch doesn’t replace the wiki.

It fixes the exact place it breaks.

---

## What It Actually Does

- Parses docs into **sections**
- Stores **byte offsets**
- Enables:
	- `search_sections`
		- `get_section`

Two calls.

No full loads.

---

## Why It Works

| Old | New |
| --- | --- |
| Load index into context | Search index on disk |
| Navigate in prompt | Retrieve exact section |
| Cost scales with size | Cost scales with answer |

---

## Even the “Fair” Comparison Still Wins

Conservative baseline:

```
Single File:   5,347 tokens
jDocMunch:     1,874 tokens

Reduction:     65%
```

Even when you handicap the comparison…

It still wins.

---

## Three-Minute Retrofit

```
pip install jdocmunch-mcp
```

MCP config:

```
{
  "mcpServers": {
    "jdocmunch": {
      "command": "uvx",
      "args": ["jdocmunch-mcp"]
    }
  }
}
```

---

## The Only Behavior Change That Matters

Stop doing this:

> “Read index.md”

Start doing this:

1. `search_sections`
2. `get_section`

---

## The One-Line Mental Model

> Your wiki is not a document.  
> It’s a database.

Stop loading it.  
Start querying it.

---

## Final Thought

Karpathy gave us something real.

Not a tool.  
A pattern.

And it’s a good one.

But it has a ceiling:

- context doesn’t scale
- tokens don’t forgive

If you don’t add structured retrieval:

> your second brain becomes your biggest expense

If you do:

> you keep the compounding upside  
> and kill the runaway token curve

That’s the difference between a clever idea…

and a system that actually survives production.[MongoDB](https://dev.to/mongodb)Promoted

[![Build seamlessly, securely, and flexibly with MongoDB Atlas. Try free.](https://media2.dev.to/dynamic/image/width=775%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fi.imgur.com%2FVYTIlUE.png)](https://www.mongodb.com/cloud/atlas/lp/try3?utm_campaign=display_devto-broad_pl_flighted_atlas_tryatlaslp_prosp_gic-null_ww-all_dev_dv-all_eng_leadgen&utm_source=devto&utm_medium=display&utm_content=runappsanywhere-v1&bb=241242)

## Build seamlessly, securely, and flexibly with MongoDB Atlas. Try free.

MongoDB Atlas lets you build and run modern apps in 125+ regions across AWS, Azure, and Google Cloud. Multi-cloud clusters distribute data seamlessly and auto-failover between providers for high availability and flexibility. Start free!