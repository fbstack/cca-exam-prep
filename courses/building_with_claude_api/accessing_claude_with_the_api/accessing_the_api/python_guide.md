# Python Guide: Being Clear and Direct with the Claude API

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (one paragraph for complete beginners)

Python is a programming language — a way of giving precise, step-by-step instructions to a computer. Think of it like writing a very detailed recipe. The computer follows each instruction exactly, in order, with no common sense of its own. If you forget a step or misspell a word, it stops and tells you something went wrong. The notebooks you're working in (called JupyterLab notebooks) let you write those instructions in small chunks called **cells**, run each chunk one at a time, and see the results immediately below each chunk. This makes it a great environment for learning, because you can experiment one small piece at a time.

---

## Cell 1: Loading Your Secret API Key from a Safe Place

```python
from dotenv import load_dotenv   # imports the dotenv helper that reads .env files
import os                        # provides access to environment variables at runtime

load_dotenv()                    # reads .env in the current working directory and
                                 # injects each KEY=VALUE pair into os.environ

api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the key we stored in .env;
                                           # returns None (not an error) if missing

print("API key loaded:", api_key is not None)  # True → key found; False → check .env
```

### What this code does

---

**Line 1: `from dotenv import load_dotenv`**

This line brings a specific tool into your workspace. Let's unpack each word:

- **`dotenv`** is a pre-built **package** — think of a package as a toolbox someone else already built and made available for you to use. The `dotenv` package specialises in one job: reading a special file called `.env` (pronounced "dot env") that stores secret values, like passwords or API keys, separately from your code.

- **`import`** means "bring this tool into my current workspace so I can use it." Imagine your workshop has a huge storage room full of specialist tools. `import` is the act of walking to that storage room and carrying a specific tool back to your workbench.

- **`from ... import ...`** is a more targeted version of import. Instead of bringing the entire `dotenv` toolbox into your workspace, you're saying: "From that toolbox, bring me only the one tool called `load_dotenv`." This keeps your workspace tidy.

- **`load_dotenv`** is a **function** — a named set of instructions you can trigger by calling its name. Think of a function like a named recipe card. The recipe exists in the box, but nothing happens until you actually call it (we do that two lines later).

Why do we need this? Your API key is a secret password that grants access to Anthropic's AI service. If you typed it directly into your code, anyone who saw your code (for example, if you shared it on the internet) could steal and use it. Storing it in a separate `.env` file — and teaching your program to read it from there — keeps the secret out of your code entirely.

---

**Line 2: `import os`**

- **`os`** is a module that comes built into Python — you don't need to install it separately. Think of it as Python's way of talking to the operating system (Windows, Mac, or Linux) that your computer runs on.

- One of the things the operating system manages is a list of **environment variables** — a kind of backstage notepad where the system (or you) can store values that programs can read. When `load_dotenv()` runs in a moment, it writes your API key onto this backstage notepad. The `os` module gives you the ability to read from that notepad.

- **`import os`** means "bring the entire `os` toolbox into my workspace." We don't need just one tool from it — we'll use its notepad-reading ability in a moment.

---

**Line 3: `load_dotenv()`**

This is where the `load_dotenv` function we imported actually runs. The two parentheses `()` at the end are crucial — they're what *trigger* the function. Writing `load_dotenv` without parentheses would just refer to the recipe card but not cook anything. Writing `load_dotenv()` says: "Execute this recipe now."

What this function does behind the scenes:
1. It looks in your current working folder (the folder your notebook is saved in) for a file named `.env`
2. That file contains lines that look like: `ANTHROPIC_API_KEY=sk-ant-abc123...`
3. `load_dotenv()` reads each line and copies each value onto the operating system's backstage notepad (called `os.environ`), making it available to your program

Nothing is printed or displayed when this runs — it works silently in the background, like a set-up step before the real action.

---

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

Let's break this into two parts:

