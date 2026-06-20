# Chapter 16 · Prompt Engineering: Don't Misread It — This Is a Psychology-Shaping Ritual

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** The internet is full of "ChatGPT god-tier spell collections." To write a prompt, do you really have to memorize incantations and say "thank you" and "please" to the AI?
> **🤔 Your turn:** The same question, add "you are a senior lawyer," and the answer instantly turns professional — did the AI "get into character"?
> **🧱 The naive move hits a wall:** Treat the prompt as "a command issued to the AI," relying on reading its "mood" — and you'll only **slide toward collecting spells and worshipping phrasings,** which break the moment you switch models.
> The truth: a prompt isn't a command, it's a **continuation condition** for "word chaining." Read on for how to "draw a circle" in high-dimensional space. 👇


Through Stage 3, Leo took Mia all the way through how an LLM is forged from nothing. Stage 4, Chapter 1 — **the mythic weapon's first move,** writing prompts.

Mia opened with a real confusion: "Those 'ChatGPT god-tier spell collections' online... do I have to memorize incantations? Do I really have to say 'thank you' and 'please' to it?"

Leo *tossed* a printed stack of "spell collections" into the trash: "**First break this superstition!** Writing prompts isn't praying to gods or reading the AI's mood — it's a **'psychology-shaping ritual':** **draw a circle** in high-dimensional space, **put a personality mask** on the model, and forcibly drag it into the knowledge cluster you want. Today I'll explain it with Chapter 8's starry sky (￣▽￣)ノ"

---

## Section 1 · Every Word You Type Marks AI's "Continuation Zone"

Leo showed his cards: "Chapter 12 already told you the only thing a large model does is **chain on from the preceding text** — before generating each token, it computes probabilities over the tens of thousands of vocabulary candidates (Chapter 14). So what is a prompt?"

> **Intuitive impression:** the prompt is a command issued to the AI → word it "pleasingly" enough and it'll do a good job.
> **Real mechanism:** the prompt is a **condition** for continuation → every word **pushes the following distribution toward some region of the training data.**

"By the first understanding," Leo said, "prompt engineering becomes the superstition of reading the AI's 'mood' and collecting spells. But the truth is — **prompt engineering = 'fencing off land' for the model with words:** narrowing its continuation range from 'the whole-web average' to the text region you want."

He hauled out Chapter 8's **word-vector starry sky** for an analogy:

> 🌌 **Draw a circle in high-dimensional space + put on a personality mask**
> When you write "pretend you are a **senior contract lawyer,**" it's not the magic of the model "getting into character." In the training data, around the words "senior lawyer" **cluster mountains of legal text:** contract clauses, legal opinions, risk warnings. The moment this sentence appears, the following tokens' probability distribution **aligns to that region** — wording turns rigorous, it proactively scans for risks clause by clause, cites article numbers.
>
> **A role word is a "personality mask" you put on the model;** put on the lawyer mask and it's dragged into the "law" cluster in the starry sky. Role-play works, and the principle is just this humble.

Leo demoed a "land-fencing machine" for Mia. The same question "explain quantum computing," adding one ingredient at a time, watching the continuation range narrow:

