---
title: "No Cost Guide to Local AI Code Assistant: Roo Code + Ollama"
source: "https://medium.com/@sahil_tah/no-cost-guide-to-local-ai-code-assistant-roo-code-ollama-6c2b4d1c252f"
author:
  - "[[Sahil Tah]]"
published: 2025-11-16
created: 2026-05-15
description: "Companies today are finding ways to leverage AI to boost productivity. Be it Design or Development, every domain today have tools already av"
tags:
  - "clippings"
---
![](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*Ie5RHPT5guowhNJw)

Photo by Igor Omilaev on Unsplash

Companies today are finding ways to leverage AI to boost productivity. Be it Design or Development, every domain today have tools already available they can work with to speed up their deliverables. Big tech companies are proudly announcing “ **AI Generated code** ” numbers during their earnings calls which is evident they’re bullish on this trend.

## Get Sahil Tah’s stories in your inbox

Join Medium for free to get updates from this writer.

While most companies have already started sharing paid licenses of these tools (like ChatGPT, Claude, Co-pilot, Figma) with their employees, others remain in the evaluation phase, weighing the merits of investment.

![](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*sTQ-kRh-9Zt4Zj22cKJWdw.jpeg)

Made using: https://imgflip.com/

In today’s fast paced world of software development, professionals heavily depend on AI tools like ChatGPT, Perplexity, and Claude to learn new things, write code, and debug issues quickly. However, corporate restrictions often driven by compliance and data privacy concerns prohibit their use, creating significant hurdles in productivity. This leaves teams frustrated, as these powerful resources remain accessible yet off-limits, forcing reliance on slower, less efficient alternatives.

## Open Source Tools to the Rescue

Thankfully, open source options offer a smart way around these restrictions. Setting them up locally gives you all the perks of an AI coding sidekick without any worry about your data heading out.

### Ollama

Ollama is an open-source platform that allows users to run large language models (LLMs) locally on their own hardware, such as laptops or desktops, without relying on cloud services. Read [here](https://docs.ollama.com/).

### Hugging Face

Often called the GitHub for AI, Hugging Face is an open-source platform and community hub where users can access, share, and collaborate on pre-trained AI models, datasets, and applications. Read [here](https://huggingface.co/).

### Roo Code

Roo Code is an open-source AI-powered coding assistant designed as a **VS Code** extension. Read more about it [here](https://roocode.com/).

## Setting up your Local AI IDE

Setup of VS code and Ollama is outside the scope of this blog. There are tons of good resources which already covers this.

1. Download and install Ollama. Once installed, start the Ollama service in your terminal with `ollama serve` to begin local model hosting.
2. Use Ollama to pull a suitable model optimized for your use case. In your terminal, execute `ollama pull gpt-oss:latest` (or your preferred model).
![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*uJtFy4wip9tcH67zYo5VwQ.png)

Command: ollama pull gpt-oss:latest

3\. Install **Roo Code** extension in VS Code.

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*rq60uw6XEtmc6dxNC5iv4w.png)

VS Code Extensions Marketplace

4\. Configure LLM Provider for this extension. With our step 1, Ollama starts a REST API server at `http://localhost:11434`. It’ll automatically pull the list of local models present, simply select the one you wish to use.

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*GFU2FcjEuShOwSXD3aT6Sg.png)

Configuring Roo Code with Ollama

This setup not only delivers secure, unrestricted AI assistance but also unlocks offline productivity, allowing developers to code and iterate without internet dependency, which is crucial for remote work or unstable connections.

## Hands on Testing

I fed the AI a Golang function and asked it to break down what it does. Here’s a quick preview:

![](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*vhZhioNLvqaat0BJObTzSw.png)

Quick Preview

You can also keep tabs on the CPU and GPU resources Ollama uses during inference by running the `ollama ps` command in another terminal while the task is underway. This handy tool displays real-time details like model loading status, memory allocation, and whether it's leveraging your GPU or falling back to CPU, helping you spot bottlenecks or optimize hardware usage.

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*uZBmW-ggtq7Po3d3Nh7pQA.png)

Command: ollama ps

Roo Code features a system of “ **Specialized personas** ” or modes that transform the AI assistant into tailored roles for different stages of software development, allowing users to switch contexts seamlessly for more efficient workflows.

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*z120WoHhBD_N5T1Rr0uCyg.png)

Roo Code’s Specialized personas

## Final Thoughts: Your Path Forward

Setting up this local, no cost AI coding assistant isn’t merely a workaround, it’s a true game changer, delivering powerful open-source capabilities while keeping your data private and freeing you to innovate without any restrictions. That said, while this solution sidesteps many compliance hurdles, it’s wise to perform thorough due diligence within your organization before rolling it out at work.