# Python Guide: Prompt Evaluation Lab

A plain-English walkthrough of every line of code in the JupyterLab notebook.
No coding background required.

---

## What Python is (for complete beginners)

Python is a language for giving instructions to a computer. Think of it like writing a very precise recipe. Each line is one instruction. The computer reads your instructions from top to bottom and does exactly what you say — no more, no less. A **JupyterLab notebook** is a special document where you write those instructions in numbered chunks called **cells**, and you can run each chunk one at a time and see the results right below it. This guide walks through every cell in the lab, one line at a time, so you know exactly what is happening and why.

---

## Cell 1: Loading Your Secret API Key

```python
from dotenv import load_dotenv  # imports the dotenv library to read .env files
import os                        # imports os module for environment variable access

load_dotenv()                    # reads .env file in current directory and loads vars into environment
api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the API key from environment; returns None if missing
print("API key loaded:", api_key is not None)  # prints True if key found, False if missing
```

### What this code does

**Line 1: `from dotenv import load_dotenv`**

Let's unpack three new ideas here.

A **library** (also called a **package** or **module**) is a collection of pre-written code that someone else built and published so you don't have to write it yourself. Think of it like a toolbox sitting on a shelf. The toolbox is called `dotenv`.

**Importing** means reaching into that toolbox and pulling out a specific tool so you can use it in your current workspace. Here, the specific tool we are pulling out is called `load_dotenv`.

`dotenv` is a library whose one job is to read a special file called `.env` (pronounced "dot env"). A `.env` file is a plain text file you keep on your computer — never shared publicly — that stores secret values like passwords and API keys. By keeping secrets in that file instead of directly in your code, you avoid accidentally sharing your credentials if you post your code online.

So this line says: *"From the dotenv toolbox, bring the load_dotenv tool into my workspace."*

---

**Line 2: `import os`**

`os` is a module that comes built into Python — you don't need to install it. It lets Python talk to the **operating system** (Windows, Mac, or Linux) that your computer runs on.

One thing the operating system manages is **environment variables** — a set of named values that live in the background of your computer session, a bit like sticky notes attached to the inside of your computer's memory. Programs can read these notes without you having to type the values out each time.

This line says: *"Bring in the os toolbox so I can read those sticky notes."*

---

**Line 3: `load_dotenv()`**

This is the first line that actually *does* something — it runs (or **calls**) the `load_dotenv` tool we imported on Line 1.

A **function** is a named set of instructions you can trigger by writing its name followed by parentheses `()`. Think of it like pressing a button on a machine. The button is labeled `load_dotenv`, and pressing it causes the machine to go find your `.env` file, read the secret values inside it, and paste them into your computer's environment variables (those background sticky notes).

After this line runs, your API key — which was sitting quietly in your `.env` file — is now available for the next line to find.

---

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

Several new ideas here. Let's take them one at a time.

A **variable** is a labeled box that holds a value. You create a box, give it a name, and put something inside. Here, the box is named `api_key`. Whatever value we find will be stored in that box so we can use it later.

The `=` sign in Python does not mean "equals" the way it does in math. It means **assignment** — "put the thing on the right side into the box on the left side."

`os.getenv("ANTHROPIC_API_KEY")` is a function call that reads an environment variable. The dot (`.`) between `os` and `getenv` means "look inside the `os` toolbox and use the `getenv` tool." We hand that tool the name `"ANTHROPIC_API_KEY"` (the name we used in the `.env` file), and it hands back the secret key value that was stored there. If it cannot find any variable with that name, it hands back a special Python value called `None`, which means "nothing."

So this line says: *"Look up the environment variable named ANTHROPIC_API_KEY and store whatever you find in the box called api_key."*

---

**Line 5: `print("API key loaded:", api_key is not None)`**

`print()` is a built-in Python function that displays a message in the output area below the cell. Whatever you put inside the parentheses gets shown on screen.

`"API key loaded:"` is a **string** — a piece of text. In Python, text is always wrapped in quotation marks.

`api_key is not None` is a **comparison expression** — a question Python evaluates to either `True` or `False`. It asks: "Is the value in the `api_key` box something other than nothing?" If your `.env` file was found and had the key, the answer is `True`. If the key was missing, the answer is `False`.

The print function will display both pieces — the label and the True/False answer — separated by a space.

### What the output means

You will see something like:

```
API key loaded: True
```

`True` tells you the key was found successfully and is ready to use. If you see `False`, your `.env` file is missing, misnamed, or does not contain a line called `ANTHROPIC_API_KEY`. You would need to fix that before continuing.

---

## Cell 2: Creating the Connection to Claude

