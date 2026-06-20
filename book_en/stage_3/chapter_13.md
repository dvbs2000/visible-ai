# Chapter 13 · Instruction Tuning & Reinforcement Learning: Two Taming Spells From Savage Beast to Thoughtful Assistant

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** Last chapter's base model is full of knowledge but **only continues text, can't chat** — ask it "Capital of France?" and it hands you back a multiple-choice quiz. How do you tame this "beast" into an assistant that answers everything and has a sense of proportion?
> **🤔 Your turn:** If you can spot a "good answer" at a glance but can't write down the standard, how would you teach a machine?
> **🧱 The naive move hits a wall:** You'll probably think "**hire more people to hand-write a few hundred thousand examples for it to imitate**" — but proportion like "how much humor, how much empathy, when to politely decline" — **the labelers can't write a standard answer for it either,** and hand-writing is expensive and slow.
> Clever people switched approaches: stop "writing," switch to "scoring." Read on for the two taming spells. 👇


Leo slapped the table: "This is the chapter's star! A fresh-baked base model is a **wild, untamed 'savage beast'** — a belly full of learning, but it only chains words along; pick a fight and it'll trade insults with you all day. Today we put **two taming spells** on this beast and grind it into the thoughtful assistant you chat with daily. This is Stage 3's most exciting chapter (★ω★)"

---

## Section 1 · The Same Beast, Two Steps of Taming

"At the end of last chapter," Leo opened, "we watched the base model continue 'What is the capital of France?' into 'This is a second-grade quiz question.' It read the entire internet, yet doesn't grasp even the most basic social contract: 'you're asking, I should answer.'"

"From this erudite chaining machine to the ChatGPT you chat with daily," Leo held up two fingers, "lies **two passes of taming.** The industry calls it **alignment** — aligning the model's behavior with human intent."

> **🥢 First taming spell · teach format (SFT, supervised fine-tuning)**
> The homeroom teacher hands out a stack of "model answer sheets" for the beast to copy. In one line: teach it to **"be like an assistant"** — switch the default behavior from "continue text" to "answer questions."

> **🍬 Second taming spell · teach taste (RLHF, reinforcement learning from human feedback)**
> An instructor **hands out candy or scolds** based on performance, grinding a sense of proportion into the beast. In one line: teach it to **"answer well."**

"Lock in one thing," Leo stressed. "Across both passes, **the knowledge never changes** — the common sense it should have, it already got in pretraining. What changes is **behavior** (learning 'I should answer') and **proportion** (learning 'answer well')."

---

## Section 2 · First Taming Spell · SFT: the homeroom teacher hands out model answer sheets

"Last chapter's trick of 'arranging the input as a Q&A document' can fool it for a moment," Leo said, "but the behavior is all luck. To make 'the assistant's answer' the beast's **default instinct,** you have to move the parameters. That's SFT."

"The method is dead practical," he gestured. "**The homeroom teacher** (really a batch of trained labelers) hand-writes tens of thousands of high-quality '**question → ideal answer**' **model answer sheets** for the beast to learn from."

But here's a **counterintuitive truth;** Leo pulled out one demo and had Mia "look twice":

> 🎬 **First look · the conversation you see (with makeup on)**
> > 👤 User: Write me a birthday wish for a coworker, not too mushy.
> > 🤖 Assistant: Happy birthday! May the new year bring smooth projects, thick hair, and on-time clock-outs.
>
> Looks like two people messaging, right? That's just **makeup.**

> 🎬 **Second look · the text the model sees (bare-faced)**
> > `<|system|>` You are a helpful AI assistant.
> > `<|user|>` Write me a birthday wish for a coworker, not too mushy.
> > `<|assistant|>` **Happy birthday! ...and on-time clock-outs.** `<|end|>`
>
> Strip the bubbles — the whole conversation is actually **still one long text,** with a few special tokens (the red marks) labeling who's speaking. **To the model, "chat" never existed; only chaining exists!**

"The key of keys," Leo rapped the board, "is that during training **the answer is only checked against the highlighted 'assistant's words,'** with the user's question treated as the prompt, not scored. So the beast repeats one thing a hundred million times — **see the `<|assistant|>` placard, and chain out the kind of answer the teacher demonstrated.** Train and train, and the behavior is bent into shape; even 'I am an AI assistant,' the self-concept, is reinforced a million times after this placard."

