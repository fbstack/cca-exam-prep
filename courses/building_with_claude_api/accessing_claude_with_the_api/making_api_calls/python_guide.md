# Python Guide: Making a Request to the Claude API

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (one paragraph for complete beginners)

Python is a programming language — a way of giving precise, step-by-step instructions to a computer. Think of it like writing a very detailed recipe. The computer reads each line from top to bottom and does exactly what it says. On its own, Python knows how to do basic things like store information and repeat tasks. But you can also bring in ready-made toolkits — called **libraries** or **packages** — that other people have built, so you don't have to write everything from scratch. This notebook uses a few of those toolkits to talk to an AI called Claude through the internet.

---

## Starter Cell 1: Load environment variables — reading your secret API key from a secure file

```python
# ── Starter Cell 1: Load environment variables ──────────────────────────────
from dotenv import load_dotenv   # imports load_dotenv to read the .env file
import os                         # imports os so we can read environment variables

load_dotenv()                     # reads .env in the current directory and sets env vars

api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the key; returns None if missing

print("API key loaded:", api_key is not None)  # True = key found; False = check your .env
```

### What this code does

**Line 1: `# ── Starter Cell 1: Load environment variables ──...`**

This line is a **comment** — it starts with a `#` symbol, which tells Python to ignore everything that follows on that line. Comments are notes left by the programmer for human readers. Python skips right over them. This one just labels what the cell is for.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack three ideas here.

A **package** (also called a library) is a collection of pre-written Python tools bundled together — like a toolbox someone else built and handed to you. `dotenv` is one such toolbox. Its job is to read a special file called `.env` (pronounced "dot env") that stores secret values like passwords and API keys, so you don't have to type them directly into your code where someone might accidentally see them.

**`import`** is the instruction that brings a tool from a package into your current workspace — like reaching into a toolbox and placing a specific wrench on your workbench.

**`from dotenv import load_dotenv`** means: "From the `dotenv` toolbox, bring me the specific tool called `load_dotenv`." `load_dotenv` is a **function** — a named set of instructions you can trigger by calling its name. Think of a function like a named recipe: `load_dotenv` is the recipe for "open the `.env` file and load its contents."

We need this because your **API key** — the secret password that proves to Anthropic's servers that you are who you say you are — should never be written directly into a notebook. If you shared your notebook online, everyone could see your key and use your account. Instead, the key lives in a private `.env` file that stays on your computer only.

---

**Line 3: `import os`**

`os` is a built-in Python module (a module is just a single-file package). It gives you tools to interact with your **operating system** — the underlying software running your computer (Windows, macOS, Linux). One of `os`'s jobs is giving you access to **environment variables**.

An **environment variable** is a piece of information your operating system stores in the background — like a global sticky note your whole computer can read. When `load_dotenv()` runs (see below), it takes the contents of your `.env` file and writes them as environment variables. The `os` module then lets you read those variables from inside Python.

---

**Line 5: `load_dotenv()`**

This line actually *calls* (executes) the `load_dotenv` function we imported on line 2. The empty parentheses `()` are how you tell Python "run this function now." 

What it does: it looks in the current folder for a file named `.env`, opens it, reads every line (which are in the format `KEY=value`, e.g. `ANTHROPIC_API_KEY=sk-abc123`), and registers each one as an environment variable. After this line runs, the key exists in memory and can be retrieved.

If no `.env` file is found, `load_dotenv()` doesn't crash — it just does nothing silently, which is why the next line is important for catching that situation.

---

**Line 7: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line does two things at once.

First, **`os.getenv("ANTHROPIC_API_KEY")`** calls a function from the `os` module named `getenv`. You pass it the name of the environment variable you want — in this case the string `"ANTHROPIC_API_KEY"` (a **string** is just text, always enclosed in quotes in Python). `getenv` looks up that name and returns its value — your actual secret key. If it can't find that variable, it returns `None`, which is Python's way of saying "nothing" or "absent."

Second, **`api_key =`** stores whatever `os.getenv` returned into a **variable** called `api_key`. A variable is like a labeled box: you give it a name, and it holds a value for you to use later. Any time you write `api_key` later in the code, Python opens that box and uses what's inside.

The `=` here is not asking a question ("are these equal?") — it is an **assignment**: "take the value on the right and store it in the box named on the left."

---

**Line 9: `print("API key loaded:", api_key is not None)`**

**`print()`** is a built-in Python function that displays something on the screen below the cell. It's the most basic way to see what's happening inside your code.

**`"API key loaded:"`** is a string — plain text — that will appear exactly as written.

**`api_key is not None`** is a **boolean expression** — a question with a yes/no answer. `None` is the special Python value meaning "nothing." So this is asking: "Does `api_key` contain something other than nothing?" If the key loaded successfully, this evaluates to `True`. If the `.env` file was missing or the variable wasn't set, it evaluates to `False`.