```python
from anthropic import Anthropic  # imports the Anthropic SDK client class

client = Anthropic(api_key=api_key)  # creates an authenticated client; all API calls go through this object
print("Anthropic client ready")       # confirms client was created without error
```

### What this code does

**Line 1: `from anthropic import Anthropic`**

`anthropic` is a library — a toolbox — published by Anthropic (the company that makes Claude). It contains pre-written code that knows how to communicate with Anthropic's servers over the internet.

Inside that toolbox is a specific tool called `Anthropic` (notice it starts with a capital letter). This is a **class** — think of a class as a blueprint or a template. You use the blueprint to build a specific **object**, which is a working thing you can actually interact with. We'll build that object on the next line.

This line says: *"From the Anthropic toolbox, bring in the Anthropic blueprint."*

---

**Line 2: `client = Anthropic(api_key=api_key)`**

Here we use the `Anthropic` blueprint to build an actual working object and store it in a variable named `client`.

Think of `client` as a telephone handset that already has Anthropic's phone number programmed in. Every time you want to talk to Claude, you pick up this handset.

`api_key=api_key` is how we hand our secret key to the object during creation. The part on the left (`api_key=`) is the name of the slot the blueprint expects. The part on the right (`api_key`) is our variable box from Cell 1 — the actual key value. This is how Anthropic's servers know you are an authorized user and not a random stranger.

This line says: *"Build an Anthropic client object using my API key, and store it in the box called client."*

---

**Line 3: `print("Anthropic client ready")`**

This simply displays a confirmation message. Since Python runs top to bottom and stops if it hits an error, reaching this line means everything above worked without crashing.

### What the output means

```
Anthropic client ready
```

This one line of output is your green light. The client was built successfully, the API key was accepted, and you are ready to make real calls to Claude.

---

## Cell 3: Smoke Test — Sending Your First Message to Claude

```python
response = client.messages.create(  # calls the Messages API; returns a Message object
    model="claude-haiku-4-5",        # selects the fast/cheap Haiku model for the smoke test
    #    model="claude-sonnet-4-5",  # alternative: Sonnet for higher-quality tasks
    max_tokens=50,                   # caps the response length; prevents runaway token use
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]  # minimal valid turn
)
print(response.content[0].text)  # response.content is a list of blocks; [0] is the first (text) block
```

### What this code does

This cell is called a **smoke test** — a term borrowed from engineering that means "let's switch it on and check that nothing catches fire." It sends the simplest possible message to Claude to confirm the whole setup works end to end.

---

**Lines 1–5: `response = client.messages.create(...)`**

This is a single instruction spread across multiple lines for readability. Python knows it is not finished until the parenthesis is closed, so it keeps reading until it finds the matching `)`.

`client.messages.create()` means: look inside the `client` object, find the `messages` section, and call the `create` function. This is the function that actually sends a message to Claude over the internet and waits for a reply.

Think of it like using a walkie-talkie: you press the button (`create`), speak your message, wait, and Claude talks back.

The reply from Claude gets stored in a variable called `response`. Think of `response` as a box that now holds everything Claude sent back — not just the text, but also information about how many words were used, which model answered, and so on.

Now let's look at the **parameters** — the settings we passed inside the parentheses:

---

**`model="claude-haiku-4-5"`**

A **parameter** is a setting you pass to a function to control how it behaves, like knobs on a stereo.

`model` tells the API which version of Claude to use. `"claude-haiku-4-5"` is Anthropic's fastest and most economical model — good for quick tests and simple tasks. Think of it as the express lane.

The next line (`# model="claude-sonnet-4-5"`) starts with a `#`, which makes it a **comment**. Python completely ignores anything after a `#` on a line. Comments are notes for humans reading the code. This comment shows you an alternative model you could swap in if you needed higher-quality responses.

---

**`max_tokens=50`**

A **token** is the unit Claude uses to measure text length — roughly one word or part of a word. "Hello" is one token. "unbelievable" might be two or three tokens.

`max_tokens=50` sets a hard ceiling: Claude will not write more than 50 tokens in its reply. This is a safety net. Without it, a misbehaving prompt could cause Claude to write thousands of words and use up your API credit quickly. For a simple "say hello" test, 50 tokens is more than enough.

---

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual conversation you are sending to Claude. Let's unpack the structure carefully.

The outer brackets `[...]` create a **list** — an ordered collection of items, like a numbered list on paper.

Inside the list is one item: `{"role": "user", "content": "Say hello in one short sentence."}`. The curly braces `{...}` create a **dictionary** — a lookup table made of paired labels and values, like a mini form where each field has a name and a value.