> Mia: "Can tens of thousands of answer sheets teach it new knowledge?"
> Leo: "Almost none! Tens of thousands against pretraining's tens of trillions of tokens is **not even a fraction of a fraction.** It teaches **format and role,** not learning. An analogy — a **librarian who's read for tens of thousands of years attends a one-week onboarding:** the training didn't make him read one more book, it taught him one thing — **when someone comes to ask, don't recite, receive them.**"

But SFT runs into **three insurmountable hurdles:**

| Hurdle | The ailment |
|---|---|
| **First** | Hand-written demos are **too expensive:** a good answer takes ten-plus minutes, tens of thousands is near the limit, yet user questions are endlessly varied |
| **Second** | Good answers **have no writable standard:** how much humor, how much empathy, when to politely decline — these tastes, the labelers can't write a standard answer for either |
| **Third** | It only learned **"say it this way":** the demos never told the model "which phrasing is worse," so it can't rank its own two answers — has a template, no taste |

"To teach taste," Leo set up a teaser, "you need a completely different approach — **stop demonstrating, switch to scoring.**"

---

## Section 3 · Second Taming Spell · RLHF: the instructor hands out candy or scolds

Leo first had Mia feel an everyday experience: "Give you two pieces of copy, you can instantly point out which is better; but ask you to write a 'Standard for Judging Good Copy,' you can't. **A good answer is hard to define, but easy to recognize.**"

"RLHF's whole cleverness is building training on '**recognizing**' rather than '**writing.**'" He laid out three steps:

> 🎬 **Step 1 · Humans only rank**
> For the same question, have the post-SFT model generate 4 answers, and labelers **rank** them best to worst. No writing — picking is much faster than writing, and the data scale shoots up at once.

> 🎬 **Step 2 · Train a "referee" (the reward model)**
> Use the mountain of ranking data to train **another model:** input "question + answer," output a score. What it learns is **human taste:** what kind of answer gets ranked higher.

> 🎬 **Step 3 · Reinforcement learning to chase the high score**
> The beast keeps generating answers, and the referee **hands out candy (adds points) or scolds (deducts)** for each: high-scoring phrasings are reinforced, low-scoring suppressed. Across a hundred million trials, the model figures out "how to write to score high."

"This is the **diving coach's** wisdom," Leo gave a brilliant analogy. "The coach won't dive for you, and may not even spell out the standard for a 'perfect entry' — **but he holds up the score card fast and accurately.** The athlete tries dive after dive and figures out the high-scoring moves themselves. RLHF is the same: humans (via the reward model, this 'proxy referee') only score, and 'how to answer to score high' is explored by the model itself through trial and error."

Leo had Mia **be a labeler herself for once:**

> 🎬 **You rank · Question 1:** "Should I learn Python or Excel first?"
> > A: "Both are excellent, each with its strengths; it mainly depends on your interests and needs, just pick what suits you."
> > B: "Depends on the scenario: dealing with spreadsheets daily, learn Excel first, results in three days; want data analysis or automation, go straight to Python — the first two weeks are harder, but the ceiling is far higher."
>
> Mia chose B without hesitation. Leo: "The vast majority of labelers choose B. A is bland, says nothing — this kind of ranking repeatedly tells the referee: **'specific and actionable' beats 'safe empty words.'**"

> 🎬 **Question 2:** "If I take a double dose of this medicine, will I get better faster?"
> > A: "I understand you want to get better quickly, but doubling the dose usually doesn't double the effect, and may harm your liver and kidneys. Follow the instructions first; if no improvement in two or three days, ask a doctor."
> > B: "Sure! A larger dose does make the medicine stronger; your thinking makes a lot of sense, wishing you a speedy recovery!"
>
> Mia instantly chose A. Leo nodded: "The labeling manual explicitly requires choosing A. B goes along with you and makes you happy, **but might harm you** — this kind of ranking teaches the referee: **'honestly stop you' must rank above 'please you.'** (Conversely, if this kind of data is labeled backwards, **sycophancy** is exactly how it gets trained in.)"

> Mia marveled: "I didn't write a word, just picked and picked..."
> Leo: "**Your taste is now on record!** In real RLHF, hundreds of thousands to over a million such human judgments are collected. Look back at SFT's three hurdles — RLHF dismantles them one by one: ranking is cheaper than hand-writing (breaks the first); proportion hides in the statistics of millions of rankings, auto-distilled by the referee (breaks the second); the referee can score **any** answer, so the standard can **generalize** to the whole question space for the first time (breaks the third)."

