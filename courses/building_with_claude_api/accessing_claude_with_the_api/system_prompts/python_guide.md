# Python Guide: System Prompts & Error Handling — CCA Exam Prep Lab

A plain-English walkthrough of every line of code in the JupyterLab notebook.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving a computer a precise set of instructions, written in something that looks a little like English. Think of it like writing a very detailed recipe — the computer follows each step exactly, in order, without guessing what you mean. This notebook is made up of "cells," each of which contains a small group of related instructions. When you "run" a cell, the computer executes every instruction in it from top to bottom. The results appear directly below the cell.

---

## Cell 1: Loading Your Secret API Key

```python
from dotenv import load_dotenv  # loads variables from .env file into os.environ
import os                        # lets us read environment variables

load_dotenv()                    # reads .env in the current working directory
api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the key (None if missing)
print("API key loaded:", api_key is not None)  # True = key found, False = check .env
```

### What this code does

---

**Line 1: `from dotenv import load_dotenv`**

This line reaches into an external **toolkit** called `dotenv` and pulls out one specific **tool** called `load_dotenv`.

- **Toolkit (library/package):** Python comes with a lot of built-in abilities, but you can also install extra toolkits that other developers have written. `dotenv` is one such toolkit. Its whole job is to help you safely manage secret values — like passwords or API keys — without hard-coding them into your script where anyone could read them.
- **Import:** Think of "importing" like opening a toolbox and taking out one specific wrench before you start working. You don't take out the whole toolbox — just the tool you need. Here, the tool we're taking out is `load_dotenv`.
- **Why we need it:** Your API key is a secret password that gives you access to Anthropic's AI. If you typed it directly into your code and then shared that code, anyone who read it could use your account and run up your bill. Instead, we store the key in a separate hidden file called `.env`, and this tool reads it for us at runtime.

---

**Line 2: `import os`**

This line imports another toolkit — this one called `os`, which stands for "operating system."

