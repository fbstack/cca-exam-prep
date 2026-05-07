# Python Guide: Getting an API Key & Making Your First Request

A plain-English walkthrough of every line of code in the JupyterLab notebook.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving precise, step-by-step instructions to a computer — written in a language that is much closer to plain English than most programming languages. Think of it like writing a very detailed recipe. The computer follows each instruction in order, from top to bottom, doing exactly what you say. A JupyterLab notebook organises those instructions into chunks called **cells**, which you run one at a time. This lets you build up a working program gradually and see the results of each step before moving on.

---

## Cell 1: Load your secret API key from a file

```python
# ── Starter Cell 1: Load environment variables ──────────────────────────────
from dotenv import load_dotenv   # load_dotenv reads key=value pairs from a .env file
import os                        # os.getenv lets us read environment variables safely

load_dotenv()                    # reads .env in the current directory and sets env vars

api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the key; returns None if missing

print("API key loaded:", api_key is not None)  # True = key found, False = check your .env
```

### What this code does

---

**Line 1: `# ── Starter Cell 1: Load environment variables ──────────────────────────────`**

This line is a **comment**. Comments are notes written for human readers — the computer ignores them completely. In Python, anything on a line that comes after a `#` symbol is a comment. They exist purely to explain what the code is doing, and they have no effect on how the program runs. Think of them like margin notes in a textbook.

---

**Line 2: `from dotenv import load_dotenv`**

This line brings a specific tool into your workspace so you can use it.

Let's unpack the vocabulary here:

- A **module** (also called a **library** or **package**) is a collection of pre-written code that someone else has already built. Rather than writing everything from scratch, you borrow their work. Think of it like a toolbox: someone else built the tools, and you're just reaching in to pick the ones you need.
- **`dotenv`** is the name of one such toolbox. Its whole purpose is to read a special file called `.env` (more on that in a moment) and make the information inside it available to your program.
- **`load_dotenv`** is a specific tool *inside* that toolbox — a **function** (a named set of instructions you can call by name, like a named recipe). This particular function does the actual work of reading the `.env` file.
- **`from ... import ...`** is how you tell Python: "Go to this toolbox (`dotenv`) and bring me this specific tool (`load_dotenv`)."

**Why do we need this?** Your Anthropic API key is a secret password. You should never write it directly in your notebook code, because notebooks can be accidentally shared or saved to version control (a system that tracks changes to files over time — like a time machine for your code). Instead, the key lives in a separate file called `.env`, and this tool reads it safely.

---

**Line 3: `import os`**

This line brings in another toolbox, this time called **`os`**.

- **`os`** stands for "operating system." It is a built-in Python toolbox (it comes with Python automatically — no installation needed) that lets your program talk to the computer's underlying system.
- One thing the `os` toolbox can do is read **environment variables** — which are named values that the operating system stores in memory, like a whiteboard your computer keeps in the background. After `load_dotenv()` runs (see next line), your API key will be written onto that whiteboard, and `os` is how you read it back.
- **`import`** on its own (without `from`) brings in the whole toolbox rather than one specific tool from it.

---

**Line 5: `load_dotenv()`**

This line actually *runs* (executes) the `load_dotenv` function you imported two lines ago.

- The **parentheses `()`** are how you tell Python to run a function. Think of them as saying "go!" — writing `load_dotenv` alone would just refer to the recipe; adding `()` says "cook it now."
- What this function does behind the scenes: it looks in the current folder for a file named `.env`, reads every line in it (which are written in the format `VARIABLE_NAME=value`), and copies those values onto the operating system's background whiteboard (the environment variables). After this line runs, your API key is available for the next line to retrieve.

---

**Line 7: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line reads your API key from the background whiteboard and stores it in a **variable**.

Let's unpack the new vocabulary:

- A **variable** is a labelled box that holds a value. You give the box a name, and then you can refer to that name later instead of repeating the value. Here, the box is named `api_key`.
- **`os.getenv(...)`** is a function from the `os` toolbox. The dot (`.`) means "look inside `os` and find the tool called `getenv`." You call it by adding parentheses with what you want to look up inside.
- **`"ANTHROPIC_API_KEY"`** (in quotes) is the name of the environment variable — the label on the whiteboard that corresponds to your API key. The quotes tell Python this is a **string** (a piece of text), not a variable name.
- The **`=`** sign here does not mean "equals" in the mathematical sense. It means "take whatever is on the right side and put it in the box named on the left side." So: run `os.getenv("ANTHROPIC_API_KEY")`, get back your API key, and store it in the box called `api_key`.
- If the `.env` file was missing or didn't contain that variable name, `getenv` returns a special value called **`None`** — Python's way of saying "nothing was found here."

