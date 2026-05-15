---
title: "Local model for coding"
source: "https://www.reddit.com/r/RooCode/comments/1jf1242/local_model_for_coding/"
author:
  - "[[MarxN]]"
published: 2025-03-19
created: 2026-05-15
description: "Do you have good experience with local model? I've tried a few on MacBook with 64GB and it works with acceptable speed. But I have a few pro"
tags:
  - "clippings"
---
Do you have good experience with local model? I've tried a few on MacBook with 64GB and it works with acceptable speed. But I have a few problems.

One is context window. I've tried to use Ollama and turned out it had 2k limit. Tried multiple ways to overcome it, and the only solution was to rewrite model with bigger context.

Then I've tried LM studio, because it can use optimized for Mac MLX models. But whatever model I'm trying to use, roo complain that its context is too small.

I'd also have possibility to use free network models, and use local model only if none of net models have free tokens. So the best would be to have some sort of ordered list of models, and roo should try them one by one until it find one which accept request. Is it possible?

---

## Comments

> **hiper2d** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/mip7lry/) · 6 points
> 
> This model works locally with RooCode: [https://ollama.com/tom\_himanen/deepseek-r1-roo-cline-tools:14b](https://ollama.com/tom_himanen/deepseek-r1-roo-cline-tools:14b)
> 
> Nothing else I tried did. Gemma 3 doesn't work. I think the problem is with very specific prompts RooCode/Cline uses. Smaller models get lost in them and cannot produce the expected output. They don't even understand what the task is. I say "hi", and they just go crazy with generating random outputs. The model I posted is probably fine-tunned on Cline's prompts, and this is why it more or less works.
> 
> > **tehsilentwarrior** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/mipmsll/) · 5 points
> > 
> > Need to give that a try.
> > 
> > I literally have a computer with a 4090 that I haven’t turned on in months.
> > 
> > It was meant for gaming but with two kids and work I basically don’t have time
> > 
> > > **hiper2d** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/mipwwwy/) · 2 points
> > > 
> > > With 4090, you can try 32B or even 70B. I don't know if it is good though. I just know it works, which is already something
> 
> > **MarxN** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/mire8w0/) · 2 points
> > 
> > yes, I've found it on my own when someone mentioned it in different thread, and it works quite well. As expected, it's much slower then for example gemini flash 2.0, but usable.

> **cmndr\_spanky** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/miulbsu/) · 4 points
> 
> I ran into the exact same problems as you and finally found a good resolution, which is on my very very similar post on this subreddit: [https://www.reddit.com/r/RooCode/comments/1jdvcce/am\_i\_doing\_something\_wrong\_or\_is\_roocode\_an/](https://www.reddit.com/r/RooCode/comments/1jdvcce/am_i_doing_something_wrong_or_is_roocode_an/)
> 
> 1. People on this subreddit aren't really using local models much so I suspect that's why there's so little info, even the 32b coder models are pretty shit compared to chatGPT and Claude, and all of the leaderboard tests you see published are tiny code challenges that don't even come close to reflecting the kind of stress on an LLM with a limited context window would have on a real code base dealing with project that solve real enterprise use cases. If you can run a 70b you might have better luck, which in theory at Q4 would fit on a 64gb m-series Mac.
> 2. LM Studio's interface for Roo is broken as fuck, there's no easy way around the context window mismatch thing and all you can do is ditch it and stick with Ollama. Use Ollama. End of story if you plan to try local models. The context window problems are simpler this way
> 3. The default coder models shit the bed instantly because the Roo prompts confuse them. Some folks have gone through the trouble of further fine tuning those coder models so they better handle instructions from Roo:
> 
> This series worked well for me: [https://ollama.com/hhao/qwen2.5-coder-tools](https://ollama.com/hhao/qwen2.5-coder-tools)
> 
> Specifically you want to use 32b, which will easily work within 64gb, but I highly suggest you also try a 70b. maybe this one:
> 
> [https://ollama.com/tom\_himanen/deepseek-r1-roo-cline-tools:70b](https://ollama.com/tom_himanen/deepseek-r1-roo-cline-tools:70b)
> 
> it should still give you enough space for a big context window given your 64gb. However, it's a reasoning model so it will potentially waste a lot of context on its self reasoning loops.
> 
> 4) Once you see Roo working properly with these flavors of popular coder models, it will actually not error out, it will successfully edit files and actually seem like it's working. However, you'll quickly notice it still doesn't produce great code that works out of the box, it gets confused about larger code bases, it doesn't understand what libraries your using and will just make wild assumptions about function calls into those libraries.
> 
> That's because local sub 70b models aren't there yet, it's just too early. Anyone on here or on YouTube telling you CODE WITH LOCAL MODELS NOW. ITS GREAT ..LIKE AND SUBSCRIBE. is a lying sack of shit trying to get clicks, because they are only testing this stuff on dumb tiny exercises, and none these local models are good enough yet for real local software development on real sizable projects (even if you do a good job of modularizing your code).
> 
> Can it help with one specific tiny function if you ask it to help you write a bubble sort function? Sure it'll do great at that.. But that's not really that useful IMO, I could google it faster :)
> 
> Most of the time I spend so much time debugging and reading docs to compensate for the garbage a 32b model is giving me, it would have been faster if I just wrote it myself without AI assistance.
> 
> > **MarxN** · [2025-03-21](https://reddit.com/r/RooCode/comments/1jf1242/comment/miziynm/) · 1 points
> > 
> > I didn't spend as much time as you, but I see it similarly. Local model works with Ollama and it's ok but not great. Free access to Gemini is much more useful
> > 
> > > **cmndr\_spanky** · [2025-03-21](https://reddit.com/r/RooCode/comments/1jf1242/comment/mizncom/) · 1 points
> > > 
> > > did you try the special qwen 2.5 tools variant for "Cline"? It's the best ollama model for Roo I've found by far, and although I was very negative in my previous post.. the 32b one is somewhat workable if you're very careful about how you prompt it, give it one file or module at a time to deal with, and are good at reading and debugging python when it stumbles.

> **ironimity** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/mipibp4/) · 2 points
> 
> LMStudio allows you to increase the context window before starting the server, this can be helpful.

> **Significant-Crow-974** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/miucyo6/) · 2 points
> 
> Coincidentally, I was involved in exactly the same exercise to see if I could get a decent lm studio or ollama llms to work with vsc for coding. I have been spending $600/month on Claude and needed to sort that out. I went through tens of llms and eventually gave up. I found that Gemini Pro v2.0 to be a reasonable alternative but the difference in quality and speed between Gemini and Claude is significant.

> **AllowMe2Retort** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/mint38e/) · 1 points
> 
> I'm currently looking into using local LLMs, any chance you could share which ones you've success with? And any particular settings changes for the LLMs or in Roo that helped getting them work better?
> 
> > **MarxN** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/mirei2t/) · 1 points
> > 
> > from those free I have quite good experience with gemini Flash 2.0. As for local
> > 
> > \- qwen2.5-coder:14b with manually extended context
> > 
> > \- maryasov/qwen2.5-coder-cline:32b also works ok, but is a little slow

