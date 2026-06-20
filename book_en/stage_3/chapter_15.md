# Chapter 15 · Scaling Laws: The Ultimate Troop-Building Formula of "Brute Force Makes Miracles"

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** A company announces it'll burn a billion dollars, train for half a year, no do-overs midway, to train its next-gen model — and before the money's even spent, the CEO dares **promise investors how much stronger the new model will be.** What gives them the nerve to open this "bet"?
> **🤔 Your turn:** If one training run is too expensive to trial-and-error, how would you "know in advance" how strong the trained large model will be?
> **🧱 The naive move hits a wall:** You think "training a large model is like alchemy, no one knows the result before the furnace opens" — but if that were true, **no one would dare promise the board a performance before training,** yet vendors dare it, generation after generation.
> They rely on a straight line that can "preview the future." Read on. 👇


Leo *snapped* up a whiteboard, grabbed a marker, eyes blazing: "There's totally a rhyme to it! This is Stage 3's grand finale — a **'brute force makes miracles' troop-building curve.** Today I'll draw you the whiteboard and explain why piling on parameters **really works,** and how much stronger **can be computed in advance** (★ω★)"

---

## Section 1 · Before the Furnace Opens, the Result Is Already Written on a Line

Leo first tossed out a news phenomenon that baffles outsiders: "A company announces it's raising several billion dollars to build a '100,000-GPU cluster' to train its next-gen model — and before the money's spent, the CEO dares **promise investors how much stronger the new model will be.** One flagship run burns hundreds of millions, runs for months, no do-overs midway — how do they dare open this 'bet'?"

"The answer is," Leo said word by word, "**this isn't a bet at all.**"

He drew a coordinate system on the whiteboard: "In 2020, OpenAI's researchers (Kaplan et al.) did a piece of grunt work — **train a big batch of models from small to large, record each one's loss** — and then discovered this chapter's star."

> **Intuitive impression:** training a large model is like alchemy → no one knows the result before the furnace opens.
> **Real mechanism:** loss descends smoothly along a **power law;** small models draw the line, large models **extrapolate.**

"First review one word," Leo said. "**Loss** is the average error on that 'guess the next word' exam (Chapter 12); the lower, the deeper the language skill. Kaplan et al. found: scale the model up and the loss drop is **astonishingly regular** — **every 10× increase in parameters drops the loss by a fixed proportion again; data volume and compute each do the same.**"

"This 'every 10×, improve by a fixed proportion' pattern is called, mathematically, a **power law,**" Leo said as he drew. "You don't need to memorize the definition, just its most valuable property — **swap the graph paper for a log scale (each square = ×10), and the power-law curve becomes a perfectly straight line. And a straight line can be extended to the right with a ruler!**"

So the whole process becomes a four-step troop-building method:

> 🎬 **Step 1 · Cheap:** train a string of small models from small to large, each cheap, results in days — cost just a thousandth or ten-thousandth of the target large model.
> 🎬 **Step 2 · Plot points:** on log paper, x-axis scale, y-axis loss, plot each small model's score.
> 🎬 **Step 3 · Connect the line:** the power law guarantees these points **line up nearly perfectly straight.**
> 🎬 **Step 4 · Extrapolate:** extend the line 100×, 10,000×, **read off what "score" the untrained large model will get,** then decide whether to spend the money.

"This isn't paper theory!" Leo rapped the whiteboard. "The most famous validation comes from **GPT-4:** the technical report says experiments with small models using **less than a ten-thousandth of its compute accurately predicted GPT-4's final loss after training.** Before the hundred-million-dollar run even began, the result was 'computed' out first."

> Mia, getting it: "No wonder they dare spend! '10,000-GPU cluster,' 'ten-billion funding' sound like a crazy gamble, but actually..."
> Leo: "**It's an engineering budget, not a gamble!** They dare spend because **the return can be previewed.** This is the underlying logic of the whole large-model arms race. The 7B, 70B, 405B in model names — that B (Billion) is the parameter count, one of the power law's three axes, the most intuitive yardstick of scale."

---

## Section 2 · Chinchilla's Revenge: piling on parameters alone isn't enough, you must balance

"But," Leo turned, "the proportion in which the three axes 'parameters, data, compute' should grow — the 2020 paper gave an answer that **misled the industry for two years.**"

