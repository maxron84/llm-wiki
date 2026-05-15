---
title: "Roo Code not using tools properly in offline setup (with Ollama models and Open AI Compatible API provider)"
source: "https://www.reddit.com/r/RooCode/comments/1klj7j5/roo_code_not_using_tools_properly_in_offline/"
author:
  - "[[VerbalVirtuoso]]"
published: 2025-05-13
created: 2026-05-15
description: "SOLVED! HAD TO CREATE A CUSTOM OLLAMA MODEL WITH LARGER CONTEXT SIZE Hi all! 👋 I love to use Roo Code, and therefore"
tags:
  - "clippings"
---
**SOLVED! HAD TO CREATE A CUSTOM OLLAMA MODEL WITH LARGER CONTEXT SIZE**

Hi all! 👋

I love to use Roo Code, and therefore I'm trying to get Roo Code to work fully offline on a local Windows system at work. I’ve successfully installed the`.vsix` package of Roo Code (version 3.16.6) and connected it to a local Ollama instance running models like `gemma3:27b-it-q4_K_M`and `qwen2.5-coder:32b` via Open WebUI. The API provider is set to "OpenAI Compatible", and API communication appears to be working fine — the model responds correctly to prompts.

However, Roo does not seem to actually **use any tools** when executing instructions like "create a file" or "write a Python script in my working directory". Instead, it just replies with text output — e.g., giving me the Python script *in the chat* rather than writing to a file.

I also notice it's not retaining memory or continuity between steps. Each follow-up question seems to start fresh, with no awareness of the previous context.

It also automatically sends another API request after providing an initial answer where it in the beginning of the request says:

\[ERROR\] You did not use a tool in your previous response! Please retry with a tool use.

My setup:

- Roo Code 3.16.6 installed via `.vsix` following the instructions from the official Roo Code repository
- VS Code on Windows
- Ollama with Gemma and Qwen models running locally
- Open WebUI used as the backend provider (OpenAI-compatible API)

Has anyone gotten tool usage (like file creation or editing) working in this kind of setup? Am I missing any system prompt config files, or can it be that the Ollama models are failing me?

Any help is appreciated!

Below is an example of a API request I tried without the offline Roo creating a new file:

> <task>

> Create the file [app.py](http://app.py/) and write a small python script in my work directory.

> </task>

> <environment\_details>

> **\# VSCode Visible Files**

> **\# VSCode Open Tabs**

> ../../../AppData/Roaming/Code/User/settings.json

> **\# Current Time**

> 5/13/2025, 12:30:23 PM (Europe, UTC+2:00)

> **\# Current Context Size (Tokens)**

> (Not available)

> **\# Current Cost**

> $0.00

> **\# Current Mode**

> <slug>code</slug>

> <name>💻 Code</name>

> <model>gemma3:27b-it-q4\_K\_M</model>

> **\# Current Workspace Directory (c:/Users/x/Documents/Scripting/roo\_test) Files**

> roo-cline-3.16.6.vsix

> </environment\_details>

---

## Comments

> **LoSboccacc** · [2025-05-13](https://reddit.com/r/RooCode/comments/1klj7j5/comment/ms2ot99/) · 4 points
> 
> Have you extended ollama context?
> 
> > **VerbalVirtuoso** · [2025-05-13](https://reddit.com/r/RooCode/comments/1klj7j5/comment/ms2oxq3/) · 1 points
> > 
> > No! How should I go forward to do this on an offline system?

> **VerbalVirtuoso** · [2025-05-13](https://reddit.com/r/RooCode/comments/1klj7j5/comment/ms2ztji/) · 2 points
> 
> Solved: Created a custom Ollama-model with bigger context window using a `Modelfile` with and `ollama create` command, and now it works!

> **armaver** · [2025-05-13](https://reddit.com/r/RooCode/comments/1klj7j5/comment/ms5jhm6/) · 1 points
> 
> How does this offline model compare to Claude 3.7, Gemini 2.5?
> 
> > **VerbalVirtuoso** · [2025-05-23](https://reddit.com/r/RooCode/comments/1klj7j5/comment/mtw7psx/) · 2 points
> > 
> > It has a much worse performance than Claude and Gemini.