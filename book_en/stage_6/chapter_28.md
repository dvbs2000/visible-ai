# Chapter 28 · Hands-On RAG: Hand-Write an External Knowledge-Base Pipeline

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** Chapter 18 understood RAG's principle diagram. Now — **can you actually write code and build, by hand, a knowledge base that queries your own documents?**
> **🤔 Your turn:** Do you think building a usable RAG requires first learning a heavy framework and deploying a vector database?
> **🧱 The naive move hits a wall:** Reach for a heavy framework and vector DB on day one — you only learn "calling packages," not RAG; and a framework's official demo runs in five minutes, fooling you that "RAG is simple," while real documents faceplant on arrival.
> First grasp the principle with 60 lines of bare Python. This chapter I'll even **deliberately shred the chunks and make the AI faceplant** then fix it. Read on. 👇


Leo rolled up his sleeves with a "finally, this moment" look: "This is **the book's most hardcore hands-on coding chapter!** Chapter 18 you understood RAG's principle diagram; today we turn it step by step **into running code** — and I'll **deliberately shred the chunks and make the AI faceplant on the spot,** then walk you through clearing the mine. Ready, code time (★ω★)"

---

## Section 1 · First Correct an Imagination: RAG = 60 lines + a list

"Before going hands-on, break a superstition," Leo said:

> **Imagined:** building RAG = learn a heavy framework + deploy a vector database.
> **Actually:** building RAG = **about 60 lines of Python + a list.**

"Frameworks and vector DBs are useful, of course," Leo stressed. "But **reach for them on day one and you only learn 'calling packages,' not RAG.** Today's goal: **60 lines, zero frameworks, no vector database** — vectors in a list, retrieval by sorting, generation by Chapter 26's one API call. Small but complete."

He hung Chapter 18's six-step diagram back on the wall: **offline index-building** (chunk → vectorize → store in list) runs once for the whole library; **online Q&A** (question to vector → take top-k → assemble prompt → generate) runs once per question.

---

## Section 2 · Five Code Segments: from document to answer

"Five code segments stitched top to bottom into one file is the complete `rag.py` — shorter than this page of explanation," Leo went segment by segment:

**① Read the document, cut into chunks**

```python
def load_chunks(path, chunk_size=300, overlap=50):
    text = open(path, encoding="utf-8").read()       # read the whole thing in
    chunks, start = [], 0
    while start < len(text):
        chunks.append(text[start:start + chunk_size]) # cut the next chunk
        start += chunk_size - overlap                 # advance, leaving 50 chars of overlap
    return chunks

chunks = load_chunks("docs.txt")
```
"**The chunk is the smallest unit of retrieval.** `overlap=50` makes adjacent chunks share 50 characters — without it, a key sentence cut exactly at the seam leaves each side with half, and nothing matches at retrieval time."

**② Loop calling embedding, store vectors in a list**

```python
def embed(text):
    resp = client.embeddings.create(
        model="text-embedding-3-small",   # for other languages, pick a model strong in that language
        input=text)
    return resp.data[0].embedding         # a string of a thousand-plus numbers -- Ch.8's "coordinates"

vectors = [embed(c) for c in chunks]      # the so-called "vector store" is just this list today
```
"Embedding is another interface alongside the chat API: send in a piece of text, get back a string of coordinates — **text close in meaning has close coordinates** (Chapter 8's entire fortune). Two engineering tips: vectorizing the whole library takes a few minutes, **save to a json file after, don't recompute on every startup.**"

**③ Cosine similarity: four lines of pure Python**

```python
import math
def cos_sim(a, b):                              # smaller angle, value closer to 1
    dot = sum(x * y for x, y in zip(a, b))      # dot product: more aligned in direction, bigger
    na  = math.sqrt(sum(x * x for x in a))
    nb  = math.sqrt(sum(x * x for x in b))
    return dot / (na * nb)                       # divide out length -- compare direction, not magnitude
```
"Chapter 8's mantra lands: **meaning is coordinates, smaller angle = more similar.** Those fancy indexes in a vector database ultimately compute the same number. **Four lines, and the whole pipeline's math tops out.**"

**④ Retrieval: question to vector, sort, take top-k**

```python
def search(question, k=3):
    q_vec = embed(question)                     # the question also becomes coordinates: must use the SAME embedding model!
    scored = [(cos_sim(q_vec, v), c)            # score every chunk in the library for similarity
              for v, c in zip(vectors, chunks)]
    scored.sort(key=lambda t: t[0], reverse=True)
    return [c for _, c in scored[:k]]           # take the top k chunks -- this is "retrieval"
```

**⑤ Assemble the prompt, call the chat API**

