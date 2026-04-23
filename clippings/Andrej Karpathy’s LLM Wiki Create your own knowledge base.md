---
title: "Andrej Karpathy’s LLM Wiki: Create your own knowledge base"
source: "https://medium.com/@urvvil08/andrej-karpathys-llm-wiki-create-your-own-knowledge-base-8779014accd5"
author:
  - "[[Urvil Joshi]]"
published: 2026-04-20
created: 2026-04-23
description: "Andrej Karpathy tweeted something that quietly broke the AI community’s understanding of how we should be using LLMs to manage knowledge."
tags:
  - "clippings"
---
Andrej Karpathy [**tweeted**](https://x.com/karpathy/status/2039805659525644595) something that quietly broke the AI community’s understanding of how we should be using LLMs to manage knowledge.

Two days later, he followed up with a GitHub gist called [**llm-wiki.md**](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). The idea isn’t a product. It’s not code. It’s a *pattern* a special one that might make will help you create a small scale personal knowledge base in few minutes.

Let’s break this down.

## 🍥The Tweet That Started It

Karpathy’s original tweet:

> “Something I’m finding very useful recently: using LLMs to build personal knowledge bases for various topics of research interest. In this way, a large fraction of my recent token throughput is going less into manipulating code, and more into manipulating…”
> 
> *— @karpathy, April 2, 2026*

And that’s what he published a single markdown file on GitHub Gist. Something he calls an **idea file**: a document meant to be copy-pasted into an LLM agent like Claude Code, OpenAI Codex or any agent, where *your* agent then instantiates the pattern for *your* specific needs.

## ✨The Core Idea: Stop Retrieving. Start Compiling.

Here’s the insight in one sentence: **instead of having the LLM re-read your raw documents every time you ask a question, build a persistent, structured wiki once and keep it updated forever.**

Karpathy used an analogy from software engineering: **compilation**.

```c
┌─────────────────────────────────────────────────────────────┐
│                  SOFTWARE ENGINEERING                       │
│                                                             │
│     Source Code  ──[ compile once ]──►  Binary              │
│     (readable)                          (runs fast every    │
│                                          single call)       │
└─────────────────────────────────────────────────────────────┘
                          ⇕  same idea  ⇕
┌─────────────────────────────────────────────────────────────┐
│                      LLM WIKI                               │
│                                                             │
│     Raw Sources  ──[ LLM compiles ]──►  Wiki                │
│     (PDFs, notes,                       (pre-synthesized,   │
│      articles)                           interlinked,       │
│                                          always ready)      │
└─────────────────────────────────────────────────────────────┘
```

You don’t execute source code every time you want to run a program. You compile it once into a binary and run *that*. Karpathy says: treat knowledge the same way. Your PDFs and notes are the source code. The wiki is the binary.

Every time you add a new document, the LLM doesn’t just index it. It **reads it, extracts the key information, updates existing pages, revises summaries, flags contradictions, and strengthens cross-links**. The wiki is a persistent, compounding artifact.

In Karpathy’s own words, the line that captures the whole philosophy:

> “Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.”

You rarely write the wiki yourself. You curate sources, ask questions, and think. The LLM handles the whole work summarizing, cross-referencing, filing, and bookkeeping.

## 🔍The Three-Layer Architecture

```c
╔══════════════════════════════════════════════════════════════╗
║                   LAYER 3 — THE SCHEMA                       ║
║                    (CLAUDE.md / AGENTS.md)                   ║
║                                                              ║
║   Rules • Conventions • Workflows • How to ingest/query     ║
║                                                              ║
║             ↕  tells the LLM HOW to behave                  ║
╠══════════════════════════════════════════════════════════════╣
║                   LAYER 2 — THE WIKI                         ║
║                 (LLM owns this entirely)                     ║
║                                                              ║
║   ┌──────────┐  ┌──────────┐  ┌──────────┐                  ║
║   │ Entity   │──│ Concept  │──│ Overview │   index.md       ║
║   │ pages    │  │ pages    │  │ pages    │   log.md         ║
║   └──────────┘  └──────────┘  └──────────┘                  ║
║       ↑ LLM creates, links, updates, maintains              ║
╠══════════════════════════════════════════════════════════════╣
║                 LAYER 1 — RAW SOURCES                        ║
║                      (IMMUTABLE)                             ║
║                                                              ║
║    📄 PDFs     📰 Articles    🎧 Podcast notes    🖼️ Images ║
║                                                              ║
║         LLM reads • NEVER modifies • source of truth         ║
╚══════════════════════════════════════════════════════════════╝
```

**Layer 1 — Raw sources.** Your curated collection. Articles, papers, meeting notes, images. Immutable. The LLM reads them but *never* modifies them. This is your ground truth. The fact that they’re immutable is a deliberate design choice: you can always re-compile the wiki from scratch if needed.

**Layer 2 — The wiki.** A directory of markdown files the LLM owns completely. Entity pages, concept pages, summaries, an index, a log. You read it. The LLM writes it.

**Layer 3 — The schema.** This is a CLAUDE.md (for Claude Code) or AGENTS.md (for Codex) file. It’s the config that turns a generic agent into a *disciplined wiki maintainer*. It defines how pages are structured, how new sources get ingested, how answers get formatted.

## 🧰The Three Operations

```c
┌──────────────────────┐
                 │      YOU (Human)     │
                 │   curates & asks     │
                 └──────────┬───────────┘
                            │
       ┌────────────────────┼────────────────────┐
       │                    │                    │
       ▼                    ▼                    ▼
┌────────────┐       ┌────────────┐       ┌────────────┐
│ 1. INGEST  │       │  2. QUERY  │       │  3. LINT   │
├────────────┤       ├────────────┤       ├────────────┤
│ Drop new   │       │ Ask a      │       │ Health-    │
│ source →   │       │ question → │       │ check wiki │
│ LLM reads, │       │ LLM reads  │       │ → find     │
│ summarises,│       │ wiki &     │       │ contra-    │
│ updates    │       │ synthesises│       │ dictions,  │
│ 10–15 wiki │       │ answer     │       │ orphans,   │
│ pages      │       │ w/ cites   │       │ stale data │
└─────┬──────┘       └─────┬──────┘       └─────┬──────┘
      │                    │                    │
      └────────────────────┴────────────────────┘
                           │
                           ▼
                 ┌──────────────────────┐
                 │   WIKI COMPOUNDS     │
                 │  (every op makes it  │
                 │     richer over time)│
                 └──────────────────────┘
```

**Ingest.** You drop a source into the raw folder. The LLM reads it, writes a summary page, and touches some related pages updating, cross-linking, flagging contradictions. A single article becomes a web of updates across your entire knowledge base.

**Query.** You ask a question. The LLM doesn’t search raw documents it reads the already synthesized wiki and answers. And here’s the compounding trick: **good answers can be filed back into the wiki as new pages**. Your explorations become permanent knowledge.

**Lint.** Periodically, you ask the LLM to audit the whole wiki. Find contradictions. Find orphan pages with no links pointing in. Find concepts that are mentioned but missing their own page. The wiki stays healthy because the LLM does the maintenance no human ever wants to do.

## ✨Let’s Actually Build One

Let’s build a working LLM Wiki together.

### What you need

1. **Claude Code** (or OpenAI Codex, or any agent) the brain
2. **Obsidian** (free, [obsidian.md](https://obsidian.md/)) — the viewer
3. A folder on your computer — your vault

### Step 1: Create the folder structure

Open your terminal:

bash

```c
mkdir llm-wiki-demo && cd llm-wiki-demo
mkdir raw
```

You now have:

```c
llm-wiki-demo/
├── raw/         (your immutable sources go here)
```

### Step 2: Open Claude Code in that folder, and paste this single message

> “I want you to read this idea file by Andrej Karpathy and help me set up an LLM Wiki in this directory. Before you do anything, ask me what this wiki will be about, and what sources I plan to feed it. Once I answer, write me a CLAUDE.md schema file based on my answer”.

paste the full contents of [Karpathy’s original gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) here

### Step 3: Claude will respond with some clarifying questions

Claude will respond with a few clarifying questions like:

- What topic will this wiki cover?
- What kinds of sources will you feed it?
- Roughly how many sources are you planning to ingest?
- What page types do you want?

### Step 4: Answer honestly

For this demo, I’m building a wiki about **AI and the philosophy of software**. My answer:

> “The wiki covers AI research and the philosophy of software. I’ll feed it short essays and blog posts from people like Rich Sutton and Andrej Karpathy. Probably 10–20 sources. I want concept pages, essay summaries, and author pages.”

Claude will now write a `CLAUDE.md` file tailored to that use case, initialize `wiki/index.md` and `wiki/log.md`, and say something like *"Ready to ingest your first source."*

## Get Urvil Joshi’s stories in your inbox

Join Medium for free to get updates from this writer.

You just built the whole schema without writing a line of code. That’s Karpathy’s pattern working exactly as intended.

### Step 5: Ingest sources

For my demo I have two sources

**#1 Rich Sutton’s “The Bitter Lesson”**

Drop Rich Sutton’s “The Bitter Lesson” into `raw/` as `bitter-lesson.pdf`.

Tell Claude:

> “Ingest `raw/bitter-lesson.pdf`."

Watch what happens. Claude reads the 2-page essay and generates something like:

```c
wiki/
├── index.md                    (updated)
├── log.md                      (new entry appended)
├── sources/
│   └── bitter-lesson.md        (summary page)
├── concepts/
│   ├── search.md
│   ├── learning.md
│   ├── moores-law.md
│   ├── general-methods.md
│   └── human-knowledge-approaches.md
├── examples/
│   ├── computer-chess.md
│   ├── computer-go.md
│   ├── speech-recognition.md
│   └── computer-vision.md
└── people/
    └── rich-sutton.md
```

One 2-page PDF just became ~10 interlinked pages. Each page cross-references the others with Obsidian-style `[[wikilinks]]`.

**#2 — Karpathy’s “Software 2.0”**

Drop **Karpathy’s “Software 2.0”** into `raw/` as `*software-2-0.pdf*`.

Tell Claude:

> “Ingest `raw/software-2-0.pdf`."

Claude doesn’t start from scratch. It reads your existing wiki first, recognizes that Karpathy’s “Software 2.0” essay is arguing something closely related to the Bitter Lesson, and does something remarkable: it **updates the existing pages** to add Karpathy’s framing, strengthens the cross-references, and creates new pages only where needed.

The `software-2-0.md` page now includes a `[[bitter-lesson]]` backlink because the LLM detected the conceptual connection between the two essays a link *no human added*.

**Your wiki got denser, not just bigger.** This is the compounding property Karpathy is pointing at.

### Step 6: Ask a synthesis question

Now the payoff:

> “How do Sutton and Karpathy agree about the future of software, and where might they disagree?”

Claude doesn’t reopen the PDFs. It reads the two wiki pages you just built, follows the `[[links]]` between them, and gives you a grounded cross-author synthesis in seconds. That answer which draws on connections that didn't exist 60 seconds ago is now a file sitting in your vault forever.

This is what Karpathy means when he says knowledge *compounds*.

### Step 7: Open Obsidian and point it at the folder

Install [Obsidian](https://obsidian.md/), create a new vault, point it at your `llm-wiki-demo/` folder, and hit the **graph view**.

You’re now looking at your knowledge as a network. Nodes are pages. Edges are the links Claude added automatically. Every source you add makes the graph denser.

That moment when the graph renders for the first time is when most people get it.

## 🔍RAG vs LLM Wiki: The Honest Comparison

The question everyone asks: is this actually better than RAG?

Honest answer: **neither wins. They solve different problems.**

```c
┌─────────────────────────────────┬─────────────────────────────────┐
│            RAG                  │          LLM WIKI               │
├─────────────────────────────────┼─────────────────────────────────┤
│                                 │                                 │
│  📄 Raw docs stay raw           │  📄 Raw docs compiled into      │
│                                 │     structured wiki pages       │
│                                 │                                 │
│  🔍 Retrieves chunks per query  │  📖 Reads pre-synthesized pages │
│                                 │                                 │
│  🔁 Stateless — every query     │  📈 Stateful — knowledge        │
│     starts from scratch         │     compounds over time         │
│                                 │                                 │
│  🧩 Answers assembled from      │  🔗 Answers drawn from already- │
│     fragments at runtime        │     connected concepts          │
│                                 │                                 │
│  🕒 Cheap per query             │  💰 Expensive ingest,           │
│                                 │     cheap query                 │
│                                 │                                 │
│  ✅ Perfect traceability to     │  ⚠️  Answers 1–2 steps removed  │
│     source (which chunk?)       │     from raw source             │
│                                 │                                 │
│  ❌ No cross-time synthesis     │  ✅ Links March article to      │
│                                 │     October article naturally   │
│                                 │                                 │
│  ✅ Fresh data always re-read   │  ⚠️  Updates require re-ingest  │
│                                 │                                 │
│  ✅ Hallucinations stay local   │  ⚠️  Hallucinations can get     │
│     to one answer               │     baked in as "facts"         │
│                                 │                                 │
│  🎯 Best for: large, changing   │  🎯 Best for: ~100–500 curated  │
│     corpora, fact lookup,       │     sources, research projects, │
│     millions of docs            │     personal knowledge, books   │
│                                 │                                 │
└─────────────────────────────────┴─────────────────────────────────┘
```

**RAG** is great when you have millions of documents that change constantly and you need precise citations to an exact chunk. Think customer support, legal search, enterprise fact lookup.

**LLM Wiki** is great when you have a bounded, curated corpus maybe a few hundred sources on a topic you’re going deep on. Research projects. A book you’re studying. A course you’re taking. Your own journal. Situations where **synthesis matters more than retrieval** where the valuable answers require connecting five sources, not looking up one.

There’s a real critique of the LLM Wiki pattern worth taking seriously: because the LLM summarizes and compresses sources into wiki pages, there’s a risk of hallucinations getting baked in as *“facts.”* With pure RAG, a wrong answer is just one wrong answer. With an LLM Wiki, a small misunderstanding can quietly propagate across linked pages.

That’s why Karpathy emphasizes the **lint** step periodic audits and why any serious implementation should spot-check generated pages against raw sources.

## 🧰Why This Actually Matters

It’s not really about wikis. Karpathy is pointing at something much older a 1945 vision by Vannevar Bush called the **Memex**: a personal, curated knowledge store where the *connections between documents* are as valuable as the documents themselves.

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*uoqtXXIOw9wbq-PQ5P700Q.png)

Bush’s vision was closer to this than to what the web became: private, actively curated, with associative trails between ideas. The reason the Memex was never really built isn’t technical. It’s that nobody wants to do the *bookkeeping* updating cross-references, keeping summaries current, noting when new data contradicts old claims.

As Karpathy writes in the gist:

> “The tedious part of maintaining a knowledge base is not the reading or the thinking it’s the bookkeeping. Humans abandon wikis because the maintenance burden grows faster than the value. LLMs don’t get bored, don’t forget to update a cross-reference, and can touch 15 files in one pass.”

**The tedious part of knowledge is finally solved.**

Your job shifts from *filing* to *thinking*. From *organizing* to *curating*. From *searching* to *asking better questions*. The LLM handles everything else.

## 🎗️Reference

- **Karpathy’s Tweet:** [https://x.com/karpathy/status/2039805659525644595](https://x.com/karpathy/status/2039805659525644595)
- **Karpathy’s original gist:** [gist.github.com/karpathy/442a6bf555914893e9891c11519de94f](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
- **Claude Code:** [claude.com/claude-code](https://claude.com/claude-code)
- **Obsidian:** [obsidian.md](https://obsidian.md/)
- **Demo source 1 — Sutton’s “The Bitter Lesson”:** [incompleteideas.net/IncIdeas/BitterLesson.html](http://www.incompleteideas.net/IncIdeas/BitterLesson.html)
- **Demo source 2 — Karpathy’s “Software 2.0”:** [karpathy.medium.com/software-2–0-a64152b37c35](https://karpathy.medium.com/software-2-0-a64152b37c35)
- **Karpathy’s LLM Wiki Changes Everything:** [https://youtu.be/04z2M\_Nv\_Rk](https://youtu.be/04z2M_Nv_Rk)