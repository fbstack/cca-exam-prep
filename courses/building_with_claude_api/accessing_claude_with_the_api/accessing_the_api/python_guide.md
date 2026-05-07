# Python Guide: Being Clear and Direct with the Claude API

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (for complete beginners)

Python is a language for giving instructions to a computer. Think of it like writing a very precise recipe: each line tells the computer exactly what to do next, in order, from top to bottom. Unlike a recipe written for a human cook (who can fill in gaps with common sense), a computer follows instructions with zero assumptions — every step must be spelled out. The good news is that other people have already written enormous collections of useful instructions (called **libraries** or **packages**), and Python lets you borrow those collections so you don't have to build everything from scratch. This lab uses several of those borrowed collections to talk to an AI model called Claude, made by a company called Anthropic.

---

## Cell 1: Loading environment variables (your secret API key)

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

**Line 1: `# ── Starter Cell 1: Load environment variables ──────────────────────────────`**

This line starts with a `#` symbol, which means it is a **comment**. Comments are notes written by the programmer for any human reading the code. Python completely ignores them — they have no effect on what the computer does. Think of them like margin notes in a textbook. This particular comment is just a visual header that labels what this section of code is for.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack three concepts here.

First, **importing** is the act of bringing a pre-written tool into your current workspace. Imagine a large toolshed full of specialist tools. "Importing" means walking to the shed, picking up a specific tool, and bringing it to your workbench. You don't need to understand how the tool was built — you just use it.

Second, **dotenv** is the name of one of those tools. It is a library (a collection of pre-written code) whose entire job is to read a special file called `.env` (pronounced "dot-env"). A `.env` file is a plain text file that sits in your project folder and holds sensitive values — like passwords or secret keys — in a format like `MY_SECRET=abc123`. Keeping secrets in this separate file means they never accidentally end up mixed in with your main code.

Third, **`load_dotenv`** is the specific function (a named set of instructions) inside the dotenv library that we actually want to use. We're not importing the entire dotenv library — we're reaching in and pulling out just this one tool.

So the whole line means: *"From the dotenv toolbox, bring me the `load_dotenv` tool."*

---

**Line 3: `import os`**

Here we are importing a library called **`os`**, which stands for "operating system." The operating system is the software that runs your computer (Windows, macOS, Linux). The `os` library gives Python a way to ask the operating system questions — things like "what folder are we in?" or "what secret values are stored in memory right now?"

Unlike the previous line, we're importing the entire `os` library (not just one piece of it), because we'll use several of its features.

The specific feature we need from `os` — accessing **environment variables** — is explained below. For now, think of environment variables as a private notepad that your operating system maintains in memory. Programs can write values to this notepad and read them back later, without those values ever being visible in the code itself.

---

**Line 5: `load_dotenv()`**

This is the first line that actually *does* something when Python runs it. The parentheses `()` after a name mean "execute this function right now." 

When `load_dotenv()` runs, it opens your `.env` file, reads each line (each `KEY=VALUE` pair), and copies those values into the operating system's in-memory notepad (called **`os.environ`** in Python). After this line runs, the rest of your program can look up those values by name.

An analogy: imagine your `.env` file is a printed list of passwords locked in a drawer. `load_dotenv()` is the act of unlocking the drawer, reading the list, and memorising the passwords so you don't need to refer to the paper again during the rest of the session.

---

**Line 8: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

Now we retrieve one specific value from the operating system's notepad.

**`os.getenv("ANTHROPIC_API_KEY")`** says: *"Look in the environment notepad for an entry called `ANTHROPIC_API_KEY` and give me its value."* If the entry exists, you get its value (a long string of letters and numbers). If it doesn't exist, you get back a special Python value called **`None`** — Python's way of saying "nothing was found here" — rather than an error that crashes the program.

**`api_key =`** is a **variable assignment**. A variable is a labeled box that holds a value. Here we're creating a box labeled `api_key` and putting the retrieved key value inside it. From this point on, any time we write `api_key` in our code, Python will look in that box and use whatever is stored there.

An **API key** is a unique secret code (like a password) that Anthropic gives you when you create an account. It identifies who you are when your program sends requests to Anthropic's servers, so they know who to bill and how much usage to allow. You must keep it secret — publishing it in code is like publishing your password online.

---

**Line 11: `print("API key loaded:", api_key is not None)`**

**`print()`** is a built-in Python function that displays something on screen. Whatever you put inside the parentheses gets shown in the notebook's output area beneath the cell.

