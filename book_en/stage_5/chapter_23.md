# Chapter 23 · Reasoning Models: The Chain-of-Thought Scratch Paper and Added "Test-Time Compute"

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** Give the AI a word problem with a few twists and it often **blurts an answer, and gets it wrong.** The knowledge is clearly all there — why does it faceplant the moment it hits multi-step reasoning?
> **🤔 Your turn:** A "one-second trap question" awaits in the main text — first think: will the answer you blurt by intuition really match the one you compute after slowing down to draft?
> **🧱 The naive move hits a wall:** You think a hard problem just needs it to "think a while" — but every model so far has **a constant amount of thinking per token,** spitting an answer in one forward pass, **with no right to 'think a while.'** Several chained steps all crammed into one computation; one misstep and it all collapses.
> How do you give it "scratch paper"? This is 2026's absolute star. Read on. 👇


Leo *snapped* the table, eyes blazing: "You've hit **2026's undisputed absolute star!** These past two years, AI training's focus has shifted from '**diligently memorize the book (pretraining)**' to '**frantically self-correct on scratch paper at answer time (test-time compute)**'! Today I'll show you a reasoning model in the back, **wrestling with itself, overturning itself and starting over** (★ω★)"

---

## Section 1 · System 1 Blurts, System 2 Drafts

"First an experiment on yourself," Leo posed a problem. "Answer **within one second:** a bat and a ball cost $1.10 together, the bat costs $1 more than the ball — how much is the ball?"

Mia blurted: "10 cents!"

Leo smiled: "Now slow down and draft — if the ball is 10 cents, the bat is $1.10, and together they're **$1.20**! The correct answer is **5 cents.**"

"Psychologist Kahneman named these two mental processes **System 1 and System 2,**" Leo said. "That's exactly this chapter's key:"

> ⚡ **System 1 · fast thinking:** blurt it out — fast, effortless, **but trips on trap questions.** Recognizing faces, reading your native language, answering "capital of France" all rely on it; it just follows the smoothest path in intuition.
> 🐢 **System 2 · slow thinking:** draft it — slow, effortful, **advancing reliably step by step.** It comes online for 17×24 or trip planning, **writing intermediate results down and checking back anytime** — scratch paper is its external memory.

"Now the chapter's most important sentence," Leo rapped the board. "**All the models from the first 22 chapters live in System 1 by default!** The mechanism was planted in Chapter 10 — every token the model generates runs the whole context through a network of **fixed depth:** one forward computation, no more, no less. However hard the problem, it can't 'think a while before speaking.'"

> 🟢 "Capital of France?" → one forward pass is **more than enough** (seen millions of times in training, the highest-probability next token is "Paris").
> 🔴 "Chickens and rabbits in a cage, 35 heads, 94 feet, how many of each?" → still one forward pass, **it won't fit!** ("assume all chickens → compute the feet gap → convert" is a several-step chained chain; answering directly = cramming the whole chain into the one computation before the first digit pops out — Chapter 15's "tightrope": one misstep, total collapse).

"This isn't the model being 'dumb,'" Leo stressed. "It's that **the architecture allots each token a constant ration of thinking — a hard problem has no right to draw more.**" This explains a string of phenomena: ask common sense, instant and right; ask a word problem, "instant" but often wrong; add "think step by step" and accuracy visibly rises; turn on "deep thinking" and it spins for a long while...

---

## Section 2 · Chain of Thought: one sentence turns mental math into paper math

"Since a hard problem loses to 'constant thinking budget,' the plainest solution is right there," Leo said. "In 2022 Google researchers found: **change not a single parameter of the model,** just have it 'write out the steps' at question time — give a few stepped examples, or simply add '**let's think step by step**' — and word-problem accuracy **jumps immediately, some test sets two or three times over!** This move is **Chain of Thought (CoT),** the very thing that is Chapter 16's technique ③."

