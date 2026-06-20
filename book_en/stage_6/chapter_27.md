# Chapter 27 · Running Ollama Locally: Install an Offline Little Assistant on Your Own Machine

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** Last chapter's chatbot has two "umbilical cords" — the **network cable** (dead offline) and the **bill** (every sentence charged). To handle private documents, or just freeload, can you cut these two cords?
> **🤔 Your turn:** A behemoth of tens of billions of parameters — how do you cram it into your memory-limited computer?
> **🧱 The naive move hits a wall:** You might think "you need a top-end NVIDIA GPU" — actually **inference just needs to fit in memory to compute,** and conflating "training" with "inference" is a common misconception.
> One line of `ollama run` brings the model home. Read on. 👇


Leo pushed the laptop toward her: "Let me cut them for you! Move the whole model **into your own computer** — inference happens entirely in your memory, runs offline, not one byte uploaded, electricity the only cost. Today I'll have you type one line of `ollama run`, **chatting in three steps** (★ω★)"

---

## Section 1 · Three Reasons to "Bring the Large Model Home"

"Are last chapter's umbilical cords worth this fuss to cut?" Leo asked and answered. "Three hard reasons:"

> 🔒 **Reason 1 · privacy:** medical records, contracts, diaries, company code — **inference happens entirely in your memory, runs offline, not one byte uploaded.** This is a hard guarantee no cloud terms can give.
> 💰 **Reason 2 · cost:** Chapter 26 computed it — resending everything makes heavy use pricier the longer, and an Agent run sends the bill soaring. **A local model is downloaded once and used freely, electricity the only cost.**
> 🎮 **Reason 3 · fun:** swap models, change personas, take it apart and experiment — no rate limits, no review, no "service adjustment notice." Chapter 25's open-source map becomes your toy from here.

"This door was pushed open by **open-weights models** (Chapter 25)," Leo said. "Llama, Qwen, DeepSeek released their trained parameters publicly, anyone can download. So only one question remains — **a behemoth of tens of billions of parameters, how do you cram it into your memory-limited computer?** That's this chapter's main line."

---

## Section 2 · Three Commands, the Model Comes Home

"Taste the sweetness before the principle," Leo said. "**Ollama** is currently the most carefree local-model butler: download, run, manage all in one." He typed in the terminal:

```bash
# ① Install: open ollama.com, download the installer, click Next (Mac/Windows/Linux)

# ② Pull up a model — one command, download and run all included
ollama run qwen3:8b

# ③ There is no step three, you can already chat:
>>> Give me a one-line tip for sleeping better.
Sure! Keep a consistent bedtime, even on weekends.
```

> 🎬 **Local memory-devouring picture-strip:** the moment Mia hits Enter—
> 　🎬 **Download:** the progress bar races, a few-GB model file lands on disk (downloaded only on first run, instant after).
> 　🎬 **Load:** the small model is **swallowed whole into memory** in a gulp — Task Manager's memory use jumps several GB instantly.
> 　🎬 **Spit words:** the `>>>` prompt lights up, Mia types a sentence, the local assistant **replies away.** Mia unplugs the network cable and asks again — **it still answers!** "It really runs in my computer!"

"Next is the chapter's most satisfying moment," Leo's eyes lit up. "**Once Ollama is installed, it runs a service permanently on your computer's port 11434, speaking the common tongue of 'OpenAI-compatible API'**! That is, Chapter 26's 30-line chat.py **switches from cloud to local by changing two lines:**"

```python
client = OpenAI(
    base_url="http://localhost:11434/v1",  # <- was pointing to the cloud, now to your own computer!
    api_key="ollama",                       # <- local doesn't bill or verify, any non-empty string works
)
# Then change model to "qwen3:8b"; streaming, the messages list... all work as usual
```

> Leo summarized: "That's the entire meaning of 'OpenAI-compatible' — **change not one line of app code, swap models freely!** Dev and debug with free local trial-and-error, switch back to a cloud flagship for launch; next chapter's RAG flips this switch once more."

---

## Section 3 · Jargon Decoded: what 7B, Q4, GGUF actually mean

"Open the Ollama model library and a row of head-spinning names: `qwen3:8b`, `llama3.3:70b-q4_K_M`... don't panic," Leo said. "There are only four pieces of jargon total:"

