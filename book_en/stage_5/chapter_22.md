# Chapter 22 · Multimodal: Welding a Vision Encoder Into the Large Model's Eyes

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** The AI can make images now — so can it **see** images, and **also** understand speech at once? Aren't seeing and hearing two completely different "organs"?
> **🤔 Your turn:** To make a model that only reads text also see images and hear audio, would you **build it a second vision brain,** or something else?
> **🧱 The naive move hits a wall:** You'll probably think "every extra sense means a whole new system, and image, text, audio brains hold meetings with each other" — but that's **clumsy and hard to assemble.**
> Clever people found: you don't need to build a new brain at all. Read on for how to "weld" the image into the text stream. 👇


Leo pulled out a welding mask and waved it: "Of course AI didn't grow two new organs — it plays a **'physical splice'!** Use a vision encoder to **compress the image into a string of special vectors** and **'weld'** them straight into the text input stream, letting it read the picture as a special kind of **'foreign language.'** Today I'll demo this welding (★ω★)"

---

## Section 1 · Everything Is Tokenizable: don't build a new brain, just a new "printer"

"The last few chapters dismantled ChatGPT's 'reading text' system inside out," Leo opened. "Chapter 11 covered how text is cut into tokens, Chapters 9 and 10 covered how attention and the Transformer process that string of tokens. Now here's the question: to make it see photos and hear speech, do we have to **start from scratch and build a whole new 'vision brain'**?"

> **Intuitive impression:** seeing and hearing are brand-new abilities → install "eyes" and "ears" on the AI and build a new brain.
> **Real mechanism:** the Transformer **only knows tokens, not their origin** → turn image and sound **into tokens,** and the original brain works as usual.

"Why does it work?" Leo revealed the key. "Recall Chapter 11 — text is cut into tokens and swapped into vectors before entering the model. That is, **what the Transformer actually eats is never 'letters,' but a string of vectors!** And it **never checks the vectors' 'origin'** — that's the breakthrough: **as long as you invent a fitting 'chop method' for each signal, everything is tokenizable.**"

"**Don't build a new brain, just build a new 'admission-ticket printer,'**" Leo said. "Each signal gets an encoder to turn itself into tokens. The three chop methods side by side:"

| Signal | Chop method | In a sentence |
|---|---|---|
| **Text** (Ch.11 review) | tokenizer cuts word blocks | one sentence → a dozen-odd tokens |
| **Image** (the ViT idea) | cut into checkerboard squares, **patches** (commonly 16×16 pixels), each compressed into a vector | one image → **a few hundred "image tokens"** |
| **Audio** (waveform slices) | slice the continuous sound wave into tens-of-millisecond strips, each encoded into a vector | one second of speech → tens of "audio tokens," **tone and pauses hidden in the vectors** |

> Leo stressed the beauty of welding: "Unifying into tokens has an **irreplaceable benefit — attention can compute across modalities directly!** Ask 'what's the weather in the image,' and the text token 'weather''s attention can **land directly on those sky patches,** with no translation in between. Your question **guides where its eyes look.**"

---

## Section 2 · A Photo's Journey: the welding-site picture-strip

All talk and no practice is empty. Scene: you upload a cat photo in ChatGPT and ask "What breed is this cat?" Leo put on the welding mask and demoed this five-step welding:

> 🎬 **Step 1 · preprocess and chop**
> The photo is first **scaled** to the model's required size, then cut into a checkerboard — a few hundred patches. "Note: however high-res the original, **pixels beyond the required size are gone at this step.**"

> 🎬 **Step 2 · the vision encoder takes the stage**
> Each patch is compressed into a vector. This stack of vectors is the **image tokens** — **the same spec as text tokens,** and no one in the queue can tell which came from the photo.

> 🎬 **Step 3 · weld into the same sequence**
> [image tokens × a few hundred] + [the text tokens of "What breed is this cat?" × a few] **line up,** and the whole queue is sent into the Transformer. "To the model, this is just one 'long prompt' — the first few hundred tokens just happen to come from a photo."

> 🎬 **Step 4 · attention scans across modalities**
> When generating the answer, 'breed'-related attention **lands heavily on the patches for the cat's face, coat color, and markings** — exactly Chapter 9's mechanism for judging "who does 'it' refer to," just with the object swapped from word to image block.

