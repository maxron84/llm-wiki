---
title: "Karpathy shares 'LLM Knowledge Base' architecture that bypasses RAG with an evolving markdown library maintained by AI"
source: "https://venturebeat.com/data/karpathy-shares-llm-knowledge-base-architecture-that-bypasses-rag-with-an"
author:
  - "[[Carl Franzen]]"
published: 2026-04-04
created: 2026-04-23
description: "Karpathy proposes something simpler and more loosely, messily elegant than the typical enterprise solution of a vector database and RAG pipeline."
tags:
  - "clippings"
---
![Elegant Victorian robot holds book in home library](https://venturebeat.com/_next/image?url=https%3A%2F%2Fimages.ctfassets.net%2Fjdtwqhzvc2n1%2F5rACVd0XGWlMS5e6o1F14i%2Fb0e0dd19d82776e22bffed439593afbc%2FCarl_Franzen_a_humanoid_robot_in_elegant_cravat_and_velveteen_3a76f138-c488-4d03-8016-c5db26b6a025_3__1_.png%3Fw%3D1000%26q%3D100&w=3840&q=85)

Credit: VentureBeat made with Midjourney

AI vibe coders have yet another reason to thank [Andrej Karpathy](https://x.com/karpathy/status/2039805659525644595), the coiner of the term.

The former Director of AI at Tesla and co-founder of OpenAI, now running his own independent AI project, recently [posted on X describing a "LLM Knowledge Bases"](https://x.com/karpathy/status/2039805659525644595) approach he's using to manage various topics of research interest.

By building a persistent, LLM-maintained record of his projects, Karpathy is solving the core frustration of "stateless" AI development: the dreaded context-limit reset.

As anyone who has vibe coded can attest, hitting a usage limit or ending a session often feels like a lobotomy for your project. You’re forced to spend valuable tokens (and time) reconstructing context for the AI, hoping it "remembers" the architectural nuances you just established.

Karpathy proposes something simpler and more loosely, messily elegant than the typical enterprise solution of a vector database and RAG pipeline.

Instead, he outlines a system where the LLM itself acts as a full-time "research librarian"—actively compiling, linting, and interlinking Markdown (.md) files, the most LLM-friendly and compact data format.

![](https://x.com/i/status/2039805659525644595)

By diverting a significant portion of his "token throughput" into the manipulation of structured knowledge rather than boilerplate code, Karpathy has surfaced a blueprint for the next phase of the "Second Brain"—one that is self-healing, auditable, and entirely human-readable.

## Beyond RAG

For the past three years, the dominant paradigm for giving LLMs access to proprietary data has been **Retrieval-Augmented Generation (RAG)**.

In a standard RAG setup, documents are chopped into arbitrary "chunks," converted into mathematical vectors (embeddings), and stored in a specialized database.

When a user asks a question, the system performs a "similarity search" to find the most relevant chunks and feeds them into the LLM.Karpathy’s approach, which he calls **LLM Knowledge Bases**, rejects the complexity of vector databases for mid-sized datasets.

Instead, it relies on the LLM’s increasing ability to reason over structured text.

The system architecture, as visualized by X user [@himanshu](https://x.com/himanshustwts/status/2039811786602607052) in part of the wider reactions to Karpathy's post, functions in three distinct stages:

![](https://x.com/i/status/2039811786602607052)
1. **Data Ingest:** Raw materials—research papers, GitHub repositories, datasets, and web articles—are dumped into a `raw/` directory. Karpathy utilizes the **Obsidian Web Clipper** to convert web content into Markdown (`.md`) files, ensuring even images are stored locally so the LLM can reference them via vision capabilities.
2. **The Compilation Step:** This is the core innovation. Instead of just indexing the files, the LLM "compiles" them. It reads the raw data and writes a structured wiki. This includes generating summaries, identifying key concepts, authoring encyclopedia-style articles, and—crucially—creating **backlinks** between related ideas.
3. **Active Maintenance (Linting):** The system isn't static. Karpathy describes running "health checks" or "linting" passes where the LLM scans the wiki for inconsistencies, missing data, or new connections. As community member **Charly Wargnier** observed, "It acts as a living AI knowledge base that actually heals itself."

By treating Markdown files as the "source of truth," Karpathy avoids the "black box" problem of vector embeddings. Every claim made by the AI can be traced back to a specific `.md` file that a human can read, edit, or delete.

## Implications for the enterprise

While Karpathy’s setup is currently described as a "hacky collection of scripts," the implications for the enterprise are immediate.

As entrepreneur [Vamshi Reddy (@tammireddy) noted in response](https://x.com/tammireddy/status/2039814328229204201) to the announcement: "Every business has a raw/ directory. Nobody’s ever compiled it. That’s the product."

Karpathy agreed, suggesting that this methodology represents an "incredible new product" category.

Most companies currently "drown" in unstructured data—Slack logs, internal wikis, and PDF reports that no one has the time to synthesize.

A "Karpathy-style" enterprise layer wouldn't just search these documents; it would actively author a "Company Bible" that updates in real-time.

As AI educator and newsletter author [Ole Lehmann put it on X](https://x.com/itsolelehmann/status/2040119257581646030): "i think whoever packages this for normal people is sitting on something massive. one app that syncs with the tools you already use, your bookmarks, your read-later app, your podcast app, your saved threads."

Eugen Alpeza, co-founder and CEO of AI enterprise agent builder and orchestration startup Edra, [noted in an X post that:](https://x.com/EugenAlpeza/status/2040094828021551495) "The jump from personal research wiki to enterprise operations is where it gets brutal. Thousands of employees, millions of records, tribal knowledge that contradicts itself across teams. Indeed, there is room for a new product and we’re building it in the enterprise."

As the community explores the "Karpathy Pattern," the focus is already shifting from personal research to multi-agent orchestration.

A recent architectural breakdown by [@jumperz](https://x.com/jumperz/status/2040166448492900356), founder of AI agent creation platform [Secondmate](https://waitlist.secondmate.io/), illustrates this evolution through a "Swarm Knowledge Base" that scales the wiki workflow to a 10-agent system managed via OpenClaw.

![](https://x.com/i/status/2040166448492900356)

The core challenge of a multi-agent swarm—where one hallucination can compound and "infect" the collective memory—is addressed here by a dedicated "Quality Gate."

Using the Hermes model (trained by Nous Research for structured evaluation) as an independent supervisor, every draft article is scored and validated before being promoted to the "live" wiki.

This system creates a "Compound Loop": agents dump raw outputs, the compiler organizes them, Hermes validates the truth, and verified briefings are fed back to agents at the start of each session. This ensures that the swarm never "wakes up blank," but instead begins every task with a filtered, high-integrity briefing of everything the collective has learned

## Scaling and performance

A common critique of non-vector approaches is scalability. However, Karpathy notes that at a scale of ~100 articles and ~400,000 words, the LLM’s ability to navigate via summaries and index files is more than sufficient.

For a departmental wiki or a personal research project, the "fancy RAG" infrastructure often introduces more latency and "retrieval noise" than it solves.

Tech podcaster [Lex Fridman (@lexfridman)](https://x.com/lexfridman/status/2039841897066414291) confirmed he uses a similar setup, adding a layer of dynamic visualization:

> "I often have it generate dynamic html (with js) that allows me to sort/filter data and to tinker with visualizations interactively. Another useful thing is I have the system generate a temporary focused mini-knowledge-base... that I then load into an LLM for voice-mode interaction on a long 7-10 mile run."

This "ephemeral wiki" concept suggests a future where users don't just "chat" with an AI; they spawn a team of agents to build a custom research environment for a specific task, which then dissolves once the report is written.

## Licensing and the ‘file-over-app’ philosophy

Technically, Karpathy’s methodology is built on an open standard (Markdown) but viewed through a proprietary-but-extensible lens (note taking and file organization app [Obsidian](https://obsidian.md/)).

- **Markdown (.md):** By choosing Markdown, Karpathy ensures his knowledge base is not locked into a specific vendor. It is future-proof; if Obsidian disappears, the files remain readable by any text editor.
- **Obsidian:** While Obsidian is a proprietary application, its "local-first" philosophy and EULA (which allows for free personal use and requires a license for commercial use) align with the developer's desire for data sovereignty.
- **The "Vibe-Coded" Tools:** The search engines and CLI tools Karpathy mentions are custom scripts—likely Python-based—that bridge the gap between the LLM and the local file system.

This "file-over-app" philosophy is a direct challenge to SaaS-heavy models like Notion or Google Docs. In the Karpathy model, the user owns the data, and the AI is merely a highly sophisticated editor that "visits" the files to perform work.

## Librarian vs. search engine

The AI community has reacted with a mix of technical validation and "vibe-coding" enthusiasm. The debate centers on whether the industry has over-indexed on Vector DBs for problems that are fundamentally about structure, not just similarity.

Jason Paul Michaels ([@SpaceWelder314](https://x.com/SpaceWelder314/status/2039908481742885007)), a welder using Claude, echoed the sentiment that simpler tools are often more robust:

> "No vector database. No embeddings... Just markdown, FTS5, and grep… Every bug fix… gets indexed. The knowledge compounds."

However, the most significant praise came from [Steph Ango](https://stephango.com/) ([@Kepano](https://x.com/kepano/status/2039831289533227446)), co-creator of Obsidian, who highlighted a concept called "Contamination Mitigation."

He suggested that users should keep their personal "vault" clean and let the agents play in a "messy vault," only bringing over the useful artifacts once the agent-facing workflow has distilled them.

## Which solution is right for your enteprise vibe coding projects?

| **Feature** | **Vector DB / RAG** | **Karpathy’s Markdown Wiki** |
| --- | --- | --- |
| **Data Format** | Opaque Vectors (Math) | Human-Readable Markdown |
| **Logic** | Semantic Similarity (Nearest Neighbor) | Explicit Connections (Backlinks/Indices) |
| **Auditability** | Low (Black Box) | High (Direct Traceability) |
| **Compounding** | Static (Requires re-indexing) | Active (Self-healing through linting) |
| **Ideal Scale** | Millions of Documents | 100 - 10,000 High-Signal Documents |

The "Vector DB" approach is like a massive, unorganized warehouse with a very fast forklift driver. You can find anything, but you don’t know why it’s there or how it relates to the pallet next to it. Karpathy’s "Markdown Wiki" is like a curated library with a head librarian who is constantly writing new books to explain the old ones.

## The next phase

Karpathy’s final exploration points toward the ultimate destination of this data: Synthetic Data Generation and Fine-Tuning.

As the wiki grows and the data becomes more "pure" through continuous LLM linting, it becomes the perfect training set.

Instead of the LLM just reading the wiki in its "context window," the user can eventually fine-tune a smaller, more efficient model on the wiki itself. This would allow the LLM to "know" the researcher’s personal knowledge base in its own weights, essentially turning a personal research project into a custom, private intelligence.

Bottom-line: Karpathy hasn't just shared a script; he’s shared a philosophy. By treating the LLM as an active agent that maintains its own memory, he has bypassed the limitations of "one-shot" AI interactions.

For the individual researcher, it means the end of the "forgotten bookmark."

For the enterprise, it means the transition from a "raw/ data lake" to a "compiled knowledge asset." As Karpathy himself summarized: "You rarely ever write or edit the wiki manually; it's the domain of the LLM." We are entering the era of the autonomous archive.