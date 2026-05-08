# Python Guide: Prompt Engineering & the Improvement Cycle

A plain-English walkthrough of every line of code in the JupyterLab notebook.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving precise, step-by-step instructions to a computer. Think of it like writing a very detailed recipe. The computer reads each instruction from top to bottom and does exactly what you say — no more, no less. Unlike a recipe written for a human cook, you cannot leave anything implied or assumed. Every step must be spelled out. The notebook you are reading is a collection of these instructions, organized into "cells" that you run one at a time, in order. Each cell builds on the work done by the cells before it.

---

## What JupyterLab is

JupyterLab is an interactive environment where you can write Python code in individual boxes called **cells**, run each cell separately, and see the result immediately below it. It is like a scratchpad and a calculator combined — you can experiment, make changes, and re-run cells without starting over from scratch. This makes it ideal for learning and for working through problems step by step.

---

## Cell 1: Loading Your Secret API Key

```python
from dotenv import load_dotenv  # loads .env file into environment variables
import os                        # provides os.getenv to read environment variables
load_dotenv()                    # reads .env in current directory
api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the key (None if missing)
print("API key loaded:", api_key is not None)  # True = key found, False = missing
```

### What this cell does

Before you can talk to Claude (Anthropic's AI), you need to prove who you are. You do this with an **API key** — think of it like a password or a hotel key card. This cell finds that key card and prepares it for use. Crucially, it reads the key from a hidden file rather than typing it directly into your notebook, which keeps the key safe.

---

**Line 1: `from dotenv import load_dotenv`**

Let's unpack three concepts here.

**What an "import" is:** Python comes with a core set of abilities built in, but many useful tools live in separate **packages** (also called **libraries** or **modules**). A package is like a toolbox sitting in your garage. Before you can use the tools inside, you have to carry the toolbox into your workshop. The word `import` does exactly that — it brings a toolbox into your current working session so you can use its tools.

**What `dotenv` is:** `dotenv` is the name of a specific toolbox. Its one job is to read a special hidden file called `.env` (pronounced "dot env") that lives in the same folder as your notebook. A `.env` file is a plain text file where you store secret values — like passwords and API keys — in a format that looks like this:

```
ANTHROPIC_API_KEY=sk-ant-abc123...
```

The reason we store secrets in a `.env` file instead of typing them directly into the notebook is security. If you share your notebook with a colleague or post it online, the `.env` file stays on your own computer. Your secrets never travel with the notebook.

**What `load_dotenv` is:** `load_dotenv` is a specific **function** inside the `dotenv` toolbox. A function is like a named recipe — a set of steps that has been written down once and given a name, so you can trigger all those steps just by calling that name. `load_dotenv` is the recipe for "open the `.env` file, read what's inside, and make those values available to the rest of the program."

The word `from` at the start means: "go into the `dotenv` toolbox, but only take out the one tool called `load_dotenv`, not the whole toolbox." This keeps things tidy.

---

**Line 2: `import os`**

This brings in another toolbox called `os`. The name stands for "operating system." Your operating system is the software that runs your computer (Windows, macOS, or Linux). The `os` toolbox gives Python a way to talk to the operating system — asking it things like "what folder am I in?" or "what is stored in the environment right now?"

**What "environment" means here:** Think of the environment as a whiteboard that your operating system keeps running at all times. Programs can write values onto this whiteboard (for example, "ANTHROPIC_API_KEY = abc123") and other programs can read from it. When `load_dotenv` reads your `.env` file, it writes the values it finds onto this whiteboard. The `os` toolbox then lets Python read from that whiteboard.

Notice this line just says `import os` without the `from` keyword. That means we are bringing in the entire `os` toolbox, not just one specific tool from it. We will refer to specific tools inside it later using dot notation, like `os.getenv` (meaning "the `getenv` tool that lives inside the `os` toolbox").

---

**Line 3: `load_dotenv()`**

This actually runs (or **calls**) the `load_dotenv` function we brought in on Line 1. The empty parentheses `()` are how Python knows you want to execute the function right now. If you wrote `load_dotenv` without the parentheses, Python would just look at the function and do nothing with it — like picking up a recipe card and reading the title but not actually cooking.

When this line runs, Python opens your `.env` file, reads the key-value pairs inside it, and writes them onto the operating system's whiteboard (the environment). After this line completes, the API key value is available to the program — but we haven't retrieved it yet. That happens on the next line.

---

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line has two parts: it **retrieves** a value and then **stores** it.

**Retrieving:** `os.getenv("ANTHROPIC_API_KEY")` means "go to the `os` toolbox, use the `getenv` (get environment variable) tool, and look up the value stored under the name `ANTHROPIC_API_KEY`." If `load_dotenv()` worked correctly on Line 3, that name is now on the whiteboard with your actual key as its value. `getenv` reads it off the whiteboard and hands it back.

If the `.env` file was missing or the key wasn't in it, `getenv` hands back a special Python value called `None`. `None` is Python's way of saying "there is nothing here" — like an empty box with a label on it.

**Storing — the concept of a variable:** The `=` sign here does not mean "equals" in the mathematical sense. It means "take whatever is on the right side and store it in a labeled box called whatever is on the left side." The labeled box is called a **variable**. Here, the box is named `api_key`. After this line runs, any time we write `api_key` elsewhere in the notebook, Python opens that box and uses the value inside — which is either our secret key or `None`.

---

**Line 5: `print("API key loaded:", api_key is not None)`**

This line sends a message to the screen so you can verify everything worked.

**What `print()` does:** `print()` is a built-in Python function that displays text or values in the output area below the cell. It is your primary way of seeing what is happening inside your program.

**What `"API key loaded:"` is:** The text inside quotation marks is called a **string** — programmers' jargon for a piece of text. Python treats anything in quotes as literal text to display, not as an instruction to execute.

**What `api_key is not None` means:** This is a question Python answers with either `True` or `False`. It is asking: "Is the value stored in `api_key` something other than `None`?" If your API key was found, `api_key` holds the actual key string, which is not `None`, so Python answers `True`. If the key was missing, `api_key` holds `None`, so Python answers `False`.

`True` and `False` are called **booleans** — a type of value that can only be one of two things. Think of a light switch: it is either on or off, nothing in between.

### What the output means

You will see one of two things:

- `API key loaded: True` — Your `.env` file was found, the key was in it, and you are ready to continue.
- `API key loaded: False` — Something went wrong. Either the `.env` file is missing, is in the wrong folder, or the key is spelled differently inside the file. You should fix this before running any other cells.

---

## Cell 2: Creating the Connection to Claude

```python
from anthropic import Anthropic  # imports the Anthropic SDK client class
client = Anthropic(api_key=api_key)  # instantiates client; all API calls go through this object
print("Anthropic client ready")  # confirms no import/auth errors at construction time
```

### What this cell does

Now that we have the API key, this cell uses it to create a **connection object** — a single, reusable piece of Python code that knows how to talk to Anthropic's servers. Every message you send to Claude in this notebook will pass through this connection object.

---

**Line 1: `from anthropic import Anthropic`**

This brings in the `Anthropic` toolbox — specifically, a tool called `Anthropic` from within it. The `anthropic` package (lowercase) is Anthropic's official **SDK**. SDK stands for Software Development Kit — it is a pre-built collection of code that Anthropic wrote so that you do not have to figure out the raw technical details of connecting to their servers yourself.

Inside this SDK lives a thing called `Anthropic` (uppercase). This is a **class** — think of a class as a blueprint or a mold. Just as a cookie cutter is a mold that you press into dough to produce individual cookies, a class is a blueprint you use to create individual **objects**. Each object made from the blueprint has all the capabilities described in the blueprint.

---

**Line 2: `client = Anthropic(api_key=api_key)`**

This line uses the `Anthropic` blueprint to create one actual connection object and stores it in a variable named `client`.

**The right side — `Anthropic(api_key=api_key)`:** Calling a class name with parentheses, like `Anthropic(...)`, tells Python to use the blueprint to manufacture a new object right now. The thing inside the parentheses, `api_key=api_key`, is an **argument** — information you hand to the blueprint so it can set up the object correctly.

Let's decode `api_key=api_key` because it looks confusing:
- The first `api_key` (before the `=`) is the name of the slot in the blueprint — it is saying "fill in the API key slot."
- The second `api_key` (after the `=`) is the variable we created in Cell 1 — it is the labeled box that contains our actual secret key.

So the whole thing means: "Create a new Anthropic connection object, and use the value stored in our `api_key` variable as its key."

**The left side — `client =`:** The finished object is stored in a variable named `client`. From this point forward, whenever we want to send a message to Claude, we go through `client`. It already has our API key built in, so we do not have to provide it again with each message.

---

**Line 3: `print("Anthropic client ready")`**

This prints the text "Anthropic client ready" to the output area below the cell. It serves as a simple confirmation: if this line prints, it means the previous line ran without errors. If something had gone wrong (for example, the API key was invalid), Python would have stopped and shown an error message before reaching this line.

### What the output means

You should see:

`Anthropic client ready`

This tells you the SDK was installed correctly and the `client` object was created without any authentication errors. You are now ready to send actual messages to Claude.

---

## Cell 3: Smoke Test — Sending Your First Message to Claude

```python
response = client.messages.create(
    model="claude-haiku-4-5",      # fastest/cheapest model — good for smoke tests
    # model="claude-sonnet-4-5",   # uncomment for higher-quality responses
    max_tokens=50,                 # caps output length to avoid runaway costs
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]  # minimal valid payload
)
print(response.content[0].text)   # content is a list of blocks; [0] is the first text block
```

### What this cell does

This sends a real message to Claude and prints what Claude says back. It is called a **smoke test** — a term borrowed from electronics, where you power on a new circuit for the first time just to check if anything catches fire. Here, we are just checking that the whole pipeline works end to end before we build anything more complex.

---

**Lines 1–5: `response = client.messages.create(...)`**

This is the core API call — the moment Python reaches out across the internet to Anthropic's servers, sends your message, and waits for Claude's reply.

**What `client.messages.create()` means:** Using dot notation, we are going inside the `client` object, finding a sub-section called `messages`, and then calling a function inside it called `create`. Think of it like navigating a filing cabinet: `client` is the cabinet, `messages` is a drawer inside it, and `create` is a specific form inside that drawer. Calling `create()` submits that form to Anthropic.

The parentheses span multiple lines here, which is fine in Python. Everything between the opening `(` on Line 1 and the closing `)` on Line 5 is the content of the form you are submitting. Each piece of content is called a **parameter** (a labeled input slot).

**`model="claude-haiku-4-5"`:** This tells Anthropic which version of Claude to use. Anthropic offers several models with different trade-offs between speed, cost, and capability. `claude-haiku-4-5` is the fastest and least expensive — well-suited for testing. The value in quotes is a string that Anthropic uses to identify which model you want, like a product code.

**`# model="claude-sonnet-4-5",`:** The `#` symbol at the start of a line makes it a **comment**. Python ignores everything on a line after a `#`. Comments are notes written for human readers. This comment is showing you an alternative — if you removed the `#` from this line and added a `#` to the line above, you would switch to a more capable (but slower and more expensive) model. Switching between options this way is called **commenting out** a line.

**`max_tokens=50`:** A **token** is the unit Claude uses to measure text — roughly one word or part of a word. Setting `max_tokens=50` is like telling a contractor "your written report must be no longer than 50 words." It puts a hard ceiling on how long Claude's response can be. This is important for controlling costs — without a ceiling, a runaway response could be extremely long and therefore expensive. For a smoke test like this, 50 tokens is more than enough.

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`:**

This is the actual conversation you are sending to Claude. Let's unpack the structure carefully.

The outer square brackets `[...]` define a **list** — an ordered collection of items, like a numbered list on paper. Each item in this list represents one message in the conversation.

Inside the list is a single item: `{"role": "user", "content": "Say hello in one short sentence."}`. The curly braces `{...}` define a **dictionary** — a lookup table where each entry has a **key** (a label) and a **value** (what's stored under that label), separated by a colon. Think of it like a very small form with two fields filled in.

- `"role": "user"` — The key is `role` and the value is `user`. This tells Claude that this message was written by the human in the conversation (as opposed to `"assistant"`, which would mean Claude itself wrote it). Claude uses this to understand who is speaking.
- `"content": "Say hello in one short sentence."` — The key is `content` and the value is the actual text of your message. This is what you are asking Claude.

So the whole `messages` parameter is: a list containing one dictionary, representing one message from the user, asking Claude to say hello in one short sentence.

**`response =`:** Whatever Claude sends back is stored in a variable named `response`. It is not just a string of text — it is a rich object containing Claude's reply, information about which model was used, how many tokens were consumed, and other metadata.

---

**Line 6: `print(response.content[0].text)`**

This navigates inside the `response` object to find Claude's actual text and prints it.

**`response.content`:** Using dot notation, we go inside the `response` object and open the part called `content`. The `content` is a **list** of message blocks — Claude's response can technically contain multiple blocks (for example, text followed by an image description). In most cases there will be just one block.

**`[0]`:** The `[0]` is how we pick a specific item from a list by its position. Lists in Python are **zero-indexed**, meaning the first item is at position 0, not position 1. So `response.content[0]` means "give me the first block in the content list." Think of it like floors in a European building, where the ground floor is called Floor 0.

**`.text`:** Going one level deeper with dot notation, `.text` is the actual text of that first block — the string of words Claude wrote. This is what we want to display.

`print()` then displays that text below the cell.

### What the output means

You will see a short greeting from Claude — something like:

`Hello! It's wonderful to meet you today.`

The exact wording may vary slightly, but it will be a single, brief sentence. If you see this, your entire setup is working: your API key is valid, the connection was made, Claude received the message, and Python successfully navigated the response to extract the text. Every cell after this one builds on this working foundation.

---

## Cell 4: The `ask()` Helper Function — A Reusable Way to Talk to Claude

```python
usage_log = []  # session-level list; every API call appends one dict here

def ask(
    prompt: str,
    system: str = "",
    model: str = "claude-haiku-4-5",
    max_tokens: int = 512,
    temperature: float = 0.0,
) -> str:
    """
    Send a single-turn prompt and return the text reply.

    Args:
        prompt:      The user-turn message text.
        system:      Optional system prompt (persona / standing instructions).
        model:       Claude model identifier string.
        max_tokens:  Maximum tokens Claude may produce.
        temperature: Sampling temperature (0 = deterministic).

    Returns:
        The assistant reply as a plain Python string.
    """
    # --- build API call kwargs ---
    kwargs = dict(
        model=model,           # selects wh
```

*(Note: the notebook source appears to be truncated at this point. The explanation below covers every line that is present, and then explains what the remaining function structure would typically contain.)*

### What this cell does

This cell creates a reusable tool — a custom function called `ask()` — that wraps all the complexity of calling the Claude API into a tidy, easy-to-use package. Instead of writing out `client.messages.create(...)` with all its parameters every single time you want to ask Claude something, you can call `ask("your question here")` and the function handles the rest. It also keeps a running log of every API call so you can track how many tokens you are using.

---

**Line 1: `usage_log = []`**

This creates an empty list and stores it in a variable named `usage_log`.

The square brackets `[]` with nothing inside them represent an empty list — like a blank sheet of paper with column headers but no rows yet. The comment tells us its purpose: every time the `ask()` function is called, it will add one record to this list containing information about that call (how many tokens were used, which model was used, etc.). At the end of a session, you could look through `usage_log` to see a history of every API call made.

Crucially, this variable is created **outside** the function definition. That means it exists at the top level of your notebook session and persists across all function calls. If it were defined inside the function, it would be wiped clean every time the function ran.

---

**Lines 3–9: `def ask(...) -> str:`**

This is the **function definition** — the declaration that a new recipe named `ask` exists and a specification of what ingredients it accepts.

**What `def` means:** `def` is short for "define." It is Python's keyword for saying "I am about to describe a new function. Here is its name and its ingredients."

**The function name — `ask`:** The name `ask` is chosen to be descriptive. It tells any human reading the code exactly what this function does: you ask it a question and it gives you an answer.

**The ingredients — parameters:** Everything inside the parentheses `(...)` after `ask` is a list of **parameters** — the inputs the function needs in order to do its job. Think of parameters like the blank lines on a form: the form exists as a fixed structure, but you fill in different values each time you use it.

Let's look at each parameter:

- **`prompt: str`** — A required input named `prompt`. The `: str` part is a **type hint** — it is a note to human readers (and some tools) saying "this input should be a string (text)." Python does not enforce type hints automatically, but they make the code much easier to understand. `prompt` is the actual question or instruction you want to send to Claude.

- **`system: str = ""`** — An optional input named `system`. The `= ""` part is a **default value** — it means "if the person calling this function does not provide a `system` value, use an empty string as the default." An empty string `""` is a string with nothing in it. The `system` parameter is for giving Claude a persona or standing instructions before the conversation begins — more on this in a moment.

- **`model: str = "claude-haiku-4-5"`** — An optional input with a default value of `"claude-haiku-4-5"`. If you do not specify a model when calling `ask()`, it will use Haiku. You can override this for a specific call by writing `ask("your question", model="claude-sonnet-4-5")`.

- **`max_tokens: int = 512`** — An optional input with a default of 512. The `: int` hint means this should be a whole number (integer). 512 tokens is roughly 380 words — generous enough for most responses but still controlled.

- **`temperature: float = 0.0`** — An optional input with a default of 0.0. The `: float` hint means this should be a decimal number. **Temperature** controls how creative or random Claude's responses are. Think of it like a dial on a music mixer. At `0.0` (the lowest setting), Claude is completely **deterministic** — given the same prompt, it will give the same answer every time, always choosing the most likely next word. At `1.0` (the highest setting), Claude is much more **creative** and varied — more willing to take unexpected turns. For evaluation and testing, `0.0` is preferred because repeatable results are easier to compare.

**`-> str:`:** The arrow `->` and the word `str` after the closing parenthesis is a **return type hint** — it tells readers that this function will hand back a string when it is done. The colon `:` at the very end signals that the function body (the actual instructions) begins on the next line.

---

**Lines 10–22: The docstring `"""..."""`**

Everything between the triple quotation marks `"""` is a **docstring** — short for "documentation string." It is a detailed note written inside the function itself, explaining what the function does, what each parameter means, and what it returns. Python treats docstrings as a special kind of text — they do not get executed as code, but they can be retrieved later by documentation tools or by typing `help(ask)` in Python.

Think of a docstring as the instruction manual glued to the inside cover of a toolbox. The tools work whether or not you read the manual, but the manual makes it far easier to use the tools correctly.

The `Args:` section lists and describes each parameter. The `Returns:` section describes what you get back when the function is done. Writing docstrings like this is considered good professional practice.

---

**Lines 24–26: `kwargs = dict(model=model, ...)`**

*(Note: the cell is cut off mid-line, so we can only see the beginning of this block.)*

**What `kwargs` is