- **The `os` module:** This is a built-in Python toolkit (it comes pre-installed, so you don't need to download it). It lets your Python code talk to the computer's operating system — things like reading files, checking folder paths, and in this case, reading **environment variables**.
- **Environment variables:** Think of these as sticky notes that your computer's operating system keeps in memory. They hold configuration values — things like your username, system settings, or in this case, your API key — that programs can read without those values being written directly into the program's code. `load_dotenv` (from Line 1) will write our API key onto one of those sticky notes; `os` will help us read it back.

---

**Line 3: `load_dotenv()`**

This line actually *calls* (runs) the `load_dotenv` tool we imported in Line 1.

- **Calling a function:** A **function** is like a named recipe. Importing it (Line 1) just puts the recipe card on your desk. Calling it — by writing its name followed by parentheses `()` — is what actually starts cooking. Here, we're running the recipe with no special instructions, so it uses its defaults.
- **What it does:** `load_dotenv()` looks in your current working folder (the folder where this notebook lives) for a file named `.env`. That file looks something like this:
  ```
  ANTHROPIC_API_KEY=sk-ant-abc123...
  ```
  It reads that file and loads its contents into the operating system's memory as environment variables — those sticky notes we mentioned. After this line runs, your API key is sitting in memory, ready to be retrieved.
- **Why a separate `.env` file?** Because when you share your code (or upload it to GitHub), you can tell the system to *ignore* the `.env` file. Your code gets shared; your secret key stays private.

---

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line reads the API key from memory and stores it in a **variable** called `api_key`.

- **Variable:** Think of a variable as a labeled box. You give the box a name (`api_key`), and you put something inside it (the actual key string). From this point on, whenever you write `api_key` in your code, Python knows to open that box and use whatever is inside.
- **`os.getenv("ANTHROPIC_API_KEY")`:** This is calling (running) the `getenv` function from the `os` toolkit. The dot (`.`) between `os` and `getenv` means "use the `getenv` tool from inside the `os` toolkit." You're saying: "Look in the operating system's memory for a sticky note labeled `ANTHROPIC_API_KEY`, and give me whatever is written on it."
- **`"ANTHROPIC_API_KEY"`:** This is a **string** — a piece of text. In Python, text is always wrapped in quote marks so that Python knows it's a value to use, not a command to execute. This string must match the name written in your `.env` file exactly, including capitalization.
- **What if the key is missing?** If `load_dotenv()` couldn't find the `.env` file, or if the file didn't contain `ANTHROPIC_API_KEY`, then `os.getenv()` returns a special Python value called `None` — which essentially means "nothing." The variable `api_key` would then hold `None`.

---

**Line 5: `print("API key loaded:", api_key is not None)`**

This line prints a message to the screen so you can immediately see whether the key was found.

- **`print()`:** This built-in Python function displays things on the screen. Whatever you put inside the parentheses gets shown below the cell when you run it. You can print multiple things by separating them with commas.
- **`"API key loaded:"`:** This is a text label — a string — that gets printed first. It's just there to make the output human-readable.
- **`api_key is not None`:** This is a **comparison expression** — a question Python evaluates and answers with either `True` or `False`. You're asking: "Is `api_key` something other than `None`?" If the key was found and loaded, `api_key` holds the actual key string, so the answer is `True`. If the key was missing, `api_key` holds `None`, so the answer is `False`.
- **`True` vs `False`:** These are called **boolean values** — named after mathematician George Boole. They represent yes/no, on/off, found/not-found answers. Python always capitalizes them.

### What the output means

You will see one of these two results:

- `API key loaded: True` — Everything worked. Your `.env` file was found, and the key was loaded into memory. You're ready to proceed.
- `API key loaded: False` — Something went wrong. Either your `.env` file doesn't exist, it's in the wrong folder, or the variable name inside it is spelled differently than `ANTHROPIC_API_KEY`. You need to fix this before the rest of the notebook will work.

---

## Cell 2: Creating the Anthropic Client

```python
from anthropic import Anthropic  # the official Anthropic Python SDK

client = Anthropic(api_key=api_key)  # creates a reusable client bound to our key
print("Anthropic client ready")       # confirms no import or auth error so far
```

### What this code does

---

**Line 1: `from anthropic import Anthropic`**

This line reaches into the `anthropic` toolkit and pulls out a tool called `Anthropic` (capital A).

- **The `anthropic` library:** This is a toolkit created and published by Anthropic (the company that makes Claude). It handles all the technical complexity of making requests to Anthropic's servers — formatting the data correctly, sending it over the internet, receiving the response, and translating it into Python objects you can work with. Without this library, you'd have to write all that networking code yourself.
- **SDK:** You may see this called a "Software Development Kit." Think of it as a pre-built remote control specifically designed to operate one particular device — in this case, Anthropic's Claude AI.
- **Why capital `Anthropic`?** In Python, names that start with a capital letter typically indicate a **class** — a blueprint for creating a reusable object. We'll use this blueprint on the very next line.

---

**Line 2: `client = Anthropic(api_key=api_key)`**

This line creates a **client object** — your personal, pre-configured connection to Anthropic's API — and stores it in a variable named `client`.

- **API:** An **Application Programming Interface** is a system that lets one piece of software talk to another. Think of it like a waiter in a restaurant: you (your code) tell the waiter (the API) what you want, the waiter takes your order to the kitchen (Anthropic's servers), and brings back what you asked for (Claude's response). You never go into the kitchen yourself.
- **`Anthropic(api_key=api_key)`:** This creates a new instance of the `Anthropic` blueprint. The part inside the parentheses, `api_key=api_key`, is a **keyword argument** — a named setting you're passing in. You're saying: "Build me an Anthropic client, and configure it to use the key stored in my `api_key` variable." The first `api_key` (before the `=`) is the name of the setting that the `Anthropic` class expects. The second `api_key` (after the `=`) is the variable we defined in Cell 1 containing the actual key value.
- **Why create a `client`?** Once created, this `client` object remembers your API key and handles all the technical setup automatically. Instead of re-authenticating on every request, you just use `client` and it already knows who you are. Think of it like logging into a website once and staying logged in — as opposed to entering your password every time you click a link.
- **Object:** An **object** in Python is a self-contained unit that bundles together data (like your API key) and capabilities (like the ability to send messages). The variable `client` holds this object.

---

**Line 3: `print("Anthropic client ready")`**

This prints a confirmation message to the screen.

- This is a **sanity check** — a simple test to verify that nothing exploded. If there had been a problem (for example, if the `anthropic` library wasn't installed, or if your API key format was invalid enough to cause an immediate error), Python would have shown a red error message instead of reaching this `print` statement.
- Seeing this message printed means the object was created without obvious errors. Note that it doesn't guarantee the key is *valid* — that will only be confirmed when we make our first actual API call in Cell 3.

### What the output means

- `Anthropic client ready` — The client object was created without errors. The library is installed and the key was accepted at the construction stage.
- A red error block instead — Most commonly this means the `anthropic` library isn't installed (fix: run `pip install anthropic` in your terminal) or the `api_key` variable is `None` and the Anthropic class refused it.

---

## Cell 3: The Smoke Test — Sending Your First Message to Claude

```python
response = client.messages.create(
    model="claude-haiku-4-5",   # fast, cheap model — ideal for smoke tests
    max_tokens=50,               # hard ceiling on output length
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]
)
print(response.content[0].text)  # content is a list; [0] is the first (only) block
```

### What this code does

This cell sends a real message to Claude and prints the reply. It's called a **smoke test** — a minimal check to confirm the whole system is wired up correctly before you build anything more complex. The name comes from electronics: if you turn on a circuit and it doesn't start smoking, it's probably okay.

---

**Lines 1–4: `response = client.messages.create(...)`**

This is a single instruction that spans multiple lines. Python knows it isn't finished until the parentheses are closed, so it treats all of it as one command.

- **`client.messages.create(...)`:** Here we're using our `client` object from Cell 2. The dots are navigation: `client` → look inside it for `messages` → look inside that for `create`. `create` is a **function** (a recipe) that sends a message to the Claude API and returns the response.
- **`response =`:** The reply that comes back from Claude gets stored in a variable called `response`. This variable will hold a complex object containing Claude's text, usage statistics, and other metadata (extra information about the transaction).
- **`model="claude-haiku-4-5"`:** This is a keyword argument telling the API *which* Claude model to use. Anthropic offers several models at different price and capability levels. `claude-haiku-4-5` is described in the notebook comments as "fast, cheap" — ideal for quick tests because you're not wasting money on an expensive model just to verify your connection works.
- **`max_tokens=50`:** This sets a **hard ceiling** on how long Claude's response can be.
  - **Token:** A token is roughly one word or word-fragment that the AI reads or generates. "Hello" is one token. "Unbelievable" might be two or three tokens. Setting `max_tokens=50` means Claude will stop generating after 50 tokens even if it has more to say. For a simple smoke test asking for "one short sentence," 50 tokens is more than enough while preventing any runaway long response.
  - This parameter is **required** by Anthropic's API — you must always tell it the maximum you're willing to receive.
- **`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`:** This is the actual conversation you're sending.
  - **List:** The square brackets `[...]` create a **list** — an ordered collection of items, like a numbered list on paper. Here, the list has just one item.
  - **Dictionary:** The curly braces `{...}` create a **dictionary** — a lookup table where every piece of information has a label (called a **key**) and a value. Think of it like a form: `role:` [user], `content:` [your message]. Each `key: value` pair is separated by a colon, and multiple pairs are separated by commas.
  - **`"role": "user"`:** This tells the API who is speaking. Claude's API uses a conversation format that alternates between `"user"` (you) and `"assistant"` (Claude). Every message must be labeled with its speaker.
  - **`"content": "Say hello in one short sentence."`:** This is the actual text of your message to Claude.
  - **Why a list of dictionaries?** Because a conversation can have multiple turns — you say something, Claude responds, you say something else, Claude responds again. Each turn is one dictionary in the list. Here we only have one turn, but the structure is built to support many.

---

**Line 5: `print(response.content[0].text)`**

This line navigates into the response object to find Claude's actual reply text and print it.

- **`response.content`:** The `response` object is like a box containing several compartments. `.content` opens the compartment that holds Claude's reply. This compartment is a **list** — because in more advanced use cases, a response might include multiple "blocks" (text, images, tool calls, etc.).
- **`[0]`:** This retrieves the **first item** from the list. In Python, lists are indexed starting from zero, not one. So `[0]` means "give me item number one" (which is stored at position zero). Since our simple request produced only one block of text, `[0]` is the only item in the list.
- **`.text`:** The item at position `[0]` is itself a structured object. `.text` retrieves just the plain text string from within it — the actual words Claude wrote.
- **The whole chain `response.content[0].text`:** Think of it as opening a set of nested boxes. The `response` is the outer box. Inside is `.content` (the content compartment). Inside that is a list, and `[0]` grabs the first item. Inside that item is `.text` — the actual words. Then `print()` displays those words on screen.

### What the output means

You should see something like:

`Hello! It's wonderful to meet you today.`

This is Claude's actual reply. If you see a short, friendly sentence, your entire setup is working end-to-end: your key is valid, your client is configured correctly, the API accepted your request, and the response came back successfully. If you see a red error block, the error message will usually tell you what went wrong (invalid key, rate limit, network issue, etc.).

---

## Cell 4: The `ask()` Helper Function

```python
usage_log = []  # session-wide list; every API call appends one entry here

def ask(prompt, system=None, model="claude-haiku-4-5", max_tokens=256):
    """
    Send a single-turn message and return the assistant's reply text.

    Parameters
    ----------
    prompt     : str   — the user message
    system     : str | None — optional system prompt (omitted if None)
    model      : str   — Anthropic model ID
    max_tokens : int   — hard output-token ceiling

    Returns
    -------
    str — the assistant's reply
    """
    # Build the base kwargs dict with always-required parameters
    kwargs = {
        "model": model,           # which Claude model to call
        "max_tokens": max_tokens,  # prevents runaway output; required by API
        "messages": [{"role": "user", "content": prompt}],  # messages must be a list of dicts
    }
    # Only add 'system' key when we actually have a non-None string.
    # Passing system=None explicitly raises a validation error because the
```

### What this code does

This cell does two things: it sets up a session-wide tracking list, and it defines a reusable **helper function** called `ask()`. A helper function is a custom recipe you write once and can call (run) as many times as you want throughout the rest of the notebook. This one wraps the process of calling Claude's API so you don't have to retype all those parameters every time.

> **Note:** The notebook cell shown above is truncated — the function definition continues in the next cell. The explanation below covers all the lines that are visible.

---

**Line 1: `usage_log = []`**

This creates an empty list called `usage_log` and places it in memory.

- **List:** The square brackets `[]` with nothing inside them create an empty list — like starting with a blank numbered list on paper. As the session continues and you make more API calls, this list will grow. Each entry will record information about one API call — specifically, how many tokens were used.
- **Why track usage?** Anthropic charges by the token. Knowing how many tokens each call uses helps you understand the cost of different prompting strategies. This is a real-world professional skill when building AI-powered applications.
- **Session-wide:** This variable is defined *outside* the `ask()` function, which means it persists for the entire time the notebook is running. Every call to `ask()` will be able to see and add to this list. Think of it as a shared ledger that all parts of the notebook can write into.

---

**Line 2: `def ask(prompt, system=None, model="claude-haiku-4-5", max_tokens=256):`**

This line *defines* (creates) a new function called `ask`. The word `def` is short for "define."

- **`def`:** This keyword tells Python: "I'm about to describe a new recipe. Remember it by this name so I can use it later."
- **`ask`:** The name of the function. You chose this name. It could have been called anything, but `ask` is descriptive — you're "asking" Claude something.
- **Parameters (the items inside the parentheses):** Parameters are the ingredients your recipe accepts. When you call `ask()` later, you'll provide actual values for these ingredients:
  - **`prompt`:** The text of your message to Claude. This is required — there's no default value, so you must always provide it.
  - **`system=None`:** An optional system prompt (instructions given to Claude before the conversation begins — more on this shortly). The `=None` part means: if you don't provide a system prompt, Python will use the default value of `None` (meaning "nothing").
  - **`model="claude-haiku-4-5"`:** Which Claude model to use. The default is already set to `claude-haiku-4-5`, so if you don't specify a model when calling `ask()`, it uses this one automatically.
  - **`max_tokens=256`:** The maximum length of Claude's reply, measured in tokens. Default is 256.
- **Default values:** Parameters with `=something` are optional — the function works fine without them, falling back to those defaults. Parameters without defaults (like `prompt`) are required.
- **The colon `:`:** In Python, a colon at the end of a line means "what follows is the body of this block." Everything indented beneath this line is the content of the function.

---

**Lines 3–15: The docstring (the triple-quoted block)**

```python
    """
    Send a single-turn message and return the assistant's reply text.
    ...
    """
```

This is a **docstring** — documentation written directly inside the function.

- **Triple quotes `"""`:** Three quotation marks in a row allow you to write text that spans multiple lines. Python doesn't execute this text as code; it stores it as documentation attached to the function.
- **Why write it?** It serves as an in-code instruction manual. Any developer (or your future self) who looks at this function can immediately understand what it does, what it accepts, and what it gives back — without having to read through all the code line by line.
- **`Parameters` section:** Lists each ingredient (parameter) the function accepts, along with its expected type and a description.
  - **`str`:** Short for "string" — meaning a piece of text.
  - **`int`:** Short for "integer" — a whole number.
  - **`str | None`:** This means the value can be either a string or `None`.
- **`Returns` section:** Describes what the function hands back when it finishes. In this case, it returns a `str` — the text of Claude's reply.

---

**Line 16: `kwargs = {`**

This begins creating a dictionary called `kwargs` (short for "keyword arguments").

- **Dictionary:** A dictionary is a lookup table where each item has a label (key) and a value, like entries in a contact book: Name → Phone Number. Here, the keys are parameter names the API expects (`"model"`, `"max_tokens"`, `"messages"`), and the values are what we want to set for each.
- **Why use a dictionary here?** Later, this dictionary will be "unpacked" and sent to the `client.messages.create()` function all at once. Building it as a dictionary first makes it easy to add or remove parameters conditionally (like the optional `system` parameter) before sending.
- **`kwargs`:** This is a conventional name in Python for a collection of keyword arguments that you're going to pass to a function. It's not a special Python keyword — it's just a naming convention that experienced developers recognize immediately.

---

**Line 17: `"model": model,`**

This adds the model identifier to the `kwargs` dictionary.

- The key `"model"` (in quotes, so it's a text label) maps to the value stored in the `model` parameter (no quotes, so it's the variable). Recall that `model` defaults to `"claude-haiku-4-5"` unless the caller specifies something different.

---

**Line 18: `"max_tokens": max_tokens,`**

This adds the token ceiling to the dictionary, using whatever value was passed in for `max_tokens` (defaulting to 256).

---

**Line 19: `"messages": [{"role": "user", "content": prompt}],`**

This constructs the messages list and adds it to the dictionary.

- The structure is the same as in Cell 3: a list (`[...]`) containing one dictionary (`{...}`), with `"role"` set to `"user"` and `"content"` set to whatever text was passed in as `prompt`.
- This function only handles **single-turn** conversations (one message from the user, one reply from Claude). Multi-turn conversations — where you accumulate a history of messages — are handled differently elsewhere in the notebook.

---

**Lines 21–23: Comment block about the `system` parameter**

```python
    # Only add 'system' key when we actually have a non-None string.
    # Passing system=None explicitly raises a validation error because the
```

These lines (starting with `#`) are **comments** — notes for humans. Python completely ignores them when running the code.

- **Comment:** A line beginning with `#` is a comment. It exists solely to explain the intent of the code to anyone reading it. This comment is telling us: don't just blindly add `system` to the dictionary. If its value is `None`, we must leave it out entirely. Anthropic's API will throw an error if you explicitly pass `system=None`. You must either provide a real string or omit the key altogether.

### What the output means

This cell produces **no visible output** when you run it. Defining a function doesn't execute it — it just stores the recipe in memory. You'll see `ask()` being called (actually used) in later cells, and that's when it will produce output. Think of this cell as writing the recipe card, not cooking the dish.

---

## Glossary

**API (Application Programming Interface)**
A system that lets one piece of software talk to another over the internet. Like a waiter who takes your order to the kitchen (the server) and brings back what you asked for. You never interact with the kitchen directly.

**Argument**
A value you provide to a function when you call it. In `ask("Hello")`, the string `"Hello"` is an argument.

**Boolean**
A value that is either `True` or `False`. Named after mathematician George Boole. Used for yes/no questions in code, like "did the key load correctly?"

**Class**
A blueprint for creating objects. The `Anthropic` class is a blueprint; `client = Anthropic(...)` creates a specific object from that blueprint.

**Client (object)**
A pre-configured connection to an external service. In this notebook, `client` is an object that knows your API key and handles all the technical details of communicating with Anthropic's servers.

**Comment**
A line in Python code that starts with `#`. The computer ignores it completely. Comments exist only to explain the code to human readers.

**Default value**
A fallback value for a function parameter. If the caller doesn't provide a value, Python uses the default. In `def ask(model="claude-haiku-4-5")`, the default model is `claude-haiku-4-5`.

**Dictionary**
A lookup table in Python, created with curly braces `{}`. Each entry is a key-value pair: `"name": "Alice"`. Like a phonebook: look up a name (key) to get a number (value).

**Docstring**
A block of text written inside triple quotes `"""..."""` at the start of a function. It documents what the function does, what it accepts, and what it returns. Python stores it but doesn't execute it.

**Environment variable**
A piece of information stored in the operating system's memory — like a sticky note the OS keeps. Programs can read these values without having them written into the program's source code. Used to keep secrets like API keys out of shared code.

**`.env` file**
A plain text file in your project folder that stores secret values like API keys. Each line looks like `KEY_NAME=value`. The `dotenv` library reads this file and makes its values available as environment variables.

**Function**
A named, reusable recipe. You define it once with `def`, and then you can run it as many times as you want by calling its name with parentheses: `ask()`.

**Import**
Bringing a tool or set of tools from an external toolkit (library) into your current script. Like taking a specific wrench out of a toolbox before you start work.

**Index**
A number used to retrieve an item from a list by its position. Python counts from zero, so `[0]` is the first item, `[1]` is the second, and so on.

**Instance**
A specific object created from a class blueprint. `client = Anthropic(api_key=api_key)` creates one instance of the `Anthropic` class.

**Integer (`int`)**
A whole number, with no decimal point. Examples: `50`, `256`, `0`, `-7`.

**Keyword argument**
A way of passing a value to a function by naming the parameter explicitly: `model="claude-haiku-4-5"`. This makes code more readable and allows arguments to be given in any order.

**Library / Package / Toolkit**
A collection of pre-written Python code that you can import and use. `anthropic` and `dotenv` are both libraries.

**List**
An ordered collection of items in Python, created with square brackets `[...]`. Like a numbered list on paper. Items can be retrieved by their position (index).

**`None`**
A special Python value meaning "nothing" or "no value." It's what you get when a variable has never been assigned a real value, or when a function returns nothing.

**Object**
A self-contained unit in Python that bundles together data and capabilities (functions). The `client` variable holds an object that knows your API key and can send messages to Claude.

**Parameter**
A named ingredient that a function accepts. Defined in the `def` line. When the function is called, actual values (arguments) are provided for each parameter.

**`print()`**
A built-in Python function that displays its contents on the screen, below the cell in JupyterLab. Used to show results, confirm that steps worked, and help with debugging.

**SDK (Software Development Kit)**
A pre-built toolkit for working with a specific platform or service. The `anthropic` library is Anthropic's SDK for Python — a remote control specifically designed for their AI.

**Smoke test**
A minimal test to check whether a system is working at all, before doing anything complex. Named after electronics: if a circuit doesn't start smoking when you turn it on, it's probably okay.

**String (`str`)**
A piece of text in Python, always wrapped in quote marks: `"like this"` or `'like this'`. The quotes tell Python it's a text value, not a command.

**System prompt**
A set of instructions given to Claude *before* the conversation begins, using the `system` parameter. It shapes how Claude behaves throughout the entire conversation — its persona, tone, limitations, and focus.

**Token**
The unit by which AI language models read and generate text. Roughly one word or word-fragment. "Hello" ≈ 1 token. "Unbelievable" ≈ 3 tokens. APIs charge by the token, and `max_tokens` controls how many the model is allowed to produce in a response.

**Variable**
A labeled box in Python's memory that holds a value. `api_key = "abc123"` creates a box labeled `api_key` and puts the text `"abc123"` inside it. Later, whenever you write `api_key`, Python opens that box and uses what's inside.