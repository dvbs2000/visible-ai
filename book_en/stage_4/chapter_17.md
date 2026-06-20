# Chapter 17 · The Context Window: The Tug-of-War Between Goldfish Memory and the Quadratic Bill

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** Chat with the AI for a while and it starts "forgetting" the setting you gave at the start; open a new session and it **doesn't know you at all.** Isn't it supposed to be smart?
> **🤔 Your turn:** Do you think the AI "remembers in its mind" what you said? If not, how does "continuous chatting" work?
> **🧱 The naive move hits a wall:** You'll probably assume "it's always online, remembering everything we chatted" — but then it should understand you more the longer you chat, and know you across sessions, and **neither of those holds.**
> The counterintuitive truth: a large model **has no memory store, only one window.** Read on. 👇


Leo pulled out his phone and waved it: "This means recomputing Chapter 9's **'quadratic handshake bill.'** Come on, let's make a **long-distance call,** chatting from sentence 1 to sentence 200 — and you'll get what 'goldfish memory' and 'a physical hard ceiling' mean (￣▽￣)"

---

## Section 1 · A Large Model Has No Memory Store, Only a "Window"

Leo first tossed out a counterintuitive fact: "A large model has **no 'memory store' whatsoever.** It doesn't 'remember in its mind' what you said; strictly speaking, it doesn't even have the concept of 'the previous round' — **every answer, to it, is the first time it's read this conversation.**"

> **Intuitive impression:** the AI is chatting with me continuously → it's always "online," remembering everything we chatted.
> **Real mechanism:** the model has only one "window" → each answer, what it can see is **only the tokens inside the window.**

"This window is the **context window,**" Leo said, "the cap on how many tokens the model can 'see' at once. The aptest metaphor is a **fixed-size desk:** all the material to consult must be laid on the desktop for the model to see; beyond the desktop there are no drawers, no shelves — **paper that doesn't fit equals nonexistent.**"

"Why design it this way? You've learned both roots:"

> 🔒 **Root 1 (Chapter 12):** knowledge is **frozen** in the parameters. The moment pretraining ends, the hundreds of billions of parameters are sealed. Chatting with it **changes not a single parameter** — the conversation content has **nowhere to be stored** inside the model. Only the window can store it.
> 🔒 **Root 2 (Chapter 9):** attention needs a **boundary.** "Every word looks at every word" — "every word" must have a cap, or the compute can't hold. This hard cap is the window size.

So how does "continuous chatting" work? Leo reconstructed it with our long-distance call:

> 🎬 **You hit send:** on screen, you only sent one line.
> 🎬 **The client packages:** the web/app stitches [system prompt + all previous Q&A + your new message] into one long text.
> 🎬 **Send the whole package to the model:** the model reads from the first token to the last — **as if reading this conversation for the first time.**
> 🎬 **Chain out an answer:** output token by token (old friend).
> 🎬 **Read and forget:** the answer is saved to the chat log, and the model instantly returns to a "blank slate" state. Next round? **Start over from step 1, just with a package one stretch longer.**

> Leo's finishing touch: "So-called 'chatting' is an illusion maintained by **the client resending the entire history for you every round.** Chapter 11's foreshadowing can now be collected: the API charges by token, and every round resends all history — **the question in round 50 pays again for all the tokens of rounds 1–49.** 'Pricier the longer you chat' isn't a pricing trick, it's the mechanism."

---

## Section 2 · Three Everyday Mysteries, One Key Opens All

"You've probably hit these three, and mostly thought it was 'AI glitching,'" Leo said. "Look again through the 'window' — **all three faces of the same mechanism:**"

> 🐟 **Mystery 1 · window full → forget the opening after chatting long**
> Once the window fills and more conversation comes, the earliest content is **squeezed out of the window.** The model didn't "forget" the opening — **the package sent to it no longer has the opening.** This is "goldfish memory."

> 🐟 **Mystery 2 · the window is session-level → new session forgets all**
> A new session = a brand-new empty window. The old session's history isn't packaged in, and the model has no cross-session storage — **it's not faking amnesia, it really is meeting you for the first time.**

> 🐟 **Mystery 3 · the window has a cap → long documents won't fit**
> A document's token count exceeds the window size, the model physically can't read it — hence a "file too long" error, or more insidiously: it's **quietly truncated,** the second half it never read, yet it answers confidently anyway.

"Here someone surely raises a hand," Leo mimicked Mia. "'Hold on, ChatGPT clearly remembers I'm a programmer!' Good question—"

> 🧠 This is the product-layer **"memory feature"** (ChatGPT's Memory, everyone's "custom instructions"): the product distills key points from your past conversations into **little notes stored in its own database,** and on a new session quietly **stuffs the notes back into the window.** **The model itself is still zero-memory — the product is passing it a cheat sheet.**