**`"API key loaded:"`** is a **string** — a piece of text, always wrapped in quotation marks. This is the label part of the message.

**`api_key is not None`** is a **logical test**. It asks: "Is the value stored in `api_key` something other than `None`?" Python evaluates this and gives back either `True` or `False`. If `api_key` contains an actual key string, the answer is `True`. If `load_dotenv()` failed to find the key and `api_key` holds `None`, the answer is `False`.

The comma between the two parts of the `print()` call means "print both of these things, separated by a space." So the output will look like either:

`API key loaded: True` — meaning everything worked.

`API key loaded: False` — meaning the `.env` file is missing or the key name is spelled wrong.

### What the output means

When you run this cell, you'll see one line of output. `True` is the healthy result — your API key was found and is ready to use. `False` means the program can't find your key and any later cell that tries to contact Anthropic will fail. Catching this early, before making any network calls, saves confusing error messages later.

---

## Cell 2: Initialising the Anthropic client

```python
# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────
from anthropic import Anthropic  # imports the official Python SDK client class

client = Anthropic(api_key=api_key)  # creates a reusable client instance;
                                     # all future API calls go through this object

print("Anthropic client ready")       # quick sanity-check — no network call yet
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────`**

Another comment, another visual header. Python ignores this. It is here for human readers.

---

**Line 2: `from anthropic import Anthropic`**

This follows exactly the same pattern as `from dotenv import load_dotenv`. We are reaching into the **`anthropic`** library (the official software package that Anthropic provides for Python programmers) and pulling out a specific tool called **`Anthropic`** (capital A).

In Python, names that start with a capital letter are usually **classes**. A class is a blueprint for creating a specific kind of object. Think of a class like a cookie cutter: the `Anthropic` class is the cutter, and in the next line we'll use it to stamp out one specific cookie — our `client` object — that has all the right shapes and abilities built in.

The **`anthropic` library** (also called an SDK, which stands for Software Development Kit) handles all the complicated work of formatting requests, managing connections, and parsing responses when your program talks to Anthropic's API.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line creates our **client object** and stores it in a variable called `client`.

Let's unpack it from right to left.

**`Anthropic(api_key=api_key)`** calls the `Anthropic` class as if it were a function (using parentheses). This creates a new **instance** — a specific, ready-to-use object built from the `Anthropic` blueprint. 

**`api_key=api_key`** is a **keyword argument** — a named value being passed into the object as it's created. The name on the left (`api_key`) is the parameter name that the `Anthropic` class expects. The value on the right (`api_key`) is the variable we created in Cell 1 that holds our actual secret key. This wires our key into the client so that every request it sends will automatically be authenticated.

Think of the `client` object like setting up an account at a post office. You give them your ID once (`api_key`), they register you, and from then on every time you come back to send a package they already know who you are. You don't have to show your ID every single time — it's stored in your account.

**`client =`** stores this newly created post-office account in a labeled box called `client`, so we can use it in every later cell.

---

**Line 7: `print("Anthropic client ready")`**

This prints a simple confirmation message to the screen. Notice it is a fixed **string** (text in quotes), not a test like before. There's no `True`/`False` here — if the `Anthropic()` call on the previous line had gone wrong (for example, if the `api_key` was completely absent), Python would have already thrown an error and stopped. The fact that we reach this `print` line at all is a sign that the object was created successfully.

The comment on the same line — `# no network call yet` — is important. Creating the `client` object does *not* contact Anthropic's servers. It just sets up a local object in your computer's memory, ready to make calls when you ask it to.

### What the output means

You'll see the message `Anthropic client ready` printed beneath the cell. This is your confirmation that the client object exists and your program is prepared to make real API requests. No money has been spent, no data has been sent — this is purely local setup.

---

## Cell 3: The smoke test — making your first real API call

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

**Line 1: `# ── Starter Cell 3: Smoke test ──────...`**

Another comment header. A **smoke test** is a developer's term borrowed from electronics — you power up a new circuit and check whether it starts smoking (i.e., catches fire). In software it means: "run the simplest possible version of the thing and confirm it doesn't catch fire." Here, we're sending the most basic possible message to Claude to verify the whole pipeline works end to end.

---

**Line 2: `response = client.messages.create(`**

This is the most important line in the first three cells. Let's break it apart.

**`client`** is the object we created in Cell 2 — our "post-office account."

**`.messages`** is a **property** of the client — a sub-object that specifically handles sending and receiving messages. Think of it as the "messages department" of the post office.