---

**Line 9: `print("API key loaded:", api_key is not None)`**

This line displays a message to let you know whether the API key was found successfully.

- **`print(...)`** is a built-in Python function that displays text in the output area below the cell. Whatever you put inside the parentheses gets shown on screen.
- **`"API key loaded:"`** is a string (text in quotes) that will appear exactly as written.
- **`api_key is not None`** is a **condition** — a question the computer evaluates to either `True` or `False`. It asks: "Is the `api_key` box holding something other than nothing?" If your key was found, the answer is `True`. If the `.env` file was missing or the variable name was wrong, the answer is `False`.
- The comma inside `print()` separates two things to display side by side. Python will print the text string, then a space, then the result of the condition.

### What the output means

You will see either:
- **`API key loaded: True`** — Your `.env` file was found and the key was successfully loaded. You're ready to proceed.
- **`API key loaded: False`** — Something went wrong. Either the `.env` file doesn't exist, it's in the wrong folder, or the variable inside it isn't named `ANTHROPIC_API_KEY` exactly. You need to fix this before any later cells will work.

---

## Cell 2: Create a reusable connection to the Anthropic API

```python
# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────
from anthropic import Anthropic  # Anthropic is the main SDK class for all API calls

client = Anthropic(api_key=api_key)  # creates a reusable client bound to our key
                                     # passing api_key explicitly keeps the notebook
                                     # self-contained and avoids implicit env look-ups

print("Anthropic client ready")  # confirms object creation succeeded
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────`**

Another comment — a human-readable label telling you what this cell is about. The computer skips it entirely.

---

**Line 2: `from anthropic import Anthropic`**

This line imports the main tool you'll use to communicate with Claude.

- **`anthropic`** (lowercase) is the name of the installed toolbox — the official Anthropic software development kit, or **SDK**. An SDK is a set of pre-built tools a company provides so that developers can talk to their service without having to write all the communication code from scratch.
- **`Anthropic`** (uppercase) is a specific tool inside that toolbox — a **class**. A class is like a blueprint or a template for creating a specific kind of object. In this case, it's a blueprint for creating a "connection manager" — an object that knows how to talk to the Anthropic API on your behalf.
- The difference in capitalisation is intentional and meaningful in Python. `anthropic` (lowercase) is the package name; `Anthropic` (uppercase) is the class inside it.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line uses the `Anthropic` blueprint to create an actual, working connection object, and stores it in a variable called `client`.

- **`Anthropic(...)`** — adding parentheses to a class name creates a new **instance** of that class. Think of it like using a cookie cutter (the class/blueprint) to cut out an actual cookie (the instance/object). The cookie is a real, usable thing.
- **`api_key=api_key`** — this is how you pass information to the class when creating the instance. The part before the `=` (`api_key`) is the **parameter name** — the label the `Anthropic` class uses to receive your key. The part after the `=` (`api_key`) is your variable from Cell 1 — the actual key value stored in that box. You're essentially saying: "Here's my password — use it for all future requests."
- **`client`** — the variable name you chose for this connection object. You could name it anything, but `client` is a conventional name for this kind of thing. Every time you want to talk to the API later, you'll use `client` as your starting point.

The indented comment lines (lines 5–6) are just notes explaining *why* you're passing the key explicitly rather than letting the library find it automatically — it makes the notebook easier to understand in isolation.

---

**Line 8: `print("Anthropic client ready")`**

This prints a simple confirmation message.

- If the previous line ran without errors, this message appears and tells you the `client` object was created successfully.
- If something was wrong (for example, a malformed API key), Python would have thrown an **error** — a message explaining what went wrong — and this `print` line would never be reached.

### What the output means

You will see: **`Anthropic client ready`**

This means Python successfully created the connection object and is ready to make API calls. No error message means everything went smoothly.

---

## Cell 3: Send a test message to Claude and read the reply

```python
# ── Starter Cell 3: Smoke test ───────────────────────────────────────────────
response = client.messages.create(   # client.messages.create is the primary endpoint
    model="claude-haiku-4-5",        # fast, cheap model — ideal for quick tests
    # model="claude-sonnet-4-5",     # uncomment for higher-quality responses
    max_tokens=50,                   # hard cap on output length; required parameter
    messages=[{                      # messages is a list of turn dicts
        "role": "user",              # 'user' marks text coming from the human side
        "content": "Say hello in one short sentence."  # the actual prompt
    }]
)

print(response.content[0].text)     # content is a list; [0] gets the first block; .text extracts the string
```