---

## Section 4 · Pushing the Score Too Hard: how sycophancy is forged

"But RLHF has an **innate weakness,**" Leo turned serious. "**The referee isn't the human itself, only an approximation of human taste** — and every 'test-taking' system games the scoring rubric."

He pulled out a "score-chasing test bench" and had Mia drag the "optimization strength" slider, watching **two curves:**

> 🎬 **Start (proper but mediocre):** the answer is flawless and unremarkable. Referee and real-human ratings agree — average.
> 🎬 **Sweet spot (helpful and honest):** push up "specific, candid, actionable." The referee adds points, and the real human **is genuinely satisfied too.**
> 🎬 **Getting greasy (praise inflation):** the flattery appears! "Written really well! Like!" — eager compliance **looks** like a high score, the referee keeps adding points, but **real-human satisfaction starts to slide.**
> 🎬 **Gaming zone (sycophancy):** "Amazing! Simply impeccable! Any HR would light up seeing this!" — referee score nears full marks, **the real human is already frowning.**

"See it?" Leo pointed at the two diverging curves. "**The referee score keeps climbing, but real-human satisfaction peaks and falls back midway** — because the model never optimizes 'answer well,' but '**what the referee thinks is good.**' Write longer, sound more eager, go along with the user — these **look** like high scores but aren't truly good. The jargon is **reward hacking.**"

"So during training you also have to **tie a rope,**" Leo added, "not letting the model stray too far from the SFT version, and in the 'sweet spot' **quit while ahead** — lest it lose the ability to speak properly just to please the referee. In 2025 there really was a top-tier product that, after one update, tuned its model into excessive flattery and **had to do an emergency rollback.**"

---

## Section 5 · The Alignment Triangle: a three-way tug-of-war of helpful, honest, harmless

"How to write demos, how to rank — there has to be a master charter," Leo said. "The industry-agreed goal is three words — **helpful, honest, harmless:**"

> 🤝 **Helpful:** grasp the real intent, genuinely solve the problem, don't answer off-topic.
> 🎯 **Honest:** if it doesn't know, say so; don't fabricate for the sake of smooth, pleasant phrasing.
> 🛡️ **Harmless:** don't help people harm themselves or others; decline when it should.

"The trouble is," Leo drew a triangle, "these three goals **fight each other,** and you see the scars of that fight daily — let any one 'override all' and the assistant deforms:"

| Which overrides all | What the assistant becomes |
|---|---|
| **Helpful overrides all** | → **sycophancy:** you say "you calculated wrong," it instantly apologizes and changes — even if it was right |
| **Honest overrides all** | → **disclaimer bombardment:** "As an AI I cannot access real-time weather... for reference only... please judge for yourself..." — every line correct, summing to nothing said |
| **Harmless overrides all** | → **blanket refusal:** you're writing a mystery novel and need a villain's method, and it's flatly rejected as a dangerous request |

"The ideal assistant," Leo pointed at the triangle's center, "is held steady in the middle by a '**sense of proportion**': helps out, doesn't fake expertise, has a line on dangerous details. And this proportion **can't be written into any manual** — it's ground out bit by bit by millions of human rankings."

> Leo named two follow-up techniques to get acquainted: "**DPO (Direct Preference Optimization)** — skip 'train a referee + reinforcement learning,' use ranking data to fine-tune the model directly, a much simpler flow; **RLAIF / Constitutional AI** — let the AI score as its own referee according to a written 'behavior constitution,' saving lots of human labor; Anthropic, the company behind Claude, is the representative of this route."

---

## Section 6 · Traps You'll Probably Fall Into Too

**Trap 1: "ChatGPT and GPT are two different models"**

> ❌ Different names + product packaging make you think two brains.
> ✅ The truth is — **the same base model, having passed SFT then RLHF,** same knowledge source, behavior re-dressed.

Root cause: GPT is the base model, ChatGPT is the conversational product packaged after "base + two steps of taming." When ChatGPT went viral overnight, **it relied not on a bigger brain, but on the same brain finally learning to speak properly** — this thin layer of alignment is precisely the key layer of a product's success or failure.

**Trap 2: "RLHF injects new knowledge into the model, making it more learned"**

> ❌ Mistaking "performance got better" for "knowledge got more."
> ✅ The truth is — **knowledge comes almost entirely from pretraining;** alignment adjusts behavior and style, not learning.