"The compute one run burns is roughly decided by two things multiplied: **how big the model (parameter count) × how much data fed (token count),**" he explained. "With a fixed budget, this is an allocation problem — want a bigger head, or let the head read more? The 2020 answer leaned toward '**head first,**' so the industry launched a pure-parameter race: GPT-3 175 billion, Gopher 280 billion, Megatron-Turing 530 billion... **parameters multiplied, but the data fed nearly stalled** (a few hundred billion tokens)."

"In 2022, DeepMind decided to redo this allocation problem," Leo got to the key. "They trained **400-plus** models of different 'size × appetite' combos, comparing who's strongest under the same budget. The conclusion shook the industry — **the optimal ratio is roughly: 20 tokens per 1 parameter!**"

"Measured by this yardstick, all the giants of the time were collectively exposed:"

> 📊 **Gopher:** 280 billion parameters, by the ratio should be fed about 5.6 trillion tokens — **actually fed only about 300 billion, less than a twentieth of what's needed!**

"A giant's skeleton that never ate a full meal," Leo said. "This is '**malnutrition**' — money spent lavishly on enlarging brain capacity, but not enough world packed into the brain. **Parameters are potential, data is what fills the potential up.**"

DeepMind delivered a "public execution," training a model called **Chinchilla** to slap faces:

| | Gopher (2021) | Chinchilla (2022) |
|---|---|---|
| Parameters | 280 billion | **70 billion (only 1/4)** |
| Training data | about 300 billion tokens | **about 1.4 trillion tokens (4×+)** |
| Training compute | same | same |
| Score | a malnourished giant | **a well-fed shorty, wins across the board!** |

"Same money, different split, a free chunk of performance!" Leo marveled. "After this, every frontier lab's training recipe was rewritten. The lesson in one line: **Scaling isn't mindlessly piling one axis; the three axes must scale up in balance.**"

> Leo added a twist: "But 20:1 is the optimum under a '**fixed training budget.**' Later everyone realized the real account to compute is another one — a trained model gets **called a hundred million times,** and a small model is much cheaper per call. So vendors started deliberately '**overfeeding**': feeding a few-billion-parameter small model over ten trillion tokens (over a thousand per parameter, far beyond 20:1); the extra training cost is earned back by a hundred million later cheap calls. The small model that runs offline on your phone is mostly fed this way — **the law didn't change, the ledger did.**"

---

## Section 3 · Emergence: average score climbs smoothly, certain problems suddenly become doable

"Anyone who's used different model generations has a feel for it," Leo said. "Give the last generation a multi-step word problem and it confidently spouts nonsense; switch to the next generation and **it suddenly computes step by step.** The ability seems not to 'gradually improve' but to 'suddenly click on some day.' Does this contradict the power law's 'smoothness'?"

He gave a live example:

> 🧮 **Problem:** A basket has 23 apples; take away 7, then split the rest evenly among 4 people. How many each?
> - **Under-scale model:** "5 each." — blurts it out, confident and wrong.
> - **Past-the-threshold model:** "First the remainder: 23 − 7 = 16; then split: 16 ÷ 4 = 4. Four each." — not only right, but spontaneously lists steps.

"This kind of task's score curve **clings to 0% for a long time, then shoots up steeply past some scale,**" Leo said. "Researchers call this **emergence.**"

"Why does the average score climb smoothly but a single item jump?" He revealed the principle. "The key is — **loss is the average score over hundreds of thousands of word-guessing problems; the average climbing steadily doesn't prevent 'one category of problem' from treading water a long time then surging.** Picture a multi-step problem as **walking a tightrope** and it clicks—"

> 🎪 **The tightrope effect:** four-step reasoning, one misstep and it all collapses, and evaluation usually only counts "all correct."
> Let the per-step confidence climb smoothly from 50% → 80% → 95%:
> - Each step 50% → chance of four in a row **under 10%**
> - Each step 80% → only **about 40%**
> - Each step 95% → four in a row jumps to **over 80%!**
>
> **Per-step ability climbs smoothly, but the "all-correct rate" hugs the floor a long time, then suddenly leaps** — smooth inner skill, paired with an "all-or-nothing" exam, naturally grows an S-shaped jump curve.

> Leo threw a bucket of **cold water:** "A 2023 Stanford paper had a provocative title — *Are Emergent Abilities of Large Language Models a Mirage?*: same models, same task, swap 'all-or-nothing scoring' for a smooth metric that 'gives partial credit per step,' and **many famous steep jumps instantly revert to gentle climbs.** **What jumps may not be the ability, but the ruler.**"
> Leo summarized: "The one conclusion that truly matters to you — **average loss can be computed in advance; when a single ability appears, no one can compute in advance to this day.** The power law insures the arms race; emergence leaves it a cliffhanger."

