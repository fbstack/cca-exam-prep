# Python Guide: Being Clear and Direct with the Claude API

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving precise, step-by-step instructions to a computer. Think of it like writing a recipe: each line is one instruction, and the computer follows them in order, top to bottom. Python is especially popular in data work and AI because it reads almost like plain English and has a vast library of pre-built tools you can borrow. In this lab, those tools let us talk to an AI model (Claude) through the internet, send it questions, and read back its answers — all from inside a notebook.

---

## Cell 1: Loading your API key safely from a hidden file

```python
# ── Starter Cell 1: Load environment variables ──────────────────────────────
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

**Line 1: `# ── Starter Cell 1: Load environment variables ──`**

This line is a **comment** — a note written for human readers that Python completely ignores when running the code. You can tell it's a comment because it starts with a `#` symbol. Comments are used throughout this notebook to label sections and explain intent. They have zero effect on what the computer does; they exist entirely for you.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack three things here: what an **import** is, what **dotenv** is, and what **load_dotenv** is.

- **Import** means "bring a tool from an external toolbox into my current workspace." Python comes with many built-in tools, but the community has also built thousands of add-on packages — collections of pre-written code that solve specific problems. To use one, you have to import it first. Think of it like pulling a specific wrench from a tool chest: the wrench exists in the chest, but you need to physically pick it up before you can use it.

- **dotenv** (short for "dot env") is one of those add-on packages. Its entire job is to read a special file called `.env` that lives in your project folder. That file holds sensitive information — like passwords and API keys — in a format that keeps them out of your main code. The dot at the start of `.env` is a naming convention that signals "this is a configuration or hidden file."

- **load_dotenv** is a specific **function** inside the dotenv package. A function is a named set of instructions you can call by name to execute — like a recipe card. `from dotenv import load_dotenv` means: "From inside the dotenv toolbox, bring me specifically the `load_dotenv` recipe card."

Why does this matter? Because your **API key** — the secret password that lets you use Claude — must never be written directly in your code. If it were, anyone who saw your code (on GitHub, in a shared notebook, in a screenshot) could steal it and rack up charges on your account. The `.env` file is kept private, never shared, and never uploaded to the internet.

---

**Line 3: `import os`**

This imports a module called **os**. A **module** is just a file of pre-written Python code. `os` is short for "operating system," and it's a standard module that comes built into Python — no installation needed.

The `os` module acts as a bridge between your Python code and the underlying operating system (Windows, Mac, Linux). One of its many jobs is giving you access to **environment variables** — a system-level key-value store that the operating system maintains in the background. Think of environment variables as a locked filing cabinet in the wall of your computer: you can put secrets in there, and programs can retrieve them by name without those secrets appearing in any code file.

Notice this line uses `import os` rather than `from os import something`. This is a slightly different style: it imports the whole `os` toolbox at once, and you'll access things inside it later using the format `os.something`.

---

**Line 5: `load_dotenv()`**

This actually *calls* (executes) the `load_dotenv` function we imported on line 2. The empty parentheses `()` are how you tell Python "run this function now." Think of it as pressing the "start" button on a recipe.

What does it do? It looks for a file called `.env` in the folder where your notebook is saved. It reads every line in that file, which will be in the format `KEY=VALUE` (for example, `ANTHROPIC_API_KEY=sk-ant-abc123`). It then takes each of those key-value pairs and loads them into Python's environment variables — the same filing cabinet that `os` can access.

After this line runs, your API key is sitting safely in memory as an environment variable, ready to be retrieved. It's never written into the notebook itself.

---

**Lines 6–7 (the blank line and comment `# injects each KEY=VALUE pair into os.environ`)**

These are spacing and a comment for readability — Python ignores them both. Good code is written to be read by humans as well as computers, and blank lines help group related instructions visually.

---

**Line 8: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line does two things: it *retrieves* a value and *stores* it in a **variable**.

- **Variable**: Think of a variable as a labeled box. You put something inside it and give the box a name so you can refer to it later. Here, the box is named `api_key`.

- **`os.getenv("ANTHROPIC_API_KEY")`**: This calls the `getenv` function from the `os` module. `getenv` means "get environment variable." It looks in the operating system's filing cabinet for a key named exactly `"ANTHROPIC_API_KEY"` and returns whatever value is stored there. If `load_dotenv()` did its job on line 5, that value will be your secret API key string.

- The `=` sign here is not asking "are these equal?" — it's an **assignment**. It means "take whatever is on the right side and put it in the box on the left." So after this line, the box named `api_key` holds your API key string.