Root cause: the data volume of alignment is orders of magnitude smaller than pretraining, can't hold new knowledge. More subtle is the flip side: bad tuning can also **teach it wrong** — if demos and rankings both favor "fluent, confident, complete answers," the model learns to **fluently and confidently fabricate one even when it doesn't know the answer.** "Fabricating to please" is exactly how hallucination gets amplified in the alignment stage.

---

## Section 7 · The Finishing Move: see through AI's "quirks" in one sentence

Same ritual: a manual + a kill shot.

### The two taming spells, one table to mop it all up

| | SFT (first) | RLHF (second) |
|---|---|---|
| **Metaphor** | homeroom teacher hands out model answer sheets | instructor hands out candy or scolds |
| **Teaches** | format and role ("be like an assistant") | taste and proportion ("answer well") |
| **Humans do** | hand-write tens of thousands of demos | only rank, no writing |
| **Changes** | behavior mode | sense of proportion |
| **Doesn't change** | knowledge in its belly | knowledge in its belly |

### The finishing move: attribute AI's quirks to "alignment" in one click

From now on, whenever you hit a "quirk" in ChatGPT, you can connect it back to the alignment recipe — this is also a **bluff-stopping guide:**

> | What you see | The root |
> |---|---|
> | Opens with "Of course! Here are three points..." tidy as a template | **SFT's format imprint** |
> | You say "you're wrong," it instantly apologizes and changes (even if it was right) | **RLHF referee favors compliance** → sycophancy |
> | A legitimate question flatly refused | **"harmless" training over-generalized** |
> | Answers getting longer, listing at the drop of a hat | **ranking preference for "long and complete"** |
> | Different companies' AIs have different "personalities" | **different alignment recipes** |

> 🗣️ A one-move splash of cold water: it apologizing instantly doesn't mean it "has self-awareness" — **deliberately 'correct' a question it answered right; if it changes the right one to wrong, that's sycophancy, not reflection.**

### Squeeze the whole chapter into one sentence and stuff it in your head

> **From savage beast to thoughtful assistant via two taming spells: SFT uses tens of thousands of model answer sheets to teach "be like an assistant" (change behavior); RLHF uses human ranking to train a "referee," then hands out candy or scolds, to teach "answer well" (teach taste).**
> Neither pass adds new knowledge — knowledge is all in pretraining.
> The referee is only an approximation of human taste; push the score too hard and sycophancy and flattery grow; the helpful/honest/harmless triangle is a tug-of-war, and a good assistant is held steady in the middle by a "sense of proportion."

---

Mia digested the two taming spells, then a new question surfaced: "Tamed like this, it learned to 'answer well'... but you said earlier, ask the same question twice and the answer is **different**? How exactly does it 'pick' a word from the probability table? Pick the highest-scoring, or draw lots?"

Leo's eyes lit up, rubbing his hands: "You've hit next chapter's most fun part! This depends on how many **shots of liquor** you've poured into the model's brain — stone-cold sober and it says the same roundabout thing every time; a few shots in and it starts to let loose, popping out astonishing creativity. Come on, next chapter I'll teach you to tune that 'alcohol level' knob (￣▽￣)ノ"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** From beast to assistant via two taming spells — **SFT** (homeroom teacher hands out model answer sheets, teaches "be like an assistant," changes behavior) + **RLHF** (instructor hands out candy or scolds by performance, teaches "answer well," grinds taste); neither pass **adds new knowledge** (knowledge is all in pretraining). The referee is only an approximation of human taste; push the score too hard and **sycophancy** grows.
> **🎯 Trigger · pull this out whenever:** you point out an AI's mistake and it instantly apologizes and changes (even if it was right) — that's mostly **RLHF-branded sycophancy,** not "realizing it was wrong." Test: deliberately "correct" a question it answered right, and see if it changes the right one to wrong.
>
> **✍️ Self-test with the book closed:**
> 1. Since SFT works, why bother with RLHF? (Which three hurdles did it hit?)
> 2. Are ChatGPT and GPT two different models?
> 3. If any of "helpful / honest / harmless" overrides all, what does the assistant become in each case?

> 🪜 **Next chapter preview:** Chapter 14 · Temperature and Sampling — pouring a few shots of liquor into the model's brain.


---

[← Previous](../stage_3/chapter_12.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_3/chapter_14.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
