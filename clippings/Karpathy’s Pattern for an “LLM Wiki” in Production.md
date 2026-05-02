---
title: "Karpathy’s Pattern for an “LLM Wiki” in Production"
source: "https://aaronfulkerson.com/2026/04/12/karpathys-pattern-for-an-llm-wiki-in-production/"
author:
  - "[[Aaron Fulkerson]]"
  - "[[View all posts by Aaron Fulkerson]]"
published: 2026-04-12
created: 2026-05-02
description: "On February 5, 2026, Anthropic pushed an update to Claude Code that changed everything. Not just for me — for everyone. Opus 4.6 with a million-token context window. MCP servers for live data. Hooks for behavioral enforcement. A CLAUDE.md schema that the model actually followed. I didn't sleep for three weeks. My wife was out…"
tags:
  - "clippings"
---
On February 5, 2026, Anthropic pushed an update to Claude Code that changed everything. Not just for me — for everyone. Opus 4.6 with a million-token context window. MCP servers for live data. Hooks for behavioral enforcement. A CLAUDE.md schema that the model actually followed. I didn’t sleep for three weeks. My wife was out of town for two of them, which is the only reason I’m still married.

I eventually called the thing I built Exo (short for exocortex — an external cognitive layer). The name came from the system itself during a late-night session when I asked it what it was becoming. 26 skills, 14 MCP servers, 8 hooks, and an Obsidian vault with hundreds of files that the model maintains. Karpathy’s gist describes the pattern. This post describes what happens when you push it past theory into production for two months.