```python
def ask(question):
    pieces = search(question)
    context = "\n---\n".join(pieces)
    prompt = ("Answer the question using ONLY the material below; "   # draw the boundary: Ch.16's technique
              "for info not in the material, answer 'I don't know'.\n\n"
              f"[Material]\n{context}\n\n[Question]{question}")
    resp = client.chat.completions.create(      # Ch.26's call code, copied as is
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": prompt}])
    return resp.choices[0].message.content
```
"The soul is those two opening instructions — **'answer only from the material' draws the boundary, 'say I don't know if absent' seals off fabrication** (the antidote to Chapter 18's trap ②, must be written into the string in black and white). **The model doesn't know the vector store exists, it just did a 'reading comprehension with material'** — but to the asker, it suddenly 'understands' your private documents."

---

## Section 3 · Faceplant Scene: I deliberately shredded the chunks

"Now witness 'stepping on a pit,'" Leo smirked, deliberately changing `chunk_size` from 300 to **40,** rebuilding the index, then asking a question whose answer is clearly in the document:

> 🎬 **Faceplant picture-strip:**
> 　🎬 **Original:** "Annual leave 10 days (note: applies only to employees with 5+ years of service)."
> 　🎬 **After shredding:** `chunk_size=40` cuts this sentence **right before the parenthesis** — the front chunk has only "Annual leave 10 days," the qualifier "5+ years" thrown into another chunk.
> 　🎬 **Mia asks:** "I just joined, how many days of annual leave?"
> 　🎬 **AI confidently answers:** "**You have 10 days of annual leave.**" — dead wrong!
> 　🎬 Mia is stunned: "How did it eat the key condition 'only after 5 years'?!"

"**Don't guess, print it out!**" Leo laid down RAG debug's first principle. He `print`ed the retrieved top-3 fragments — sure enough, the hit chunk **had only 'Annual leave 10 days,' the qualifier nowhere in it.**

"The lesion lies plainly in the printed string," Leo said. "**Chunk it wrong and even a god can't save what follows** — the 'material' the model got was incomplete itself."

**How to fix?** Leo set `chunk_size` back to **300,** `overlap` to **50,** and rebuilt:

> 🎬 **Fixed picture-strip:**
> 　🎬 Now "Annual leave 10 days (note: applies only to employees with 5+ years of service)" **stays complete in the same chunk.**
> 　🎬 Mia asks again "I just joined, how many days of annual leave?" — the AI answers: "**The material shows 10 days of annual leave applies only to employees with 5+ years of service; you just joined, and the material doesn't provide a corresponding number.**" ✓
> 　"**Fixed!** It not only didn't fabricate, it honestly drew the boundary — those two instructions, 'answer only from material + say I don't know if absent,' are the backstop."

---

## Section 4 · Tuning Intuition: every knob has a price

"The code has three unremarkable numbers: `chunk_size=300`, `overlap=50`, `k=3`," Leo said. "They aren't truths, they're a **seesaw's fulcrum** — turn either way and you pay a price:"

| Knob | Too small | Too big |
|---|---|---|
| **chunk_size** | half a sentence per chunk, hit the word but lose the context (the faceplant just now) | one chunk mixes many topics, similarity diluted, easily grabs the wrong chunk, and crowds the window (Ch.17) |
| **top-k** | when the answer is scattered, the key evidence isn't fully hauled | the window crams with half-relevant chunks, noise drowns the right answer |

> Leo gave the heaviest line: "These parameters **have no universal value, the optimum grows on your own documents** — change one parameter, run ten real questions, see if answers get better or worse; **this crude method beats every tutorial.**"

---

## Section 5 · The Faceplant Clinic: three symptoms, print before you debug