| Jargon | One plain sentence | One more line |
|---|---|---|
| **7B / 70B** | parameter count: 7 billion / 70 billion adjustable "knobs" | B = Billion. Chapter 15's scaling law: more parameters usually smarter, **also more memory-hungry.** Check this number first when picking |
| **Q4 / Q8** | quantization: each parameter compressed from 16 bits to 4/8 bits | **like compressing lossless music to mp3:** Q4 is 1/4 the size, precision loses just a little |
| **GGUF** | the universal model-file format for local inference | that few-GB file you download is it — the model world's "universal container" |
| **context 32K** | how long a conversation it can remember — this also eats memory | the longer you chat, the bigger the KV cache (Ch.17); shrinking the context is a hidden memory-saving switch when tight |

"Of the four, **quantization** is most worth seeing with your own eyes," Leo pulled out a "quantization microscope":

> 🔬 **Quantization microscope picture-strip:**
> 　🎧 **FP16 original:** 16 bits (2 bytes) per parameter, 7B body ≈ 14 GB — **studio-lossless WAV,** nothing lost, biggest size, most laptops can't hold it.
> 　🎧 **Compressed to Q8:** 8 bits (1 byte) per parameter, 7B ≈ 7 GB — **high-bitrate mp3,** half the size, the ear basically can't tell.
> 　🎧 **Compressed to Q4:** 4 bits (0.5 byte) per parameter, 7B ≈ 3.5 GB — **ordinary mp3,** only 1/4 the size, still great for everyday chat. **That's why Q4 became local players' default.**

> Leo explained the nerve to cut: "The model's 'knowledge' is **spread across the overall distribution of billions of parameters;** a single parameter being a bit coarse barely affects the big picture — the same cleverness as mp3 cutting details the ear isn't sensitive to."

---

## Section 4 · Mentally Compute "How Big a Model My Computer Can Run"

"The first hurdle to running a model locally isn't how strong the GPU is, but **whether the model fits entirely in memory,**" Leo said. "Good news: you can mentally compute it from the name. The chapter's only formula—"

> **Memory needed (GB) ≈ parameter count (B) × bytes per parameter × 1.2**

"Three numbers: **parameter count** is the 7, 14, 70 in the name; **bytes per parameter** is set by the quantization tier (FP16=2, Q8=1, Q4=0.5); **×1.2** leaves 20% headroom for runtime overhead. Example: 7B's Q4 version ≈ 7×0.5×1.2 ≈ **4.2 GB** — an 8GB-memory computer can run it!"

Then Leo revealed a **counterintuitive** fact, neatly tying off an earlier hook:

> 💡 **For running local models, the Mac is actually the sweet-spot machine!**
> A desktop's discrete GPU has **VRAM and RAM as two separate blocks** (a card labeled 12GB VRAM, that's the cap); while Mac's **Unified Memory has VRAM and RAM as one,** buy 64GB and you can give a big chunk to the model.
> **More crucial is bandwidth:** Apple M-series chips' unified memory has extremely high bandwidth, the CPU/GPU directly share the same fast memory without shuttling data back and forth — so **an 8B model runs blazingly fast on a Mac,** spitting words as smoothly as an online service. This is the underlying reason "Mac players can light up that 64GB tier."

---

## Section 5 · Honest Expectations: can run ≠ runs well

"Chapter 25 said open source is catching up, but recognize this," Leo turned serious. "The **7B quantized version** running on your computer is neither open-source's strongest (you can't hold the strongest tier anyway), much less the cloud frontier — there's an obvious generation gap in between. **It's like a home printer: prints homework and contracts crisply, but don't expect a publication-grade art album.**"

> 🏠 **Hand to local:** high-volume, simple, **sensitive** jobs — bulk tagging, info extraction, rewrite-polish, summarizing private documents (unlimited, free, data never leaves), plus the most important one: **freely tinker and learn.**
> ☁️ **Leave to the cloud:** complex, brain-burning, **quality-demanding** jobs — multi-step reasoning, long-range Agents, high-quality long text. A local 7B brute-forcing these will only refresh your understanding of "hallucination."

"Luckily you've mastered the best-of-both posture," Leo said. "The same code **switches between local and cloud with one base_url change.** Sensitive data goes local, hard bones to the cloud — **that's the engineer's answer, not picking a side.**"

---

## Section 6 · Traps You'll Probably Fall Into Too

