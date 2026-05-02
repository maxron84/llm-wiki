---
title: "LLM Wiki v2: Extending Karpathy's Pattern with Production Lessons"
source: "https://tamiltech.in/article/llm-wiki-v2-extending-karpathy-pattern"
author:
  - "[[Tamiltech]]"
published: 2026-04-27
created: 2026-05-02
description: "LLM Wiki v2 extends Karpathy's viral pattern with lifecycle management, typed relationships, and automation. The post-RAG approach for production AI."
tags:
  - "clippings"
---
When Andrej Karpathy published his "LLM Wiki" Gist in early April 2026, it crossed **16 million views in three weeks** — extraordinary numbers for what was, on the surface, a folder structure. The pattern was simple: maintain a structured wiki of context, decisions, and entity descriptions that you feed to an LLM at the start of every session, instead of trying to RAG-search a sprawling knowledge base in real time.

The pattern resonated because it solved a problem developers had been quietly struggling with: **RAG systems that look impressive in demos but rot in production**. Two weeks later, on April 26, the developer community published **LLM Wiki v2** — a Gist that extends Karpathy's pattern with hard-won lessons from running this approach in production agent systems. Within 24 hours it had 985 stars and 143 forks.

For Indian developers building AI features into existing products, this is the most practically useful AI engineering content of the month. Here's what v2 actually adds.

## What Karpathy's Original Pattern Said

The original LLM Wiki Gist proposed treating your codebase context like a Wikipedia for your LLM:

- A folder of `.md` files, one per important entity (classes, modules, decisions, people, terminology)
- Cross-linked using simple `[[double brackets]]`
- Loaded into the LLM's context at the start of every coding session
- Updated by humans (and the LLM itself) as the system evolves

Why it took off: it's the opposite of RAG. Instead of hoping retrieval finds the right chunk, you give the LLM the entire relevant context up front. With 1M-token context [windows](https://tamiltech.in/article/nsa-anthropic-mythos-microsoft-vulnerabilities "NSA Using Anthropic’s Mythos to Probe Microsoft and Other Software – What It Means for Indian Users") now standard ([DeepSeek](https://tamiltech.in/article/deepseek-v4-open-source-code-insane "DeepSeek V4 + Open Source Code is Insane: 1.6T Parameters, MIT License, and a 1/20th Price Tag") V4, GPT-5.5, Qwen 3.6), this is finally affordable.

## What v2 Identified as Broken