> 🏆 **【Golden Playbook · RAG Faceplant Clinic】**
> RAG is a pipeline, **debug principle one: locate which stage broke before you fix;** the strongest tool isn't an observability platform, it's `print()`.
>
> | Symptom | Suspect first | How to check |
> |---|---|---|
> | **Retrieval comes back all irrelevant** | embedding | print top-k fragments + scores and eyeball them. **A non-English document with an English embedding is the prime suspect** — switch models; also check file encoding (garbled into the store = vectors all noise) |
> | **Fragments relevant but answer off-topic** | the assembled prompt | **don't guess, `print(prompt)`!** See if fragments are truncated, duplicate chunks stuffed in, or material too long crowding the question out of attention focus (Ch.17) |
> | **Not in the material, yet it hard-fabricates** | the prompt boundary | check whether you wrote "say I don't know if absent" in black and white (the antidote to Ch.18's trap ②); leave it out and it'll inevitably backfill from pretraining memory |

---

## Section 6 · Upgrade Path & Traps You'll Probably Fall Into

"Today's 60 lines are the skeleton of all production-grade RAG," Leo said. "Everything after is **swapping parts, not swapping the blueprint:** documents grow to hundreds of thousands of chunks and list iteration is slow? Swap the list for **pgvector** or a dedicated vector DB (just making 'store vectors + find nearest neighbor' fast and stable); want it more accurate? Add **reranking** (coarse-haul 50 chunks first, then fine-rank to 5) or **hybrid retrieval** (vector by meaning + keyword by literal text). **However high you upgrade the gear, the flow is still these six steps you just hand-wrote.**"

> 🏆 **【Golden Playbook · Pitfall Guide】**
>
> **Trap 1: "RAG engineering = call a library, one `.query()` and done"**
> ❌ A framework's official demo runs in five minutes, creating the illusion "RAG is simple."
> ✅ Truth: **retrieval quality is 80% of the work** — chunking strategy, embedding choice, top-k, prompt boundary, each must be tuned by hand.
> 　Root cause: running ≠ answering accurately. The demo uses clean data and simple questions; your real documents faceplant on arrival — and what needs tuning is exactly the parameters these five code segments expose. **The deeper the framework encapsulates, the less you know where to turn the knob.**
>
> **Trap 2: "Pick any embedding, they all just make vectors"**
> ❌ Thinking embeddings are all the same.
> ✅ Truth: **for non-English you must pick a model strong in that language,** and run a small eval with your own real Q&A before deciding.
> 　Root cause: embedding is the **foundation** of the whole pipeline — a crooked foundation makes later ranking and prompt assembly all for nothing. Models' ability across languages differs markedly; **run your own few dozen real "question → should-hit fragment" pairs, count the hit rate, then decide.**

---

## Section 7 · The Finishing Move

> 🏆 **【Golden Playbook · The Finishing Move: RAG faceplant, always print before concluding】**
>
> Your RAG answered wrong; don't rush to switch models or reach for a framework, follow the pipeline **trace by trace:**
> 　🗣️ **"Print the top-k fragments → relevant? Irrelevant = retrieval/embedding's fault (switch language model, check encoding, tune chunk); relevant → print the full prompt → fragments complete, boundary instruction present? All present → only then switch to a stronger chat model."**
> - Lock in the key fact: **RAG's knowledge is in the store, not the parameters** — adding a document just needs chunk + embedding + append, **not one parameter trained** (which is why knowledge updates cost near zero and win near-unanimously over fine-tuning).
> - Want fully offline? Point base_url to Chapter 27's Ollama, and these 60 lines become **a private knowledge base with no internet.**

### Chapter summary table

| Code segment | Does what | In a sentence |
|---|---|---|
| ① load_chunks | chunking | overlap prevents a sentence being severed |
| ② embed | vectorize, store in list | the "vector store" is just a list, remember to save to disk |
| ③ cos_sim | compute distance | four lines, compare direction not magnitude |
| ④ search | take top-k | the question uses the same embedding model |
| ⑤ ask | assemble prompt, generate | "say I don't know if absent" must be hard-coded |

> **Squeeze the whole chapter into one sentence:** hands-on RAG = 60 lines of Python + a list, running the loop chunk → vectorize → cosine similarity → top-k retrieval → assemble prompt → generate; shredding the chunks loses the qualifier and the AI faceplants on the spot, the fix is a bigger chunk_size + overlap. RAG debug's first principle is always "print to locate which stage broke"; knowledge is in the store not the parameters, adding a document needs zero training.

---

Mia ran her own notes into the RAG, Q&A flying, thrilled. But Leo put away his smile and turned serious: "The demo runs, but don't rush to launch it for real users. I'll ask you two **soul-searching questions** — how do you know it's **actually any good?** And how do you guard against someone slipping it a '**knockout drug**'?"

Mia froze: "Knockout drug? Code can be... drugged?"

Leo nodded, eyes flashing with "attack-defense drill": "Next chapter, we'll play something exciting! I'll **bury a sentence** in a user review and watch the large model 'fall for it' on the spot, led by the nose — then teach you how to guard. Come on, last two chapters (★ω★)"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** hands-on RAG = **60 lines of Python + a list,** running the loop "chunk → vectorize → cosine similarity → top-k retrieval → assemble prompt → generate"; shredding the chunks loses the qualifier and the AI faceplants, the fix is a bigger chunk_size + overlap; knowledge is in the store, not the parameters, **adding a document needs zero training.**
> **🎯 Trigger · pull this out whenever:** your RAG answered wrong, don't rush to switch models — **principle one is always "`print` to locate which stage broke":** print the top-k fragments (irrelevant = retrieval/embedding's fault) → print the full prompt (boundary instruction present?) → only then switch to a stronger model.
>
> **✍️ Self-test with the book closed:**
> 1. Change chunk_size from 300 to 50, then to 2000 — how does Q&A quality change in each case, and why?
> 2. The answer is clearly in the document yet it's wrong — give a three-step debug within 5 minutes.
> 3. Adding a new document to the knowledge base — does it need retraining? At minimum, which vectors to recompute?

> 🪜 **Next chapter preview:** Chapter 29 · Evaluation & Safety — meet each move with a counter, guard against the knockout drug slipped to AI.


---

[← Previous](../stage_6/chapter_27.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_6/chapter_29.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