**`.create(`** is a **method** — a function that belongs to an object. Calling `.create()` tells the messages department: "compose and send a new message to Claude." The opening parenthesis `(` starts the list of details (called **arguments** or **parameters**) we need to provide.

**`response =`** is going to store whatever Claude sends back. A **response** is the complete package of data that comes back from the API — not just the text Claude wrote, but also metadata like how many tokens were used, why Claude stopped writing, and so on. All of that gets stored in this `response` variable (box).

---

**Line 3: `model="claude-haiku-4-5",`**

This is the first argument inside `.create()`. It tells Anthropic *which version of Claude* to use. Anthropic offers several models at different speeds and price points:

- **Claude Haiku** is the fastest and least expensive — like a quick, capable assistant who gives brief answers
- **Claude Sonnet** is more powerful but slower and more expensive — better for complex tasks

We're using Haiku here because this is just a test. There's no point paying more for a powerful model to say "Hello."

The format `model="claude-haiku-4-5"` is a **keyword argument**: the word on the left (`model`) is the parameter name the API expects, and the string on the right (`"claude-haiku-4-5"`) is our chosen value. The comma at the end separates this argument from the next one.

---

**Line 4: `# model="claude-sonnet-4-5",`**

This line is commented out (starts with `#`), so Python ignores it entirely. It's here as a convenient reminder — if you want a more powerful model, you can delete the `#` to "uncomment" this line and add a `#` to the Haiku line above to disable that one. Only one model can be active at a time.

---

**Line 5: `max_tokens=50,`**

A **token** is the basic unit that AI language models read and write. It's roughly equal to one word, though short words might be one token each and longer words might be split across two or three tokens. "Hello" is one token. "Unbelievable" might be two or three tokens.

**`max_tokens=50`** sets a hard ceiling: Claude is not allowed to write more than 50 tokens in its response. This is like telling someone "please answer in ten words or fewer." For a smoke test where we just want a single sentence, 50 tokens is generous. This parameter also protects against runaway costs — without a limit, a misbehaving prompt could generate an extremely long (and expensive) response.

---

**Lines 6–8: `messages=[...]`**

This is the most structurally interesting part. Let's take it in layers.

**`messages=`** is a keyword argument that expects a **list** of **dictionaries** representing the conversation history.

A **list** in Python is an ordered collection of items, written with square brackets `[` and `]`. Think of it as a numbered list on paper — item 1, item 2, item 3 — where order matters.

A **dictionary** in Python is a lookup table made of **key-value pairs**, written with curly braces `{` and `}`. Think of it like a form with labeled fields: "Name: Alice, Age: 30." You look up a field by its label (the **key**) to get its value.

Each dictionary in the `messages` list represents one **turn** of conversation. A turn is a single message from either the user or the assistant.

**`{"role": "user", "content": "Say hello in one short sentence."}`**

This dictionary has two keys:
- `"role"` — who is speaking. The value `"user"` means this message comes from the human. The other option would be `"assistant"` for messages from Claude.
- `"content"` — the actual text of the message. Here it's the instruction `"Say hello in one short sentence."`

So the whole `messages` argument says: "Here is a conversation with one turn — the user said 'Say hello in one short sentence.'"

The closing `]` ends the list, and the closing `)` ends the `.create()` call. The entire call spans multiple lines for readability; Python is fine with this as long as the parentheses are properly matched.

---

**Line 11: `print(response.content[0].text)`**

When Claude responds, the API sends back a structured object — not just raw text, but a container with multiple pieces of information. This line reaches inside that container to extract just the text.

**`response`** is the whole container we stored in the `response` variable.

**`.content`** is a property of the response object. It holds a **list** of **content blocks** — the reason it's a list is that in more advanced usage, Claude can return multiple blocks (for example, if it uses tools). For a plain text response, there will be exactly one block.

**`[0]`** means "give me the item at position zero." Python (like most programming languages) counts from zero, not one. So `[0]` means "the first item," `[1]` would mean "the second item," and so on. Here we want the first (and only) content block.

**`.text`** is a property of that content block that holds the actual text string Claude wrote. Without `.text`, you'd get the entire block object (which includes type information and other metadata). With `.text`, you get just the words.

The overall result: Claude's plain-text reply gets printed to the screen.

### What the output means

Running this cell makes a real network request to Anthropic's servers, which costs a small amount of money (fractions of a cent for 50 tokens). If everything is working, you'll see Claude's single greeting sentence printed beneath the cell — something like:

`Hello! I hope you're having a wonderful day.`