- If the key was *not* found in the `.env` file, `os.getenv` returns a special value called `None` — Python's way of saying "nothing is here." Importantly, this does not crash the program; it just means the box holds the value "nothing."

---

**Line 11: `print("API key loaded:", api_key is not None)`**

This line prints a message to the screen so you can see whether things worked.

- **`print()`** is one of Python's most fundamental built-in functions. It displays whatever you put inside the parentheses as text output below the cell.

- **`"API key loaded:"`** is a **string** — a piece of text surrounded by quotation marks. Python treats anything in quotes as literal text, not as code.

- **`api_key is not None`** is a **boolean expression** — a question with a yes/no answer. It's asking: "Is the value inside the `api_key` box something other than 'nothing'?" If your key was found and loaded, this evaluates to `True`. If it's missing, it evaluates to `False`.

- `print()` here receives two things separated by a comma: the text label and the True/False result. It will display them side by side.

### What the output means

If everything worked, you'll see:
```
API key loaded: True
```
This means your `.env` file was found, it contained a value for `ANTHROPIC_API_KEY`, and that value is now stored in memory as `api_key`. You haven't connected to the internet yet — this is just confirming the local setup is correct.

If you see `API key loaded: False`, your `.env` file is missing, is in the wrong folder, or the key inside it is spelled differently than `ANTHROPIC_API_KEY`. Nothing will work until this shows `True`.

---

## Cell 2: Creating a reusable connection to the Claude API

```python
# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────
from anthropic import Anthropic  # imports the official Python SDK client class

client = Anthropic(api_key=api_key)  # creates a reusable client instance;
                                     # all future API calls go through this object

print("Anthropic client ready")       # quick sanity-check — no network call yet
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise the Anthropic client ─`**

Another comment — a section label for human readers. Python ignores it entirely.

---

**Line 2: `from anthropic import Anthropic`**

This imports the `Anthropic` **class** from a package also called `anthropic`.

- **Package**: A package is a collection of Python code files bundled together and installable as a unit. The `anthropic` package is the official Python software development kit (SDK) that Anthropic (the company that makes Claude) has published. It wraps up all the complexity of making internet requests to Claude's servers into easy-to-use Python commands.

- **Class**: A class is a blueprint for creating objects. Think of a class like a cookie cutter: the cutter itself is the class, and each cookie you stamp out is an **instance** (a specific object created from that blueprint). Here, `Anthropic` is a blueprint for creating a "connection object" that knows how to talk to the Claude API.

- **`from anthropic import Anthropic`**: From inside the `anthropic` package, bring me specifically the `Anthropic` class blueprint.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This creates an actual connection object (an **instance** of the `Anthropic` class) and stores it in a variable called `client`.

- **`Anthropic(api_key=api_key)`**: The parentheses here mean "make a new object from this blueprint." The part inside the parentheses, `api_key=api_key`, is passing information to the blueprint as it creates the object. Specifically, it's saying: "When you build this connection object, give it the API key stored in my `api_key` variable."

- Notice `api_key=api_key` looks a bit odd — the same word twice. The left side (`api_key=`) is the *name of the setting* as the `Anthropic` class expects it. The right side (`api_key`) is the *variable we defined in the previous cell* that holds our actual key string. They happen to share a name, but they refer to different things.

- **`client =`**: The resulting object — a fully configured, ready-to-use connection to Claude — is stored in a variable named `client`. From this point forward, every time we want to send a message to Claude, we'll use `client` to do it.

Think of `client` as a telephone that's already dialed into Claude's office, with your credentials verified. You don't need to redial or re-authenticate every time — you just pick up `client` and start talking.

---

**Line 7: `print("Anthropic client ready")`**

This prints the text `Anthropic client ready` to confirm the cell ran without errors. Importantly, the comment notes "no network call yet" — creating the client object does *not* immediately contact Claude's servers. It just prepares the connection. No API costs are incurred here.

### What the output means

```
Anthropic client ready
```

This one-line confirmation tells you the `anthropic` package is installed correctly and the `Anthropic` object was created without errors. If you saw an error instead, it would likely mean either the package isn't installed, or the `api_key` variable from Cell 1 is `None`.

---

## Cell 3: Smoke test — sending your first real message to Claude

```python
# ── Starter Cell 3: Smoke test ───────────────────────────────────────────────
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

**Line 1: `# ── Starter Cell 3: Smoke test ─`**

A comment labeling this section. The term **smoke test** comes from electronics: you build a circuit, plug it in, and check whether smoke appears — meaning "does this even work at all?" In software, a smoke test is the minimal possible check to confirm the basic plumbing is functional before doing anything serious.

