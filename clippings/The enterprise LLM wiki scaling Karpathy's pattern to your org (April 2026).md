---
title: "The enterprise LLM wiki: scaling Karpathy's pattern to your org (April 2026)"
source: "https://falconer.com/guides/enterprise-llm-wiki-karpathy"
author:
published: 2026-04-28
created: 2026-05-02
description: "Andrej Karpathy's LLM Wiki pattern works brilliantly for personal knowledge. It breaks at company scale. Learn why the four properties (capture, link, compound, stay current) need a different implementation for engineering teams, and how an enterprise LLM wiki has to work."
tags:
  - "clippings"
---
Andrej Karpathy [recently published](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) a pattern he calls the LLM Wiki: a personal knowledge base in plain markdown, compiled and maintained by an LLM, designed to compound over time. You drop articles, papers, and notes into a `raw/` folder. An agent reads everything, writes structured wiki pages, builds cross-references, surfaces contradictions, and keeps the whole thing current. As [VentureBeat described it](https://venturebeat.com/data/karpathy-shares-llm-knowledge-base-architecture-that-bypasses-rag-with-an), the LLM acts as a full-time research librarian rather than a chatbot you feed snippets to.

It works. Engineers across the Obsidian and second-brain communities have already built their own versions and reported the same thing: a knowledge system that gets smarter as you add to it, without the maintenance burden that kills most note-taking workflows.

Then the same engineers go to work, where their company’s knowledge lives in a Confluence graveyard, a Notion deployment nobody updates, and fourteen Slack threads from Q3 that resolved a question everyone keeps re-asking. The personal pattern works beautifully. The company version doesn’t have a Karpathy maintaining it.

This guide walks through Karpathy’s LLM Wiki pattern, why it doesn’t translate directly to company scale, and how an enterprise LLM wiki has to work to deliver the same compounding effect across an organization.

## Key takeaways

- Karpathy’s LLM Wiki is a personal-knowledge pattern: structured markdown, maintained by an LLM, compounding over time.
- The same four properties that make it work at the personal level (capture, link, compound, stay current) are needed at the company level, but the maintenance model has to change.
- A personal LLM wiki works because one motivated person curates it. A company can’t depend on a single curator without recreating the wiki problem it’s trying to solve.
- Most existing enterprise tools (Confluence, Notion, Glean) handle one or two of the four properties. None handle the maintenance loop the way Karpathy’s pattern does.
- An enterprise LLM wiki has to do the maintenance automatically, ingesting from the tools where work actually happens (GitHub, Slack, Linear, Granola) rather than from a curated `raw/` folder.
- Y Combinator’s Spring 2026 RFS names this missing primitive directly: a company brain that AI agents can actually use.

## What Karpathy’s LLM Wiki does

The LLM Wiki pattern is simpler than it sounds. There’s a `raw/` folder where source material goes (articles, papers, transcripts, notes). The files are immutable; you don’t edit them. There’s a `wiki/` folder of LLM-generated pages: summaries, concept articles, cross-references, an index. And there’s a `CLAUDE.md` schema file that tells the agent how to operate on the vault.

The LLM does four things on a loop. It compiles raw sources into structured wiki pages. It creates and maintains backlinks between pages so concepts that appear in multiple sources get connected. It surfaces contradictions and gaps when the same fact appears differently across sources. And it runs periodic [health checks](https://venturebeat.com/data/karpathy-shares-llm-knowledge-base-architecture-that-bypasses-rag-with-an) where Karpathy asks the agent to lint the wiki for inconsistencies, missing context, and stale information before any of it propagates into downstream queries.

The result is a knowledge graph that grows richer with every new source ingested. Karpathy describes the workflow as Obsidian being the IDE, the LLM being the programmer, and the wiki being the codebase. The user explores and asks questions; the LLM does the maintenance.

That last part is the key insight, and it’s the part most note-taking systems get wrong. As [one of the early implementers wrote](https://aimaker.substack.com/p/llm-wiki-obsidian-knowledge-base-andrej-karphaty), the theory of Zettelkasten-style personal knowledge systems is beautiful and the practice usually fails because the maintenance kills it. The LLM Wiki pattern moves the maintenance off the human. That’s why it works.

## Why the pattern doesn’t translate directly to company scale

The four properties that make Karpathy’s pattern work (capture, link, compound, stay current) are all necessary for a working enterprise knowledge system. None of them translate directly.

### Capture: the source folder doesn’t exist at company scale

Karpathy’s vault has a `raw/` folder full of immutable source material. Articles he saved. Papers he read. Transcripts he made. Curated by him, deliberately, over time. That curation is a feature of the personal pattern — it works because one person controls what goes in.

A company has no equivalent folder, and that’s not a failure of discipline — it’s a structural difference. The “raw sources” of organizational knowledge are scattered across GitHub PRs, Slack threads, Linear tickets, Granola meeting notes, design docs in Google Drive, incident retrospectives in Notion, and customer conversations in Zendesk. The source material *is* the work, happening in real time across fourteen tools.

For an enterprise LLM wiki to capture what the company knows, it has to ingest from where the knowledge actually lives rather than from a curated folder a single person maintains.

### Link: bidirectional connections need cross-tool semantics

Obsidian’s killer feature is the bidirectional link. You see what references what; you discover connections you didn’t know existed. The LLM Wiki pattern leans heavily on this. When the agent compiles a new wiki page, it creates backlinks to every related concept. The graph thickens.

Confluence pages sit in folders. Notion pages sit in databases. The connections that exist are the ones humans manually create, and humans don’t have the time or memory to create them at the rate the company generates content.

Worse, an enterprise knowledge graph has to link across tools, not just across files. A decision made in Slack needs to link to the PR that implemented it, the Linear ticket that tracked it, the Granola transcript where it was first discussed, and the Notion doc that documents the resulting system. None of those links exist by default, and none of them survive when one of the tools changes.

### Compound: stale context doesn’t compound, it rots

Karpathy’s wiki compounds because every new source he ingests gets integrated into the existing structure. The agent updates affected pages, creates new ones where needed, and flags contradictions when sources disagree. Each addition makes the whole system more useful.

A company knowledge base does the opposite without intervention. New docs get written; old ones don’t get updated. [Stack Overflow’s 2024 developer survey of 65,000 professional developers](https://survey.stackoverflow.co/2024/) found that more than 60 percent spend 30 minutes or more a day searching for solutions, and 68 percent encounter a knowledge silo at least once a week. The same survey found that for people managers (the most experienced engineers), the silo rate climbs to 73 percent. Each new doc adds to the pile rather than to the graph, because nothing is doing the work of integration.

### Stay current: health checks need to run automatically

This is the hardest property to preserve at scale. Karpathy runs health checks on his vault — asking the LLM to find inconsistencies between sources, fill in missing information, and flag pages that contradict newer material. At the personal scale, that works well: one person sees the lint output and acts on it.

At the company scale, the same approach doesn’t generalize. The engineer who wrote the runbook six months ago is on a different project; the senior engineer who knew the context has moved on; the system the doc described has been rewritten twice. The inconsistencies that Karpathy surfaces through deliberate health checks accumulate quietly across company knowledge until a new hire builds against the wrong assumption, or an AI agent reads the stale runbook and produces code that worked in 2024.

[Anthropic’s engineering team frames context as the scarcest resource for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents): agents need just-in-time access to current, accurate context to perform reliably on real work. A knowledge graph that hasn’t had a health check in a year is the worst possible input for an agent.

![Diagram showing the four properties of an enterprise LLM wiki (capture across tools, cross-tool linking, compounding integration, automated drift detection) compared side-by-side with their personal-scale equivalents in Karpathy's LLM Wiki pattern (raw folder ingestion, Obsidian backlinks, incremental compilation, manual health checks). Falconer brand palette.](https://falconer.com/api/file/s3/images/1777418671532-69z7d6.png)

## What an enterprise LLM wiki has to do differently

The four properties stay the same. The implementation has to scale, which means the maintenance has to be automatic and the ingestion has to come from the tools where work actually happens.

For capture, the model is tool-native ingestion rather than curated folder import. An enterprise LLM wiki connects directly to GitHub, Slack, Linear, Granola, Google Drive, and the rest of the stack. There’s no `raw/` folder for someone to populate; the tools themselves are the raw layer, and the ingestion runs continuously rather than on the schedule of whoever happens to be saving sources.

Linking shifts from Obsidian-style file-internal backlinks to cross-tool semantic resolution. A decision in Slack, the PR that implemented it, the Linear ticket that tracked it, and the runbook that describes the resulting system all have to connect. The graph has to understand that “the payments service” in a design doc is the same entity as “payments-service” in a GitHub repo and “@payments-team” in a Slack channel. This is closer to a knowledge graph than to a vault of linked markdown files.

Compounding has to handle conflicting sources, not just additive ones. When a new PR contradicts the runbook, the system needs to detect the contradiction, draft an update, and route it to the document owner for review. Karpathy’s vault handles contradictions by surfacing them to him; an enterprise wiki has to surface them to the right person, which means the system has to understand ownership and routing.

The health check changes from on-demand to continuous. Drift detection across thousands of documents and millions of code lines isn’t something a person triggers by asking. It runs as a background loop, surfacing flagged content on a schedule the team can act on (weekly review rather than quarterly audit), with the LLM doing the same kind of inconsistency-checking Karpathy runs on his vault, just at a cadence that matches enterprise change rates.

| Property | Karpathy’s LLM Wiki (personal) | Enterprise LLM wiki |
| --- | --- | --- |
| Capture | Manual curation into a `raw/` folder | Continuous ingestion from GitHub, Slack, Linear, Granola, Google Drive, Notion |
| Link | Obsidian bidirectional links within a vault | Cross-tool entity resolution into a knowledge graph |
| Compound | LLM compiles new sources into existing wiki pages | System detects drift, drafts updates, routes to document owner |
| Stay current | Karpathy runs periodic health checks himself | Continuous drift detection running in the background |
| Schema | `CLAUDE.md` file in the vault | SSOT designations and ownership metadata, enforced as a property of the system |

## How to build an enterprise LLM wiki

The setup mirrors Karpathy’s pattern, just with enterprise-scale ingestion and automation in place of personal curation.

### Step 1: Connect your sources

Where Karpathy’s vault has a `raw/` folder, an enterprise LLM wiki connects to the tools where engineering work actually happens: GitHub for code and PRs, Slack for the threads where decisions get resolved, Linear for tickets and project context, Granola for meeting notes and standups, Google Drive for design docs and RFCs, and existing wikis (Notion, Confluence) for whatever’s already been written down. The goal is full coverage, because partial coverage produces a partial graph.

### Step 2: Let the system map the knowledge graph

Karpathy’s LLM compiles raw sources into wiki pages with backlinks. An enterprise LLM wiki does the same thing across tools: it reads the connected sources and produces a knowledge graph that knows what’s current, what’s stale, what’s canonical, and what’s missing entirely (topics the team discusses in Slack but has never documented). This is the structural equivalent of Karpathy’s `wiki/` folder, generated automatically from real work artifacts rather than curated notes.

### Step 3: Set sources of truth

Karpathy’s `CLAUDE.md` schema tells the agent how to operate on his vault. The enterprise equivalent is designating which documents are canonical for each domain: architecture decisions, runbooks, onboarding guides, product specs, API references. Once a doc is marked canonical, the system monitors it from that point forward and treats conflicting sources as supplementary context rather than competing truth.

### Step 4: Ship normally

This is the property that distinguishes an enterprise LLM wiki from every documentation mandate that’s failed before it. The team doesn’t change how it works. As PRs merge, Slack threads resolve, and decisions land, the system detects which documents are now affected and drafts proposed updates. The document owner reviews and accepts or rejects in seconds. The knowledge graph grows as a byproduct of work, not as additional work.

### Step 5: Query the wiki

Karpathy queries his wiki with Claude Code, asking complex research questions and getting answers grounded in his own sources. An enterprise LLM wiki gets queried by engineers (“what does our auth system actually do?”), by PMs (“what was the decision on database sharding from Q3?”), and increasingly by AI coding agents through protocols like [Claude MCP](https://www.anthropic.com/news/model-context-protocol). The agents query the same knowledge graph the humans do, which means agent outputs ground in the same current context the team operates from.

![Screenshot of Falconer's interface showing a knowledge graph view with sources from GitHub, Slack, Linear, and Notion connected by relationships, with a side panel displaying a recently auto-drafted documentation update awaiting owner review.](https://falconer.com/api/file/s3/images/1777418694350-d0hbz.png)

## Why retrieval tools don’t solve this

Most enterprise knowledge tools on the market are retrieval tools. They make it easier to find what’s been captured. Glean, Notion AI, and Confluence search all work this way.

Retrieval doesn’t fix the maintenance problem. A semantic search engine pointed at a stale knowledge base returns confidently-worded answers from documents that haven’t been true since Q2. Better retrieval over bad context delivers wrong answers more quickly than slow retrieval over bad context.

The personal-knowledge-management community figured this out years ago. What makes Karpathy’s LLM Wiki interesting is the maintenance loop, not the search layer. The graph stays accurate because the LLM is doing the linting, drafting, and reconciliation work continuously. The Obsidian power user who builds a serious vault values the same thing: a system where the notes stay true, not just a better way to query notes that may or may not be. The same logic holds at company scale, but the maintenance problem is harder because nobody owns the curation the way Karpathy owns his vault.

## How Falconer maps to the pattern

[Y Combinator’s Spring 2026 Requests for Startups](https://www.ycombinator.com/rfs) names the missing primitive directly: “If we want every company to run on AI automation, we need a new primitive: a company brain. A system that pulls knowledge out of all these fragmented sources, structures it, keeps it current, and turns it into an executable skills file for AI.”

[Falconer](https://falconer.com/) is that system, built on the same principles Karpathy’s LLM Wiki uses, scaled to enterprise constraints. Connect GitHub, Slack, Linear, Granola, Google Drive, and existing docs. Falconer ingests across all of them, builds the cross-tool knowledge graph, runs continuous drift detection, and drafts updates as the underlying work changes. AI coding agents query the same graph through Claude MCP, so the agents read current context rather than the snapshot the wiki captured six months ago.

The setup is end-of-day. The compounding effect is the same one Karpathy’s vault produces: a knowledge system that gets richer the longer it runs, because every PR, thread, decision, and meeting feeds back into the graph automatically. The pattern scales because the maintenance scales with it.

## Frequently asked questions

**What is an enterprise LLM wiki?** An enterprise LLM wiki is a knowledge layer that captures, links, compounds, and maintains everything an organization knows across its tools (GitHub, Slack, Linear, Notion, meeting notes), structured as a queryable knowledge graph that humans and AI agents can both read. It’s the company-scale version of the LLM Wiki pattern Andrej Karpathy described for personal knowledge management.

**How is this different from Karpathy’s LLM Wiki?** The pattern is the same; the implementation has to be different at company scale. Karpathy’s LLM Wiki has a curated `raw/` folder, an Obsidian vault, and one motivated user running the maintenance loop. An enterprise LLM wiki has to ingest continuously from the tools where work actually happens, build a cross-tool knowledge graph, and run drift detection automatically because no single person can maintain the curation across an entire organization.

**Why doesn’t Obsidian work for a company?** Obsidian works at the personal scale because one user owns the vault and runs the maintenance. At the team or company scale, the maintenance burden compounds with team size; nobody owns the update, the bidirectional links don’t span tools, and the vault goes stale within a quarter. An enterprise LLM wiki has to handle the maintenance the way Karpathy handles his vault, automatically.

**Why doesn’t adding AI search to Confluence or Notion fix this?** Retrieval doesn’t fix the underlying maintenance problem. A semantic search engine pointed at a stale knowledge base returns confident answers from outdated documents. The reason Karpathy’s LLM Wiki works isn’t the search layer; it’s that the underlying graph stays accurate because the LLM is doing the maintenance work in a loop. Without that loop, smarter search over bad context just produces wrong answers faster.

**Can AI coding agents query an enterprise LLM wiki?** Yes, and this is increasingly the primary use case. Coding agents reading stale internal documentation produce stale outputs; the [Anthropic engineering team](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) describes context as the scarcest resource for AI agents. An enterprise LLM wiki gives agents a continuously-maintained knowledge graph to query, typically through protocols like [Claude MCP](https://www.anthropic.com/news/model-context-protocol).

**How long does it take to set up?** With Falconer, most teams are running by end of day. Connect GitHub, Slack, Linear, Granola, and the rest of the stack, and the system maps the knowledge graph automatically. There’s no manual tagging, no taxonomy design, no migration of existing docs.

**What happens to existing documentation?** The system ingests existing wikis (Notion, Confluence, internal markdown) as additional sources. It maps what’s current versus stale, surfaces contradictions, and maintains everything from that point forward. Teams don’t have to clean up their existing docs first; the system uses the cleanup as part of the baseline.

**Why is this called a “wiki” rather than just a knowledge base or documentation system?** The “wiki” framing comes from Karpathy’s pattern, where the LLM compiles raw sources into a structured, interlinked set of pages that resemble Wikipedia at the personal scale. The enterprise version preserves the structure (queryable, interlinked, continuously maintained) while changing what the inputs and outputs look like. Calling it a “knowledge base” understates the part that matters most: the active maintenance loop.