> 🔴 **Direct answer:** "A coat goes up 10%, then down 10% — pricier or cheaper than the original?" → "The same." ✗
> 　(After the rise the base changed, but "the same" is intuition's smoothest continuation, System 1 bites it.)
> 🟢 **Chain of thought:** same problem + "think step by step" → "Let the original be 100; up 10% to 110; down 10% means down 11, giving 99 — **cheaper than the original.**" ✓
> 　(Same model, not one parameter moved, the only difference: three lines of text before the answer.)

"Why does 'write a few more lines' save the day?" Leo unpacked it into three layers (each a mechanism planted earlier, now powered on):

> 🔌 **Layer 1 · the draft enters the context:** every token the model writes lines up in the context (Chapter 17's desk). **The draft it writes itself gets the same treatment as the question you typed** — all later generation is conditioned on it.
> 🔌 **Layer 2 · mental math into paper math:** once "up 10% to 110" is written, the next step needn't hard-remember it "in the head" — **attention looks straight back at this line** (Chapter 9). Relying on paper not memory, the error rate plummets.
> 🔌 **Layer 3 · big problem into small:** a four-step problem split into four "walk one step" generations, **each step's difficulty falling back into the range one forward pass can steadily handle** — a string of System 1's, relay-simulating System 2.

> Leo summarized CoT and pointed out the weak spots: "It **didn't make the model smarter, it just dropped each step back to a difficulty System 1 can steadily handle.** But as a prompt trick it has **three innate weaknesses:** ① **it relies on you reminding it** (forget the spell and it's back to blurting); ② **one road to the end** (chaining doesn't turn back; a wrong second step is rarely erased and redone); ③ **won't switch approaches** (hits a dead end, won't back up to the fork and try another). 'Can draft' and 'drafts well' are two different things — teaching the model the latter is the next act."

---

## Section 3 · Reasoning Models: forge drafting into an instinct

"In September 2024, OpenAI released **o1:** the first mainstream model that **writes a long draft first, then answers, with no reminder at all,**" Leo told it like a storyteller. "In January 2025, **DeepSeek-R1** followed, and even **laid the training method and the model weights open to everyone.**"

"If you've ticked 'deep thinking' in DeepSeek, this scene won't be unfamiliar—" Leo demoed a backstage picture-strip:

> 🎬 After the question, a big stretch of **gray small text** scrolls first: "Hmm, the user asked about a scheduling conflict... let me try sorting by dependency first... **wait, this is wrong,** A and B can't run in parallel, let me recompute..." and only after tens of seconds does the formal answer appear.

"That gray small text is the draft — **not a performance for you, but its real answering process,**" Leo said. "ChatGPT's 'Thinking...,' Claude's extended thinking, same thing."

"How do you forge 'drafting' from a trick into an instinct?" Leo revealed the training idea. "The most intuitive road is Chapter 13's SFT: hire people to write millions of perfect drafts for the model to imitate. But this road has two knots — textbook-grade drafts are **expensive and scarce;** worse, **a human's solving path isn't necessarily the path most natural to the model** (forcing it to imitate is like forcing a lefty to practice from a righty's copybook)."

"The breakthrough hides in the task itself," Leo's eyes lit up. "**A math problem's answer can be auto-graded by machine; whether code is right, run the tests and you know!** Since right-and-wrong needs no human judging, you can let the model **try freely on its own — give it candy when it's right.** This is exactly where reinforcement learning shines:"

> 🎬 **Pose questions:** draw from an auto-verifiable problem bank (math with answer keys, coding with unit tests).
> 🎬 **Generate freely:** let the model freely write "draft + answer," sampling many for the same problem, approaches all over the place.
> 🎬 **Machine grades:** look only at whether the final answer is right, **not judging whether the draft "looks human"** — no human grader, scale as big as you want.
> 🎬 **Reinforce:** drafts leading to the correct answer are rewarded, the model updates toward "drafting like that" (same family as Chapter 13's RLHF, just the reward comes from the hard standard of "right or wrong").
> 🎬 **Loop tens of thousands of rounds:** drafts get longer, more "skilled" — which moves are useful, **the accuracy rate decides.**

> Leo reached the chapter's highlight — the **aha moment:** "In the whole process, **no one taught the model a single 'thinking action.'** But midway through training, something magical happened: R1's training records show the model **spontaneously** writes 'wait, let me recheck this step,' then really turns back, finds the error, and switches approach to redo — the research team called this moment the '**aha moment.**'"
> "**Reflection, verification, backtracking, switching approaches** — moves that happen to patch chain-of-thought's three weaknesses, all **grew by themselves** under the pressure of 'only right answers get candy,' like Chapter 15's emergence, with no one writing them into a single line of code."

---

## Section 4 · The Second Curve: think a while at answer time

"Now we can cash in the hook planted at the end of Chapter 15," Leo said. "That chapter said: pure-parameter-piling's returns slow, the frontier bets its chips on 'test-time compute.' **The reasoning model is what that chip landing looks like** — beyond 'raise the model big,' AI gains a **second improvement curve:**"

> 📈 **First curve · training-time compute:** **raise the model big** (more parameters, data, GPUs). Chapter 15's star, investment in months and hundreds of millions of dollars — a one-time "education investment."
> 📈 **Second curve · test-time compute:** **think a while** at answer time (the more draft budget, the higher the hard-problem score). Investment in seconds and tokens — "on-the-spot performance" paid per question.

"But the second curve **isn't a free lunch,**" Leo computed. "The bill has two columns: **latency** (spinning tens of seconds, enough to drive off a casual chat); **cost** (draft tokens are generated and billed one by one like answer tokens, and a hard problem's draft is often **several to dozens of times longer than the final answer**). So 'whether to turn on slow thinking' becomes real skill:"

| Worth thinking a while | Don't crack a nut with a sledgehammer |
|---|---|
| Math, code, multi-constraint planning | Casual chat, summary, translation/rewrite |
| Long chain, one wrong step ruins all, right/wrong often verifiable | One-shot "intuition questions" |
| Every draft line trades for accuracy, money well spent | Ordinary model steady in one forward pass, faster and cheaper |

> Leo demoed a classic comparison: "Chickens and rabbits — **direct mode** bets the answer in one forward pass, 'chickens 12 rabbits 23,' **exactly reversed** ✗; **slow mode** drafts line by line: ① lay out conditions → ② assume all chickens → ③ convert → ④ get chickens 23 rabbits 12 → **⑤ wait, verify** (heads 23+12=35 ✓, feet 46+48=94 ✓) → ⑥ answer ✓. **The same model, spending a few dozen extra tokens of 'test-time compute,' lifts accuracy from about 30% to about 90%.**"

---

## Section 5 · Traps You'll Probably Fall Into Too

**Trap 1: "Reasoning models completely crush ordinary models; from now on use them for everything"**

> ❌ Reading "new generation" as "complete replacement."
> ✅ The truth is — its advantage is **concentrated on long-chain, verifiable hard problems;** on simple tasks it's slower, pricier, and may "overthink" into detours.

Root cause: slow thinking's payoff comes from "splitting a big problem small," but chat, rewrite, translation are one-shot small problems to begin with — the draft's extra tokens are pure cost. Research also found an "**overthinking**" phenomenon: a simple question generates a big draft, occasionally **overturning the originally correct first intuition into a wrong one.** **Ordinary and reasoning models are two tools in the box, not old-replaced-by-new** — which is why every product keeps both "fast/slow" routes.

**Trap 2: "That unfolded thinking process is the model's real thinking inside its head"**

> ❌ Anthropomorphizing plus literalizing.
> ✅ The truth is — the draft is "**useful text the training rewarded;**" it genuinely helps the model answer right, **but doesn't guarantee it faithfully reflects the internal computation.**

Root cause: the draft, like the answer, is text generated token by token (Chapter 12), and training only rewards "the draft leading to the correct answer," **never rewarding 'the draft faithfully reporting the computation.'** Interpretability research found the two may not match: the model sometimes **"has the leaning answer in mind" first, then generates a plausible-looking derivation** (post-hoc rationalization). Honestly: **the draft is very useful, but treating it as a "thought livestream" lacks sufficient evidence for now** — this is still an open research question.

---

## Section 6 · The Finishing Move: which problem is worth thinking a while

Same ritual: a manual + a kill shot.

### The reasoning-model core, one table to mop it all up

| Concept | In a sentence |
|---|---|
| **System 1 vs. System 2** | ordinary model blurts (constant thinking per token), reasoning model drafts first |
| **Chain of thought (CoT)** | write steps = draft into context, turn mental math into paper math, big problem into small |
| **Forging a reasoning model** | reinforcement learning on auto-verifiable tasks; reflection/verification emerge on their own |
| **The second curve** | test-time compute: think a while at answer time, trade tokens and latency for accuracy |

### The finishing move: two bills decide whether to "turn on slow thinking"

From now on, don't pick reasoning mode by feel, compute **two bills:**

> 　🗣️ **"① Time bill: can this task afford a tens-of-seconds draft? ② Money bill: the draft is often several to dozens of times longer than the answer — does that money buy enough accuracy gain?"**
> - Math/code/multi-constraint planning (long chain, verifiable right/wrong) → **turn it on,** every draft line trades for accuracy.
> - Casual chat/summary/translation (one-shot intuition questions) → **don't,** the ordinary model is faster and cheaper, and guards against "overthinking" detours.
> - Don't treat R1's scrolling thinking process as an "inner monologue" — it's **useful text the training rewarded,** not necessarily a faithful reflection of internal computation.

### Squeeze the whole chapter into one sentence and stuff it in your head

> **Reasoning model = forge "drafting" from a prompt trick (chain of thought) into a model instinct: do reinforcement learning on "machine-auto-gradable" tasks like math/code, letting the model write a long draft, reflect, verify, overturn, and redo, before answering.**
> It cashes in the second scaling curve, "test-time compute" — trading answer-time tokens and latency for hard-problem accuracy (System 1 → System 2).
> But it doesn't completely replace ordinary models (simple problems slower and pricier, may "overthink"), and that thinking process isn't necessarily a "thought livestream" (it's useful text the training rewarded).

---

## 🎓 Stage Transition

Mia fully got it and sighed: "Can paint, see, think... AI's repertoire is more and more complete!"

Leo nodded, then turned: "But have you noticed — the stronger it gets, **the more tools you want to connect to it?** Query databases, hook up browsers, read local files... But Chapter 19 left a loose thread: the model can 'issue request slips,' **yet every tool's interface looks wildly different,** application teams write glue code daily and keep connecting wrong. This is too chaotic!"

Mia: "Is there a unified standard?"

Leo fished a standard **USB socket** from his pocket and waved it, smiling mysteriously: "Right into the next chapter! Today's hottest **MCP** is here to fit a **unified USB port** onto all the world's tools — any large model, plug and play (★ω★)"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** reasoning model = forge "drafting" from a prompt trick (chain of thought) **into a model instinct** — do reinforcement learning on "machine-auto-gradable" math/code, letting it **write a long draft, reflect, verify, overturn and redo** before answering; this cashes in the second curve "**test-time compute**" (trade answer-time tokens and latency for accuracy).
> **🎯 Trigger · pull this out whenever:** to pick "deep thinking" or not, compute two bills — **① can you afford a tens-of-seconds draft? ② the draft is often several times longer than the answer — does that money buy accuracy?** Math/code/multi-constraint planning = on; chat/summary/translation = off (slower, pricier, may "overthink"). Don't treat R1's thinking process as an "inner monologue," it's **useful text the training rewarded.**
>
> **✍️ Self-test with the book closed:**
> 1. Use this chapter's mechanism to explain: why does adding "write out each step first" often save a trap question?
> 2. "AI's thinking looks exactly like a human's, this is its inner monologue" — two things to discount; what are they?
> 3. Of three things (customer-service FAQ / cross-department scheduling / copy polishing), which is worth turning on reasoning mode?

> 🪜 **Next chapter preview:** Chapter 24 · The MCP Ecosystem — fitting AI's toolbox with a standard USB port.


---

[← Previous](../stage_5/chapter_22.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_5/chapter_24.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
