# Chapter 14 · Temperature and Sampling: Pouring a Few Shots of Liquor Into the Model's Brain

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** Ask ChatGPT the same question twice and you **get two different answers;** hit "regenerate" and it rephrases again. Isn't a program "same input, same output"? How exactly does it "pick" words?
> **🤔 Your turn:** At each step the model actually hands over a "candidate-word probability table." Picking a word from this table — would you pick the highest-scoring, or draw lots?
> **🧱 The naive move hits a wall:** Always pick the highest-scoring (greedy) looks most stable — but researchers found this writes long text that's **stiff, hollow, and loves to repeat** ("I think it's great. I think it's great. ..." stuck in a loop). Yet drawing purely by probability, long text sooner or later **pops out a nonsense word.**
> One side a parrot, the other a drunk — what to do? Read on for that "alcohol level" knob. 👇


Leo gave a mysterious smile and fished a small bottle of liquor from under the desk and shook it: "This all depends on how many **shots of liquor** you've poured into the model's brain! Stone-cold sober, it says the same roundabout thing every time; a few shots in and it lets loose, popping out astonishing creativity — and may also start babbling. Today I'll teach you to tune that 'alcohol level' knob; the jargon is **temperature** (￣▽￣)ノ"

---

## Section 1 · The Answer Isn't "Thought Out," It's "Drawn"

Leo first punctured a counterintuitive truth: "You've surely seen it — ask ChatGPT the same question twice, get two answers; unhappy, hit 'regenerate' and it rephrases. Isn't a program 'same input, same output'?"

"The riddle was planted back in Chapter 10," he said. "The Transformer processes all the way to the end and **hands over not a word but a probability table:** the hundred-thousand-plus tokens in the vocabulary, each assigned a share of probability, summing to exactly 100%. **The model's work ends here.**"

