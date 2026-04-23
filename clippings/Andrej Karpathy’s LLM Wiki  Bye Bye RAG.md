---
title: "Andrej Karpathy’s LLM Wiki : Bye Bye RAG"
source: "https://medium.com/data-science-in-your-pocket/andrej-karpathys-llm-wiki-bye-bye-rag-ee27730251f7"
author:
  - "[[Mehul Gupta]]"
published: 2026-04-07
created: 2026-04-23
description: "What is LLM Wiki by Andrej Karpathy?"
tags:
  - "clippings"
---
## What is LLM Wiki by Andrej Karpathy?

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*Us_SQvB3quoCzctI)

Photo by Patrick Tomasso on Unsplash

Today we’re going to talk about a very interesting idea that is slowly picking up, **LLM Wiki**. This concept was shared by Andrej Karpathy, and once you really understand it, you start noticing the limitations of how we currently use LLMs.

Most people today interact with tools like ChatGPT or NotebookLM in a very similar way. You upload some documents, maybe PDFs or notes, and then you ask questions. It feels powerful, but under the hood, the system is doing something quite repetitive.

Let’s break this down step by step.

## [Model Context Protocol: Advanced AI Agents for Beginners (Generative AI books)](https://www.amazon.in/Model-Context-Protocol-Beginners-Generative-ebook/dp/B0FCCF348X?ref_=saga_dp_ss_dsk_dp&source=post_page-----ee27730251f7---------------------------------------)

### Model Context Protocol: Advanced AI Agents for Beginners (Generative AI books) eBook: Gupta, Mehul, Sen, Niladri…

www.amazon.in

## The Problem with Current LLM Usage (RAG)

Right now, most systems rely on something called Retrieval-Augmented Generation.

> The idea is simple. Whenever you ask a question, the system searches through your uploaded documents, pulls out the most relevant pieces, and then generates an answer from those pieces.

It works well for many cases, especially when the answer exists clearly in one or two documents. But the problem starts showing up when your knowledge grows.

Every time you ask a question, the LLM is essentially doing this:

> Search the documents again
> 
> Pick relevant chunks again
> 
> Generate the answer again

There is no memory of past understanding. There is no accumulation of knowledge.For example, imagine you have 50 PDFs about AI, startups, and markets. You ask:

***“Which AI companies are most likely to win in the next 5 years?”***

To answer this well, the model might need to combine ideas from multiple documents, trends, company strategies, funding data, and technology shifts. But with RAG, it has to rediscover all of that every single time.

It’s like studying for an exam by re-reading all your books every time you get a question.

## The Core Idea of LLM Wiki

Now here’s where the shift happens.

Instead of repeatedly searching raw documents, the idea behind LLM Wiki is:

> Let the LLM **build a structured knowledge base first**, and then use it. Think of it like your own personal Wikipedia, but automatically maintained by the LLM.

When you add a new document, the LLM doesn’t just store it. It processes it. It extracts key ideas. It creates or updates “pages.” It links concepts together.

Over time, your system stops being a collection of files and becomes a **connected knowledge graph written in natural language**.

So instead of:

\-Raw PDFs and notes

You now have:

\-Clean, structured, interlinked knowledge pages

## How It Actually Works (Detailed Example)

Let’s take a more concrete example.

Say you are researching electric vehicles.

You upload three documents:

- A Tesla annual report
- A deep dive into battery technology
- A blog on EV market trends

In a typical RAG system, these would just sit as three separate documents.But in an LLM Wiki system, something very different happens.

The LLM reads all three and starts creating structured pages like:

- “Tesla”
- “Battery Technology”
- “EV Market Trends”

> Now comes the important *part*, **linking**.

- The Tesla page will reference battery technology because Tesla’s performance depends on batteries.
- The battery page will mention future innovations and cost reductions.
- The market trends page will connect both Tesla and battery advancements to adoption rates.

So instead of three isolated documents, you now have a connected system.

Now when you ask:

> “Who will dominate EV batteries in the future?”

The LLM doesn’t go back to raw PDFs. It navigates your already-built wiki. It reads summarized, structured, and linked knowledge. That means:

> Faster answers
> 
> More consistent reasoning
> 
> Better multi-document understanding

And here’s the powerful part, the answer itself can be saved as a new page like:

## Get Mehul Gupta’s stories in your inbox

Join Medium for free to get updates from this writer.

“Future Leaders in EV Batteries”

### So your system is not just answering questions. It is growing smarter over time.

## RAG vs LLM Wiki (The Real Difference)

The difference becomes very clear when you look at how both systems behave over time.

> RAG is stateless in practice. Each query is independent. Nothing compounds. LLM Wiki is stateful. Knowledge builds on top of previous knowledge.

You can think of it like this:

- RAG: Search → Answer → Reset
- LLM Wiki: Read → Organize → Link → Improve → Reuse

A few key differences worth noting:

- RAG works directly on raw documents every time
- LLM Wiki works on structured and refined knowledge
- RAG answers disappear inside chat history
- LLM Wiki converts answers into reusable knowledge
- RAG does not improve with usage
- LLM Wiki compounds knowledge, almost like compound interest

## Why This Is Powerful

The hardest part of building a knowledge base is not reading information. It’s maintaining it.

Things like:

- Updating notes when new information comes in
- Connecting related ideas
- Keeping everything consistent
- Avoiding contradictions

Humans are not great at this at scale. It becomes messy very quickly. LLMs, on the other hand, are very good at exactly this kind of work.

They can:

- Update multiple pages when new data arrives
- Maintain links between concepts
- Rewrite sections for clarity
- Detect and resolve contradictions

So your role shifts.

Instead of organizing knowledge manually, you focus on:

- Finding high-quality inputs
- Asking meaningful questions

And the LLM handles the structure and maintenance.

## A Simple Way to Think About It

Think of it in very simple terms.

Your files are raw ingredients.The LLM Wiki is the cooked meal.

With RAG, you cook every time you are hungry. With LLM Wiki, you build a kitchen that keeps improving its recipes.

## Where You Can Use This

This idea is not limited to research. It becomes useful anywhere knowledge builds over time.

For example:

- Learning AI deeply (connecting concepts like transformers, RAG, agents)
- Creating YouTube content (topics, scripts, linked ideas)
- Building courses (structured modules instead of scattered notes)
- Personal knowledge management
- Team knowledge bases in companies

Anywhere you are collecting and reusing information, this approach fits naturally.

## Final Thought

This idea might look small at first, but it represents a real shift in how we use LLMs.

We are moving from:

> LLMs that simply answer questions
> 
> to
> 
> LLMs that **build and maintain knowledge systems**

And once that system exists, answering questions becomes the easiest part.