If you see an error instead, the most common causes are: a missing or invalid API key, no internet connection, or a billing issue with your Anthropic account. This smoke test is designed to surface those problems here, before you run the more complex cells later in the lab.

---

## Cell 4: Importing additional tools and creating the usage log

```python
import re                          # standard-library regex module used in rubric scoring
import json                        # used to pretty-print structured data

# ── Usage log: every API call appends a record here ─────────────────────────
usage_log = []                     # list accumulates one dict per API call;
```

### What this code does

---

**Line 1: `import re`**

This imports Python's built-in **`re`** module. The name `re` stands for **regular expressions** — a specialised mini-language for searching, matching, and manipulating text using patterns.

Think of regular expressions as a very sophisticated "Find" tool. Where a normal search might look for the exact word "hello," a regular expression can look for patterns like "any number followed by a colon" or "any word that starts with a capital letter." Later in this lab, `re` will be used to check whether Claude's response matches certain criteria when scoring its quality.

This is a **standard-library** module, which means it comes pre-installed with Python — there's nothing to download. All you have to do is import it.

---

**Line 2: `import json`**

This imports Python's built-in **`json`** module. **JSON** (pronounced "JAY-son") stands for JavaScript Object Notation. Despite the name, JSON is a universal format — not specific to any one programming language — for writing structured data in a way both humans and computers can read.

JSON looks a lot like Python dictionaries and lists. For example:
```json
{"name": "Alice", "scores": [95, 87, 91]}
```

The `json` module will be used in this lab to **pretty-print** data — that is, to display nested dictionaries and lists with neat indentation that's much easier for a human to read than a compressed one-liner.

---

**Line 4: `# ── Usage log: every API call appends a record here ─────────────────────────`**

Another comment header — Python ignores it. It signals to a human reader that the code below it is about setting up a tracking system.

---

**Line 5: `usage_log = []`**

This creates an empty **list** and stores it in a variable called `usage_log`.

An empty list `[]` is exactly what it sounds like — a container that currently holds nothing, like an empty notebook. As the lab progresses and you make more API calls, a record of each call (how many tokens it used, what model was used, etc.) will be **appended** (added to the end of) this list. By the end of the lab, you'll be able to look back at `usage_log` and see a complete history of every request you made and what it cost in tokens.

The comment on the same line — `# list accumulates one dict per API call` — tells us the format: each item added to this list will be a **dictionary** (a labeled record with multiple fields).

### What the output means

This cell produces no visible output. It is purely setup — importing tools and creating an empty container to hold data. If the cell runs without error (no red error messages), everything worked correctly. The two imported modules (`re` and `json`) are now available for use in any later cell, and `usage_log` is ready to receive records.

---

## Glossary

**API (Application Programming Interface)** — A structured way for one piece of software to talk to another. Like a waiter who takes your order to the kitchen and brings back what you asked for — you don't need to go into the kitchen yourself; the waiter handles the interaction. Anthropic's API lets your Python program send messages to Claude and receive replies.

**API key** — A unique secret code (like a password) that identifies your account when making API requests. Must be kept private — publishing it would let others make requests billed to your account.

**Argument / Parameter** — A value you provide to a function or method when calling it. If a function is a recipe, arguments are the specific ingredients you put in. Some arguments have names (keyword arguments, like `model="claude-haiku-4-5"`) and some are positional (unnamed, identified by their order).

**Class** — A blueprint for creating objects. Like a cookie cutter: the class defines the shape; each object created from it is a specific cookie. In this lab, `Anthropic` is a class.

**Client** — In networking, a "client" is a program that makes requests to a server. Here, our Python program is the client; Anthropic's computers are the server. The `client` variable holds an object that manages all our requests.

**Comment** — A line of code starting with `#` that Python ignores completely. Written by the programmer for human readers.

**Dictionary** — A Python data structure that stores labeled values as key-value pairs, written with curly braces `{}`. Like a form with labeled fields. Example: `{"role": "user", "content": "Hello"}`.

**dotenv / `.env` file** — A plain text file that holds secret values like API keys in a `KEY=VALUE` format. The `dotenv` library reads this file and makes those values available to your program without them appearing in your code.

**Environment variable** — A named value stored in the operating system's memory (not in a file) that programs can read. Useful for secrets because they don't appear in source code.

**Function** — A named set of instructions that you can call by name to execute. Like a named recipe: calling `load_dotenv()` runs all the instructions inside `load_dotenv`.

**Import** — The act of bringing a pre-written library or tool into your current program. Like fetching a tool from a shed and bringing