> Leo rapped the board: "Remember this layering — **'memory' is a product feature, not a model ability.** Every experience that looks like 'it remembered' is, in essence, the same move: **store the info outside the window, stuff it back in when needed.** Remember this move and next chapter's RAG will feel familiar."

---

## Section 3 · The Quadratic Handshake Bill: why a long call gets pricier the longer it runs

"Now recompute Chapter 9's bill," Leo picked up the phone. "Why is the window so precious? Chapter 9's 'every word looks at every word' now bares its fangs."

> 🤝 **The handshake bill:** 10 people at a round table, about **45 pairwise relationships;** switch to a 100-person meeting, about **5,000 pairs** — **10× the people, about 100× the 'mutual looks'!**
> Attention is identical: **10× the window length, compute climbs quadratically to about 100×.**

"Our long call to round 200," Leo demoed for Mia, "three bills arrive together:"

> 📈 **Bill 1 · compute:** length ×10, mutual looks ×100 — VRAM, electricity, chips all charged.
> 💸 **Bill 2 · wallet:** every question pays again for **all the history's tokens** (Chapter 11).
> ⏱️ **Bill 3 · time:** paste a super-long document and the AI is silent forever? It's **reading the whole window from the start** before it can chain the first word.

Leo also laid out an arms-race scorecard:

| Window magnitude | Roughly holds | Era |
|---|---|---|
| 4k tokens | a few-thousand-word article | around 2020, first-gen level |
| 128k tokens | a novella / a project's core code | 2023+ flagship standard |
| 1M+ tokens | several novels / an entire codebase | 2024–2025 top models |

> Mia: "So just make the window bigger?"
> Leo: "**Pricier, slower, and easier to lose the middle** (next section). So the engineering consensus isn't 'bigger window is better,' but — **make every token that enters the window worth its ticket.** Two window-saving routes: ① **rolling summary:** compress distant conversation into a short summary kept in the window; ② **RAG on-demand retrieval:** keep material external, each round retrieve only the most relevant few passages into the window (a whole chapter next)."

---

## Section 4 · Lost in the Middle: a big window doesn't mean clear reading

"By the above logic, a big enough window seems to fix everything," Leo turned. "But in 2023 Stanford and others ran an experiment that doused everyone in cold water."

> 🎬 **The experiment:** give the model dozens of documents, with **only one** hiding the answer, then place this key document at **different positions** in the context — beginning, middle, end — and ask the same question.
> 🎬 **Result:** placed at the **beginning** or **end,** likely correct; placed in the **middle,** accuracy drops noticeably — worst case even worse than giving no document and letting it answer closed-book!
> 🎬 Plotted, the accuracy is a **U-shaped curve;** the paper's title is **Lost in the Middle.**

"Why?" Leo explained. "Two intuitive explanations stack: one, **the statistics of training data** — articles state the thesis at the start, summarize at the end, and the latest few lines of a conversation are most relevant; important info naturally loves to sit at the two ends; two, super-long text is rare in the training corpus to begin with, so the model **lacks practice** at allocating attention to 'the far middle.'"

This lands as three practical habits:

> ✅ **Put key constraints at both ends:** the prompt sets the tone at the start, restates at the end — Chapter 16's "key points at both ends" mechanism is right in this U-shaped curve.
> ✅ **Slim long material first:** extract the relevant sections before asking, don't dump 200 pages of original in — **it fitting doesn't mean it's read clearly.**
> ✅ **Cite by name when asking:** "per the refund clause in Section 3..." pulls attention to the right spot better than "per the document above..."

---

## Section 5 · Squeeze the "Allergy History" Out of the Window by Hand

All talk and no practice is empty. Leo set up a window holding at most **4 rounds of conversation** (plus a pinned system prompt) and staged an "amnesia scene":

> 🎬 **Round 1:** Mia says "I'm allergic to peanuts." Key info **enters the window.**
> 🎬 **Rounds 2, 3, 4:** chat as usual. The AI proactively avoids peanuts — not that it "remembers," but that when this round was packaged, Round 1 was **still in the package** and it re-read it.
> 🎬 **Window full:** all 4 slots taken. In a real product this moment is **usually silent, with no "about to forget" warning.**
> 🎬 **Round 6 (new conversation comes in):** the earliest Round 1 is **squeezed out, grayed, drifting away.** It's still in the chat log (you can scroll to it), but **the package sent to the model no longer has it.**
> 🎬 **Amnesia scene ⚠:** you ask "can I eat peanut butter?", the window no longer has the allergy history. The model isn't lying or getting dumber — **it genuinely can't see it,** so by common sense it eagerly recommends peanut butter. **This is the standard script for a long-conversation faceplant.**

> Leo gave three countermeasures: "① Restate key info **now and then,** putting it back in the window's latest position; ② for long conversations **periodically have the AI summarize key points** and open a new session with the summary; ③ don't count on it to 'remember' important constraints, **bring them every time.**"

---

## Section 6 · Traps You'll Probably Fall Into Too