> 🏆 **【Golden Playbook · Pitfall Guide】**
>
> **Trap 1: "Install Ollama and you've freeloaded a ChatGPT"**
> ❌ Both called "large models," similar names mask the scale gap.
> ✅ Truth: **a local small model and a cloud frontier model have an obvious generation gap** — it's a "good-enough assistant," not a free Claude/GPT.
> 　Root cause: you're running a 7B quantized version, and the cloud flagship's parameter count is **one to two orders of magnitude** larger, with a whole data center behind it. Translation and summary feel no gap, but complex reasoning exposes it. Set expectations right and it's full of surprises instead.
>
> **Trap 2: "No NVIDIA GPU, so local large models aren't for me"**
> ❌ Conflating "training" with "inference."
> ✅ Truth: **Mac unified memory is the agreed sweet spot, and pure CPU can run it too** — the only difference is words-per-second speed.
> 　Root cause: training really can't do without data-center GPUs (Chapter 12), but **inference just needs the model to fit in memory to compute.** The llama.cpp ecosystem optimized CPU inference to a usable degree; Mac unified memory makes the old "not enough VRAM" headache vanish entirely.

---

## Section 7 · The Finishing Move

> 🏆 **【Golden Playbook · The Finishing Move: tell from the name whether it'll run】**
>
> From now on, see any local model name and you can conclude without downloading:
> 　🗣️ **"Memory needed ≈ parameter count (the number in the name) × bytes per parameter (FP16=2/Q8=1/Q4=0.5) × 1.2. If the result is smaller than your memory, it'll run."**
> - 16GB want to run 32B-Q4? 32×0.5×1.2 ≈ 19.2GB, **over** — fall back to 14B-Q4 (≈8.4GB).
> - Q4 or Q8? Default **Q4:** the memory saved buys longer context, even a size-up model (**parameter count over precision** is common local-scene wisdom).
> - No discrete GPU? Don't despair: **Mac unified memory's high bandwidth is a sweet-spot machine,** pure CPU runs it too, just slower.
> - The hardest-core way to verify "data never leaves": **unplug the network cable and chat one more line** — still answers, proving inference really happens in your computer.

### Chapter summary table

| Jargon | Plain | Selection tip |
|---|---|---|
| **7B/70B** | parameter count | check first, bigger = smarter and more memory-hungry |
| **Q4/Q8** | quantization (mp3 squeeze) | default Q4, save 3/4 the size, precision loses a little |
| **GGUF** | model-file format | that few-GB file you download |
| **Unified Memory** | Mac sweet spot | VRAM+RAM as one + high bandwidth, 8B runs blazingly fast |

> **Squeeze the whole chapter into one sentence:** running a model locally = one line of `ollama run` brings an open-weights model into your own computer (cutting the network and bill umbilical cords); mentally compute memory from the name (params × bytes × 1.2), quantization is like mp3 compression (Q4 default), Mac unified memory's high bandwidth is a sweet-spot machine. Can run ≠ runs well (7B is a good-enough assistant, not a free GPT), sensitive/high-volume jobs go local, hard bones to the cloud — switch with one base_url change.

---

Mia played with unplugging the network cable, having a blast, then suddenly remembered something serious: "Wait! Chapter 18 you demoed RAG's principle diagram — chunk, vectorize, retrieve... Now I can call the API and run a model locally, **can I actually write code and build, by hand, a knowledge base that queries my own documents**?"

Leo rolled up his sleeves with a "finally, this moment" look: "Right into the next chapter! This is **the book's most hardcore hands-on coding chapter** — we'll hand-write a few dozen lines of clean Python and run the full loop of chunk, compute distance, fill the prompt. I'll even **deliberately shred the chunks and make the AI faceplant on the spot,** then fix it with you! Come on (★ω★)"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** running a model locally = one line of `ollama run` brings an **open-weights model** into your own computer (cutting the network and bill cords); mentally compute memory from the name — **parameter count × bytes per parameter × 1.2;** quantization is like compressing lossless music to mp3 (Q4 default), **Mac unified memory's high bandwidth is a sweet-spot machine.**
> **🎯 Trigger · pull this out whenever:** see any local model name and judge without downloading whether it'll run (16GB run 32B-Q4? 19.2GB, over → fall back to 14B); **sensitive/high-volume jobs go local, hard bones to the cloud** — switch with one base_url change. The hardest way to verify "data never leaves": **unplug the cable and chat one more line.**
>
> **✍️ Self-test with the book closed:**
> 1. 16GB memory wants to run a 32B Q4 version — mentally compute, feasible? If not, fall back to which tier?
> 2. The same model, both Q4 and Q8 fit — which to pick?
> 3. No NVIDIA GPU, are local large models really not for me?

> 🪜 **Next chapter preview:** Chapter 28 · Hands-On RAG — hand-write an external knowledge-base pipeline.


---

[← Previous](../stage_6/chapter_26.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_6/chapter_28.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