> 🎬 **Add nothing:** continuation range "whole-web average," most likely written **encyclopedia-entry style** — watery and generic. (Not that the model's bad, the range is too wide, it can only take the average.)
> 🎬 **+ "you're a science columnist":** drag the distribution from the whole-web average toward the "science columnist" region, encyclopedia-tone and paper-tone drop accordingly.
> 🎬 **+ "explain to a high-schooler who's never studied physics":** the more-metaphor, less-jargon text region is raised, academic reviews nearly out.
> 🎬 **+ "no formulas, under 300 words":** hard anchors, sealing off the paper region's road and trimming the long-encyclopedia tail.
> 🎬 **All four ingredients:** the continuation range narrows to a **slit,** output basically "hits where you point."

> Mia, getting it: "So every word in the prompt is 'voting' on the continuation?"
> Leo: "Sharp! **Word gone, vote gone** — remove an ingredient and the distribution snaps back. This gives you a **universal test ruler:** from now on, see any 'prompt trick' and first ask — **does it push the distribution toward the region I want?** If it makes sense, worth keeping; if it doesn't, it's mostly a hit-or-miss folk remedy."

---

## Section 2 · The Five Techniques: each makes sense, so each is stable

"These five cover 90% of everyday scenarios," Leo said. "Every good example here has **no fancy wording at all, only plain information gains** — who you are, who it's for, what shape, what's forbidden. **Change the information, not the tone — that's exactly the dividing line between engineering and mysticism.**"

| Technique | Bad → Good example | Why it works (distribution language) |
|---|---|---|
| **① Set role & audience** | "explain quantum computing" → "you're a science writer, explain to a high-schooler, more metaphors, no formulas" | role sets "who's writing," audience sets "who it's for," dragging the distribution away from the widest, blandest "encyclopedia average" |
| **② Give examples (few-shot)** | pile on ten adjectives → give two satisfying samples "in this style" | examples have no ambiguity, **pattern-continuation is the model's strongest skill;** one of the GPT-3 paper's most important findings |
| **③ Give steps (chain of thought)** | "just give the answer" → "think step by step: list the givens, then the process, then the answer" | multi-step reasoning is like walking a tightrope (Chapter 15); having it write the intermediate steps = **turn mental math into paper math** |
| **④ Fix the format** | "organize into structured data" → give a JSON template "output strictly like this" | "structured" has ten thousand layouts, **a template is the strongest anchor in the context;** a must when handing off to a program |
| **⑤ Draw the boundary** | "what's our Q3 return rate" (it'll fabricate) → "if the material doesn't say, answer 'not provided,' don't estimate" | "admitting it doesn't know midway" rarely follows in training data, **giving 'I don't know' a way out genuinely lowers the hallucination rate** |

> Leo highlighted techniques ③ and ⑤: "**'Think step by step' noticeably raises math accuracy** — that's chain of thought (CoT), and Chapter 23's reasoning models simply trained 'draft first' into a default action. And technique ⑤ is the key to lowering hallucination: if you don't crack the door, the path 'say I don't know' barely exists in the distribution, and the model can only hard-fabricate."

---

## Section 3 · system vs. user: the character bio the director hands the actor

Mia: "I noticed some settings stay effective after writing once, and some get forgotten after a few rounds..."

"Good observation! This brings up the prompt's two roles." Leo gave a theatrical analogy. "Messages sent to the model come in three kinds: system, user, assistant. It's a play—"

> 🎬 **system = the character bio** (the director hands it to the actor before filming)
> Holds **global behavior constraints:** identity and tone, conduct rules, safety red lines, fixed output format. Pre-set, **pressing on every continuation,** usually invisible to the ordinary user.

> 🎬 **user = the lines** (sentences the audience hands up after filming starts)
> Holds **the current single matter:** the specific question, material, temporary request. Changes every round, rolling forward with the conversation.

"Why is a global constraint in system more 'effective' than stuffed into user?" Leo revealed the principle. "Two reasons — one, **position:** system always sits at the front of the conversation, unmoving the whole time; two, more crucial, **training:** in Chapter 13's alignment stage, the model was specifically tuned so '**system requirements outrank user requirements**' (vendors call this the instruction hierarchy)."

He demoed a controlled experiment, same constraint "no more than 3 sentences per answer":

> 🎬 **Written as user's first line:** Round 1 obediently 2 sentences; by Round 3, hit by a strong "introduce 5 books one by one" request, it's **flooded outright: 9 sentences;** Round 4 it's completely forgotten — the constraint sinks further back each round, its influence diluted by new content round after round.
> 🎬 **Written into system:** Rounds 1, 2, 3, 4 **never break form** — fixed position + instruction-hierarchy training, double insurance.

> Leo summarized: "ChatGPT's 'custom instructions,' Claude's project settings — what you fill in is this system field. The various 'personas' you've seen — customer service, sharp-tongued film critic, Socratic tutor — are all, in essence, a system prompt you can't see."

---

## Section 4 · Structure a Long Prompt: write it as a document, don't send it as chat

"Once a task gets complex, the prompt grows to hundreds or thousands of words," Leo said. "The most common failure here isn't 'the model can't,' but '**the model didn't notice**' — you clearly wrote 'no more than 100 words' and it acts blind. Two structural principles cure this:"

> 📐 **Principle 1 · Partition the information:** background / task / constraints / examples each get their own section, separated by subheadings or dividers. Clearly structured preceding text corresponds to the high-quality "manual, requirements doc" region of the training data.

> 📐 **Principle 2 · Put key points at both ends:** for very long context, the model uses the **middle** worst — researchers call it **lost in the middle** (Chapter 17 dissects the mechanism). So key constraints **set the tone at the start, restate at the end,** don't bury them in the middle of the third paragraph waiting to be missed.

The two principles combined, a decent long prompt looks like this:

> **【Background】** We're an online-education company for programmers, launching *Rust for Beginners* next week.
> **【Task】** Write 3 course-teaser blurbs to post in tech communities.
> **【Constraints】** Each ≤ 60 characters; professional and restrained; no "hey folks"; no promise of "easy overnight mastery."
> **【Example】** The best-performing one last time: "Pointers gave you a decade of headaches? Rust trades ownership for a night of peace."
> **【Restated】** Output only the 3 blurbs, no explanation. (Put what you care about most as a restatement at the end.)

> Leo's finishing touch: "Look at the last line — that's 'key points at both ends.' This template look familiar? It packs **the five techniques into one orderly container.**"

---

## Section 5 · Traps You'll Probably Fall Into Too

**Trap 1: "Spells like 'I'll tip you $100' and 'take a deep breath before answering' are the essence; copy them and they work"**

> ❌ Mistaking an isolated case for a rule.
> ✅ The truth is — **only techniques that make sense from the distribution angle are stable;** folk remedies that don't make sense are hit-or-miss and break on a different model or version.

Root cause: these remedies mostly come from someone's one test screenshot, with weak effects and polar-opposite performance across models. **The model has no account, only a distribution** — "tipping" adds no information about the task. The five techniques are stable across models precisely because they change **the information itself** (role, examples, steps, format, boundary). Pull out the test ruler: **if it doesn't make sense distribution-wise, don't keep it.**

**Trap 2: "As long as the prompt is good enough, the model can answer any question right"**

> ❌ Conflating a "wording problem" with a "knowledge problem."
> ✅ The truth is — **a prompt can only guide the distribution, not inject knowledge the model doesn't have;** the knowledge boundary needs RAG (Chapter 18) and tool calls (Chapter 19) to fill.

Root cause: the model doesn't know your company's meeting conclusion last week, today's stock price, your internal price list — however exquisite the prompt, it can only fabricate. A practical rule: **what's still wrong or fabricated across ten phrasings isn't the prompt's fault** — time to look in Chapters 18 and 19.

---

## Section 6 · The Finishing Move: one test ruler, engineering or mysticism

Same ritual: a manual + a kill shot.

### The prompt-engineering core, one table to mop it all up

| Concept | In a sentence |
|---|---|
| **prompt's essence** | set a continuation condition for word chaining, draw a circle in high-dim space, put on a personality mask |
| **five techniques** | set role & audience / give examples / give steps / fix format / draw boundary |
| **system vs. user** | character bio (global, presses every round) vs. lines (current matter) |
| **long prompt** | partition info + key points at both ends (guard against lost in the middle) |

### The finishing move: one "distribution test ruler," tell engineering from mysticism in a second

From now on, see any "prompt magic" and don't rush to copy — first measure with the ruler:

> 　🗣️ **"Does it push the distribution toward the region I want? Does it add real information?"**
> - Makes sense (role / examples / steps / format / boundary) → **engineering, stable, worth keeping.**
> - Doesn't (tipping / emotional pressure / deep breath) → **mysticism, folk remedy, hit-or-miss.**
>
> Plus a knowledge-boundary mantra: **what ten phrasings can't fix is a knowledge problem, not a wording problem** — time for RAG or tools.

### Squeeze the whole chapter into one sentence and stuff it in your head

> **Writing a prompt isn't chanting spells; it's drawing a circle in the model's high-dimensional semantic space, putting on a personality mask, dragging the continuation range from "whole-web average" to the knowledge cluster you want.**
> Every one of the five techniques changes "information," not "tone"; a global constraint in system (character bio) is more effective than in user (lines).
> But a prompt can only guide the distribution, not inject new knowledge — which is exactly why the later RAG and tools are needed.

---

Mia put the test ruler away, then a question surfaced: "You just said 'the whole conversation history is a continuation condition'... so the longer I chat with it, the more it has to read, right? Will it eventually **forget the opening** and **get pricier the longer we chat**?"

Leo slapped his thigh: "You've hit next chapter's vital spot! Your instinct is spot-on — this means recomputing Chapter 9's **'quadratic handshake bill.'** Come on, next chapter I'll make a **long-distance call** with you to round 200, and you'll get what 'goldfish memory' and 'a physical hard ceiling' mean (￣▽￣)"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** writing a prompt = **draw a circle, put on a personality mask** in the model's semantic space, dragging the continuation range from "whole-web average" to the cluster you want; the five techniques (set role & audience / give examples / give steps / fix format / draw boundary) **each change "information," not "tone";** a global constraint in system is more effective than in user.
> **🎯 Trigger · pull this out whenever:** see any "prompt magic," measure with the ruler — **"Does it push the distribution toward the region I want? Does it add real information?"** Makes sense = engineering, doesn't (tipping / deep breath) = mysticism; and **what ten phrasings still get wrong is a knowledge problem, not a wording problem** (time for RAG/tools).
>
> **✍️ Self-test with the book closed:**
> 1. Why does "you are a pediatrician" make the answer more professional? (Explain with "distribution.")
> 2. Why does "think step by step" raise math accuracy?
> 3. The model keeps getting your company's internal price list wrong — should you keep polishing the prompt?

> 🪜 **Next chapter preview:** Chapter 17 · The Context Window — the tug-of-war between goldfish memory and the quadratic bill.


---

[← Previous](../stage_3/chapter_15.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_4/chapter_17.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