> **yeswearecoding** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/minx6w3/) · 1 points
> 
> On my RTX 3060 12gb, I can use a 2b model (granite3.2) with a context of 64k maximum. The speed is usable, but quickly the context isn't enough. Maybe you could have more luck with your Macbook (if it's an ARM version) To set the context number, the better solution have found is to create a model from a Modelfile (which come from original model, 'show' option from command ligne)

> **nxtmalteser** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/miokncn/) · 1 points
> 
> Following

> **Eastern-Cookie3069** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/miovd6s/) · 1 points
> 
> I've been wondering how the new gemma 3 models work for this, has anyone tried it? It seems to be very good for single-GPU systems.

> **Eastern-Cookie3069** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/miove2b/) · 1 points
> 
> I've been wondering how the new gemma 3 models work for this, has anyone tried it? It seems to be very good for single-GPU systems.

> **Dry\_Gas\_1433** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/mir64g5/) · 1 points
> 
> Correction: Ollama has a default global context of 2k, but you can increase it either globally or per model. It’s a setting. You just need enough VRAM for the model plus context or everything slows down.
> 
> > **MarxN** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/mire31h/) · 1 points
> > 
> > Unfortunately it didn't work. Sth like  
> > ollama set parameter num\_ctx 32768
> > 
> > or using env variables didn't make any change.  
> > I had to "rewrite" model to make it using bigger context.
> > 
> > > **fr34k20** · [2025-03-24](https://reddit.com/r/RooCode/comments/1jf1242/comment/mjhilg1/) · 2 points
> > > 
> > > Did you found a bug ? I also recognized this behavior. Only changing the modelfile works properly. This has nothing todo with the model itself. It us with ollama
> > > 
> > > **Dry\_Gas\_1433** · [2025-03-22](https://reddit.com/r/RooCode/comments/1jf1242/comment/mj4zg9e/) · 1 points
> > > 
> > > All depends on which model. Some have a small context.
> > > 
> > > Anyway, did you have any luck?

> **joey2scoops** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/miryav8/) · 1 points
> 
> Ollama applies a 2k context limit to all their models. But you can change it. [https://github.com/ollama/ollama/blob/main/docs/faq.md#how-can-i-specify-the-context-window-size](https://github.com/ollama/ollama/blob/main/docs/faq.md#how-can-i-specify-the-context-window-size)
> 
> > **MarxN** · [2025-03-20](https://reddit.com/r/RooCode/comments/1jf1242/comment/miskn43/) · 2 points
> > 
> > saw it long time ago, but it simply didn't work for me. Ollama just ignored all such attempts
> > 
> > > **fr34k20** · [2025-03-24](https://reddit.com/r/RooCode/comments/1jf1242/comment/mjhiq8s/) · 1 points
> > > 
> > > Yep same for me on Linux and Mac. So I guess it’s a bug ?

> **WandyLau** · [2025-03-19](https://reddit.com/r/RooCode/comments/1jf1242/comment/mip347l/) · 1 points
> 
> I don’t think any open source models can meet your requirements on consumer’s device. The capability is not enough.