> 🎬 **Step 5 · chain out text**
> "This is a British Shorthair..." generated token by token, Chapter 12's old rule.

> Leo dropped the chapter's heaviest line: "Note the weight of Step 2 — **what the AI sees isn't the image, it's a few hundred vectors!** It has no retina, no 'visual feel'; the photo was compressed into a string of numeric impressions at the door."

This fact explains a pile of phenomena at once:

| What you see in products | The mechanism behind it |
|---|---|
| Send a few images and the answer slows noticeably, quota drops fast | one image equals a few hundred to a thousand tokens, attention objects surge (Ch.9), billing by token (Ch.11) |
| It starts forgetting after stuffing many screenshots into a long chat | image tokens devour the context window, early content squeezed off the "desk" (Ch.17) |
| Crop and enlarge the image, ask again, and it suddenly answers right | cropping = spend the same token budget concentrated on the key area, each patch goes "high-res" |
| Same image, different questions, different focus | attention guided by the question lands on different patches — it doesn't "look first then answer," it "looks while being asked" |

---

## Section 3 · The Two-Generation Watershed: external translator vs. native bilingual

"'Turn images into tokens' sounds natural, but the industry took a detour to get here," Leo said. "Early 'AI that can see' was mostly **stitched together:**"

> **Gen 1 · pipeline style:** bolt on an image-recognition model, first **translate the image into a text description,** then feed it to the LLM.
> 　→ The LLM proper never "saw" the image. **Like hearing a friend describe a photo over the phone — details the friend didn't mention, you never know.** Big info loss, exposed on deeper questions (ask "what brand is the logo in the keyboard's corner" and if the description didn't mention it, it never knows).

> **Gen 2 · native multimodal:** at training, image and text are **learned mixed together,** the image directly becoming tokens in the same sequence.
> 　→ **The GPT-4o, Gemini route.** Seeing the image is "seeing with its own eyes"; and the output end can equally chain out image tokens, audio tokens — **understanding and generation are connected.**

"The key difference is the **training stage** (Chapter 12)," Leo said. "Native multimodal feeds image-text-mixed data to the same model during pretraining — it reads both the text and 'reads' the inset images of a web page. So the vector for 'golden retriever' and the patch vectors of a golden-retriever photo **sit close in the same vector space** (Chapter 8's old map, now with a few new continents)."

This watershed is most dramatic in **voice conversation:**