Python will print both pieces together, separated by a space, giving you either:  
`API key loaded: True` or `API key loaded: False`

### What the output means

You will see either:

- **`API key loaded: True`** — Your `.env` file was found, the `ANTHROPIC_API_KEY` variable was inside it, and the key has been stored in the `api_key` variable. You're ready to proceed.
- **`API key loaded: False`** — Something went wrong. Either the `.env` file doesn't exist, it's in the wrong folder, or the variable isn't named exactly `ANTHROPIC_API_KEY`. You must fix this before the rest of the notebook will work.

This check matters because if you proceed with a missing key, every API call later will fail with an authentication error. Catching it here saves confusion.

---

## Starter Cell 2: Initialise the Anthropic client — creating your connection to Claude

```python
# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────
from anthropic import Anthropic   # imports the Anthropic SDK client class

client = Anthropic(api_key=api_key)  # creates a reusable client; api_key auth is passed explicitly

print("Anthropic client ready")   # confirms the object was created without errors
```

### What this code does

**Line 1: `# ── Starter Cell 2: Initialise the Anthropic client ──...`**

Another comment. Python ignores it. It's a label telling human readers what this section does.

---

**Line 2: `from anthropic import Anthropic`**

This brings in a tool from the `anthropic` package — the official Python library that Anthropic (the company that makes Claude) has built for you. 

Specifically, it imports something called `Anthropic` — note the capital A. In Python, names that start with a capital letter are often **classes**. A **class** is like a blueprint or template for creating an object. Think of it like a cookie cutter: the class is the cutter shape, and what you create with it is the cookie.

The `Anthropic` class is a blueprint for creating a **client** — a connection manager that knows how to send requests to Anthropic's servers and receive responses back.

An **SDK** (Software Development Kit) is a ready-made toolkit a company provides so that developers can talk to their service without having to figure out all the technical plumbing themselves. This SDK handles the HTTP requests, error formatting, and authentication for you.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line creates an actual client object using the `Anthropic` blueprint. `Anthropic(api_key=api_key)` calls the class like a function — this is called **instantiation**, meaning "make a real instance from the blueprint." The result is a live object stored in a variable called `client`.

**`api_key=api_key`** passes your API key into the client so it can include it in every request it makes. Notice `api_key` appears twice here: the first one (on the left of `=`) is the *parameter name* the `Anthropic` class expects, and the second one (on the right) is the *variable* from your earlier code that holds the actual key value.

Think of `client` like a phone that's already been set up with a phone plan (your API key). Every time you want to talk to Claude, you use this phone — you don't have to re-enter your credentials each time.

---

**Line 6: `print("Anthropic client ready")`**

