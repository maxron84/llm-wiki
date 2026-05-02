---
title: "What Is Andrej Karpathy’s LLM Wiki — And How Can You Extend It?"
source: "https://medium.com/@visrow/what-is-andrej-karpathys-llm-wiki-and-how-can-you-extend-it-08e967d25321"
author:
  - "[[Vishal Mysore]]"
published: 2026-04-18
created: 2026-05-02
description: "Karpathy’s LLM Wiki compiles documents into a persistent, compounding knowledge base. This article explains the pattern and extends it with 5W1H context framing (LLM WikiZZ) — with a live demo and open-source code"
tags:
  - "clippings"
---
## Karpathy’s LLM Wiki compiles documents into a persistent, compounding knowledge base. This article explains the pattern and extends it with 5W1H context framing (LLM WikiZZ) — with a live demo and open-source code

On April 4, 2026, Andrej Karpathy — co-founder of OpenAI and former AI Director at Tesla — [published a GitHub Gist titled “LLM Wiki”](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) that quietly set off a wave of discussion across the AI developer community. Within two weeks it had accumulated over 5,000 stars and 4,400 forks, with dozens of independent implementations appearing on GitHub. The idea Karpathy proposed was deceptively simple: instead of using an LLM to retrieve and answer from raw documents at query time — the standard RAG approach used by tools like NotebookLM and ChatGPT file uploads — you use the LLM to **incrementally build and maintain a persistent, interlinked wiki** from your sources. The LLM reads each new document, extracts key information, updates entity and concept pages, flags contradictions with existing knowledge, and strengthens cross-references — all before you ask a single question. The result is a compounding knowledge base that gets richer with every source added, rather than rediscovering the same relationships from scratch on every query. Karpathy described the human’s role simply: *“curate sources, direct the analysis, ask good questions.”* The LLM handles everything else.

## The “Transient Knowledge” Paradox

When you upload a document to a Large Language Model (LLM), you are usually trapped in a cycle of transient RAG. The system rediscovers the document from scratch for every query, neglecting the “Context Debt” that builds up when an LLM doesn’t truly understand the fundamental frame of the data.

## Get Vishal Mysore’s stories in your inbox

Join Medium for free to get updates from this writer.

**LLM WikiZZ** is my open-source tool designed to break this cycle. Inspired by Andrej Karpathy’s vision of a compounding “LLM-Wiki,” it forces an autonomous **Discovery Phase** before a single question is answered. It teaches the LLM to architect its own scaffolding before it starts building the response.

## What is LLM WikiZZ?

WikiZZ is an experimental logic layer that sits between the user and the LLM. Instead of direct prompting, it implements a structured **5W1H Wiki Frame**:

1. **Who**: The target audience/persona context.
2. **What**: The core mission objective.
3. **When**: The temporal and urgency context.
4. **Where**: The situational and environmental context.
5. **Why**: The underlying motivation/value.
6. **How**: The structural and formatting requirement.

> Live demo is here [https://vishalmysore.github.io/lllmwikiZZ/](https://vishalmysore.github.io/lllmwikiZZ/)

## How WikiZZ Transforms the “Wiki” Workflow

## 1\. Autonomous Scaffolding

In traditional workflows, the user is the “Clerk,” manually specifying the context for every query. In WikiZZ, the LLM becomes the “Architect.” By clicking “Generate Wiki,” the LLM analyzes the entire document and autonomously populates the 5W1H frame. This turns raw data into a persistent, shared mental model between the human and the machine.

## 2\. The Contrast Engine

One of the hardest parts of evaluating AI performance is seeing the “value-add” of context. WikiZZ runs a side-by-side comparison:

- **Plain Mode**: Standard, context-less RAG.
- **WikiZZ Mode**: The query refined through the persistent 5W1H window.

Users can see exactly how the framing adds technical specificity and logical organization that plain queries often hallucinate away.

## 3\. The LLM Jury

The system includes a high-intelligence **Evaluator LLM** that acts as a judge. It semantically analyzes the delta between the two answers, identifying specifically what improved — whether it was situational relevance, concision, or technical depth.

## Technical Architecture

- **Zero-Server/Static-First**: The app runs entirely in your browser. Privacy is prioritized; your documents are parsed locally via `FileReader` and never stored.
- **Secure CORS Proxying**: It leverages a secure Cloudflare Worker to route API requests to high-performance providers like **NVIDIA NIM, Anthropic, and Gemini**.
- **Persistent Context**: Once generated, the WikiZZ Frame persists for the session, compounding its value over multiple queries.

![](https://miro.medium.com/v2/resize:fit:2548/format:webp/1*VUL_WYZVjCWMg7-D3O3_IQ.png)

## Turning Translators into Architects

LLM WikiZZ proves that the most valuable thing an LLM can do isn’t answering the question — it’s **understanding the request**.

Consider a technical document on global warming: A “Plain” query might give you a standard list of environmental impacts. But with **WikiZZ Framing**, the LLM recognizes its “Why” and “What” as providing a technical guide for policymakers. Suddenly, that simple list is restructured into a mapped directory of chemical emissions — all without the user asking for that extra depth.

This is the shift from a machine that translates to a machine that architectures.

![](https://miro.medium.com/v2/resize:fit:3066/format:webp/1*fNagndx8wAW-uMxCyDDFFA.png)

## The Compounding Web: From Documents to a Living Knowledge Graph

As the LLM continuously ingests documents through the WikiZZ 5W1H frame, something remarkable begins to happen. Each new document doesn’t just add isolated pages to the wiki — it adds **nodes to a growing, interconnected web**. The “Who” of one document connects to the “Who” of another. The “Why” of a medical paper links to the “Why” of a policy document. Over time, the LLM stops seeing documents as separate silos and starts seeing a **structured semantic network** — where a surgeon, a patient, and a policymaker are all connected nodes with different lenses on the same underlying knowledge. This is the true promise of LLM WikiZZ: not just better answers to individual queries, but the gradual emergence of a **living knowledge graph** where context compounds, contradictions surface automatically, and the distance between any two pieces of information collapses. The wiki doesn’t just grow — it gets smarter with every document added, every query asked, and every 5W1H frame generated

*This article builds on Andrej Karpathy’s LLM Wiki pattern. All extensions and implementations are original work by the author.*