**The right side first: `os.getenv("ANTHROPIC_API_KEY")`**
- `os.getenv` is a function from the `os` toolbox. `getenv` stands for "get environment variable."
- The text inside the quotes — `"ANTHROPIC_API_KEY"` — is the **name** of the value you want to look up on the backstage notepad. It's case-sensitive, so it must match exactly what's in your `.env` file.
- If the value is found, `os.getenv` returns it (hands it back to you). If it's not found, it returns a special Python value called `None`, which means "nothing" or "no value." Importantly, returning `None` is not an error — it's a polite way of saying "I looked, but didn't find anything."

**The left side: `api_key =`**
- `api_key` is a **variable** — think of it as a labeled box. You're creating a box, writing the label "api_key" on it, and placing whatever `os.getenv` returns inside it.
- The `=` sign here doesn't mean "equals" the way it does in maths. It means "take the value on the right and store it in the box on the left." Programmers call this **assignment**.

So this line creates a box called `api_key`, runs to the backstage notepad to find the value named `ANTHROPIC_API_KEY`, and stores whatever it finds in the box. Everything from here on can use `api_key` to refer to your secret key without the key ever appearing directly in your code.

---

**Line 5: `print("API key loaded:", api_key is not None)`**

- **`print()`** is a built-in Python function that displays output in the area below the cell. Think of it as Python's way of talking back to you.