---

## Section 4 · The Ceiling Debate: how much longer can brute force make miracles

"By 2024, the mood changed," Leo said. "The three power-law lines still hold mathematically — the problem is the '**toll**' of walking right along them began to spiral out of control. The industry started openly debating: is the pure-parameter-piling road near its end?"

He drew three walls on the whiteboard:

> 🧱 **Wall 1 · Data:** the power law wants **quality** tokens, and the public internet's high-quality text is **finite** — a frontier model's one-run data intake is already on the same order as 'the whole web's premium stock.' Photocopying more low-quality pages won't feed a smarter model.
> 🧱 **Wall 2 · Cost:** moving one square right on the log axis = real money ×10. Flagship training is already hundred-million-dollar level, the next generation is widely estimated at **billion-dollar level,** with power and chips equally strained.
> 🧱 **More painful · diminishing returns:** this is actually the power law's own prophecy — the further right, the **smaller** the loss drop the same ×10 investment buys. Loss still drops, but that user "wow" **gets more and more expensive.**

"So from 2024 on, the frontier competition turned to new axes," Leo said. "**Not abandoning Scaling, but Scaling somewhere else:**"

> 🆕 **New axis ① · test-time compute (think more at answer time):** rather than throwing all compute at training, let the model "think" a few more steps before answering — OpenAI's o-series in late 2024 and DeepSeek-R1 in early 2025 pushed this road into the mainstream (detailed in Chapter 23). **The scale race isn't over, it just shifted from 'pile at training' to 'pile at answering.'**
> 🆕 **New axis ② · data quality & synthetic data:** from "feed more" to "feed better" — finely filter, rewrite, even use a strong model to generate "textbook-grade" data to feed the next generation. **Same compute, one serving of good data beats several of bad.**

> Leo gave a fair conclusion: "'Scaling is dead' and 'Scaling forever' are both clickbait. The agreed status quo of 2025 is — **brute force still makes miracles, but the unit price of miracles is soaring; the frontier keeps piling scale on one hand while pushing more chips onto the new axes on the other. Force still has to be exerted; the key became where to exert it.**"

---

## Section 5 · Traps You'll Probably Fall Into Too

**Trap 1: "The power law is so reliable, pile on scale infinitely and you must reach AGI"**

> ❌ Mistaking "loss," this average report card, for "intelligence" itself.
> ✅ The truth is — the power law only guarantees "average word-guessing error" descends smoothly, **not that all abilities strengthen linearly;** data, cost, and power each have physical ceilings.

Root cause: how much ability gain is left as loss approaches its limit, **no one can read from the power law** — the emergence debate shows exactly that single abilities and the average score don't sync. And every square right on the line costs real money ×10: **the mathematical straight line slams into physical walls.**

**Trap 2: "In the Scaling era, small models have no value"**

> ❌ Staring only at "strongest," forgetting "good enough, cheap, offline."
> ✅ The truth is — **a distilled small model + on-device deployment is another important front,** phones, cars, and privacy scenarios all count on it.

Root cause: let the large model be the teacher and "distill" its skill into a small model (detailed in Chapter 27), pair it with Chinchilla-style "overfeeding," and a few-billion parameters can cover lots of everyday tasks — per-call cost differs by orders of magnitude, and on-device running doesn't even upload data. **The frontier piles scale, the industry shrinks scale; both fronts hold at once.**

**Trap 3: "'Emergence' shows the model suddenly clicked at some instant, even sprouting consciousness"**

> ❌ Anthropomorphism + media love of dramatic narrative.
> ✅ The truth is — emergence describes the **jump in evaluation scores;** the model's internal ability climbs continuously, and some jumps may be an illusion created by the scoring method.

Root cause: the model has no "click moment" — per-step ability rises smoothly all along, and the "all-or-nothing" exam displays a gentle climb as a leap (the tightrope effect). **Mentally embellishing a score jump into "awakening" slides from science into sci-fi.**

---

## Section 6 · The Finishing Move: see through "the ten-billion gamble" in one sentence

Same ritual: a manual + a kill shot.

### The Scaling core, one table to mop it all up

| Concept | In a sentence |
|---|---|
| **Power law / Scaling Laws** | on log paper, loss follows scale in a straight line; small models draw, large models extrapolate |
| **Chinchilla balance** | about 20 tokens per parameter; piling only parameters = malnutrition |
| **Emergence** | average score climbs smoothly, all-correct rate jumps tightrope-style (real or not still debated) |
| **2024 pivot** | data wall + cost wall → bet on test-time compute & data quality |