Prints the message `Anthropic client ready` to the screen. This line exists purely as a sanity check. If the `Anthropic(...)` call on the previous line had thrown an error (for example, if the `anthropic` package wasn't installed), Python would have stopped before ever reaching this line. Seeing this message printed is confirmation that the client was built successfully.

### What the output means

- **`Anthropic client ready`** — The client object was created without errors. Your API key was accepted by the local code (note: it hasn't contacted the internet yet — that happens in the next cell). You're ready to make actual API calls.

If instead you see a red error message here, the most likely causes are: the `anthropic` package isn't installed, or the `api_key` variable is `None` (which happens when the previous cell didn't load the key successfully).

---

## Starter Cell 3: Smoke test — sending a real message to Claude and checking everything works end-to-end

```python
# ── Starter Cell 3: Smoke test — verify end-to-end connectivity ──────────────
response = client.messages.create(
    model="claude-haiku-4-5",          # fast, cheap model — perfect for smoke tests
    #    model="claude-sonnet-4-5",    # uncomment for higher-quality responses
    max_tokens=50,                     # hard ceiling; Claude stops here even mid-sentence
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]  # minimal prompt
)

print(response.content[0].text)       # .content is a list of blocks; [0] gets the first text block
```

### What this code does

**Line 1: `# ── Starter Cell 3: Smoke test...`**

A comment. A **smoke test** is a term from engineering meaning "the most basic test possible — just switch it on and check it doesn't smoke." In software, it means: send the simplest possible request to confirm the whole system is connected and working before you invest effort in more complex work.

---

**Lines 2–6: `response = client.messages.create(...)`**

This is the most important pattern in the entire notebook, so let's take it slowly.

**`client.messages.create(...)`** calls a function that sends a message to Claude over the internet and waits for a response. The dot notation (`client.messages.create`) means: "on the `client` object, find the `messages` section, and within that, call the `create` function." Think of it like dialing a specific extension on a phone system.

The whole expression starting with `response =` stores the result of that function call into a variable named `response`. `response` will hold the complete reply package that Claude sends back — not just the text, but also information like how many tokens were used.

The function call spans multiple lines (lines 2–6) — Python allows this when you're inside parentheses, and it's done here purely for readability. It's all one instruction.

Now let's look at each **parameter** (a parameter is a piece of information you hand to a function to tell it how to behave):

---

**`model="claude-haiku-4-5"`**

This tells Claude *which version* of itself to use. Anthropic offers different Claude models that trade off between speed, cost, and capability:

- **Claude Haiku** is the fastest and cheapest — great for quick tests and simple tasks
- **Claude Sonnet** is more capable but costs more per request

`"claude-haiku-4-5"` is a **string** (text in quotes) containing the model's official identifier. You must spell it exactly as Anthropic defines it, or the API will return an error.

---

**`#    model="claude-sonnet-4-5",`**

This line is commented out (starts with `#`), so Python ignores it. It's left there as a convenience — if you want to switch to Sonnet, you delete the `#` at the start of this line and add a `#` to the line above. This is a common pattern for offering easy alternatives without deleting code.

---

**`max_tokens=50`**

A **token** is roughly one word or part of a word — it's the unit Claude uses to read and write text. "Hello" is one token. "Unbelievable" might be two or three tokens. 

`max_tokens=50` sets a hard limit: Claude will stop generating text after 50 tokens, even if its sentence isn't finished. This is useful for smoke tests because it caps cost and response time. For a proper application, you'd set this higher — perhaps 1000 or 4000, depending on how long a response you need.

Think of `max_tokens` like setting a maximum word count on an essay. Claude will never go over it.

---

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual conversation you're sending to Claude. Let's break it into pieces:

The outer `[...]` means this is a **list** — an ordered collection of items. Think of a list like a numbered checklist. This list contains the conversation history.

Inside the list is one item: `{"role": "user", "content": "Say hello in one short sentence."}`. The `{...}` means this is a **dictionary** — a lookup table made of paired keys and values, like a two-column table. Each key is on the left of the colon, and its value is on the right.

This dictionary has two keys:
- **`"role"`** — whose turn is this message? The value `"user"` means it's the human speaking. Claude's replies use `"assistant"` as the role.
- **`"content"`** — what was actually said. Here it's the prompt `"Say hello in one short sentence."`.

The reason `messages` is a list (and not just a single message) is that multi-turn conversations need to include all the previous back-and-forth. For this smoke test, there's only one message: the user's greeting prompt.

---

**Line 8: `print(response.content[0].text)`**

This retrieves and displays Claude's reply. Let's decode the chain:

- **`response`** — the variable holding everything Claude sent back
- **`.content`** — a property of the response that contains a **list** of content blocks. Claude's reply is delivered as a list because in advanced use cases, Claude can return multiple blocks (e.g. text and tool calls). Even when there's just one reply, it comes back as a list with one item.
- **`[0]`** — this accesses the **first item** in the list. In Python, lists are numbered starting from zero (not one). So `[0]` means "the first item," `[1]` would mean the second, and so on.
- **`.text`** — this property on the first block holds the actual string of text Claude wrote.

Put together: "Take the response, find its content list, grab the first item in that list, and get the text from it." Then `print()` displays it.

### What the output means

You will see a single sentence printed below the cell — Claude's response to "Say hello in one short sentence." It might look like:

`Hello! It's a pleasure to meet you today.`

This output confirms that:
1. Your API key is valid and was accepted by Anthropic's servers
2. The `client` object is correctly configured
3. The request format is correct
4. Claude responded successfully
5. The code for extracting text from the response (`response.content[0].text`) works

If you see an error instead — particularly an `AuthenticationError` — your API key is wrong or missing. A `ConnectionError` would mean a network problem. Any error here tells you something needs fixing before you proceed.

---

## Section 1, Prerequisites Cell: Checking that all required packages are installed

```python
# Verify required packages are importable — run this before anything else
import sys

required = ["anthropic", "dotenv"]   # packages the rest of the notebook depends on

for pkg in required:                  # iterate over each required package name
    try:
        __import__(pkg)               # attempt to import by name string
        print(f"✅ {pkg}")            # success — package is available
    except ImportError:
        print(f"❌ {pkg} — run: pip install {pkg}")  # tell the user exactly what to install

print(f"\nPython {sys.version}")      # log the Python version for reproducibility
```

### What this code does

**Line 1: `# Verify required packages are importable — run this before anything else`**

A comment explaining the purpose of this cell. It's a reminder to run this cell first to catch any missing tools before the rest of the notebook tries to use them.

---

**Line 2: `import sys`**

`sys` is another built-in Python module. It gives you information about the Python environment itself — things like which version of Python is running, and where Python looks for packages. We need it here for the last line of the cell.

---

**Line 4: `required = ["anthropic", "dotenv"]`**

This creates a **list** called `required` and stores it in a variable. A list in Python is written with square brackets `[...]`, and each item is separated by a comma. This list contains two strings — the names of the packages this notebook needs to have installed.

Think of this like a packing checklist: before you leave on a trip, you check that everything on the list is in your bag.

---

**Line 6: `for pkg in required:`**

This begins a **for loop** — one of Python's most important patterns. A for loop says: "Go through every item in this collection, one at a time, and do something with each item."

The word `pkg` is a **loop variable** — a temporary box that holds each item as the loop processes it. On the first pass through the loop, `pkg` holds `"anthropic"`. On the second pass, it holds `"dotenv"`. The colon `:` at the end means "here comes the block of code to run for each item."

Everything indented below this line (lines 7–10) is the "body" of the loop — the instructions that get repeated for each package. Python uses **indentation** (spaces at the start of a line) to show which lines belong inside the loop.

---

**Line 7: `try:`**

`try` begins a **try/except block** — Python's way of gracefully handling errors. Normally, if Python encounters an error, it stops immediately and shows a red message. A try/except says: "Try running this code, but if something goes wrong, don't crash — instead, do this other thing."

The colon `:` again marks the start of a code block. Everything indented under `try:` is the code Python will *attempt* to run.

---

**Line 8: `__import__(pkg)`**

**`__import__()`** is a built-in Python function that imports a package by name — but instead of writing `import anthropic` directly, it lets you import using a *variable* that holds the name as a string. The double underscores on either side are a Python convention indicating this is a "behind the scenes" system function.

So on the first loop pass, this is effectively the same as `import anthropic`. On the second pass, it's the same as `import dotenv`.

Why not just use `import` directly? Because the package name is stored in a variable (`pkg`), and the regular `import` keyword can't take a variable — it needs a literal name. `__import__()` solves that.

If the package is installed, this line runs without issue. If it's not installed, Python raises an **`ImportError`** — a specific type of error meaning "I couldn't find that package."

---

**Line 9: `print(f"✅ {pkg}")`**

If the import succeeded (no error), this line runs and prints a green checkmark followed by the package name.

The `f"..."` is an **f-string** (formatted string). It's a way of embedding variable values directly into text. Anything inside curly braces `{}` within an f-string gets replaced with the actual value of the variable. So `f"✅ {pkg}"` becomes `✅ anthropic` on the first pass and `✅ dotenv` on the second.

---

**Line 10: `except ImportError:`**

This is the second half of the try/except block. If an `ImportError` was raised during the `try` block (meaning the package wasn't found), Python jumps here instead of crashing. The code indented below this line runs only when that specific error occurs.

---

**Line 11: `print(f"❌ {pkg} — run: pip install {pkg}")`**

If the package is missing, this prints a red X, the package name, and the exact command the user needs to type in their terminal to install it. For example: `❌ anthropic — run: pip install anthropic`.

**`pip`** is Python's package installer — a command-line tool that downloads and installs packages from the internet. `pip install anthropic` would fetch the Anthropic SDK and install it on your computer.

This is excellent design: rather than letting the notebook crash with a confusing technical error later, it catches the problem early and tells the reader exactly what to do.

---

**Line 13: `print(f"\nPython {sys.version}")`**

After the loop finishes checking both packages, this line prints the Python version.

**`sys.version`** is a property of the `sys` module that holds a string describing the exact Python version running right now — something like `3.11.0 (main, Oct 24 2022, 18:26:48)`.

**`\n`** is a special code inside strings meaning "start a new line here." It's like pressing Enter. So this prints a blank line first, then the Python version — just for spacing and readability.

Why does version matter? Because some packages require a minimum Python version, and if a lab isn't working as expected, knowing the Python version helps someone diagnose the problem.

### What the output means

You should see something like:

```
✅ anthropic
✅ dotenv

Python 3.11.0 (main, Oct 24 2022, 18:26:48) [...]
```

- **A ✅ next to a package name** — that package is installed and ready to use. Good.
- **A ❌ next to a package name** — that package is missing. Stop, open a terminal, run the `pip install` command shown, and then re-run this cell before continuing.
- **The Python version line** — this tells you (and anyone helping you debug) which version of Python is running. For this notebook, you need Python 3.11 or later.

This cell is your safety net. Running it first prevents a cascade of confusing errors later.

---

## Glossary

| Term | Definition |
|------|------------|
| **API** | Application Programming Interface — a system that lets your code talk to another service over the internet. Like a waiter who takes your order to the kitchen and brings back what you asked for. |
| **API key** | A secret password-like string that proves your identity to an API service. Should never be shared or put directly in code