### What this code does

---

**Line 1: `# ── Starter Cell 3: Smoke test ──────────────────────────────────────────────`**

Another comment label. A **smoke test** is an informal term borrowed from engineering — it means the simplest possible test to confirm something basically works (as in: plug it in and check that it doesn't produce smoke). Here, we're just making sure Claude responds to a simple message before doing anything more complex.

---

**Lines 2–10: `response = client.messages.create(...)`**

This is the most important block of code so far. It sends a message to Claude and waits for a reply. Let's take it piece by piece.

**`response = ...`**

The result of everything on the right side will be stored in a variable called `response`. This variable will contain Claude's full reply, along with some additional information about the request.

**`client.messages.create(...)`**

This is the function call that actually contacts the Anthropic API.

- **`client`** — the connection object you created in Cell 2.
- **`.messages`** — a sub-section of the client object that deals specifically with the messages API. The dot means "look inside `client` for something called `messages`."
- **`.create(...)`** — a function inside `.messages` that *creates* a new message exchange — meaning it sends your message to Claude and returns the response. Think of this like pressing "Send" on an email, except you get a reply back almost immediately.
- The parentheses contain all the details of your request, spread across multiple lines for readability. Python allows you to spread a single instruction across many lines when it's inside parentheses — it treats everything until the closing `)` as one unit.

---

**`model="claude-haiku-4-5",`**

This line specifies *which version* of Claude you want to talk to.

- **`model`** is a parameter — a setting you pass to the function to tell it how to behave. Think of parameters like settings on an appliance: you're configuring the function before it runs.
- **`"claude-haiku-4-5"`** is a string (text in quotes) naming a specific Claude model. Anthropic offers several models with different trade-offs between speed, cost, and response quality. `claude-haiku-4-5` is described in the notebook as fast and inexpensive — good for testing.
- The **comma** at the end separates this parameter from the next one. When you pass multiple settings to a function, you separate them with commas.

---

**`# model="claude-sonnet-4-5",`**

This is a commented-out line — notice the `#` at the start. It's an *alternative* model option that has been switched off. The notebook author is showing you that you *could* use a different, higher-quality model by removing the `#` from this line (called "uncommenting") and adding a `#` to the line above.

---

**`max_tokens=50,`**

This sets a hard upper limit on how long Claude's response can be.

- **Token**: In AI language models, text is broken into chunks called **tokens** — roughly one word or part of a word each. "Hello" is one token; "unbelievable" might be two or three. Tokens are the unit the model uses internally to read and write text.
- **`max_tokens=50`** tells Claude: "Don't write more than 50 tokens in your reply." This keeps the test response short and cheap (since API costs are partly based on how many tokens are generated).
- The notebook notes this is a **required parameter** — the API will refuse to work if you leave it out. You must always tell Claude the maximum length of the response you're willing to accept.

---

**`messages=[{`**

This begins the `messages` parameter — a **list** of conversation turns.

- A **list** in Python is an ordered collection of items, like a numbered list on paper. It is written with square brackets `[...]`.
- Inside this list, each item is a **dictionary** — a lookup table made up of paired labels and values, written with curly braces `{...}`. Think of a dictionary like a form with labelled fields: each label (called a **key**) has a value next to it.
- This list currently has one dictionary inside it (one conversation turn — your message to Claude). In a multi-turn conversation, you would have multiple dictionaries in this list, each representing one message from either the user or the assistant.

---

**`"role": "user",`**

This is the first key-value pair inside the dictionary.

- **`"role"`** is the key — the label. It tells the API *who* is speaking in this turn.
- **`"user"`** is the value — the answer to that label. It means this message is coming from the human side of the conversation.
- The colon `:` separates the key from its value inside a dictionary.
- The API accepts two possible roles: `"user"` (the human) and `"assistant"` (Claude). Keeping track of roles is how Claude understands the back-and-forth structure of a conversation.

---

**`"content": "Say hello in one short sentence."`**

This is the second key-value pair in the dictionary.

- **`"content"`** is the key — the label for the actual text of the message.
- **`"Say hello in one short sentence."`** is the value — the actual words you're sending to Claude. This is your **prompt**: the instruction or question that Claude will respond to.
- The closing `}]` on the next line closes the dictionary `}` and then closes the list `]`.

---

**`)`**

This closing parenthesis ends the entire `client.messages.create(...)` call that began on line 2. Everything between the opening `(` and this `)` was the configuration for that single API call.

---

**Line 12: `print(response.content[0].text)`**

This line extracts Claude's actual reply from the response object and displays it.

- **`response`** — the variable holding everything that came back from the API call.
- **`.content`** — the part of the response that contains the actual text Claude wrote. The dot means "look inside `response` for something called `content`." This is a list, because in theory a response could contain multiple content blocks.
- **`[0]`** — this reaches into the list and pulls out the item at position zero. In Python (and most programming languages), counting starts at 0, not 1. So `[0]` means "the first item," `[1]` would mean "the second," and so on. In most cases with this API, there will be exactly one content block, so you always grab `[0]`.
- **`.text`** — once you have the content block, `.text` extracts just the plain text string from it, leaving behind any other technical information in the block that you don't need right now.
- The entire chain `response.content[0].text` is like a set of Russian nesting dolls: you open `response`, find `content` inside it, pull out item `[0]` from that list, and then extract the `.text` from that item.

### What the output means

You will see a single short sentence written by Claude — something like:

**`Hello! It's wonderful to meet you today.`**

This confirms the entire pipeline works end to end: your API key is valid, the client connected to Anthropic's servers, your message was delivered, and Claude's reply came back successfully. If you see an error instead, the most common cause is an invalid or missing API key from Cell 1.

---

## Glossary

| Term | Plain-English definition |
|---|---|
| **API** (Application Programming Interface) | A waiter who takes your order to the kitchen and brings back what you asked for. It's the way two pieces of software talk to each other — here, your Python code talks to Anthropic's Claude models through the API. |
| **API key** | A secret password that proves to Anthropic's servers that you are an authorised user. Without it, all API requests are rejected. |
| **Cell** | A chunk of code in a JupyterLab notebook that you can run independently. Each cell runs top to bottom when you execute it. |
| **Class** | A blueprint or template for creating objects. Like a cookie cutter: the class defines the shape; each object you create from it is an individual cookie. |
| **Client** | An object that manages your connection to a remote service (here, the Anthropic API). It knows your credentials and provides functions for making requests. |
| **Comment** | A note in code that the computer ignores, written for human readers. In Python, anything after a `#` on a line is a comment. |
| **Condition** | A question that evaluates to either `True` or `False`. Used to make decisions in code. |
| **Dictionary** | A lookup table made of key-value pairs, written with curly braces `{}`. Like a form with labelled fields — each label (key) has a corresponding value. |
| **Environment variable** | A named value stored by the operating system in memory, outside of your code files. Like a whiteboard your computer keeps in the background. |
| **Function** | A named set of instructions that you can run by calling its name followed by parentheses `()`. Like a named recipe: writing the name refers to it; adding `()` executes it. |
| **Import** | Bringing a tool or toolbox into your current workspace so you can use it. Like reaching into a toolbox and placing a specific tool on your workbench. |
| **Instance** | A specific object created from a class blueprint. If the class is a cookie cutter, the instance is one individual cookie. |
| **JupyterLab** | An interactive coding environment that runs in a web browser, where you write and run code in cells and see results immediately. |
| **Library / Module / Package** | A collection of pre-written code you can import and use. Someone else built the tools; you borrow them. |
| **List** | An ordered collection of items written with square brackets `[...]`. Like a numbered list on paper — order matters, and you can access items by their position number. |
| **`None`** | Python's way of representing "nothing" or "no value found." If a function can't find what you asked for, it often returns `None`. |
| **Object** | A bundle of data and functions that belong together, created from a class blueprint. The `client` in this notebook is an object. |
| **Parameter** | A setting or piece of information you pass to a function to tell it how to behave. Like the settings on an appliance. |
| **`print()`** | A built-in Python function that displays text in the output area below a cell. |
| **Prompt** | The text or question you send to an AI model to get a response. |
| **Python** | A programming language — a way of writing instructions for a computer in a form closer to plain English than most alternatives. |
| **SDK** (Software Development Kit) | A set of pre-built tools a company provides so developers can interact with their service without writing all the communication code from scratch. |
| **Smoke test** | The simplest possible test to confirm something basically works before investing in more complex testing. |
| **String** | A piece of text in Python, written inside quotation marks `"like this"`. |
| **Token** | A word-chunk that an AI model reads and writes — roughly one word or part of a word. Costs and length limits are measured in tokens. |
| **Variable** | A labelled box that holds a value. You give it a name, put a value inside, and refer to the name later to retrieve or use the value. |
| **`.env` file** | A plain text file used to store secret values like API keys, kept separate from your code so secrets are never accidentally shared. Each line follows the format `VARIABLE_NAME=value`. |
| **`[0]`** | A way of accessing the first item in a list. Python counts positions starting from zero, so position 0 is the first item, position 1 is the second, and so on. |