> 🎬 **Three-stage (old):** your voice → ① transcribe to text → ② the LLM reads only the transcript → ③ text-to-speech → AI voice
> 　**Fatal flaw:** the latency is the three stages summed (wait a second or two to speak, can't interrupt); **the sigh you let out, that laugh, were all thrown away at step ①** — which is why old voice assistants are always half a beat slow and always have an announcer voice.

> 🎬 **Native voice (new):** your voice → sliced into audio tokens **straight into the model** → voice tokens **straight out** → AI voice
> 　**Advantage:** no transcript station in the middle, latency drops sharply, can interrupt anytime; **it can hear you laughing and lower its voice to reply** (the GPT-4o / Gemini Live route).

> Leo nailed it in one line: "**Three-stage hears what words you said, native hears how you say those words** — latency and tone lose at the same spot."

---

## Section 4 · Traps You'll Probably Fall Into Too

**Trap 1: "AI 'sees' my photo like a human does"**

> ❌ Equating "can describe a photo" with "having vision."
> ✅ The truth is — what it "sees" is a **vector sequence compressed from a few hundred patches,** and the resolution and detail are **hard-limited by the token budget.**

Root cause: the photo is scaled, chopped, and compressed into vectors at the door, and **details beyond the budget (corner small print, distant faces) never entered its "brain" at all.** So "why didn't it see the text on the watermark" isn't an attitude problem, it's a token-budget problem — **crop and enlarge the key area and resend, often instantly effective.**

**Trap 2: "It can understand images, so counting the 17 birds in a picture is surely no problem"**

> ❌ Thinking "understand" means "count precisely."
> ✅ The truth is — **fine-grained counting and small-text OCR are multimodal's most common faceplants** — same root as Chapter 11's "can't count the r's in strawberry."

Root cause: counting requires aligning each bird one by one, precisely, without repeats or omissions, while the model gets a **holistic compressed vector impression** — a few birds crowded together may be squeezed into the same patch, just as a text model sees tokens not letters. It's good at "what's the whole, roughly what relationships," not "precisely which one." **For counting that matters and reading small text, enlarge, crop, ask in chunks, or hand it to a dedicated tool to verify.**

---

## Section 5 · The Finishing Move: what it sees isn't the image, it's a few hundred vectors

Same ritual: a manual + a kill shot.

### The multimodal core, one table to mop it all up

| Concept | In a sentence |
|---|---|
| **Core method** | everything is tokenizable: the Transformer doesn't ask origin, just turn image/audio into tokens |
| **How an image enters** | cut into patches → vision encoder compresses into vectors → weld into the text sequence |
| **Cross-modal attention** | "weather"'s attention lands directly on the sky's patches — your question guides its eyes |
| **Two-generation watershed** | pipeline (external translator) vs. native (image-text co-trained bilingual) |

### The finishing move: puncture "AI really saw it" in one sentence

From now on, see AI "describe images, hear and interpret sound," and you know the truth is one line:

> 　🗣️ **"What AI sees isn't the image, it's a few hundred vectors compressed from patches — it has no retina, the photo was chopped and compressed into a string of numeric impressions at the door."**
> - It didn't see the small watermark text? Not an attitude problem, a token budget — **crop and enlarge the key area and resend.**
> - Have it count the 17 birds? Don't — it's a natural blind spot (same root as can't-count-r), **hand it to a dedicated tool to verify.**
> - "Multimodal = first convert to text description then process"? Outdated — **native multimodal turns the image directly into tokens,** details bypass the text bottleneck.

### Squeeze the whole chapter into one sentence and stuff it in your head

> **Multimodal = don't build a new brain, just build a new "printer": use vision/audio encoders to cut images into patches and sound into slices, compress into same-spec tokens as text, and "weld" them into one sequence — the Transformer doesn't ask origin, attention computes across modalities as usual.**
> What AI sees isn't the image, it's a few hundred vectors, so it's hard-limited by token budget (can't read small text, can't count precisely).
> Native multimodal (image-text co-trained) replaced pipeline style (bolt-on recognition) — seeing the image is "seeing with its own eyes," voice is "hearing how you say it" not "what words you said."

---

Mia took off the welding mask and posed a deeper question: "It can see, hear, speak, and paint now... but these are all **reactions.** Can it **think**? If I give it a problem with several twists, can it, like a human, **work it out on scratch paper first and fix mistakes,** rather than blurting it out?"

Leo *snapped* the table, eyes blazing: "You've hit **2026's undisputed absolute star!** AI training's focus has shifted from '**diligently memorize the book**' to '**frantically self-correct on scratch paper at answer time**'! Come on, next chapter I'll show you a reasoning model in the back, **wrestling with itself, overturning itself and starting over** (★ω★)"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** multimodal = **don't build a new brain, just build a new "printer"** — use encoders to cut images into **patches** and sound into slices, compress into **same-spec tokens** as text, and "weld" them into one sequence; the Transformer doesn't ask origin, attention computes across modalities as usual. **What AI sees isn't the image, it's a few hundred vectors** (so it's hard-limited by token budget).
> **🎯 Trigger · pull this out whenever:** AI doesn't see the small watermark text, or can't count the 17 birds — you instantly know it's the **token budget / compression** at fault (crop and enlarge the key area); "multimodal = convert to text first then process" is outdated, native multimodal turns the **image directly into tokens.**
>
> **✍️ Self-test with the book closed:**
> 1. "Multimodal relies on first converting the image to a text description then feeding the model" — where is this outdated?
> 2. Use the "desk" metaphor: why does it start forgetting, and get slower and pricier, after sending 30 screenshots?
> 3. Why is "count the 17 birds in the image" its natural blind spot?

> 🪜 **Next chapter preview:** Chapter 23 · Reasoning Models — the chain-of-thought scratch paper and added "test-time compute."


---

[← Previous](../stage_5/chapter_21.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_5/chapter_23.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