### The finishing move: answer for the CEO, "how do you guarantee the trained model won't flop"

From now on, see "billion-dollar model training" news and you'll know it's no gamble—

> 　🗣️ **"Small first, then large, draw-and-extrapolate: first train a row of small models at a thousandth of the cost, plot the loss on log paper, the power law guarantees they line up straight; extend to the target scale and read off the final loss before training even begins."**
> - GPT-4 did exactly this (a ten-thousandth of the compute locked in the answer in advance).
> - But the honest CEO must add: **what can be predicted is the average loss; when a specific ability (like multi-step reasoning) emerges, no one can compute in advance to this day.**
> - And another bucket of cold water: hearing "our model emerged ability X," first ask two things — **is the scoring 'all-or-nothing'? Switch to partial credit, does the curve still jump?**

### Squeeze the whole chapter into one sentence and stuff it in your head

> **Scaling Laws = loss descends smoothly with parameters/data/compute by a power law, a straight line on log paper, extrapolating from small models to large — "brute force makes miracles" has a mathematical guarantee for the first time.**
> But you must balance (about 20 tokens per parameter, piling only parameters = a malnourished Chinchilla); average loss can be predicted, but a single ability's "emergence" is tightrope-like, and may be an illusion the ruler creates.
> After 2024 it hit the data wall and cost wall, the race pivoted to "think more at answer time" and "feed better data" — force still has to be exerted, the key is where.

---

## 🎓 Stage 3 · Clearing-the-Level Recap

Mia slumped in her chair, exhaling: "From a pile of debris to a chatting ChatGPT... I've seen the whole journey!"

Leo smiled and strung the five chapters into a "forging chronicle":

> 1️⃣1️⃣ **Token** — the word-cutting knife chops language into Lego blocks; the model sees only IDs, not letters.
> 1️⃣2️⃣ **Pretraining** — play a trillion rounds of word chaining on the entire internet, compression is intelligence, forging the "savage beast" base.
> 1️⃣3️⃣ **SFT + RLHF** — two taming spells, grinding the beast into a thoughtful assistant that answers everything and knows proportion.
> 1️⃣4️⃣ **Temperature sampling** — pour a few shots of liquor into the brain, tuning "how to draw words," not the knowledge.
> 1️⃣5️⃣ **Scaling Laws** — brute force makes miracles with a mathematical guarantee, but must balance, and it hit a ceiling.

"You notice," Leo said meaningfully, "these five chapters are a **complete production line:** cut the material (Token) → pour in knowledge (pretraining) → tame it obedient (alignment) → tune the card-playing style (sampling) → and why the whole line gets stronger and stronger, Scaling Laws backstops it. **By here, how an LLM is forged from nothing, you've watched the whole way through!**"

Mia's eyes lit up: "The engine's installed, the fire's lit, and it speaks properly now... so next, you teach me **how to put it to use,** right?"

"Exactly!" Leo slapped the table. "Stage 4 — **Application · Putting Large Models to Work!** From writing good prompts, to giving AI an external knowledge base and tools, to building an agent that works on its own. **The mythic weapon is forged; time to draw it (★ω★)**"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** **Scaling Laws** = loss descends smoothly with parameters/data/compute by a **power law,** a **straight line** on log paper, **extrapolating** from small models to large — "brute force makes miracles" has a mathematical guarantee for the first time. But you must **balance** (about 20 tokens per parameter, piling only parameters = a malnourished Chinchilla); and when a single ability "emerges," no one can compute in advance to this day.
> **🎯 Trigger · pull this out whenever:** see "10,000-GPU cluster / ten-billion funding," don't take it as a crazy gamble, it's **an engineering budget whose return the power law computes;** see "model X emerged ability Y," first ask two things — **is the scoring "all-or-nothing"? Switch to partial credit, does the curve still jump?** (emergence may be an illusion the ruler creates)
>
> **✍️ Self-test with the book closed:**
> 1. How does the CEO guarantee the model won't flop before training begins? (How did GPT-4 do it?)
> 2. Use Chinchilla to rebut "fixed budget means throw it all at parameters."
> 3. Why does the average score climb smoothly, yet some problems seem to "suddenly become doable"? (The tightrope effect.)

> 🪜 **Next stage preview:** Stage 4 · Application — putting large models to work (Chapters 16–20).


---

[← Previous](../stage_3/chapter_14.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_4/chapter_16.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