This dictionary has two fields:
- `"role": "user"` — this tells Claude the message is coming from a human user (as opposed to the AI assistant).
- `"content": "Say hello in one short sentence."` — this is the actual text of the message.

The Claude API is designed around **turns** — alternating messages between a user and an assistant, like a conversation. Even for a single message, you must wrap it in this structure.

---

**`print(response.content[0].text)`**

After Claude replies, everything is packed into the `response` object. This line digs into that object to find the actual text.

`response.content` is a **list of content blocks**. Claude can theoretically return multiple blocks (text, images, etc.), so the API always gives you a list even if there is only one item.

`[0]` means "give me the item at position zero." In Python, counting always starts at zero, not one. So position `0` is the first item, position `1` would be the second, and so on.

`.text` pulls out the text property of that first content block — the actual words Claude wrote.

Put together: *"Look in the response, find the list of content blocks, take the first one, and print its text."*

### What the output means

You will see a short greeting from Claude, something like:

```
Hello! It's wonderful to meet you today.
```

This confirms the entire pipeline is working: your `.env` file was read, the API key was valid, the request reached Anthropic's servers, Claude processed it, and the reply came back correctly. You are ready to proceed with the lab.

---

## Cell 4: Installing and Verifying Required Modules

```python
import sys   # sys gives us access to the Python interpreter info
import json  # json is used throughout for structured output parsing
import re    # re is used in rubric scoring for pattern matching

# Print Python version so learners can confirm ≥ 3.9
print("Python version:", sys.version)

# Confirm all imports succeeded
print("json module ready:", json.__version__ if hasattr(json, '__version__') else 'built-in')
print("re module ready: built-in")
print("Environment check complete ✅")
```

### What this code does

This cell imports three standard Python modules (all built in — no installation needed) and then prints confirmation messages so you can verify everything loaded correctly.

---

**Line 1: `import sys`**

`sys` is a built-in Python module that gives you information about the Python program itself — the interpreter. Think of the **interpreter** as the translator that reads your Python code and turns it into instructions the computer's processor can actually run.

We import `sys` here mainly to read and display the Python version number on the next few lines.

---

**Line 2: `import json`**

**JSON** stands for JavaScript Object Notation. Despite the name, it has nothing to do with JavaScript in practice — it has become a universal format for exchanging structured data between programs and over the internet. Think of it as a very organized, standardized shopping list that both humans and computers can read.

A JSON structure looks like this:

```json
{"name": "Claude", "version": 3, "capable": true}
```

The `json` module lets Python read JSON text (convert it into Python dictionaries and lists) and write JSON text (convert Python dictionaries and lists back into that format). This lab uses JSON extensively because we will ask Claude to reply in JSON format so we can reliably extract numeric scores.

---

**Line 3: `import re`**

`re` stands for **regular expressions** — a powerful mini-language for searching through text using patterns. Think of it like a very advanced "Find" function in a word processor.

For example, a regular expression can find "any sequence of digits" or "any word that starts with a capital letter" in a block of text. In this lab, `re` is used to help pull structured scores out of Claude's text responses when JSON parsing alone isn't enough.

---

**Line 5: `print("Python version:", sys.version)`**

`sys.version` is a **property** of the `sys` module — a stored piece of information you can read. It contains a string (text) describing the exact version of Python running.

The lab requires Python version 3.9 or higher. This print statement lets you confirm you meet that requirement.

---

**Lines 8–10: The three print statements**

```python
print("json module ready:", json.__version__ if hasattr(json, '__version__') else 'built-in')
```

This line is a single `print` statement with a slightly complex expression inside it. Let's break it down.

