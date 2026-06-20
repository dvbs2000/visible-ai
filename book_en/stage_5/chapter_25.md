# Chapter 25 · Open vs. Closed Source: The 2026 Panorama of Warring Immortals

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** GPT, Claude, Gemini, Llama, DeepSeek... so many models — to actually pick for a project, which do you **choose**? Is it pricier-is-better, bigger-is-stronger?
> **🤔 Your turn:** A hospital doing internal medical-record Q&A, vs. you installing yourself a writing assistant — would they pick the same model?
> **🧱 The naive move hits a wall:** Stare only at the leaderboard and "pick the strongest" — but "strongest" may not be compliant (data can't leave the network), may not be cost-effective (at low volume the API is actually cheaper); **a written-exam valedictorian may not be able to do your particular surgery.**
> What model selection should ask isn't "which is strongest," but "which is most suitable." Read on for that grid of warring immortals. 👇


Leo spread out a **world map** covered in colorful tiles: "You've hit the book's general-knowledge **perfect finale!** This is a **2026 'grid of warring immortals'** — the open-source camp and the closed-source camp are in a celestial brawl over cost, privacy, and business. Today I'll teach you how to **pick the right weapon for yourself** in this martial world (★ω★)"

---

## Section 1 · Setting the Name Straight: the "open-source model" you hear is mostly "open weights"

"Before getting hands-on, set the name straight," Leo said. "Nine in ten people get this wrong. **Open-source software** (Linux, Python) 'open source' means **the source code is fully public** — you get the recipe, every line inspectable, theoretically reproducible from scratch. But the '**open-source large model**' in the news, nine times out of ten, gives you only **the weights file** — the final readings of those billions of 'knobs' (Chapter 3's weights) after training."

> 🍽️ **Open weights = giving you the cooked dish, not the recipe**
> You can **download, deploy, fine-tune** (reheat, re-cut), but it's a cooked dish: **the training data (ingredients), training code and recipe (the cooking) are all undisclosed.** The accurate term is **open weights.**

"Why does everyone hide the recipe?" Leo said. "Training data is both a commercial company's **most valuable trump card** and a **copyright minefield** — 'open weights' is exactly the compromise between commercial interest and the open spirit." He laid out an "openness three-tier ladder":

| Tier | What you get | Metaphor | Representatives |
|---|---|---|---|
| **Closed API** | rent the ability only, the model lives on the vendor's servers | takeout: the food's good, no entry to the kitchen, can't take the dish home | GPT, Claude, Gemini |
| **Open weights** | the model file itself, locally deployable, fine-tunable | a bought cooked dish: reheat and re-cut, but no recipe | Llama, Qwen, DeepSeek, Mistral |
| **Fully open source** | weights + training code + training data all public | even the recipe and ingredients, reproducible from zero | OLMo (Allen AI) — a minority, research-driven |

> Leo stressed: "From here this chapter unifies the terms: **open weights vs. closed API.** The industry's 'open-source model' is, nine in ten, really the middle tier."

---

## Section 2 · Two Routes, Five Dimensions

"**Closed API is like takeout:** order and eat on demand, pay by volume, but the kitchen's at someone else's place; **open weights is like cooking your own:** the dish (model) is free, but the pots and pans (GPU), water and power (ops) you provision yourself," Leo laid out a five-dimension table:

| Dimension | Closed API | Open weights |
|---|---|---|
| **Access** | register an account, send requests per the docs, running in minutes (hands-on Ch.26) | download weights, deploy to your own GPU/server (hands-on Ch.27) |
| **Data privacy** | data must **go to the vendor's servers** (leaves the network) | can **run fully locally, data never leaves the door** |
| **Ability ceiling** | frontier abilities usually appear **first** in closed flagships | following closely, and the gap **shrinks fast** (the R1 moment below) |
| **Cost structure** | billed by token, pay for what you use (**OPEX**) | weights free, but GPU, electricity, ops are real money (**CAPEX**) |
| **Customizability** | limited: prompts + the vendor's limited fine-tune interface | free: fine-tune, prune, "mod" into any shape |

---

## Section 3 · The Grid of Warring Immortals: who's on which line (as of 2026)

"This is a snapshot of the landscape as of 2026," Leo pointed at the world map. "**This field updates extremely fast; for the roster and rankings, go by each company's latest release** — memorize the positioning in one line, don't memorize benchmark scores. First the **closed Big Three:** the model doesn't leave home, the ability is sold as a service."

> 🇺🇸 **OpenAI · GPT:** ChatGPT's creator, who brought large models into public view; the most complete product line in chat, multimodal, and reasoning, still the industry's weathervane.
> 🇺🇸 **Anthropic · Claude:** built on AI safety research, with long-text, code, and Agent ability as its reputation calling card, extremely sticky among engineers.
> 🇺🇸 **Google · Gemini:** the native-multimodal route, backed by Search, Android, and the office suite — unmatched on distribution channels.

"Now the **open-weights camp:** the model file is yours, deploy as you like — **Chinese strength is especially dense on this side.**"

> 🇺🇸 **Meta · Llama:** the leader who made "open weights" an industry convention, with the largest derivative ecosystem (note its license has commercial terms, not a traditional open-source license).
> 🇨🇳 **Alibaba · Qwen:** one of the most complete size lineups and most diligently iterated open-weights series, top of the global developer community's adoption.
> 🇨🇳 **DeepSeek:** famed for extreme engineering efficiency, the "value king," whose R1 rewrote overnight the narrative that "open weights are always half a beat behind" (detailed next section).
> 🇪🇺 **Mistral:** Europe's lone-flag-level representative, starting from "small and efficient," walking on both open weights and commercial API.
> 🇨🇳 **Moonshot · Kimi / Zhipu · GLM:** the former breaking out on ultra-long context, the latter from the Tsinghua lineage, among the earliest to do Chinese-English bilingual large models — both iterating openly continuously.

> Leo made one **important-trend** note: "The camp boundaries are **blurring!** The closed Big Three each have open-weights side branches too: Google has Gemma, OpenAI released gpt-oss. **The two routes are a spectrum, not two fortresses** — the same company often bets both ways."

---

## Section 4 · The DeepSeek-R1 Moment: the gap can be sharply compressed

"Chapter 23 covered reasoning models — let the model draft first then answer," Leo said. "**In January 2025, this storyline exploded in the open-weights camp** — DeepSeek-R1 released with **open weights + a permissive license,** with reasoning ability **rivaling the strongest closed reasoning models of the time,** while the training investment was far below the outside world's imagination of a 'frontier model' (the exact figure is disputed, 'much cheaper' is the consensus)."

"Within a week it topped app-store charts in multiple countries, even triggering **a shake in US tech stocks,**" Leo said. "The reason for the shock wasn't that it was 'strongest,' but that it proved one thing: **the moat of closed-source leadership may be far shallower than everyone thought.**"

> **The default assumption before R1:** open weights always lag closed by **one to two years.** So the selection logic was crude: want the strongest, pay money and hand over data.
> **The new consensus after R1:** the gap can be **sharply compressed.** The distance between the two routes is no longer a "generation gap" but a "body-length" that can be closed at any time.

> Leo summarized: "The selection question shifted from '**which is strongest**' to '**which is most suitable**' — the next section gives you four questions."

---

## Section 5 · The Selection Compass: flick through four questions

"Real projects **don't need leaderboard memorization,** just answer four questions," Leo set out a "compass." "Tap an option, see which way the needle swings — **note the first question is a veto:**"

> 🧭 **① Can the data leave the network?**
> 　Yes → lean closed API; **no (too sensitive) → veto, must be locally-deployable open weights!**
> 🧭 **② Which budget do you prefer?**
> 　Pay-as-you-go (OPEX) → lean closed; one-time investment (CAPEX) → lean open weights.
> 🧭 **③ Need deep customization?**
> 　Prompts suffice → lean closed; need fine-tune/modding → lean open weights.
> 🧭 **④ Does the team have ops capability?**
> 　No one to manage GPUs → lean closed; have a GPU team → lean open weights.

Leo had Mia practice a few scenarios:

> 🎬 **Hospital internal medical-record Q&A** → **open weights:** records are most sensitive, "can't leave the network" vetoes, local deployment the only direction.
> 🎬 **Personal writing assistant** → **closed API:** no hard privacy constraint, low volume, pay-by-token nearly zero barrier; buying a GPU to write essays isn't even worth the electricity.
> 🎬 **A startup building an MVP in two weeks** → **closed API:** the first priority is validating demand, OPEX starts fastest with zero ops; once it runs, then evaluate migration.
> 🎬 **Offline industrial inspection on a factory floor** → **open weights:** the line is often physically off-network, the API can't even be called, so the model must move into local devices.

> Leo's finishing touch: "See it? **The first question 'does data leave the network' is a veto** — once triggered, the other three only decide 'how to land it,' no longer 'which side to pick.' Otherwise, lean wherever most signals point; if tied, the engineering world's common move is **use the closed API to run fastest first, bind few vendor-private features, leave migration room,** and migrate later as volume grows."

---

## Section 6 · Traps You'll Probably Fall Into Too

**Trap 1: "Open source (open weights) = free"**

> ❌ Taking "free to download" as "free to use" (the inertia of the smartphone-app era).
> ✅ The truth is — **the weights cost nothing, but the GPU, electricity, and ops labor to run the model all cost money** — only worth it at high volume.

Root cause: a large model is a **power-guzzling heavy asset.** At low volume, the pay-by-token closed API is often **cheaper instead;** what open weights saves is pay-as-you-go, traded for fixed investment — **the tipping point is "volume."**

**Trap 2: "Closed is definitely stronger, open weights is 'making do'"**

> ❌ Staring only at the leaderboard top.
> ✅ The truth is — **it depends on the scenario:** frontier reasoning is often led by closed flagships, but **a vast number of everyday tasks open weights already handles more than amply.**

Root cause: translation, summary, customer service, internal Q&A don't need "strongest" at all — **good enough, compliant, cheap is the winner** — and the R1 moment further proves the top gap can be compressed at any time. **Selection looks at "is the task good enough + are the constraints met," not the leaderboard's number one.**

---

## Section 7 · The Finishing Move: from "which is strongest" to "which is most suitable"

Same ritual: a manual + a kill shot.

### The open/closed landscape, one table to mop it all up

| Concept | In a sentence |
|---|---|
| **Name straightening** | "open-source model" is nine in ten "open weights" (gives the cooked dish, not the recipe) |
| **Two routes** | closed API (takeout, OPEX, zero ops) vs. open weights (cook your own, CAPEX, moddable) |
| **The R1 moment** | open weights rivals closed, the gap can be sharply compressed |
| **Four selection questions** | data leaves network? budget OPEX/CAPEX? need fine-tune? have ops? (first question is a veto) |

### The finishing move: pick a model — don't ask "which is strongest," ask "which is most suitable"

From now on, selecting a model for any project, don't be led by leaderboards, run the **four questions:**

> 　🗣️ **"① Can the data leave the network? (no → veto, lock open-weights local deployment) ② Pay-as-you-go or one-time investment? ③ Need deep fine-tuning? ④ Does the team have GPU ops?"**
> - "DeepSeek's weights are a free download = not a cent spent"? → wrong, **GPU, electricity, ops all cost money,** at low volume the API is often cheaper.
> - "Closed is definitely stronger, open weights is making do"? → wrong, **everyday tasks open weights already handles,** good-enough-compliant-cheap is the winner.
> - Signals tied? → use the closed API to run fastest first, leave migration room.

### Squeeze the whole chapter into one sentence and stuff it in your head

> **Two routes in the AI martial world: closed API (takeout — pay-as-you-go, zero ops, data leaves the network, frontier-leading) vs. open weights (cook your own — weights free but GPU/ops on you, data never leaves, deeply moddable); the industry's "open source" is nine in ten "open weights," gives the dish not the recipe.**
> The DeepSeek-R1 moment proves: open weights can rival closed, the gap can be sharply compressed — selection shifts from "which is strongest" to "which is most suitable."
> Selection needs just four questions (data leaves network? budget? customization? ops?); the first, "can data leave the network," is a veto — don't memorize leaderboards, just answer the constraints.

---

## 🎓 Stage 5 · Clearing-the-Level Recap

Mia folded the world map and exhaled: "These five frontier chapters... I feel like I can finally understand **every hot term** in today's AI news!"

Leo smiled and strung the five chapters into a "frontier panorama":

> 2️⃣1️⃣ **Diffusion models** — text-to-image isn't painting, it's "wiping" a picture out of noise.
> 2️⃣2️⃣ **Multimodal** — cut image/audio into tokens "welded" into the sequence, AI reads the picture as a foreign language.
> 2️⃣3️⃣ **Reasoning models** — 2026's star: from "memorizing" to "frantically self-correcting on scratch paper at answer time."
> 2️⃣4️⃣ **MCP** — fit all the world's tools with a unified USB plug, multiplication to addition.
> 2️⃣5️⃣ **Open vs. closed** — the grid of warring immortals, four selection questions from "strongest" to "most suitable."

"You notice," Leo said meaningfully, "these five chapters swept **today's AI cutting edge** in full — can paint, see, think, connect everything, and know the lay of the land. **From the 'three nesting dolls' newbie in Chapter 1 to now calling the shot on a project's model — you've transformed!**"

Mia's eyes lit up: "But... I haven't **built** anything by hand yet! I get all the principles, can I actually **write code and build one**?"

"Exactly what the final stage does!" Leo slapped the table. "Stage 6 — **Hands-On · Building AI Apps Yourself!** Call your first API, run a large model locally, build a RAG knowledge base by hand, and the evaluation and safety you must understand before launch. **You've learned 25 chapters of concepts; now they finally become a running application — this is the last leap from learner to builder (★ω★)**"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** two routes — **closed API** (takeout: pay-as-you-go, zero ops, data leaves the network, frontier-leading) vs. **open weights** (cook your own: weights free but GPU/ops on you, data never leaves, deeply moddable); the industry's "open source" is nine in ten "open weights" (gives the dish, not the recipe). The **DeepSeek-R1 moment** proves: the gap can be sharply compressed, selection shifts from "which is strongest" to "which is most suitable."
> **🎯 Trigger · pull this out whenever:** selecting a model for any project, run the **four questions** — ① can data leave the network (**no = veto, lock open-weights local deployment**) ② budget OPEX or CAPEX ③ need deep fine-tuning ④ have GPU ops; don't memorize leaderboards, just answer the constraints.
>
> **✍️ Self-test with the book closed:**
> 1. "DeepSeek's weights are a free download, so switching to it costs nothing" — what's right and wrong?
> 2. A law firm doing sensitive contract Q&A, team with zero ops experience — run the four questions for direction.
> 3. Frontier abilities usually appear first in ____; a vast number of everyday tasks ____ already handles; so the selection question became ____.

> 🪜 **Next stage preview:** Stage 6 · Hands-On — building AI apps yourself (Chapters 26–30).


---

[← Previous](../stage_5/chapter_24.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_6/chapter_26.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