---

**Line 2: `response = client.messages.create(`**

This is the line that actually sends a message to Claude over the internet.

- **`client.messages.create`**: Remember `client` is our telephone connected to Claude. It has sub-tools accessible with the dot (`.`) notation. `client.messages` accesses the "messages" section of the API, and `.create` is the specific function that sends a new message and waits for a reply.

- **`API`**: An API (Application Programming Interface) is like a waiter in a restaurant. You (the Python code) tell the waiter what you want. The waiter takes your order to the kitchen (Claude's servers). The kitchen prepares a response and the waiter brings it back to you. The API is the standardized system for placing and receiving those orders.

- **`response =`**: The reply that comes back from Claude gets stored in a variable named `response`. Everything Claude says, plus metadata about the exchange (like how many tokens were used), lives inside this variable.

- The opening parenthesis `(` here starts a function call that spans multiple lines. Python knows the call isn't finished until it sees the matching closing parenthesis `)`. The indented lines below are all **arguments** — settings being passed to the `create` function.

---

**Line 3: `model="claude-haiku-4-5",`**

This argument tells Claude's API which specific AI model to use for generating the response.

- **Model**: A model is a specific trained version of an AI system. Different models have different capabilities, speeds, and costs. Think of models like car trim levels: the base model gets you from A to B reliably, while the premium model has more features but costs more.

- **`"claude-haiku-4-5"`**: This is the name of a specific Claude model — the faster, more economical option. The comment explains it's good for smoke tests because it's cheap: you're just checking connectivity, not doing serious work.

- The trailing **comma** `,` after the value is how Python separates arguments in a function call when they're written one per line. Don't let it trip you up — it's just punctuation.

---

**Line 4: `# model="claude-sonnet-4-5",`**

This entire line is **commented out** — notice the `#` at the start. That means Python completely skips it. The line is there as a convenience: if you wanted to use the more capable (but more expensive) Sonnet model instead, you'd remove the `#` from this line and add a `#` to the line above. This pattern of leaving alternative options commented out is common in notebooks.

---

**Line 5: `max_tokens=50,`**

This sets a hard limit on how long Claude's response can be, measured in **tokens**.

- **Token**: A token is the unit that AI language models use to read and write text. It's roughly equivalent to one word, though short words might be one token while longer or unusual words might be broken into two or three tokens. The sentence "Hello, how are you?" is about 5 tokens.

- **`max_tokens=50`**: This instructs Claude: "Do not write a response longer than 50 tokens." For a smoke test where we just want Claude to say hello, 50 tokens is more than enough — and it protects against accidentally asking for a very long (and expensive) response.

- Setting `max_tokens` is a best practice for cost control. API providers charge per token, so capping output length prevents unexpected bills.

---

**Lines 6–8: `messages=[...]`**

```python
messages=[
    {"role": "user", "content": "Say hello in one short sentence."}
]
```

This argument defines the **conversation history** — what has been said so far.

- **`messages=`**: The `messages` argument expects a **list**. A list in Python is an ordered collection of items, like a numbered list on paper. Lists are written with square brackets `[...]`, and items inside are separated by commas.

- **`[{"role": "user", "content": "..."}]`**: This list contains one item — a single message. That message is written as a **dictionary**.

- **Dictionary**: A dictionary in Python is a lookup table — like a phone book where you look up a name to get a number. It's written with curly braces `{...}`, and each entry is a **key-value pair** in the format `"key": value`. Here, the dictionary has two keys: `"role"` and `"content"`.

- **`"role": "user"`**: The `role` key identifies *who* is speaking in this message. Claude's API expects a conversational format where messages are labeled by role. `"user"` means this is the human's turn. The other main role is `"assistant"` (Claude's replies). This role system is how multi-turn conversations are tracked — but more on that in later cells.

- **`"content": "Say hello in one short sentence."`**: The `content` key holds the actual text of the message — what you're saying to Claude. Here, you're asking it to greet you briefly.

The overall picture: `messages` is a list of dictionaries, where each dictionary represents one turn in a conversation. Right now there's only one turn (your question). Claude's reply will come back in the `response` variable.

---

**Line 9: `)`**

This closing parenthesis ends the `client.messages.create(...)` function call that started on line 2. Everything between these two parentheses were the settings (arguments) for that one call. The call is now complete, the request has been sent to Claude's servers, Claude has generated a reply, and that reply is stored in `response`.

---

**Line 11: `print(response.content[0].text)`**

This extracts the actual text of Claude's reply and prints it.

Let's walk through the chain piece by piece:

- **`response`**: The variable holding everything that came back from Claude — not just the text, but also metadata like token counts, the model used, why Claude stopped writing, etc.

- **`response.content`**: The `content` attribute (a named piece of data attached to the response object) is a **list of content blocks**. Why a list? Because Claude can technically return multiple types of content in one response (text, tool calls, etc.). Even when it's just plain text, it's wrapped in a list for consistency.

- **`response.content[0]`**: The square bracket notation `[0]` means "get the item at position zero." Python (like most programming languages) counts positions starting from 0, not 1. So `[0]` means "the first item." Here, we're grabbing the first (and in this case, only) content block.

- **`.text`**: This accesses the `text` attribute of that content block — extracting the actual string of words that Claude wrote.

The full expression `response.content[0].text` can be read as: "From the response, take the content list, grab the first block, and give me its text."

### What the output means

You should see something like:
```
Hello! It's wonderful to meet you today.
```
(The exact wording will vary — Claude doesn't produce identical responses every time.)

This output confirms that:
1. Your API key is valid and accepted
2. Your internet connection can reach Anthropic's servers
3. The `anthropic` Python package is working correctly
4. Claude understood and responded to a simple prompt

This is your green light to proceed with the rest of the lab. If you see an error instead, it almost always means the API key in Cell 1 is wrong, expired, or has no remaining credits.

---

## Cell 4: Setting up imports and the usage tracking log

```python
import re                          # standard-library regex module used in rubric scoring
import json                        # used to pretty-print structured data

# ── Usage log: every API call appends a record here ─────────────────────────
usage_log = []                     # list accumulates one dict per API call;
```

### What this code does

---

**Line 1: `import re`**

This imports Python's built-in `re` module. **`re`** stands for **regular expressions** — a powerful mini-language for searching through text using patterns.

- **Regular expression (regex)**: Imagine you want to find every phone number in a document, but they might be written as `555-1234`, `(555) 1234`, or `555.1234`. A regex lets you write one pattern that matches all those formats at once. It's a compact way of describing "find text that looks like this."

- In this lab, `re` is used later for the **rubric scoring** step — programmatically checking whether Claude's responses contain certain patterns (like the right number of bullet points, or the presence of a specific word).

- `import re` (without the `from ... import` style) brings in the whole `re` toolbox. You'll use it later as `re.something(...)`.

---

**Line 2: `import json`**

This imports Python's built-in `json` module.

- **JSON** (pronounced "JAY-son") stands for JavaScript Object Notation. Despite the name, it has nothing to do with JavaScript in practice — it's simply a widely-used standard format for structuring data that both humans and computers can read. It looks like nested dictionaries and lists with a specific syntax. An example of JSON might look like: `{"name": "Alice", "score": 95}`.

- In this lab, `json` is used to **pretty-print structured data** — that is, to display dictionaries and lists in a neatly indented, readable format rather than as a jumbled single line.

---

**Line 4: `# ── Usage log: every API call appends a record here ─`**

A comment explaining what the next line creates. "Every API call appends a record here" means: each time we send a message to Claude, we'll add a summary of that call (model used, tokens consumed, cost, etc.) to this log.

---

**Line 5: `usage_log = []`**

This creates an empty **list** and stores it in a variable named `usage_log`.

- **List**: An ordered collection of items, written with square brackets. Right now it's empty — `[]` means "a list with nothing in it yet."

- **Why create it empty?** Because it will be filled up gradually as the lab progresses. Each time a cell calls the Claude API, a new record (a dictionary with details about that call) will be added to this list using Python's `.append()` method (which you'll see in later cells). Think of `usage_log` as a blank expense report that will be filled in one row at a time.

- At the end of the lab, you'll be able to review `usage_log` to see every API call made, how many tokens each used, and what the estimated cost was. This is a practical skill for managing API expenses in real projects.

### What the output means

This cell produces no visible output — there's no `print()` statement. That's intentional. You're just setting up tools (imports) and creating an empty container (`usage_log`) that other cells will use. In JupyterLab, a cell with no output simply shows a blank space below it after running, which is perfectly normal.

---

## Glossary

**API (Application Programming Interface)**: A standardized system for one piece of software to communicate with another over a network. Like a waiter taking orders between a customer and a kitchen — you don't need to know how the kitchen works, just how to place an order.

**API key**: A secret string of characters that acts as your password for an API. It identifies who you are and authorizes your requests. Must be kept private at all times.

**Argument**: A value you pass into a function to configure its behavior.