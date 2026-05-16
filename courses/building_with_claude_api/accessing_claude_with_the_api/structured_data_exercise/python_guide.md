# Python Guide: Structured Data Exercise — Prefill + Stop Sequence

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving instructions to a computer using plain-looking text. You write out steps in a specific grammar that the computer understands, and it follows those steps in order. Think of it like writing a very precise recipe: the computer is the chef, and it will follow your instructions exactly as written — no more, no less. A JupyterLab notebook lets you write those instructions in small, runnable chunks called **cells**, and you can see the results of each chunk immediately after running it. This makes it easy to learn and experiment one step at a time.

---

## Cell 1: Load Environment Variables

This cell's job is to safely load your secret API key from a hidden file on your computer, so the rest of the notebook can use it without you ever typing the key directly into your code.

```python
# ── Starter Cell 1: Load environment variables ──────────────────────────────
from dotenv import load_dotenv   # imports the dotenv helper that reads .env files
import os                         # stdlib module for environment variable access

load_dotenv()                     # reads .env in the current directory and sets env vars
api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the key from the environment
print("API key loaded:", api_key is not None)  # True means the key was found
```

### What this code does

---

**Line 1: `# ── Starter Cell 1: Load environment variables ──────────────────`**

This line starts with a `#` symbol, which means it is a **comment**. Comments are notes written for humans — the computer ignores them completely when it runs the code. This comment is simply a label telling anyone reading the notebook what this cell is for. Think of it like a sticky note attached to a section of a folder.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack this carefully, because it introduces two important ideas: **modules** and **importing**.

A **module** is a collection of pre-written Python tools that someone else has already built. Instead of writing everything from scratch, you can bring in these ready-made tools. Think of it like a toolbox: instead of forging your own hammer, you reach into a toolbox and take out the hammer that's already there.

The toolbox here is called `dotenv`. It is a module specifically designed to read a special hidden file called a `.env` file (pronounced "dot env"). That file is where you store sensitive information — like passwords or secret keys — so they never end up written directly in your code where someone might accidentally see them.

`load_dotenv` is the specific tool inside that toolbox that we want. The phrase `from dotenv import load_dotenv` means: "Go to the `dotenv` toolbox and bring out the specific tool called `load_dotenv` and make it available here."