> **Intuitive impression:** think out the answer → type it word by word. (Then that "regenerate" button shouldn't exist at all)
> **Real mechanism:** every word is **drawn by lot, by probability,** from the table.

"'Which word to pick next,'" Leo stressed, "is **a separate, independent step,** called **sampling.** From the same probability table, **a different way of drawing lots gives the model a completely different 'personality.'**"

He used one concrete scene to run through the whole chapter: have the model continue "The weather today is really ___." The top few of its probability table look roughly like this:

> 　nice 43% · good 22% · sunny 14% · pleasant 10% · terrible 4% · cold 3% · hot 2% · weird 1%

"Note 'weird' is just **the start of the long tail,**" Leo reminded. "In the real vocabulary, behind it queue a hundred-thousand-plus more obscure words. And the **temperature** knob does exactly one thing — **decide what this probability table looks like before lots are drawn.**"

---

## Section 2 · Temperature = Alcohol Level: from stone-cold sober to dead drunk

"Imagine a photo editor's **contrast** slider," Leo gave an analogy. "Pull right, bright spots brighter, dark spots darker, the subject pops; pull left, the whole image grays into a blur. **Temperature is the probability table's contrast slider, just reversed — the lower the temperature, the stronger the contrast.**"

"But today let's use my favorite analogy — **alcohol level!**" He twisted the imaginary knob and showed Mia three states of drunkenness:

> 🎬 **T ≈ 0 · stone-cold sober (perfectly clear, stubborn)**
> **Wildly amplify** the score gaps, the top one crushes the field. "nice"'s probability is pushed toward 100%, "weird" is out entirely. The model trends toward "**always pick number one,**" the **greedy mode** — draw ten times, basically ten "nice"s. **Stable, but dull, saying the same roundabout thing every time.**

> 🎬 **T ≈ 1 · tipsy (plays it as is)**
> The scores go to the draw untouched. This is the model's "raw judgment": number one leads but doesn't monopolize, the long tail has a tiny chance. **Stable with variation, occasionally rephrasing.**

> 🎬 **T ≈ 2 · dead drunk (let loose)**
> The score gaps are **flattened,** probabilities trend toward "everyone gets a share." Even "weird" gets a sizable probability — **draw a few times and a weird word is bound to appear.** Vivid, sometimes sparking astonishing creativity, **but also spouting wild nonsense at any moment.**

"Why can one knob change the 'personality'?" Leo revealed the principle. "The key is the pre-draw step, **softmax,** is extremely sensitive to 'score gaps.' Real numbers: in the scene, "nice" is 3.8 points higher than "weird," a probability ratio of about **45:1;** set T to 0.5 (scores all doubled) and the gap becomes 7.6 points, the ratio explodes to about **2000:1** — "weird" has no chance; conversely set T to 2 (scores halved) and the gap shrinks to 1.9 points, the ratio shrinks to about **7:1** — obscure words turn the tables, nonsense takes the stage."

> ⚠️ Leo drew an **important boundary:** "**Temperature changes only 'how it draws,' not 'what the model knows'!** When you twist the knob, the model's parameters, knowledge, and reasoning ability **don't change one bit** — raising the heat can't squeeze out knowledge it doesn't have, lowering it can't supply ability it lacks. It's just the same brain, two **ways of playing its cards.**"

---

## Section 3 · Why You Need This Knob: parrot vs. drunk

Mia: "So why not fix it at one setting?"

"Because **both extremes have their own way of dying!**" Leo said.

> **🤖 Always-pick-number-one (T→0)'s death: the parrot**
> Researchers found early that greedy-written long text is **stiff, hollow, repeat-prone** — once a sentence has appeared, it enters the context and in turn raises its own probability of appearing again, so the model falls into a "I think it's great. I think it's great. I think it's great." loop.

> **🍺 Draw purely by raw probability (high heat)'s death: the drunk**
> Every step leaves the door open for the long tail, so long text **sooner or later draws a nonsense word.**

"One side a parrot, the other a drunk," Leo spread his hands, "which is why you need a **continuously adjustable knob,** to pick your own spot between 'stable' and 'lively.'"

He laid out a table — the same "The weather today is really ___," at three temperatures:

| Temperature | Number one "nice" | Number eight "weird" | The feel |
|---|---|---|---|
| T = 0.1 | ≈100% | ≈0% | parrot: draw a hundred times, basically all "nice" |
| T = 1.0 | 43% | 1% | raw judgment: stable with variation |
| T = 2.0 | 27% | 4% | let loose: draw a few and "weird," "terrible" pop out |

---

## Section 4 · Trim the Long Tail Before Drawing: top-k and top-p

"Temperature has a blind spot it can't control — **the long tail,**" Leo said. "The demo only drew 8 words, but behind "weird" queue a hundred-thousand-plus words: "glucose," "function," "declaration form"... each with minuscule probability, but a hundred thousand "minuscules" often add up to several percent. A 500-word answer = drawing several hundred lots, **a 1% per-draw accident rate, over several hundred draws, hits a mine almost certainly.**"

He gave a faceplant scene:

> 🍲 Braised beef recipe: cube the beef, blanch in cold water, brown with rock sugar, then pour in **... a customs declaration form.**

"One long-tail accident destroys a whole stretch of professionalism!" Leo said. "What **truncation strategies** do is **trim** the tail of the probability table before drawing, drawing only in the 'reliable zone.' Two ways to trim:"

> ✂️ **top-k · fixed-quota truncation:** keep only the **k highest-probability** words (40, 50 are common in practice), zero the rest, redistribute proportionally and draw. Simple rule, but "one-size-fits-all," ignoring the distribution's shape.

> ✂️ **top-p · adaptive truncation (nucleus sampling):** from number one down, **accumulate probability and stop the moment it just reaches p (say 90%),** zero everything outside the circle. **Tightens automatically when the distribution is sharp, loosens when it's flat** — following the model's "confidence."

"That's why top-p came from behind to become most systems' default," Leo summarized. "In real systems, temperature and top-p almost always pair up, **the full four-step line: shape it (temperature) → trim the tail (top-p) → renormalize the survivors → draw lots.** Temperature manages 'how daring to risk,' top-p manages 'where the line is.'"

> Leo drew a **limitation:** "Truncation guards against 'obvious nonsense words,' **not 'fluent wrong statements.'** A high-probability, grammatically perfect false statement sails through all truncation — this is Chapter 29's '**hallucination**': truncation controls weird words, not confident, straight-faced nonsense."

---

## Section 5 · In Practice: where to set this knob

"There's no 'best temperature,' only the 'right temperature,'" Leo said. "Before twisting, ask one thing: does this task want **right,** **natural,** or **broad**?"

| Setting | Wants | Suitable jobs |
|---|---|---|
| **Low 0–0.3** | wants **right** | fix bugs, translate contracts, extract data to a format — small answer space, low error tolerance, needs stable and reproducible |
| **Medium 0.5–0.8** | wants **natural** | chat, write emails, summarize articles — reliable yet not template-like. Most chat products' default |
| **High 0.8–1.2** | wants **broad** | brainstorm names, slogans, story openings — produce 20 at once for a human to pick. High heat handles divergence, gatekeeping handed back to the human |

> Leo added two reminders: "① Each API's default and range differ (some default 1.0, max 2.0); check the official docs before getting hands-on; ② **The web ChatGPT/Claude don't expose this knob,** the vendor picked a compromise for you — so 'the same model' feels different in different products, often not because the model changed, **but because the sampling settings changed.**"

---

## Section 6 · Traps You'll Probably Fall Into Too

**Trap 1: "Temperature is a 'creativity' switch; turn it up and AI gets more creative"**

> ❌ "Creativity" is an anthropomorphic word vendors and influencers love.
> ✅ The truth is — it only tunes the **probability table's smoothness;** high heat makes obscure words easier to draw, **bringing no new knowledge.**

Root cause: the model's knowledge and ability were set in pretraining (Chapter 12); temperature only changes "how to draw from existing judgments." **High-heat "surprises" all come from long-tail words — and surprise and nonsense are the very same batch of words.**

**Trap 2: "At T = 0 the model is most rigorous, the answer is definitely correct"**

> ❌ Mistaking "determinism" for "accuracy."
> ✅ The truth is — T=0 only guarantees "pick the highest-probability word each step;" **high probability ≠ factually correct, and it's wrong more confidently.**

Root cause: if the model favors a wrong statement to begin with (the wrong phrasing is more common in the training corpus), T=0 instead guarantees it **makes the same mistake every time.** **Temperature solves "stability," never "correctness."**

**Trap 3: "Different answers each time means AI has emotions, has free will"**

> ❌ Anthropomorphic projection.
> ✅ The truth is — it just **drew lots once more** from the same probability table; the random number differs, so the word differs.

Root cause: human behavior varies from mood, so we attribute the model's variation to "it's thinking" too. **Verification is easy: set the temperature near 0, and "free will" instantly vanishes** — real moods don't have a switch.

---

## Section 7 · The Finishing Move: see through "regenerate" in one sentence

Same ritual: a manual + a kill shot.

### The sampling core, one table to mop it all up

| Knob | Manages | In a sentence |
|---|---|---|
| **temperature** | the probability table's shape (alcohol level) | low heat sober and stubborn, high heat let-loose and daring |
| **top-k** | fixed-quota tail trim | always keep the top k |
| **top-p (nucleus)** | adaptive tail trim | accumulate to 90% and stop, following confidence |

### The finishing move: demystify "AI's creativity" in one sentence

From now on, whoever brags "turn up the temperature and AI gets more creative," gently correct:

> 　🗣️ **"Temperature only tunes the probability table's smoothness, adds not a shred of new knowledge — high-heat 'surprises' all come from long-tail words, and surprise and nonsense are the very same batch."**
> - Want **right** (code, facts) → low heat, but remember: stable ≠ correct, facts still need human checking.
> - Want **broad** (brainstorming) → high heat, paired with top-p as a nonsense backstop.
> - See "different answers each time, so AI has free will"? → set temperature to 0, "free will" vanishes on the spot.

### Squeeze the whole chapter into one sentence and stuff it in your head

> **At each step the model hands over only a probability table; "which word to pick" is a separate sampling step; temperature = alcohol level, tuning the table's shape, not the model's knowledge.**
> Low heat is stone-cold sober and greedily stubborn (loves to repeat); high heat is dead-drunk and let-loose, daring creativity but also spouting nonsense. top-k/top-p trim the long tail before drawing, guarding against long-tail accidents.
> Temperature only manages "stable or not," not "right or not" — and fluent wrong statements (hallucination) even truncation can't stop.

---

Mia twisted the imaginary alcohol knob, having a blast, then suddenly looked up: "Wait... you said temperature is only 'how to draw,' and the model's ability is set in pretraining. So where does its **ability** actually come from? Why is each generation stronger than the last? Is it... pile on more parameters and it gets smarter? Is there a rhyme to it?"

Leo *snapped* out a whiteboard and marker, eyes blazing: "You've hit Stage 3's grand-finale bullseye! There really is a 'rhyme' to it — a **'brute force makes miracles' troop-building curve!** Come on, next chapter I'll draw you the whiteboard and explain why piling on parameters **really works** (★ω★)"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** at each step the model hands over only a probability table; "which word to pick" is a separate **sampling** step; **temperature = alcohol level,** tuning the table's **shape** (low heat sober and stubborn, high heat let-loose and daring), **not the model's knowledge;** top-k/top-p trim the long tail before drawing, guarding against nonsense words.
> **🎯 Trigger · pull this out whenever:** whoever brags "turn up temperature and AI gets more creative," correct them — temperature only makes obscure words easier to draw, **surprise and nonsense are the same batch;** want "right" (code/facts) → low heat, want "broad" (brainstorm) → high heat; but temperature only manages "stable or not," **not "right or not."**
>
> **✍️ Self-test with the book closed:**
> 1. Why is "the answer isn't thought out, it's drawn"?
> 2. At T=0, is the model most rigorous and the answer definitely correct?
> 3. Why does a poetry generator dare run high heat, while a customer-service bot keeps it low?

> 🪜 **Next chapter preview:** Chapter 15 · Scaling Laws — the ultimate troop-building formula of "brute force makes miracles."


---

[← Previous](../stage_3/chapter_13.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_3/chapter_15.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