**Trap 1: "The AI remembers our last conversation; the more we chat, the better it understands me"**

> ❌ The product interface is too much like "chatting with a person."
> ✅ The truth is — **the model itself has zero memory, zero growth;** every "remembered" is the product's cheat sheet of storing info outside the window and stuffing it back in.

Root cause: the session list is always there and the memory feature occasionally shows up, easily making you imagine "an AI that knows me." Two steps to expose it: **turn off the memory feature and open a new session** — it really is meeting you for the first time; **open the "memory" list in settings** — those little notes are the entirety of its "knowing you," deleted and it forgets. **Telling model ability from product feature is this stage's most important discernment.**

**Trap 2: "Bigger window is always better; pick the model with the biggest window"**

> ❌ Mistaking "fits" for "reads well."
> ✅ The truth is — **a big window is pricey, slow, and easy to lose the middle;** putting the right info in the window beats stuffing all info in.

Root cause: 10× the window, compute climbs quadratically, every round is fully billed by token, first-word latency grows, and lost in the middle tells you: the longer you stuff in, the easier key info buried in the middle is missed. **A big window is a precious ability ceiling, not the default usage** — the everyday optimum is to slim the material, put key points at both ends, and use RAG when there's a lot.

---

## Section 7 · The Finishing Move: put the right info in the window

Same ritual: a manual + a kill shot.

### The context-window core, one table to mop it all up

| Concept | In a sentence |
|---|---|
| **context window** | the cap on tokens the model can see at once, a fixed-size desk |
| **continuous chatting** | an illusion the client maintains by resending all history every round, so it gets pricier |
| **goldfish memory** | window full squeezes the opening, new session resets — memory is a product feature, not a model ability |
| **lost in the middle** | key info buried in the middle is most easily missed, put key points at both ends |
| **quadratic bill** | window ×10, compute ×100 — a big window is pricey and slow |

### The finishing move: puncture "the AI remembers me" in one sentence

From now on, whoever says "the AI understands me more the longer we chat, remembers everything," rebut with "the window":

> 　🗣️ **"The model is zero-memory — all it can see is the tokens packaged into the window this one time. Every 'remembered' is the product's cheat sheet of storing key points outside the window and stuffing them back in when needed."**
> - Verify: turn off the memory feature and open a new session, it instantly meets you for the first time.
> - Long-document answer wrong? Not that it's dumb — key info buried in the window's middle got missed (lost in the middle).
> - Pricier the longer you chat? The quadratic handshake bill — every round resends all history.

One sentence to close the chapter, and the one most worth taking away: **putting the right info in the window beats stuffing all info in.**

### Squeeze the whole chapter into one sentence and stuff it in your head

> **A large model has no memory store, only a fixed-size "window"; continuous chatting is an illusion held up by the client resending all history every round.**
> Window full squeezes the opening (goldfish memory), new session resets, key info buried in the middle is easily missed (lost in the middle); window ×10, compute ×100 (quadratic bill), so pricey and slow.
> The fix isn't an infinitely big window, but "put the right info in the window" — rolling summary and RAG do exactly this.

---

Mia digested it and followed up: "'Put the right info in the window'... but my company has hundreds of documents, thousands of pages of manuals — **which passages are the 'right info'?** Do I have to manually flip and copy-paste into the window for every question? That's way too tiring!"

Leo's eyes lit up: "You've hit next chapter's core! Of course you don't flip manually — we hire the model a **'secretary who's very good at flipping through books.'** Put plainly, it turns a closed-book exam into an **open-book exam:** hand it an 'external manual' right in the exam hall and teach it to '**flip the book first, then copy from it.**' Come on, next chapter I'll demonstrate how RAG chunks and indexes (★ω★)"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** a large model has no memory store, only a fixed-size **context window;** "continuous chatting" is an illusion held up by the client **resending all history every round** (so it gets pricier). Window full squeezes the opening (goldfish memory), key info buried in the middle is easily missed (lost in the middle), window ×10 compute ×100 (quadratic bill).
> **🎯 Trigger · pull this out whenever:** whoever says "the AI understands me more, remembers what we chatted," rebut — **the model is zero-memory, every "remembered" is the product's cheat sheet of storing key points outside the window and stuffing them back in;** a long-document wrong answer isn't dumbness, it's key info buried in the middle being missed — **put key points at both ends.**
>
> **✍️ Self-test with the book closed:**
> 1. A new session "forgets everything" — is that a bug? Give two countermeasures.
> 2. A 200-page contract clearly fits, yet it gets the Page-87 clause wrong — why?
> 3. The same question, asked at the end of a 100-round long conversation vs. in a new session — which is pricier?

> 🪜 **Next chapter preview:** Chapter 18 · Retrieval-Augmented Generation (RAG) — the large model's ultimate open-book exam guide.


---

[← Previous](../stage_4/chapter_16.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_4/chapter_18.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