- Inside the parentheses, there are two things separated by a comma:
  - `"API key loaded:"` — a piece of text (called a **string** in Python, because it's a string of characters) that will appear exactly as written
  - `api_key is not None` — this is a **comparison** that asks a yes/no question

- **`api_key is not None`** — Python evaluates this like a detective checking a fact: "Is the value in the `api_key` box something other than nothing?" If your key was found and loaded, the answer is `True`. If the key was missing (and the box contains `None`), the answer is `False`.

- `True` and `False` are special Python values called **booleans** — they represent the two possible outcomes of any yes/no question.

So the full `print` line displays something like:
`API key loaded: True`

### What the output means

If you see `API key loaded: True` — your `.env` file was found, your API key was read successfully, and you're ready to make API calls. If you see `API key loaded: False` — something went wrong: your `.env` file might be missing, misnamed, or the key inside might be spelled differently than `ANTHROPIC_API_KEY`. You'd need to fix this before continuing.

---

## Cell 2: Creating a Connection to the Anthropic API

```python
from anthropic import Anthropic  # imports the official Python SDK client class

client = Anthropic(api_key=api_key)  # creates a reusable client instance;
                                     # all future API calls go through this object

print("Anthropic client ready")       # quick sanity-check — no network call yet
```

### What this code does

---

**Line 1: `from anthropic import Anthropic`**

- **`anthropic`** (lowercase) is the name of the official Python package published by Anthropic — the company that makes Claude. This package gives you pre-built tools for communicating with Claude's API.

- **`Anthropic`** (uppercase, after the `import`) is a specific tool inside that package — a **class**. Think of a class as a blueprint or a mould. Just like a mould for making cups defines the shape of every cup, the `Anthropic` class defines what an API connection looks like and what it can do.

- The distinction between `anthropic` (the package/toolbox) and `Anthropic` (the specific blueprint inside it) matters: you're importing the blueprint so you can use it to create an actual connection in the next line.

- **API** stands for **Application Programming Interface**. Imagine a restaurant: the kitchen is Anthropic's AI model doing the actual thinking, you are the customer with a request, and the API is the waiter who carries your request to the kitchen and brings back Claude's response. You never go into the kitchen directly — you always communicate through the waiter.

---

**Line 2: `client = Anthropic(api_key=api_key)`**

This is one of the most important lines in the notebook. Let's read it carefully.

**The right side: `Anthropic(api_key=api_key)`**
- This **calls** the `Anthropic` blueprint (class) with the parentheses `()`, which tells Python: "Use this blueprint to create a real, working connection object."
- Inside the parentheses: `api_key=api_key` — this passes your secret key to the connection. The first `api_key` (before the `=`) is the name of the slot that `Anthropic` expects you to fill. The second `api_key` (after the `=`) is the variable (your labeled box from Cell 1) containing your actual key value.
- This is how the `Anthropic` object knows it's authorised to make requests on your behalf — it carries your credential with it.

**The left side: `client =`**
- `client` is a new variable — another labeled box. This box stores the fully configured, ready-to-use API connection.
- The name `client` is conventional — it signals "this is the object I use to talk to the external service." You could name it anything, but `client` is the standard choice that other developers will recognise.

**Why is this called an "instance"?**
When you use a class blueprint to create something real, the result is called an **instance** of that class. The `Anthropic` class is the blueprint; `client` is the one specific connection object you built from that blueprint. You could, in theory, create multiple clients (say, one per API key), but in practice you almost always need just one.

---

**Line 3: `print("Anthropic client ready")`**

- This prints the text `Anthropic client ready` to confirm that the previous line ran without errors.
- Notice there's no network activity yet — you haven't asked Claude anything. You've merely set up the connection object. Think of it like picking up a telephone and confirming you have a dial tone, but not yet dialling a number.
- This kind of "did it work?" check is called a **sanity check** — a quick confirmation that things are in order before you proceed.

### What the output means

`Anthropic client ready` tells you the connection object was created successfully. If you saw an error instead (like `ModuleNotFoundError`), it would mean the `anthropic` package isn't installed in your Python environment, and you'd need to run `pip install anthropic` in your terminal first.

---

## Cell 3: Smoke Test — Sending Your First Message to Claude

```python
response = client.messages.create(      # calls the Messages endpoint
    model="claude-haiku-4-5",           # cheapest/fastest model — good for smoke tests
    # model="claude-sonnet-4-5",        # uncomment if you want a more capable model
    max_tokens=50,                      # hard ceiling on output length (in tokens)
    messages=[                          # the conversation history — one turn here
        {"role": "user", "content": "Say hello in one short sentence."}
    ]
)

print(response.content[0].text)        # content is a list of blocks; [0] is the first;
                                       # .text extracts the plain-string payload
```

### What this code does

---

**Line 1: `response = client.messages.create(`**

This line starts an actual API call — the first time your notebook reaches out over the internet to Claude. Let's unpack it:

- **`client`** is the connection object you created in Cell 2 — your direct line to Anthropic.

- **`.messages`** — the dot here means "go inside the `client` object and find the part called `messages`." Think of `client` as a Swiss Army knife; `.messages` opens the specific blade designed for sending and receiving chat messages.

- **`.create(`** — this calls the `create` function on the `messages` part. In Anthropic's system, "create" means "create a new message exchange" — in other words, send your message and ask for a response. The opening parenthesis `(` tells Python that the function's **arguments** (its settings and inputs) are coming on the lines that follow.

- **`response =`** — whatever `client.messages.create(...)` returns (Claude's reply, packaged up with lots of metadata) gets stored in a new variable called `response`. Think of `response` as a box that will hold the entire package that comes back from the API — like the envelope that arrives after you post a letter.

---

**Line 2: `model="claude-haiku-4-5",`**

- **`model`** is a **parameter** — a named slot you fill in to configure the API call. Think of parameters as fields on a form. The `model` field tells Claude's API which version of the AI to use.

- **`"claude-haiku-4-5"`** is the name of a specific Claude model. Anthropic offers several models at different price/performance trade-offs, like different tiers of service. Claude Haiku is described here as the cheapest and fastest — good for quick tests where you don't need maximum quality.

- The **comma** at the end tells Python: "more settings are coming on the next lines — don't evaluate the function yet."

- A **smoke test** (mentioned in the comment) is an engineering term for the very first quick test of a system — like turning something on for the first time just to see if it catches fire (metaphorically). It's deliberately minimal: a simple input, tiny output, just to confirm the plumbing works.

---

**Line 3: `# model="claude-sonnet-4-5",`**

- The `#` symbol makes this line a **comment**. Python ignores everything on a line that starts with `#`. Comments are notes for human readers only — the computer skips them entirely.

- This line is also **commented out** — meaning it's code that has been temporarily disabled by putting a `#` in front of it. If you wanted to use the Sonnet model instead of Haiku, you'd remove the `#` from this line (called **uncommenting**) and add a `#` to the Haiku line above.

---

**Line 4: `max_tokens=50,`**

- **`max_tokens`** is another parameter — this one controls the maximum length of Claude's reply.

- **Token** is an important concept for understanding AI language models. A token is a chunk of text — roughly one word, or sometimes part of a word, or sometimes a punctuation mark. For example, "hello" is one token, "unbelievable" might be two tokens, and "!" is one token. The AI doesn't read letter by letter or word by word — it works in these token chunks.

- **`50`** means "stop generating text once you've produced 50 tokens." This is a hard ceiling — Claude cannot go over it. For a single greeting sentence, 50 tokens is more than enough (a typical sentence is 10–20 tokens).

- Why limit tokens? Every token costs money (fractions of a cent, but they add up), and longer responses take more time. For a smoke test, you want a tiny, fast, cheap response just to confirm the connection works.

---

**Lines 5–7: The `messages` parameter**

```python
messages=[                          # the conversation history — one turn here
    {"role": "user", "content": "Say hello in one short sentence."}
]
```

- **`messages=`** is the parameter that carries the conversation history to Claude. It's always a **list** — an ordered collection of items, like a numbered list on paper.

- A **list** in Python is written with square brackets `[...]`. Each item inside is separated by a comma.

- Inside this list, there is one item: `{"role": "user", "content": "Say hello in one short sentence."}`. This item is a **dictionary** — a lookup table of key-value pairs, written with curly braces `{...}`. Like a phone book where you look up a name (the key) to get a number (the value).

- This dictionary has two key-value pairs:
  - `"role": "user"` — the `role` key tells Claude who is speaking. The value `"user"` means this message comes from the human side of the conversation. (The other option would be `"assistant"`, which represents Claude's previous replies in a multi-turn conversation.)
  - `"content": "Say hello in one short sentence."` — the `content` key holds the actual text of the message. This is your prompt — the instruction you're giving Claude.

- The closing `]` ends the list, and the closing `)` ends the entire `client.messages.create(...)` call.

Why is the conversation stored as a list of dictionaries? Because Claude has no memory between separate API calls. Every time you call the API, you must send the entire conversation history — every message from both sides — so Claude can understand the context. As conversations grow longer, this list grows longer too.

---

**Line 8 (blank line)**

Blank lines in Python have no effect — they're just visual breathing room to make the code easier to read. Python ignores them.

---

**Line 9: `print(response.content[0].text)`**

This is where you actually see what Claude said. Let's read it piece by piece from the inside out:

- **`response`** — the variable holding everything that came back from the API (Claude's reply plus metadata like token counts, the model used, stop reasons, etc.)

- **`.content`** — Claude's API packages its reply as a list of **content blocks**. A content block is a self-contained piece of the response — usually text, but could theoretically be other types. `.content` accesses that list.

- **`[0]`** — this is how you access a specific item in a list by its position. Python counts from zero, not one. So `[0]` means "the first item." (The second item would be `[1]`, the third `[2]`, and so on.) For a simple single-response call like this, there's only one content block, and it's at position `[0]`.

- **`.text`** — the content block at position `[0]` is an object with several properties. `.text` reaches into it and pulls out just the plain text string — the actual words Claude wrote. Without `.text`, you'd get the whole block object printed, which would include technical details you don't need right now.

- **`print(...)`** — displays the result in the output area below the cell.

### What the output means

You'll see something like:
`Hello! It's wonderful to meet you today.`

This confirms the entire pipeline is working:
1. Your `.env` file was read correctly (Cell 1)
2. The `Anthropic` client was created with your key (Cell 2)
3. A real network request was made to Anthropic's servers (Cell 3)
4. Claude processed your prompt and returned a response
5. Your code successfully extracted the text from that response

If instead you see an error like `AuthenticationError`, it means your API key is invalid or wasn't loaded. If you see a `ConnectionError`, it means there's a network problem.

---

## Cell 4: Setting Up the Usage Log and Helper Tools

```python
import re                          # standard-library regex module used in rubric scoring
import json                        # used to pretty-print structured data

# ── Usage log: every API call appends a record here ──────────────────────────
usage_log = []                     # list accumulates one dict per API call;
```

### What this code does

---

**Line 1: `import re`**

- **`re`** is a module built into Python (no installation needed) that handles **regular expressions** — a powerful but compact language for searching and matching patterns in text.

- A regular expression (often abbreviated as "regex") lets you describe a text pattern and then find, extract, or replace text that matches it. For example, you could write a regex that means "find any number that appears after the word 'Score:'". Without regex, you'd have to write many lines of code to do the same thing.

- In this notebook, `re` will be used later in the **rubric scoring** section to automatically check whether Claude's responses meet certain criteria (like containing the right number of bullet points or staying under a word limit).

---

**Line 2: `import json`**

- **`json`** is another module built into Python. JSON stands for **JavaScript Object Notation** — but don't let that name confuse you. Despite the name, JSON is a universal format used everywhere, not just in JavaScript.

- JSON is a structured way of writing data that both humans and computers can read — like a very organised shopping list. It uses curly braces for groups of key-value pairs and square brackets for lists, which is why Python dictionaries and lists can be easily converted to and from JSON.

- In this notebook, `json` will be used to **pretty-print** structured data — meaning it will display dictionaries and lists in a neatly indented, easy-to-read format rather than as a single cramped line. Think of it as the difference between a well-formatted table and a long run-on sentence containing the same information.

---

**Line 3: `# ── Usage log: every API call appends a record here ──...`**

- This entire line is a **comment** (starts with `#`). Python ignores it completely. It's a visual separator and note for the human reader, explaining what the code below is about.

---

**Line 4: `usage_log = []`**

- `usage_log` is a new **variable** — a labeled box being created here and starting empty.

- `[]` is an **empty list** — a list with no items in it yet, like a brand new notebook with no entries. The square brackets with nothing between them signal "this is a list, and right now it's empty."

- As the comment explains, every time the code makes an API call later in the notebook, it will add a record to this list. Each record will be a **dictionary** containing details like how many tokens were used, what the response was, and which model was called.

- Why track this? API calls cost money based on token usage. By logging every call, you can review your costs and usage patterns at the end of the session — like keeping a receipt for every purchase so you can review your total spending later.

### What the output means

This cell produces no visible output — it runs silently. It's pure setup: bringing tools in and creating an empty container. When it runs without error, that's a success. The `usage_log` list exists now in Python's memory, ready to be filled in by later cells.

---

## Glossary

**API (Application Programming Interface)**
A structured way for one piece of software to communicate with another. Analogy: a waiter who takes your order to a kitchen and brings back what you asked for. You interact with the waiter; you never go into the kitchen directly.

**Argument / Parameter**
A piece of information you pass into a function to configure how it works. Parameters are the named slots (like fields on a form); arguments are the values you fill into those slots. (The two words are often used interchangeably in casual conversation.)

**Assignment**
The act of storing a value into a variable using the `=` symbol. In Python, `=` does not mean "equals" — it means "take the value on the right and put it in the box on the left."

**Boolean**
A value that is either `True` or `False` — the result of any yes/no question in Python.

**Class**
A blueprint or mould for creating objects. The `Anthropic` class is the blueprint; `client` is the specific object built