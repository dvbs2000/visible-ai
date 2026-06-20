# Chapter 26 · Your First API Call: Wake the Cloud Brain in a Few Lines of Code

> ### 🎯 Before you turn the page · The puzzle this chapter cracks
>
> **🔥 The pain:** The first 25 chapters were all "understanding," now it's the real deal — **how exactly do you send a piece of text to a cloud large model and get the answer back?** Sounds like you need to read papers and buy GPUs?
> **🤔 Your turn:** How many lines of code, at minimum, do you think "making an AI app" needs?
> **🧱 The naive move hits a wall:** You think "making an AI app = read papers, train a model" — that's the **model-builders'** business (burning Big Tech's money), **not your concern.**
> A model-user only needs to know one thing: mail a letter, receive a reply. Read on for 30 lines of code. 👇


Through Stage 5, Leo took Mia through today's AI cutting edge in full. Stage 6 — **the book's final showdown, Hands-On!** The first 25 chapters were all "understanding"; from this chapter on, **the real deal of writing code.**

Mia rubbed her hands, a bit nervous: "I... I've never even written code, can I really build an AI app?"

Leo tapped a few keys in the terminal and a string starting with `sk-` popped up; his eyes lit up: "Look! Your first **API Key** in life is generated! Don't be scared — **'using a model' and 'building a model' are two different things.** Building a model is Big Tech burning power; using a model, you only need to know one thing: **mail a piece of text to the cloud and get the generated text back.** The whole thing, 30 lines of Python. Today I'll teach you to **write the first letter to that cloud brain** (★ω★)"

---

## Section 1 · So-Called "Making an AI App" Is Essentially Mailing a Letter

"The first 25 chapters you watched others build models — trillion-token pretraining, RLHF alignment, electricity enough for a city," Leo said. "**The good news: that's all the 'model-builders'' business.**"

> **Imagined:** making an AI app = read papers, buy GPUs, train a model. (That's Chapters 12–13's "building a model" plot.)
> **Actually:** making an AI app = **mail a letter, get a reply back.** (The model is long trained and deployed, rented by token, you just "order off the menu.")

"Strip away all the SDK sugar," Leo revealed the truth. "One API call is essentially an **HTTPS POST request:** mail a piece of JSON to the vendor's server, and seconds later receive a piece of JSON reply." He broke this "letter" into the **letter-writing trio:**

| Part in code | Mailing metaphor | Does what |
|---|---|---|
| **client + base_url** | the recipient address | which cloud brain this letter goes to (change this line to connect another) |
| **api_key** | your mailing card | **both ID card and bank card:** the server recognizes you by it and charges you by it |
| **model** | which dish to order | which model (ability and price differ tens of times — Chapter 25's map pays off) |
| **messages** | the letter's content | what you want to say, a list (a three-person script, next section) |

> Leo laid down **the first discipline** in advance: "That `sk-`-prefixed Key **must never be hard-coded into your code!** It's your bank card — code can be shown to anyone, the Key can't. First hide it in an 'environment variable':"

```bash
# Run in the terminal (macOS / Linux):
pip install openai                    # install the official SDK, one line
export API_KEY="sk-…your-secret-key…" # store the key in an env var (current terminal only)
# Windows PowerShell: $env:API_KEY = "sk-…"
```

---

## Section 2 · The 30 Lines, Dissected Piece by Piece: how the letter is written and mailed

"First, serve the full letter — a chatbot that keeps a conversation going in the terminal," Leo said. "Not understanding it is fine, we'll dissect it piece by piece:"

```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["API_KEY"],     # <- recipient + mailing card: fetch the key from the env at runtime
    # base_url="https://…/v1",         # <- want another model? usually change this line + key
)

messages = [                           # <- letter content: starts from the "character bio" (system)
    {"role": "system", "content": "You are a patient AI teaching assistant. Keep answers concise."}
]

while True:                            # <- multi-turn dialogue = a loop + a list
    user_input = input("\nYou: ")
    if user_input.strip() == "quit":
        break
    messages.append({"role": "user", "content": user_input})   # what you said, stored into the letter

    stream = client.chat.completions.create(
        model="gpt-4o-mini",           # <- which dish: just write the model name
        messages=messages,             # <- note: every turn mails the "entire" history
        stream=True,                   # <- write-and-send (streaming)
    )
    reply = ""
    print("AI: ", end="")
    for chunk in stream:               # <- receive the reply chunk by chunk
        piece = chunk.choices[0].delta.content or ""
        print(piece, end="", flush=True)
        reply += piece
    messages.append({"role": "assistant", "content": reply})   # what it said, also stored into the letter
```

"The core of the whole letter is that **messages list** — it's a **three-person script,**" Leo explained the roles:

> 🎭 **system = the director:** the model's "character bio" (identity, tone, rules). Chapter 16's prompt engineering, the main battlefield is this one line.
> 🎭 **user = the user:** what you (or your user) say each turn.
> 🎭 **assistant = the model:** the model's previous replies. **Note: in multi-turn dialogue, it's 'your code' that stuffs them back into the list.**

---

## Section 3 · The Truth of Multi-Turn Dialogue: you store the list, resend it all every turn

"Chapter 17's foreshadowing cashes in here," Leo turned serious. "**The API server is completely stateless** — every request is isolated, forgotten when done; it doesn't even have the concept of 'the previous turn'!"

"Then how does it 'remember' what we chatted?" Mia asked.

"**It doesn't remember at all!**" Leo pointed at the code. "To 'keep chatting,' the only way is **you maintain the messages list yourself and resend the entire history unchanged every turn.** Two direct corollaries: **one, pricier the longer you chat** (the longer the history, the more tokens mailed); **two, delete the history from the list and it forgets on the spot.**"

He demoed an "amnesia experiment" picture-strip:

> 🎬 **Turn 1:** Mia says "My name is Mia." The code `append`s this into the list.
> 🎬 **Turn 2:** Mia asks "What's my name?" — the AI answers "Your name is Mia." "It 'remembers' not from good memory, but because **the message with the name was just resent unchanged by your code.**"
> 🎬 **Turn 3 (scissors ✂️):** Leo adds a line before sending, `messages = [messages[0], messages[-1]]` (keep only system + the latest line), and asks "What's my name?" — the AI answers "**Your name never appeared in this conversation...**"
> 　"**Amnesia confirmed!** The message with the name was deleted by you, and the model side **never stored anything at all.** So-called 'memory' is that list in your code — Chapter 17 verified."

---

## Section 4 · Streaming, Fractions of a Cent, and Three Lifesaving Rules

**Streaming output:** Chapter 10 covered it — the model spits out **token by token** by nature. `stream=False` = wait for it all then send you a package; `stream=True` = send a bit the moment it's spit out. **Total time is nearly the same, but 'when the first word appears' differs vastly** — ChatGPT's typewriter effect isn't showing off, it's an experience necessity.

**Price intuition:** the bill looks at only two numbers — **how many tokens mailed in + how many it newly generates.** The chapter's only formula:

> **Cost = input tokens × input price + output tokens × output price**

"Compute one with order-of-magnitude figures," Leo said. "Say input $0.15/M, output $0.60/M; one ordinary Q&A (500 input + 300 output tokens) ≈ **$0.00026, a fraction of a cent!** A single chat is usually well under a cent." He turned: "**But what really burns money is Chapter 20's Agent loop** — one task runs dozens of rounds on its own, each round stuffing in big stretches of tool results, and consumption jumps one or two orders of magnitude. **Before writing a loop that calls the API, set a usage cap in the console first!**"

> 🏆 **【Golden Playbook · Three Lifesaving API-Key Rules】**
> Every year newbies hard-code a Key into code and push it to GitHub, **picked up within minutes by key-scanning crawlers, waking to an astronomical bill.** Don't be the next:
> - **Rule 1 · Key out of the code repo:** environment variables or a secrets manager are its home. Once pushed to a public repo, consider it leaked — the crawlers see it before your coworker.
> - **Rule 2 · Key out of the frontend page:** web/mini-app code is visible to all. The right posture: the frontend talks only to your own backend, which holds the Key and sends the request on its behalf.
> - **Rule 3 · Leaked, revoke and reissue immediately:** revoke the old key and generate a new one in the console with one click, then check the bill — the process is on par with "bank-card password leaked."

---

## Section 5 · Traps You'll Probably Fall Into Too

> 🏆 **【Golden Playbook · Pitfall Guide】**
>
> **Trap 1: "Calling the API means my data goes to train the model"**
> ❌ Conflating "free chat products" with "the API."
> ✅ Truth: **mainstream vendors' API data is not used for training by default** — but each policy differs, be sure to read the data terms.
> 　Root cause: some consumer-facing free products do use conversations to improve the model (usually toggleable); the API goes by commercial terms, and mainstream vendors don't train on request data by default. For company secrets, don't go by impression — **open the data policy and check item by item.**
>
> **Trap 2: "The code runs on my computer, so the model runs on my computer"**
> ❌ The reply printing in your own terminal creates the illusion "the computation is local."
> ✅ Truth: **inference happens on a cloud GPU cluster,** your 30 lines of code just mailed a letter.
> 　Root cause: your code is "ordering," the cooking is done by rows of GPUs in the vendor's data center — **which is also why it doesn't work offline, and every call costs money.** Want the model to truly run on your own computer? That's exactly the next chapter's topic.

---

## Section 6 · The Finishing Move

> 🏆 **【Golden Playbook · The Finishing Move: puncture "making an AI app is hard" in one sentence】**
>
> From now on, whoever says "making an AI app needs reading papers, buying GPUs, training a model," gently correct:
> 　🗣️ **"Using a model ≠ building a model. Making an AI app is essentially mailing a letter (HTTPS POST): mailing card (key) + recipient (base_url) + order (model) + letter content (messages), 30 lines of Python and done."**
> - "The AI remembers what we chatted?" → No, the server is stateless, **memory is just that list in your code,** resent in full every turn (so pricier the longer, forgets when deleted).
> - Key leaked = bank card leaked: **out of the repo, out of the frontend, revoke immediately on leak.**
> - Before writing an Agent loop, **set a usage cap first** — a single chat is a fraction of a cent, dozens of loop rounds × user count is another matter.

### Chapter summary table

| Part | Mailing metaphor | In a sentence |
|---|---|---|
| **client/base_url** | recipient address | which cloud brain it's mailed to |
| **api_key** | mailing card | ID card + bank card, never into the code |
| **model** | which dish to order | the model decides ability and price |
| **messages** | letter content | system/user/assistant three-person script, maintained yourself |
| **stream** | write-and-send | total time unchanged, but first word fast |

> **Squeeze the whole chapter into one sentence:** calling the API = mailing a letter to the cloud brain — identity by key, recipient by base_url, dish by model, content by the messages list; the server is stateless, "multi-turn memory" is just that list in your code resent in full, so pricier the longer, forgets when deleted. The Key is a bank card, be sure to hide it in an environment variable.

---

Mia successfully ran her first program, clapping with excitement, but soon frowned: "But... this chatbot has two **umbilical cords** — one **network cable** (dead offline), one **bill** (every sentence charged). If I want to handle private documents, or just want to freeload to my heart's content... can I **cut** these two cords?"

Leo slapped his thigh: "You've hit the next chapter! Of course you can cut them — move the whole model **into your own computer!** No API key, no network, no money. Come on, next chapter I'll have you type one line of `ollama run` and install an **offline little assistant** on your own machine (★ω★)"


---

## 🧰 Pack it into your toolbox

> **🔑 Method in one sentence:** calling the API = **mailing a letter** to the cloud brain (HTTPS POST) — identity by key, recipient by base_url, dish by model, content by the **messages list;** the server is **stateless,** "multi-turn memory" is just that list in your code **resent in full** (so pricier the longer, forgets when deleted).
> **🎯 Trigger · pull this out whenever:** writing any AI app, lock in three lifesaving rules — **key out of the code repo, out of the frontend, revoke immediately on leak** (it's your bank card); before writing an Agent loop, **set a usage cap in the console first.**
>
> **✍️ Self-test with the book closed:**
> 1. Change system to "sharp-tongued film critic" — what changes, what doesn't?
> 2. "The AI remembers what we chatted" — one line of code punctures this illusion; how?
> 3. The code runs on my computer, so does the model run on my computer?

> 🪜 **Next chapter preview:** Chapter 27 · Running Ollama Locally — install an offline little assistant on your own machine.


---

[← Previous](../stage_5/chapter_25.md) ｜ [📖 Contents](../README.md) ｜ [Next →](../stage_6/chapter_27.md)


> Reading *The Visible AI* · 30 free chapters —— back to the [**project home**](../../README.en.md). If it helped, a ⭐ Star helps others find it.