The v2 authors ran the pattern at scale (their "agentmemory" project is a persistent memory engine for [AI coding](https://tamiltech.in/article/microsoft-missed-cursor-and-copilot-push "Microsoft’s Missed Shot at Cursor and the Push to Make Copilot the New GitHub Hero") agents) and documented exactly what fails:

### 1\. Wikis Rot — Fast

Without lifecycle management, a wiki accumulates stale entries that contradict current code. The LLM, being agreeable, follows the wiki. Bugs ensue. v2 prescribes **explicit "freshness" metadata** per entry: when last verified, by whom, against what version of the code.

### 2\. Cross-Links Become Junk

Karpathy's `[[double brackets]]` work for 50 entries. At 500 they become unmaintainable orphan-link sprawl. v2 introduces **typed relationships** — "depends-on", "supersedes", "owned-by", "implements" — borrowed from semantic web and ontology engineering. The LLM can then reason about graph traversal, not just keyword match.

### 3\. Humans Stop Updating It

The single biggest reason wikis die: nobody wants to maintain them. v2 proposes **[automation](https://tamiltech.in/article/openclaw-4-24-changed-ai-agents-forever "OpenClaw 2026.4.24 Just Changed AI Agents Forever: Voice Calls, Google Meet, DeepSeek V4 Bundled") hooks** — git pre-commit hooks that prompt the dev "you changed this class, update its wiki entry?", and LLM-generated drafts the human only has to approve.

### 4\. Quality Erodes Silently

v2 adds **quality controls** — periodic audit prompts where the LLM is asked to find contradictions in the wiki itself, plus a "trust score" per entry that decays over time without verification.

## The Practical Architecture

v2 spells out the file structure in concrete terms:

```
llm-wiki/
├── _meta/
│   ├── conventions.md       # how this wiki is organised
│   ├── ontology.md          # the typed relationships
│   └── audit-log.md         # what was checked when
├── entities/
│   ├── classes/
│   ├── modules/
│   ├── people/
│   └── decisions/
├── relationships.json       # the graph layer
└── README.md                # entry point loaded first
```

The `relationships.json` file is the v2-specific innovation. It's a flat JSON array of typed edges — when an LLM is loaded, it gets the structured graph alongside the markdown content, letting it reason about "what depends on what" without text-pattern matching.

## Why This Matters for Indian AI Developers

India's AI developer scene has gone through three phases in 18 months:

1. **Phase 1 (mid-2025)**: Everyone bolted RAG onto everything
2. **Phase 2 (early 2026)**: RAG started visibly underperforming as scale grew
3. **Phase 3 (now)**: Long-context LLMs + structured wikis are emerging as the production-grade alternative

For Bengaluru/Hyderabad SaaS teams shipping AI copilots into legal-tech, fin-tech, healthtech — the LLM Wiki pattern is the missing piece between "demo works on small dataset" and "agent actually understands our system in production." v2's lifecycle and quality patterns are what make it deployable.

## How to Adopt v2 in an Existing Project

If you're running a [Laravel](https://tamiltech.in/article/laravel-13-routing-complete-guide-parameters-groups-named-routes-model-binding "Laravel 13 Routing Complete Guide: Routes, Parameters, Groups, Named Routes, Route Model Binding &amp; Resource Controllers") /Django/FastAPI codebase and want to try this:

1. **Start with 5 entries** — your top 5 domain entities (User, Order, Payment, etc.). Don't try to wiki the whole codebase day 1.
2. **Auto-generate v1 with an LLM** — use Claude or DeepSeek to walk your code and produce initial drafts. Human-review each one.
3. **Add the relationships.json** — list the explicit dependencies between your 5 entries. Use the v2-suggested types (depends-on, implements, supersedes).
4. **Wire the git pre-commit hook** — the v2 Gist includes a sample bash hook that diffs changed files against the wiki and prompts for updates.
5. **Set a freshness audit cadence** — weekly job that asks an LLM to flag stale entries based on git activity vs wiki last-updated-at.
6. **Iterate** — add new entities only when an LLM session noticeably benefits.

Total setup time: half a day for a small codebase. The cost is in the discipline, not the tooling.

## What v2 Still Doesn't Solve

Honest limitations the v2 authors acknowledge:

- **Multi-language wikis** — currently English-only assumption. Indian polyglot codebases (Tamil/Hindi domain terms in English code) need a translation layer not yet specified.
- **Wiki for non-code knowledge** — works well for code; less clear how to adapt for organisational knowledge (HR policies, customer history, etc.)
- **Multi-agent contention** — when 3 agents simultaneously try to update the wiki, you need locking or conflict resolution. v2 hand-waves this.
- **Cost at scale** — loading 50KB of wiki context into every LLM call adds up. Pricing analysis for a 10M-call/month workload would be useful and isn't in v2.

## The Deeper Argument

v2's core thesis is unfashionable but probably correct: **RAG was a 2023 workaround for 8K-context LLMs**. With 1M-token windows now standard and dropping in price (DeepSeek V4-Pro at ₹185 per 1M output tokens), the economic case for retrieval over inclusion is weakening every quarter.

If you're an Indian AI engineer who built a vector DB pipeline last year and it's now creaking under maintenance load — read v2. Not as a religious conversion, but as a pragmatic alternative to consider on your next project.

## Where to Find It

Search "LLM Wiki v2" on GitHub Gist or check the original Karpathy Gist for the linked v2. The community is also discussing it actively on Hacker News and the Latent Space Discord. Indian AI engineering communities (BangaloreAI, MadrasML) have started study groups around the pattern.