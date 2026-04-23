---
title: "Karpathy's LLM Knowledge Bases: The Post-Code AI Workflow"
source: "https://antigravity.codes/blog/karpathy-llm-knowledge-bases"
author:
  - "[[Antigravity.codes]]"
published: 2026-04-03
created: 2026-04-23
description: "Andrej Karpathy's new workflow uses LLMs to build personal knowledge bases from raw docs. Full breakdown, tools, and how to build your own."
tags:
  - "clippings"
---
📄

UPDATE: Karpathy released a full GitHub gist. We broke it down word by word — [Read the complete Idea File guide →](https://antigravity.codes/blog/karpathy-llm-wiki-idea-file)

Andrej Karpathy — co-founder of OpenAI, former AI lead at Tesla, and the person who coined “vibe coding” — just revealed a significant shift in how he uses LLMs. He's spending less time generating code and more time building **personal knowledge bases**: structured, interlinked markdown wikis that an LLM compiles and maintains from raw source documents.

Get the latest on AI, LLMs & developer tools

New MCP servers, model updates, and guides like this one — delivered weekly.

## 1\. What Karpathy Said

On April 3, 2026, Karpathy shared his workflow in a post titled **“LLM Knowledge Bases”**:

The core insight: **“A large fraction of my recent token throughput is going less into manipulating code, and more into manipulating knowledge.”**

He describes a system where he drops raw source material — articles, papers, repos, datasets, images — into a directory, and an LLM incrementally compiles it all into a structured wiki. The LLM writes the articles, creates backlinks, categorizes concepts, and interlinks everything. Karpathy says he rarely touches the wiki directly.

His research wiki on a single topic has grown to approximately **100 articles and 400,000 words**.

## 2\. The 6-Step Workflow

Karpathy's system follows a clear pipeline. Here's how each step works:

### Step 1: Data Ingest

Raw source documents go into a

```
raw/
```
directory. Articles, papers, GitHub repos, datasets, images — anything relevant to the research topic. To convert web articles to markdown, Karpathy uses the **Obsidian Web Clipper** browser extension and downloads related images locally so the LLM can reference them.

### Step 2: LLM Compilation

An LLM incrementally “compiles” the raw documents into a structured wiki — a collection of

```
.md
```
files in a directory structure. The wiki includes:

- Summaries of all data in
	```
	raw/
	```
- Backlinks between related articles
- Categorization of data into concepts
- Full articles for each concept
- Cross-links between everything

The key quote: **“The LLM writes and maintains all of the data of the wiki, I rarely touch it directly.”**

### Step 3: Scale

Once the wiki reaches critical mass (Karpathy's is ~100 articles, ~400K words on one research topic), it becomes a powerful knowledge substrate. The LLM has enough context to answer complex, multi-step questions that would take a human hours to piece together.

### Step 4: Querying

With enough articles, Karpathy asks his LLM agent complex research questions against the wiki. The agent follows links, cross-references articles, and synthesizes answers from multiple sources within the knowledge base.

### Step 5: Multi-Format Output

Instead of getting answers in text or a terminal, Karpathy has the LLM render output in different formats:

- **Markdown files** — for wiki articles and documentation
- **Marp slides** — for presentations (markdown-based slide decks)
- **Matplotlib images** — for charts and data visualization

All of these are viewed in **Obsidian**, which serves as the frontend for the entire system.

### Step 6: Health Checks

Karpathy runs LLM “health checks” over the wiki — like linting for knowledge. These checks:

- Find inconsistent or contradictory data across articles
- Impute missing data by searching the web
- Discover interesting connections between concepts
- Surface candidates for new articles

The Compiler Analogy

Think of it like a code compiler:

```
raw/
```
is the source code, the LLM is the compiler, the wiki is the executable, health checks are the test suite, and queries are the runtime. Karpathy is building GCC for knowledge.

## 3\. From Vibe Coding to Knowledge Compilation

This is the latest step in a clear evolution of how Karpathy uses LLMs:

| Date | Concept | What Changed |
| --- | --- | --- |
| **Feb 2025** | Vibe Coding | “Forget the code even exists” — talk to the AI, accept all, ship |
| **Dec 2025** | “Never felt this behind” | Called it a “magnitude 9 earthquake” for programmers |
| **Jan 2026** | Agentic Engineering | The disciplined successor: humans write <1% of code, orchestrate AI agents |
| **Apr 2026** | **LLM Knowledge Bases** | **Beyond code entirely — LLMs compile and maintain knowledge** |

Each phase expanded the frontier of what LLMs can orchestrate. [Vibe coding](https://antigravity.codes/blog/vibe-coding-guide) was about generating code without reviewing it. **Agentic engineering** was about orchestrating agents who generate code with oversight. Now, **LLM knowledge bases** move the entire paradigm beyond code — the LLM is managing *knowledge*, not just software.

The throughline: **markdown is becoming the programming language of the AI era**. Whether it's

```
GEMINI.md
```
files instructing agents,
```
program.md
```
files driving research, or
```
raw/
```
directories being compiled into wikis — the interface between humans and AI is plain text.

## 4\. Real-World Use Cases

Karpathy built his knowledge base for ML research, but the pattern applies far beyond that. Here are concrete use cases where this workflow delivers outsized value:

### Competitive Intelligence

Ingest competitor websites, product changelogs, job postings, press releases, and SEC filings into

```
raw/
```
. The LLM compiles a competitive wiki with company profiles, product feature matrices, hiring trends, and strategic direction analysis. Run health checks weekly to detect new developments. Ask questions like: “Which competitors are hiring for AI infrastructure roles and what does that signal about their roadmap?”

### Technical Due Diligence

VCs and acquirers can ingest a target company's public repos, documentation, blog posts, team profiles, and tech stack evidence. The compiled wiki surfaces architecture patterns, technical debt signals, team expertise distribution, and technology choices. One analyst reported reducing due diligence time from 2 weeks to 3 days using a similar workflow.

### Academic Literature Reviews

PhD students and researchers can clip 50–100 papers into

```
raw/
```
and have the LLM compile a literature review wiki: key findings per paper, methodology comparisons, citation networks, research gaps, and contradicting results. The health check can identify papers you missed by searching for topics that appear underrepresented in your collection.

### Developer Documentation

Ingest your codebase's README files, API docs, architecture decision records, incident postmortem reports, and Slack thread exports. The compiled wiki becomes a living knowledge base that new team members can query: “Why did we choose Postgres over DynamoDB?” or “What were the root causes of our last 5 incidents?”

### Product Research

Product managers can ingest user feedback (support tickets, NPS surveys, feature requests, app store reviews), competitor analysis, market reports, and user interview transcripts. The wiki compiles patterns: top pain points, most-requested features, competitor advantages, and user segments. Query it for prioritization: “Which feature would address the most user complaints while also differentiating us from Competitor X?”

### Legal and Compliance

Ingest regulatory documents, compliance requirements, internal policies, audit reports, and industry guidelines. The wiki compiles requirements by category, maps them to your current controls, and surfaces gaps. Health checks flag when regulations update. Especially valuable for teams dealing with GDPR, SOC 2, HIPAA, or financial compliance frameworks.

### Personal Learning

Building expertise in a new domain? Clip every article, tutorial, and video transcript you consume into

```
raw/
```
. The wiki organizes your learning into a structured curriculum: foundational concepts, intermediate techniques, advanced topics, and open questions. Over weeks, it becomes a personalized textbook that reflects exactly what you've studied and where the gaps are.

| Use Case | Raw Sources | Wiki Output | Key Query Examples |
| --- | --- | --- | --- |
| Competitive Intel | Websites, changelogs, job posts | Company profiles, feature matrices | “What's Competitor X's pricing trend?” |
| Literature Review | Papers, articles, preprints | Findings summaries, gap analysis | “Which methods outperform baseline on Y?” |
| Dev Documentation | READMEs, ADRs, postmortems | Architecture guides, decision logs | “Why was Redis chosen for caching?” |
| Product Research | Feedback, surveys, interviews | Pain points, feature priorities | “Top 3 churn drivers this quarter?” |
| Personal Learning | Articles, tutorials, transcripts | Structured curriculum, concept map | “What prerequisites am I missing for X?” |

## 5\. Community Reactions

Karpathy's post generated immediate reactions from notable figures in the AI and knowledge management space:

### Steph Ango (Obsidian CEO)

Kepano responded with a critical insight about **vault separation**: keep your personal vault clean and high signal-to-noise with known content origins, and create a separate “messy vault” for agent-generated content. This prevents contamination — you always know which knowledge was human-curated versus AI-compiled. It's the knowledge management equivalent of separating production from staging.

### Elvis Saravia (DAIR.AI)

Elvis Saravia, who runs the DAIR.AI open-source AI research community, confirmed the pattern: “I have also been obsessed with building LLM knowledge bases. LLMs are excellent at curating and searching (finding connections) once data is stored properly.” He shared examples of using the approach for AI research curation, noting that the key is getting the data structure right first.

### The Graph RAG Connection

Multiple developers pointed out the connection to **Graph RAG** — a retrieval technique where knowledge is stored as a graph of interconnected nodes rather than flat documents. Karpathy's wiki-with-backlinks approach is essentially a manual, markdown-based implementation of Graph RAG. Tools like **Neural Composer** for Obsidian are now building this into plugins, using Obsidian's 3D graph view to visualize connections that the AI discovers.

### The “Second Brain” Community

The personal knowledge management (PKM) community — followers of Tiago Forte's “Building a Second Brain” methodology and the Zettelkasten method — recognized Karpathy's workflow as a significant evolution. Where traditional PKM requires manual note-taking, linking, and review, Karpathy's approach automates the most labor-intensive parts while keeping the human in the loop as curator and questioner.

Key Debate: Contamination Risk

The biggest concern raised across the community: **how do you trust AI-compiled knowledge?** If the LLM hallucinates a connection between two concepts, that false link now lives in your wiki and could influence future queries. Kepano's vault separation approach and Karpathy's health checks are both mitigations, but this remains an open problem. The consensus: always link wiki articles back to their

```
raw/
```
sources, so every claim is traceable.

## 6\. Tools & Ecosystem

Several tools and projects already align with Karpathy's vision. Here's the current landscape:

### Obsidian + AI Plugins

**Obsidian** (1.5M+ users) is the foundation of Karpathy's setup. The Obsidian CEO, Steph Ango (kepano), responded to Karpathy's post suggesting developers keep personal vaults clean and create separate vaults for agent-generated content — preventing contamination of human-curated knowledge.

**Obsidian Skills**, a project by kepano, teaches AI agents to work with Obsidian's native formats:

```
[[wikilinks]]
```
, callouts, Bases, and Canvas. It's the interface layer between AI agents and your vault.

### Knowledge Base Tools

| Tool | What It Does | Best For |
| --- | --- | --- |
| **Notemd** | Chunks docs, auto-generates wiki-links, creates concept notes | Obsidian users wanting AI-powered linking |
| **AI Knowledge Filler** | Generates structured files with validated YAML and WikiLinks | Generating Obsidian-ready content from LLMs |
| **library-mcp** | MCP server for exploring Markdown KBs through Claude Desktop | Querying existing knowledge bases via AI |
| **Obsidian Skills** | Agent skills for Markdown, Bases, Canvas, CLI | Teaching AI agents Obsidian-native formats |
| **LLM Workspace** | Integrates local LLMs directly into Obsidian vaults | Private, offline knowledge work |

### MCP Servers for Knowledge

The [MCP ecosystem](https://antigravity.codes/mcp) is growing fast with knowledge-focused servers. Servers like **gnosis-mcp** load markdown docs into SQLite for AI search, while **library-mcp** lets you explore markdown KBs through Claude Desktop. These turn your knowledge base into a tool that any AI assistant can use.

If you're building your own MCP integration, our [MCP server tutorial](https://antigravity.codes/blog/build-custom-mcp-server-antigravity) covers the full setup with TypeScript and the MCP SDK.

## 7\. How to Build Your Own LLM Knowledge Base

You don't need Karpathy's setup to start. Here's a practical approach using tools available today:

### The Minimum Viable Knowledge Base

DIRECTORY STRUCTURE  
  
my-research/  
raw/ # Source documents (articles, papers, notes)  
article-1.md  
paper-2.md  
images/  
wiki/ # LLM-compiled wiki (don't edit manually)  
INDEX.md  
concepts/  
concept-a.md  
concept-b.md  
output/ # Query results, slides, charts  
\_meta/ # Compile state, config

### Step-by-Step Setup

**1\. Install Obsidian + Web Clipper.** Obsidian is free and local-first. The Web Clipper extension lets you save any web article as a clean

```
.md
```
file with one click.

**2\. Create your raw/ directory.** Start dropping in source material: articles you read, papers you reference, code snippets, images, screenshots. Clip web articles with Web Clipper, download images locally.

**3\. Write a compilation prompt.** Give your AI agent (Antigravity, Claude Code, or any agentic IDE) a system prompt that tells it how to compile the wiki:

EXAMPLE COMPILATION PROMPT  
  
You are a wiki compiler. Read all files in raw/  
and compile them into wiki/ following these rules:  
  
1\. Identify all key concepts mentioned across documents  
2\. Create one.md article per concept in wiki/concepts/  
3\. Each article should summarize what raw/ says about it  
4\. Use \[\[wiki-links\]\] to connect related concepts  
5\. Update wiki/INDEX.md with a table of contents  
6\. Only process raw files modified since last compile  
  
Frontmatter for each article:  
\---  
title: Concept Name  
sources: \[list of raw/ files referenced\]  
related: \[\[linked-concepts\]\]  
last\_compiled: 2026-04-03  
\---

**4\. Run the compilation.** Point your agent at the directory and let it compile. For the first run, expect it to take time. After that, incremental builds are fast — only new or changed

```
raw/
```
files get processed.

**5\. Query your wiki.** Ask the agent questions like: “What are the key differences between X and Y based on the wiki?” or “Create a Marp slide deck summarizing the top 5 concepts.”

**6\. Run health checks.** Periodically ask the agent to audit the wiki: find contradictions, missing links, thin articles, and orphaned concepts.

### Agent Setup Tips

If you're using an agentic IDE like Antigravity, you can set up [agent skills](https://antigravity.codes/blog/mastering-agent-skills) to automate the compilation. Create a skill that watches

```
raw/
```
for changes and triggers incremental compilation. For [multi-agent setups](https://antigravity.codes/blog/antigravity-agent-orchestration-multi-agent), dedicate one agent to ingestion, another to compilation, and a third to health checks.

## 8\. Advanced Patterns

Once the basic pipeline is working, these patterns take it further:

### Multi-Source Ingestion Pipeline

Go beyond manual clipping. Build automated ingestion from multiple source types:

INGESTION SOURCES  
  
\# Web articles  
Obsidian Web Clipper → raw/articles/  
  
\# PDFs (papers, reports)  
PDF → Claude Vision / pdf2md → raw/papers/  
  
\# YouTube videos  
Transcript API → clean markdown → raw/videos/  
  
\# GitHub repos  
Clone → README + key files → raw/repos/  
  
\# Podcasts / audio  
Whisper → transcript → raw/audio/  
  
\# RSS feeds (automated)  
Cron + RSS parser → raw/feeds/

### Compilation Profiles

Different wikis need different compilation strategies. A research wiki should extract methodology and citations. A competitive intel wiki should extract pricing, features, and positioning. Define profiles with different compilation prompts:

- **Research profile:** Extract claims, evidence strength, methodology, citations, contradictions
- **Competitive profile:** Extract product features, pricing, positioning, team size, funding
- **Learning profile:** Extract concepts, prerequisites, difficulty levels, practical exercises
- **Decision log profile:** Extract decisions, alternatives considered, rationale, who decided, when

### Scheduled Health Checks

Don't wait for manual triggers. Set up scheduled health checks that run automatically:

- **Daily:** Check for new raw files and trigger incremental compilation
- **Weekly:** Run full consistency check across all articles
- **Monthly:** Web-verify facts in the wiki against current sources (catches outdated info)
- **On-demand:** Deep analysis for specific queries or decision points

### Cross-Wiki Linking

If you maintain multiple knowledge bases (e.g., one per project or research area), the LLM can find connections across them. A concept in your “ML Research” wiki might relate to something in your “Product Strategy” wiki. Cross-wiki health checks surface these non-obvious connections.

### Version Control Your Knowledge

Since the entire wiki is plain markdown, it works naturally with Git. Commit after each compilation run. This gives you:

- Full history of how your knowledge evolved
- Ability to diff between compilation runs
- Rollback if a compilation introduces errors
- Collaboration via branches and pull requests
- CI/CD for knowledge — automated health checks on every commit

## 9\. What This Means for AI Developers

Karpathy's shift signals something bigger than a personal workflow change. It suggests where the entire AI-assisted development paradigm is heading.

### From Code Generation to Knowledge Management

The AI coding tools conversation has been dominated by code generation: GitHub Copilot, Cursor, Antigravity, Claude Code. But Karpathy is showing that once code generation is “solved enough,” the frontier moves to **knowledge orchestration**. The bottleneck isn't writing code — it's understanding the problem domain deeply enough to know what to build.

### Markdown as the Universal Interface

Every step in the Karpathy workflow uses markdown: raw documents are markdown, the wiki is markdown, outputs are markdown (or markdown-derived formats like Marp). This isn't accidental. Markdown is:

- Human-readable and human-editable
- LLM-friendly (models are heavily trained on it)
- Version-controllable with Git
- Tool-agnostic (works with Obsidian, VS Code, any text editor)
- Portable and future-proof (plain text never goes obsolete)

### The Role of the Developer Shifts

In vibe coding, the developer prompted and accepted. In agentic engineering, the developer orchestrated agents. In the knowledge base paradigm, the developer becomes a **curator and questioner**. You decide what sources to ingest, what questions to ask, and how to validate the compiled knowledge. The LLM does the heavy lifting of organizing, connecting, and maintaining.

The Takeaway

The developers who thrive in the next phase won't just be the best prompters or the best agent orchestrators. They'll be the ones with the **best knowledge systems** — structured, maintained, and queryable repositories that give their AI agents the context needed to do exceptional work.

### Start Small

You don't need to replicate Karpathy's 400K-word wiki on day one. Start with one research topic. Drop 10 articles into

```
raw/
```
. Run the compilation. Query it. See if the structured output gives you insights you wouldn't have found by reading the articles individually.

If you're already using [vibe coding](https://antigravity.codes/blog/vibe-coding-guide) or agentic workflows, adding a knowledge base layer is a natural next step. The tools exist. The workflow is proven. The only question is what topic you start with.

### Get the Ultimate Antigravity Cheat Sheet

Join 5,000+ developers and get our exclusive PDF guide to mastering Gemini 3 shortcuts and agent workflows.