This post is a combination of lessons from two+ months of building. I’ve incorporated Andrej Karpathy’s notes, too. Also, Brad Feld, whose [Adventures in Claude](https://adventuresinclaude.ai/) inspired me significantly. And I’ve sourced from dozens of builders in the Claude Code community sharing patterns. All hardened by running the system hard, every day, on real work — prepping for board meetings, triaging email, updating product strategy, creating product docs, unit tests, code, analyzing relationships, tracking my own health data.

What I want to give you is the architecture, the patterns that worked, the things I got wrong, and a path to build your own. Everything here is published as an implementation blueprint on [GitHub](https://github.com/AaronRoeF/claude-code-patterns) — 153 patterns, including 13 specifically on the AI Wiki pattern. Point your Claude agent at that URL and tell it to build a plan. It will.

## The Pattern

Andrej Karpathy published a gist in early 2026 called “LLM Wiki” that codifies a different approach. Three layers: raw sources (immutable documents — PDFs, transcripts, bookmarks, notes), the wiki (LLM-generated markdown — summaries, entity pages, cross-references, contradiction flags), and the schema (a CLAUDE.md file that tells the LLM how to maintain the wiki). The raw sources are your inputs. The wiki is the LLM’s persistent, evolving understanding of those inputs. The schema is the operating manual.

The key insight is that the wiki layer is a compounding artifact. Every time you feed the system a new document, the model doesn’t just summarize it — it integrates it. Cross-references to existing entities are already there. Contradictions get flagged. The synthesis on Thursday reflects everything you read on Tuesday, plus everything since. It’s a persistent knowledge graph maintained by an LLM — the way Vannevar Bush imagined the Memex in 1945 — except the librarian is tireless and the cross-referencing is automatic. Also, this isn’t just about the knowledge, it’s about the behavior, learning, and improving your execution because you’ve built learning loops into the system.

Karpathy’s gist is worth reading in full: [github.com/karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). It’s clean, minimal, and gets the architecture right at the conceptual level.

## What I Built

I’d been building this independently for months before the gist dropped. Brad Feld’s [Adventures in Claude](https://adventuresinclaude.ai/) inspired me and gave me several great insights — pushing Claude Code beyond writing software into full operational workflows. What started as a few markdown files and a CLAUDE.md turned into something I didn’t plan to build.

**Before:** I was using Claude the way most people do. Open a session. Paste some context. Ask questions. Get good answers that vanished the moment I closed the terminal. Every meeting prep started from scratch. Every memo required me to re-explain the backstory. Every week I lost hours re-establishing context that should have been ambient.

**During:** I started small. A CLAUDE.md file with some basic instructions. A folder of people files — one markdown file per key contact with notes from meetings, relationship history, communication preferences. Then skills — natural language triggers that fired specific workflows. “Prep Sarah” would pull calendar events, search email threads, check CRM deal status, scan LinkedIn, and pull the meeting transcript from the last conversation. The output was a briefing document. The side effect was that the people file got richer every time I used it.

Underneath the skills, I built a canonical context graph — a ground-truth representation of our business and my life that every workflow draws from. ICP personas built from 375+ named buyers and 2,700+ data points. Jobs-to-be-done mapped to 12 specific data bleed vectors we’d validated with customers. Product tenets. Competitive positioning. Account histories. People files with relationship context going back months. Personal ground truths too — health baselines, communication patterns, decision-making tendencies. The context graph is what makes the skills smart. Without it, a meeting prep skill is just a calendar lookup. With it, the system knows that the person you’re meeting cares about data sovereignty because they told you so three months ago in an email thread you’ve already forgotten.

Three learning loops keep the context graph honest — capture observations daily, review weekly, graduate the patterns that hold up into permanent rules and skill improvements. I’ll explain the graduation mechanism in the next section. The short version: the ICP personas started as templates. Two months of graduated learnings from real sales conversations turned them into something a CISO would recognize as their own buying committee.

Then the system grew. I built 26 skills with natural language triggers — meeting prep, structured memos, a full Working Backwards PM methodology, CRM analytics, content ghostwriting, psychoanalytic profiling of key relationships, biometric health tracking. These aren’t slash commands you have to memorize. Say “prep Sarah” or “how’s the pipeline” or “draft a post about confidential AI” and the right workflow fires. The triggers are encoded in a schema file. The LLM reads the schema and routes.

I wired 14 MCP servers — 7 custom-built — pulling live data from Gmail, Slack, HubSpot CRM, Jira, Apple Notes and Reminders, and Calendar, Things 3 task manager, WHOOP biometrics, an Obsidian vault, iMessage history, Granola meeting transcripts, Google Drive, and Playwright for browser automation. The Obsidian vault is the wiki layer — an ExecOS directory with people files, account files, decision logs, competitive intel, priorities, project directories, daily observations, and generated analyses. Eight hook scripts enforce behavior: email safety gates that block sends without approval, TIL capture on every commit, MCP audit logging, test auto-sync, mobile permission approvals.

**After:** The system compounds. In a single day, I ran a competitive and market-research sweep that would have cost seven figures and taken twelve months if I’d hired a consulting firm. The system pulled web intelligence, CRM data, email threads with prospects, meeting transcripts from the last quarter, and the ICP context graph — then synthesized them into a gap analysis that identified three product-positioning weaknesses I hadn’t seen. I converted the findings into dramatically improved PRDs that same week. Then I wrote code to improve OPAQUE based on the competitive gaps identified in the research. The context graph meant the model understood our architecture, our product tenets, and the specific customer pain points well enough to suggest sensible changes. Board meeting prep? Ninety seconds — it pulls email threads, pipeline data, Jira velocity, competitive intel, and the people files with notes from every prior 1:1. That used to take hours.

![](https://aaronfulkerson.com/wp-content/uploads/2026/04/651a7845-23af-4f73-ad7c-7acea850383a_1_102_o.jpeg?w=2048)

And then I planned a backcountry camping trip with my son. The same system that runs product strategy and writes code also knows my preferences (UNESCO, archeology, geology…), my kid’s hiking pace, and which trails I’ve been tracking in my notes. The trip was epic. The range is the point.

The architecture has a dual-identity layer that matters. Personal skills — health tracking, iMessage relationship analysis, psychological profiling — stay private on my machine. Work skills — meeting prep, memos, PM methodology, CRM analytics — are packaged independently and distributed to team members. Same framework, different permission boundaries. The personal layer makes me more effective. The work layer makes the team more effective.

![](https://aaronfulkerson.com/wp-content/uploads/2026/04/30a3907a-3984-4068-b703-7a4c2fd9b9a4_1_102_a.jpeg?w=2048)

## Where Production Diverges from Theory

Karpathy’s gist is a clean conceptual model. Running it at production scale for months reveals five places where the theory needs extension.

**First**, live data feeds replace static file drops. Karpathy describes dropping source files into a directory. My raw sources are 14 MCP servers pulling live data — calendar events that change hourly, email threads that grow daily, CRM deals that move through pipeline stages, biometric data that refreshes every morning, meeting transcripts that appear after every call. The “ingest” operation happens automatically every time a skill runs. I don’t maintain a source directory. The source directory is my entire digital life, accessed through APIs.

**Second,** skill routing replaces ad-hoc prompting. Karpathy’s operations — Ingest, Query, Lint — are manual prompts you type into a session. I have 26 skills with trigger phrases encoded in the schema. Say “prep Sarah” and Claude pulls calendar, email, LinkedIn, Granola transcripts, and Notion — then writes a briefing to a specific file in the vault. Say “wrap Sarah” after the meeting and it captures action items, updates the people file, flags follow-ups for my task manager. The workflow is encoded, not improvised. The difference matters at scale. When you’re running 15 meetings a week, you can’t afford to prompt-engineer each one.

**Third,** learning loops that graduate. Karpathy mentions filing good answers back into the wiki. I built three formal learning loops. Daily observations get captured — things I notice about how the system works, patterns in customer conversations, mistakes I made, insights from reading. Weekly reviews scan accumulated observations, find cross-session patterns, and propose graduations. A graduation means a pattern has enough evidence to become a permanent rule in CLAUDE.md, an improvement to a skill file, or a new entry in a shared knowledge base. The system doesn’t just accumulate knowledge. It accumulates judgment.

**Fourth,** hooks enforce what instructions suggest. A CLAUDE.md instruction says “don’t send email without approval.” That’s a suggestion to an LLM — it can be reasoned around, ignored under pressure, or simply forgotten after context compaction. A hook script that exits with code 2 blocks the action deterministically. But the interesting hooks aren’t the guardrails. They’re the ones that make the system self-maintaining. A post-commit hook captures learning observations every time I commit code — the system learns as a side effect of working. A post-compact hook re-injects critical state after context compression so the model doesn’t lose orientation mid-session. A file-change hook auto-generates test assertions when new skills are created — the test suite maintains itself. A permission-request hook forwards approval prompts to my phone via push notification so I can approve actions while I’m away from the terminal. Instructions set intent. Hooks enforce behavior and automate the maintenance that would otherwise require discipline I don’t have at 11pm.

**Fifth,** auto-enrichment as a side effect. Meeting prep reads a person file. Meeting debrief updates that person file with new context, action items, relationship signals. Pipeline reports pull deal data and update account files. Every skill that reads from the vault also writes back to it. The knowledge base gets richer from normal work — no dedicated “maintenance sessions” required. This is the compounding mechanism Karpathy describes, but implemented as a side effect of workflows people already run, not as a separate maintenance task they have to remember.

## What the Theory Got Right That I Missed

Honest accounting. Karpathy’s gist revealed some gaps in my production system that I’d been blind to precisely because I’d built it incrementally with my learning loop as guidance.

I had no vault-wide lint operation. No orphan detection, no broken link scanning, no stale content identification. I was maintaining hundreds of files and had no way to know which ones had drifted out of date or lost their cross-references. I built it after reading the gist. The first lint pass found 23 orphaned files and 11 broken cross-references.

I had no formal index file. The LLM was searching the vault every time it needed to orient itself — burning tokens and sometimes missing files that had been renamed or reorganized. A curated INDEX.md that catalogs every major entity, with one-line descriptions and file paths, cut orientation time dramatically. The model scans an index instead of searching a filesystem.

I had no activity log tracking how the knowledge base evolved over time. When did a people file last get updated? Which files changed this week? What’s been stale for 90 days? Added. The LOG.md now captures every significant vault mutation with a timestamp and a one-line description.

I had no source provenance tracking. Which files are human-written originals? Which are LLM-generated summaries? Which are LLM-generated but human-reviewed? Without this metadata, the model couldn’t assess its own confidence in a source. Added provenance tags to the YAML frontmatter of every file.

The point isn’t that my system was incomplete. Every production system is incomplete. The point is that stepping back to compare notes with someone thinking about the same problem from first principles — even when you’re further along in implementation — reveals structural gaps that incremental building hides. Karpathy was thinking about the architecture. I was thinking about the workflows. Both perspectives made the system better.

## The Adoption Path

I published the full pattern library on GitHub — **153** techniques for pushing Claude Code beyond coding, including 13 specifically on the AI Wiki pattern: [github.com/AaronRoeF/claude-code-patterns](https://github.com/AaronRoeF/claude-code-patterns) (start from the README)

Point your Claude agent at that URL and tell it to build a plan. The tips are written as implementation blueprints — file trees, example configs, YAML frontmatter templates, step-by-step sequences. The starting path:

1. Set up Obsidian and the Obsidian MCP server. This gives you a persistent, searchable, graph-connected vault that your LLM can read and write.
2. Create your CLAUDE.md schema. This is the operating manual — what the vault contains, how files are organized, what conventions the model should follow.
3. Build your first skill. Meeting prep is the highest-ROI starting point. One trigger phrase, one workflow that pulls from multiple data sources, one output file that updates the vault.
4. Add INDEX.md and LOG.md. The index is the table of contents. The log is the changelog. Both save tokens and improve the model’s ability to navigate your vault.
5. Wire your first hook. Post-compact context reload — when the model compresses its context window, the hook re-injects critical state so you don’t lose orientation mid-session.
6. Build your first learning loop. Capture observations daily. Review weekly. Graduate the patterns that hold up into permanent rules and skill improvements.

The system compounds. Every session makes the next one richer. Every meeting prep enriches the people files that make the next meeting prep better. Every learning loop graduation makes the system smarter about how it operates. You don’t have to build all 26 skills on day one. You have to build one, use it for a week, and feel the difference between a stateless tool and a compounding one.

## The Compounding Advantage

The tedious part of maintaining a knowledge base has never been the reading or the thinking. It’s the bookkeeping. LLMs handle that. The wiki pattern puts each capability where it belongs — the model does the cross-referencing, the consistency maintenance, the flagging. You do the judgment and the taste.

I owe the lineage. Karpathy codified the architecture. Brad Feld demonstrated the art of the possible. The Claude Code team at Anthropic built the harness. I just wired it together and ran it hard for two months straight.

Some of you who know me know that from 2006 to 2010, my friend Steve Bjorg and I built MindTouch — one of the top 5, often top 3, most popular open source projects in the world at the time. It was an enterprise wiki that defined the category. Great UX, WYSIWYG with drag/drop tools, RESTful, headless before anyone called it that. The codebase still powers [LibreTexts](https://libretexts.org/) and many other high traffic destinations; indeed, MindTouch still ~100 million monthly users across a variety of deployments to this day. We spent years thinking about how organizations capture, structure, and retrieve knowledge at scale.

![](https://aaronfulkerson.com/wp-content/uploads/2026/04/img.jpg)

We sold MindTouch to NICE Systems. The technology is largely obsolete now — like most enterprise SaaS in this new agentic world. The open source code lives on through LibreTexts (and many other highly trafficked deployments) and drives real value, but even that will likely become just another node in a distributed agentic graph.

Twenty years later, I’m building a wiki again. The difference is that this time, I’m not writing the wiki. An elastic team of agents is — distributed across local markdown files, Obsidian vaults, Notion publishing endpoints, CRM feeds, email threads, and calendar APIs. The wiki isn’t a single application anymore. It’s not even a single repo. It’s a living system stretched across every data source I touch. Exo is distributed and self-learning. Every graduated observation makes the system sharper. Every corrected mistake becomes a permanent rule. The agents never forget to update a cross-reference, never let a page go stale, and never decide the maintenance isn’t worth the effort. That’s how every wiki I’ve ever built eventually died — under the weight of its own bookkeeping. This one doesn’t have that problem.

Knowledge that compounds is a different kind of advantage. It’s patient. It’s quiet. And it gets wider every day.