Why does this matter? API keys (which we'll explain shortly) are like passwords. Writing them directly in code is a security risk. The `.env` file approach keeps them separate and safe.

---

**Line 3: `import os`**

This imports another module, this time one called `os`. The `os` module is part of Python's built-in standard library — meaning it comes pre-installed with Python itself, no extra installation needed.

`os` stands for "operating system." It gives Python the ability to communicate with the underlying computer system — things like reading files, navigating folders, and importantly for us, accessing **environment variables**.

An **environment variable** is a piece of information stored at the operating system level, outside of your code. Think of it like a notice board on the wall of your office: you can write things on it, and any program running in that office can look up at the board and read what's there. `load_dotenv` (from the previous line) writes your secret key onto that board, and `os` gives us the tools to read from it.

The word `import` here (without `from`) means: "Bring in the whole `os` toolbox and make it available under the name `os`."

---

**Line 5: `load_dotenv()`**

This is the first line that actually *does* something when the computer runs it. The previous lines just brought tools into the workspace — this line uses one of those tools.

`load_dotenv()` is a **function call**. A **function** is like a named recipe: someone has written out a set of instructions and given them a name. When you write the name followed by `()`, you are telling the computer: "Go run those instructions now."

Specifically, `load_dotenv()` goes and finds the `.env` file sitting in the same folder as this notebook, reads it, and loads whatever is written inside it onto the operating system's notice board (the environment variables). After this line runs, your API key is accessible to the rest of the program — but it was never typed into the notebook itself.

The parentheses `()` are required — they are how Python knows you want to *run* the function, not just refer to it by name.

---

**Line 6: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line introduces another foundational concept: the **variable**.

A **variable** is a labeled box that holds a value. You give the box a name, and you can put something inside it. Later, whenever you write that name, Python goes and looks in the box to get the value. Here, the box is named `api_key`.

The `=` sign doesn't mean "equals" the way it does in maths. In Python, it means **assignment** — "put the thing on the right side into the box named on the left side."

The right side is `os.getenv("ANTHROPIC_API_KEY")`. Let's break that down:
- `os` is the module we imported earlier
- `.getenv` is a specific tool inside that module. The dot (`.`) is how Python navigates to a specific tool inside a module — like saying "go to the `os` toolbox and pick up the `getenv` tool"
- `"ANTHROPIC_API_KEY"` is the **argument** — the input we're giving to this function. It is the name of the specific environment variable we want to look up on the notice board
- `getenv` looks up that name on the notice board and returns whatever value is stored there — in this case, our secret API key

After this line runs, the variable `api_key` holds our secret key. We can now pass it to other parts of the program without ever writing the actual key in the code.

---

**Line 7: `print("API key loaded:", api_key is not None)`**

This line prints a message to the screen so we can see what happened.

`print()` is a built-in Python function that displays text in the output area below the cell. Whatever you put inside the parentheses gets shown on screen.

We're printing two things separated by a comma:
- `"API key loaded:"` — this is a **string**, which is just text enclosed in quotation marks. Python will display it as-is
- `api_key is not None` — this is a **comparison** that produces either `True` or `False`

**None** is a special Python value that means "nothing" or "empty." If `load_dotenv()` couldn't find the key, `api_key` would be `None`. So `api_key is not None` is asking: "Does `api_key` contain something real, rather than nothing?" If the key was found successfully, this evaluates to `True`. If not, it evaluates to `False`.

So the output will be either:
- `API key loaded: True` — success, the key was found
- `API key loaded: False` — something went wrong (the `.env` file might be missing or the key might be misnamed)

### What the output means and why it matters

This cell produces a single line of output. If you see `API key loaded: True`, everything is working — your secret key has been safely loaded into memory and is ready to use. If you see `False`, you need to stop and fix your `.env` file before continuing, because nothing else in the notebook will work without this key.

---

## Cell 2: Initialise the Anthropic Client

This cell creates the official connection object that will carry all your requests to Claude's API. Think of it as picking up the phone and getting a dial tone — you haven't made a call yet, but the line is open and ready.

```python
# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────
from anthropic import Anthropic   # the official Python SDK class

client = Anthropic(api_key=api_key)  # creates an authenticated client instance
print("Anthropic client ready")       # confirms object was constructed without error
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise the Anthropic client ─────────────`**

Another comment — a human-readable label that the computer ignores. It tells us this cell's purpose: setting up the Anthropic client.

---

**Line 2: `from anthropic import Anthropic`**

This imports a specific tool from a module called `anthropic`. The `anthropic` module is the official Python **SDK** (Software Development Kit) published by Anthropic — the company that makes Claude. An SDK is a pre-built set of tools that makes it easier to communicate with a particular service. Instead of manually constructing complex web requests, the SDK handles that complexity for you.

The specific tool we're importing is called `Anthropic` (capital A). In Python, when a name starts with a capital letter, it usually signals that it is a **class**. A **class** is like a blueprint or a template for creating objects. Think of a class as a cookie cutter: the cutter itself isn't a cookie, but you use it to stamp out cookies. Similarly, `Anthropic` is a blueprint you use to create a working client object.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line uses the `Anthropic` blueprint to create an actual client object, and stores it in a variable named `client`.

- `client` is a **variable** — a labeled box — that will hold our working connection object
- `Anthropic(...)` is how you use the class blueprint to create a real object. The parentheses with inputs inside is called **instantiation** — you are creating one specific instance of the blueprint
- `api_key=api_key` is a **keyword argument** — an input where you specify both the name of the slot (`api_key`) and the value to put in it (the `api_key` variable we created in Cell 1). The left side (`api_key=`) is the name of the parameter the Anthropic blueprint expects; the right side (`api_key`) is the value from our variable

Think of this like filling out a form: the form has a field labeled "API Key" and you write your actual key into that field. The resulting object knows who you are and is authorized to make requests on your behalf.

The **API** is like a waiter at a restaurant: you give the waiter your order, the waiter takes it to the kitchen (Anthropic's servers), and brings back what you asked for. The `client` object is your permanent, authenticated connection to that waiter.

---

**Line 5: `print("Anthropic client ready")`**

This prints a simple confirmation message. There's no complex logic here — if this line runs without an error appearing, it means the `Anthropic(...)` object was created successfully. If there had been a problem (like an invalid API key), Python would have thrown an error before reaching this line and the message would never appear.

### What the output means and why it matters

You should see the text `Anthropic client ready` printed below the cell. This is your confirmation that you have a working, authenticated connection to Claude's API. Every subsequent cell that sends a request to Claude will use this `client` variable, so it must be set up correctly before anything else will work.

---

## Cell 3: Smoke Test — Verify End-to-End Connectivity

This cell sends a real, live request to Claude and prints the response. The purpose is purely to confirm that everything is working end-to-end — the key is valid, the network connection is working, and Claude is responding. A "smoke test" is an industry term for a quick basic check: if you turn something on and smoke comes out, you know something is wrong. If no smoke, you're safe to continue.

```python
# ── Starter Cell 3: Smoke test — verify end-to-end connectivity ─────────────
response = client.messages.create(
    model="claude-haiku-4-5",          # fast, cheap model — good for connectivity checks
    #    model="claude-sonnet-4-5",
    max_tokens=50,                      # hard cap; smoke test only needs a short reply
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]
    # messages is a list of dicts — the minimum required field besides model/max_tokens
)
print(response.content[0].text)        # content is a list of blocks; [0] is the first text block
```

### What this code does

---

**Line 1: `# ── Starter Cell 3: Smoke test — verify end-to-end connectivity ──`**

Another comment label. No effect on execution.

---

**Lines 2–7: `response = client.messages.create(...)`**

This is one large statement spread across multiple lines. Python allows you to break a long statement across lines when it's inside parentheses, which makes it easier to read.

Let's understand the overall shape first, then go inside it.

`response = client.messages.create(...)` means:
- `client` is the connection object we created in Cell 2
- `.messages` is a sub-section of that client object dealing specifically with sending messages
- `.create(...)` is the function that actually sends a new message request to Claude

The result — whatever Claude sends back — gets stored in the variable called `response`.

Think of it this way: `client` is your telephone connection to a restaurant (the API). `.messages` is like pressing the button for the kitchen. `.create(...)` is like placing your order. And `response` is the box where the food arrives.

Now let's go through each input (called a **parameter** or **argument**) inside the parentheses:

---

**`model="claude-haiku-4-5"`**

This tells the API which Claude model to use. Anthropic offers several versions of Claude with different speed and capability trade-offs. `claude-haiku-4-5` is a fast, lightweight, and inexpensive model — ideal for a quick connectivity check where you just want to know if the connection works, not to get the best possible answer.

The line directly below it — `#    model="claude-sonnet-4-5",` — is a commented-out alternative. The `#` at the start means this line is ignored. It's left there as a reminder that you could switch to a more powerful model by removing the `#` and commenting out the haiku line instead. This is a common developer practice called "leaving an option visible but inactive."

---

**`max_tokens=50`**

This sets a hard upper limit on how long Claude's response can be.

A **token** is the unit Claude uses to measure text. It's roughly equivalent to a word, though some short words count as one token and some longer words count as two. Think of tokens like word-chunks. Setting `max_tokens=50` means: "Don't give me a response longer than 50 word-chunks." 

For a smoke test, we only need a brief reply to confirm the connection works, so 50 tokens is more than enough. Limiting tokens also limits cost, since API usage is typically billed per token.

---

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual conversation you're sending to Claude. Let's peel back the layers:

- The outer `[...]` means this is a **list** — an ordered collection of items, like a numbered list on paper
- Inside the list, there is one item: `{"role": "user", "content": "Say hello in one short sentence."}`
- That item inside `{...}` is a **dictionary** — a lookup table where each entry has a key (a label) and a value. Think of it like a form with labelled fields

This dictionary has two fields:
- `"role": "user"` — this tells Claude that this message is from the user (as opposed to from Claude itself, which would be `"assistant"`)
- `"content": "Say hello in one short sentence."` — this is the actual text of the message

The Claude API is designed around the idea of a **conversation** — a back-and-forth list of messages alternating between user and assistant. Even if you're only sending one message, it still needs to be wrapped in this list format. It's like the formal structure of a letter: even a one-line letter still needs to be in an envelope.

---

**Line 8: `print(response.content[0].text)`**

This prints what Claude said back to us.

`response` is the variable holding everything Claude returned. It's a structured object (like a box containing several compartments). Let's navigate to the text:

- `response.content` — the `.content` compartment of the response, which holds a **list** of content blocks. Claude can technically return multiple blocks (for example, a mix of text and other types), so the API always returns a list
- `[0]` — this means "get the first item in the list." In Python, lists are **zero-indexed**, meaning counting starts at 0, not 1. So `[0]` is the first item, `[1]` would be the second, and so on. Think of it like floors in a European building where the ground floor is called Floor 0
- `.text` — the actual text string inside that first content block

Altogether, `response.content[0].text` means: "From the response, go to the content list, take the first item, and get its text."

### What the output means and why it matters

You should see a short, friendly sentence from Claude — something like `"Hello! It's great to meet you."` 

This single line of output is actually confirming many things at once:
1. Your API key is valid
2. Your internet connection is working
3. The Anthropic servers are reachable
4. The Python client library is functioning correctly
5. Claude understood the request and responded

If anything in this chain is broken, you'll see an error instead of a sentence. Getting a clean response here gives you the green light to proceed with the rest of the lab.

---

## Markdown Cells: The Lab Introduction and Reference Tables

The next several cells in the notebook are **markdown cells**, not code cells. They don't contain Python — they contain formatted text that explains what the lab is about. These cells run like code but produce readable text rather than computer output.

### What the Lab Introduction covers

The markdown introduction tells us this lab is about two advanced API techniques:

**Assistant Prefilling** means giving Claude the beginning of its own reply before it starts generating. Normally, you send a message and Claude writes the entire response from scratch. With prefilling, you write the first few characters of Claude's response yourself — for example, you write `{` to signal that you want the response to start as a JSON object. Claude then continues from where you left off. This is more reliable than asking Claude to "only return JSON" in plain language, because you've already locked in the format before any words are generated.

**Stop Sequences** are specific strings of text that, when Claude generates them, immediately cause Claude to stop writing. For example, if you set the stop sequence to ` ``` ` (three backticks — a common code fence marker), Claude will stop the moment it generates that string. Importantly, the stop sequence itself is *not* included in the response you receive. This is used to trim unwanted closing markers from output.

### What the API Parameter Reference Table explains

The reference table in the markdown describes every parameter you can pass to the `client.messages.create()` function:

| Parameter | What it is |
|---|---|
| `model` | Which Claude model to use |
| `max_tokens` | The maximum length of the response |
| `messages` | The list of conversation turns (user/assistant back-and-forth) |
| An assistant message at the end of `messages` | How you do prefilling — Claude continues from wherever this message left off |
| `stop_sequences` | A list of strings that, when generated, stop Claude immediately |
| `system` | A top-level instruction that sets Claude's overall role or behavior |

The table also clarifies what happens when a stop sequence fires:
- Everything Claude wrote *before* the stop sequence **is** included in the response
- The stop sequence string itself **is not** included in the response

This distinction matters practically: if you use ` ``` ` as your stop sequence and ask Claude to generate code, you'll get the code without the closing triple-backtick fence. This makes it easy to extract clean structured data.

---

## Glossary

**API (Application Programming Interface):** A standardized way for one piece of software to talk to another. Like a waiter who takes your order to the kitchen and brings back the result, an API lets your code send requests to a remote service (like Claude) and receive structured responses.

**API key:** A secret string of characters that identifies you to an API and proves you have permission to use it. Like a password combined with a username, all in one string. Never share this or write it directly in code.

**Argument / Parameter:** An input you provide to a function when you call it. Like filling in the blanks on a form — the form defines the fields, and you provide the values.

**Assignment (`=`):** In Python, the `=` sign doesn't mean "equals" — it means "store this value into this variable." The name on the left gets the value on the right.

**Class:** A blueprint or template for creating objects. Like a cookie cutter that you use to stamp out individual cookies. The class itself isn't the object — it's the pattern used to create objects.

**Client (in API context):** A software object that manages your connection to an API. It holds your credentials and provides organized methods for making requests.

**Comment (`#`):** Text in code that the computer ignores, written for human readers. Starts with `#`.

**Dictionary:** A Python data structure that stores key-value pairs — like a phone book where you look up a name (key) to find a number (value). Written with curly braces `{...}` and colons between keys and values.

**Environment variable:** A piece of information stored at the operating system level, outside your code. Programs can read from it without the value being written in the source code. Good for storing secrets.

**`.env` file:** A plain text file that stores environment variables in a simple `KEY=VALUE` format. The `dotenv` library reads this file and loads its contents into the environment.

**Function:** A named, reusable set of instructions. You run a function by writing its name followed by parentheses `()`. Like a named recipe you can call by name.

**Import:** Bringing pre-built tools from a module or library into your current code. Like reaching into a toolbox and pulling out a specific tool.

**Instance / Instantiation:** Creating one specific object from a class blueprint. Like using a cookie cutter (class) to stamp out one actual cookie (instance).

**Keyword argument:** A function argument where you specify the name of the parameter alongside the value, like `api_key=api_key`. This makes code more readable and avoids errors from putting arguments in the wrong order.

**List:** An ordered collection of items in Python, written with square brackets `[...]`. Items are numbered starting from 0. Like a numbered list on paper.

**Markdown:** A lightweight formatting language used to write readable text with headings, bold, tables, and more. JupyterLab renders markdown cells as formatted text rather than running them as code.

**Module:** A collection of pre-written Python tools you can import and use. Like a specialized toolbox.

**`None`:** A special Python value meaning "nothing" or "no value." Used when a variable exists but has nothing stored in it.

**Object:** A self-contained bundle of data and functions created from a class. In this notebook, `client` is an object — it holds your API credentials and has methods (functions) for making requests.

**Prefilling (assistant prefill):** An API technique where you include an assistant message at the end of your conversation list, giving Claude the beginning of its own response. Claude will then continue generating from that point forward, allowing you to lock in a specific output format.

**Print:** A built-in Python function that displays text in the output area of a notebook cell. Used to show results and confirm things worked correctly.

**SDK (Software Development Kit):** A pre-built collection of code that makes it easier to work with a specific service or platform. The `anthropic` module is Anthropic's official Python SDK.

**Smoke test:** A quick, basic test to confirm something works at all before doing more detailed work. Named after the idea that if you turn on an electrical device and it doesn't smoke, it probably works well enough to test further.

**Stop sequence:** A string you provide to the Claude API that, when generated, immediately halts Claude's output. The stop sequence itself is excluded from the returned text.

**String:** Text data in Python, enclosed in either single quotes `'...'` or double quotes `"..."`.

**Token:** The unit Claude uses to measure text. Roughly equivalent to a word, though it can be less (common short words) or more (long or rare words). API pricing and `max_tokens` limits are measured in tokens.

**Variable:** A named container that holds a value. Like a labeled box — you can put a value in, take it out, or replace it later. You create a variable by writing `name = value`.

**Zero-indexing:** Python counts the positions in a list starting from 0, not 1. The first item is at position `[0]`, the second at `[1]`, and so on. Many beginners trip over this at first — it's simply a convention the language uses.