`hasattr(json, '__version__')` is a Python function that asks: "Does the `json` module have a property called `__version__`?" (Properties with double underscores on both sides are Python's way of storing internal metadata.) It returns `True` or `False`.

The structure `A if CONDITION else B` is called a **conditional expression** — a compact way of saying "use A if the condition is true, otherwise use B." Think of it as a one-line decision. Here: if `json` has a version number, print it; if not, print the word `'built-in'` instead.

This exists because `json` is built into Python and doesn't always expose a version number — this code handles both cases gracefully.

```python
print("re module ready: built-in")
```

This one is straightforward — it just prints a confirmation message. `re` is always built in, so no version check is needed.

```python
print("Environment check complete ✅")
```

A simple final confirmation message. Reaching this line means all three imports worked without error.

### What the output means

You will see something like:

```
Python version: 3.10.0 (default, ...)
json module ready: built-in
re module ready: built-in
Environment check complete ✅
```

The key thing to check is that the Python version shown is 3.9 or higher (3.10, 3.11, 3.12, etc. are all fine). If the version is older, some code in the lab may not work correctly.

---

## Glossary

**API (Application Programming Interface)**
A set of rules that lets one program talk to another. Here, the Anthropic API lets your Python code send messages to Claude and receive replies — like a waiter who takes your order to the kitchen and brings back what you asked for.

**API key**
A unique secret string of characters that proves your identity to a service. Like a password, but specifically for programs to use when making API calls. Never share your API key publicly.

**Assignment**
In Python, the `=` sign means "store this value in this variable." It is not a mathematical equality statement.

**Boolean**
A value that is either `True` or `False` — nothing else. Named after mathematician George Boole. Used for yes/no decisions in code.

**Built-in**
A module or function that comes with Python automatically — you don't need to install it separately.

**Call (a function)**
To run a function by writing its name followed by parentheses. Like pressing a button to trigger a machine.

**Class**
A blueprint or template for creating objects. The `Anthropic` class is a blueprint for creating a client object that knows how to talk to Anthropic's servers.

**Client**
In programming, a client is the part of a system that makes requests to a server. Your Python code is the client; Anthropic's servers are the server.

**Comment**
A note in code that Python ignores. Anything after a `#` on a line is a comment, written for human readers only.

**Conditional expression**
A compact one-line decision: `A if CONDITION else B`. Uses A if the condition is true, B if it's false.

**Dictionary**
A data structure that stores pairs of labels and values, like a phone book where you look up a name to get a number. In Python, written with curly braces: `{"key": "value"}`.

**dotenv**
A library that reads `.env` files and loads their contents into environment variables.

**.env file**
A plain text file that stores secret configuration values (like API keys) privately on your computer. Never shared publicly or uploaded to code repositories.

**Environment variable**
A named value stored in the background of your computer's operating system session. Programs can read these values without you typing them into the code directly.

**Function**
A named set of instructions you can trigger by writing its name followed by parentheses. Like pressing a labeled button on a machine.

**hasattr()**
A Python built-in function that checks whether an object has a particular property. Returns `True` or `False`.

**Import**
The act of bringing a library, module, or specific tool into your current workspace so you can use it.

**Interpreter**
The program that reads your Python code and translates it into instructions the computer's processor can run.

**JSON (JavaScript Object Notation)**
A standardized text format for representing structured data — dictionaries, lists, numbers, and text — that both humans and computers can read and write. Widely used for exchanging data between programs.

**Library (also: package, module)**
A collection of pre-written code that someone has published for others to use. Saves you from having to write common functionality from scratch.

**List**
An ordered collection of items in Python, written with square brackets: `[item1, item2, item3]`. Items are accessed by their position number, starting at zero.

**max_tokens**
A parameter that sets a hard ceiling on how many tokens Claude can produce in a single response.

**Model**
A specific trained version of an AI system. Different models have different capabilities, speeds, and costs. `claude-haiku-4-5` is fast and economical; `claude-sonnet-4-5` is more capable.

**Module**
See "Library." In Python, these words are often used interchangeably, though technically a module is a single file of code and a package is a collection of modules.

**None**
A special Python value meaning "nothing" or "no value." Used when a variable exists but has no meaningful content yet.

**Object**
A working instance built from a class blueprint. The `client` variable holds an Anthropic object — a fully constructed, ready-to-use thing built from the Anthropic blueprint.

**Operating system**
The core software that manages your computer's hardware and runs your programs. Examples: Windows, macOS, Linux.

**Parameter**
A setting you pass to a function to control how it behaves. Like knobs on a piece of equipment.

**Print (function)**
A built-in Python function that displays text or values in the output area below a cell. Used to give feedback about what the code has done.

**Property**
A piece of information stored inside a module or object that you can read by writing its name after a dot. For example, `sys.version` is the version property of the `sys` module.

**Regular expression (re)**
A pattern-matching mini-language for searching through text. Like an advanced "Find" function that can search for patterns rather than just exact words.

**Smoke test**
A quick initial test to confirm that a system is basically functional before running more complex tests. If the smoke test passes, you proceed; if it fails, something fundamental is broken.

**String**
A piece of text in Python, always wrapped in quotation marks (single or double): `"Hello"` or `'Hello'`.

**Token**
The unit of text that language models like Claude use internally. Roughly one word or part of a word. APIs charge by token and impose limits in tokens, not characters or words.

**Turn**
One message in a conversation. The Claude API structures conversations as alternating turns: one from the user, one from the assistant, one from the user, and so on.

**Variable**
A labeled box in Python's memory that holds a value. You create one by writing a name, then `=`, then the value you want to store.