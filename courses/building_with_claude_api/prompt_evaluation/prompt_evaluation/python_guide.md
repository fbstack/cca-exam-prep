# Python Guide: Prompt Evaluation Lab

A plain-English walkthrough of every line of code in the JupyterLab notebook.
No coding background required.

---

## What Python is (for complete beginners)

Python is a language for giving instructions to a computer. Think of it like writing a very precise recipe: you write each step in order, and the computer follows every step exactly as written. Unlike a recipe for a human, you cannot skip steps or assume the computer will "figure it out" — every instruction must be spelled out. This notebook is a series of those recipes, organized into named sections called **cells**. Each cell does one focused job, and you run them in order from top to bottom.

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

---

**Line 1: `from dotenv import load_dotenv`**

This line goes to a collection of pre-written Python tools called **dotenv** and picks up one specific tool named `load_dotenv`, bringing it into our workspace so we can use it.

To understand what "importing" means: imagine Python has a giant warehouse of specialized toolboxes. Each toolbox is called a **library** or **module**. By default, none of those tools are sitting on your workbench — you have to explicitly bring them over. The word `import` is how you do that. The phrase `from dotenv import load_dotenv` is more specific: instead of bringing the entire dotenv toolbox, we're reaching in and pulling out just the one tool we need — the `load_dotenv` function.

A **function** is a named set of instructions packaged together so you can run them by calling that name. Think of it as a saved recipe you can cook any time by just saying its name out loud.

Now, what is `dotenv`? The name stands for "dot env file." A `.env` file (pronounced "dot-env file") is a plain text file on your computer that stores **sensitive values** — things like passwords and API keys — that you don't want to type directly into your code. By keeping them in a separate file, you protect yourself from accidentally sharing them when you share your code. `dotenv` is the tool that reads that file.

---

**Line 2: `import os`**

This brings in Python's built-in `os` module. The **os module** is a toolbox that lets Python talk to the operating system — the underlying software (like Windows or macOS) that runs your computer. Among many other things, the `os` module can read **environment variables**.

An **environment variable** is like a sticky note that the operating system keeps on a shared corkboard. Any program running on your computer can read notes from this corkboard if it knows the note's label. When `load_dotenv()` runs (next line), it copies the values from your `.env` file onto this corkboard. Then `os` gives us the tool to read them back.

---

**Line 3: `load_dotenv()`**

This is where we actually *use* the tool we imported in Line 1. Calling a function by name followed by parentheses `()` is like pressing a "run" button for that recipe. Here, `load_dotenv()` opens the `.env` file sitting in the same folder as this notebook, reads every key-value pair inside it (for example, `ANTHROPIC_API_KEY=your-secret-key-here`), and posts those values to the operating system's environment variable corkboard so other parts of our program can read them.

Notice the comment after the `#` symbol: Python ignores everything on a line after `#`. These are called **comments** — notes the human programmer writes for other humans (or their future selves) to explain what the code is doing. The computer skips them entirely.

---

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line does two things in one:

First, `os.getenv("ANTHROPIC_API_KEY")` calls another function — this one from the `os` toolbox — named `getenv`. The name means "get environment variable." We hand it the label `"ANTHROPIC_API_KEY"` (in quotation marks, because it's a piece of text, which Python calls a **string**), and it goes to the corkboard and reads the sticky note with that label. Whatever value is written on that note gets handed back.

Second, `api_key =` stores that returned value in a **variable** named `api_key`. A variable is like a labeled box: you put something inside it and later retrieve it by using the label. From this point forward, whenever we write `api_key` anywhere in our code, Python opens that box and uses whatever is inside.

If the sticky note doesn't exist on the corkboard (meaning `load_dotenv()` didn't find the key in your `.env` file), `os.getenv()` hands back a special value called `None`, which is Python's way of saying "nothing is here."

An **API key** is a unique password that proves to Anthropic's servers that you are an authorized user. Without it, the server won't respond to your requests. It is called a "key" because, like a physical key, it unlocks access to a service.

---

**Line 5: `print("API key loaded:", api_key is not None)`**

This line shows a result on screen. `print()` is a built-in Python function that displays whatever you put inside its parentheses.

We're printing two things, separated by a comma:
- The text string `"API key loaded:"` — a label so we know what we're looking at
- The result of `api_key is not None`

The phrase `api_key is not None` is a **comparison expression**. It asks a yes-or-no question: "Is the value stored in `api_key` something other than `None`?" Python evaluates this question and produces one of two possible answers: `True` or `False`. These are called **boolean values** — named after the mathematician George Boole, who developed the logic of true/false reasoning.

So if your API key was found and loaded correctly, this line prints: `API key loaded: True`
If something went wrong and the key is missing, it prints: `API key loaded: False`

---

### What the output means

When you run this cell, you'll see one line printed, something like:

```
API key loaded: True
```

`True` tells you the setup worked — Python found your API key in the `.env` file and stored it in the `api_key` variable, ready to use in the next cells. If you see `False`, you'll need to check that your `.env` file exists and that it contains a line that reads `ANTHROPIC_API_KEY=` followed by your actual key.

---

## Cell 2: Creating the Connection to Claude

```python
from anthropic import Anthropic  # imports the Anthropic SDK client class

client = Anthropic(api_key=api_key)  # creates an authenticated client; all API calls go through this object
print("Anthropic client ready")       # confirms client was created without error
```

### What this code does

---

**Line 1: `from anthropic import Anthropic`**

Following the same pattern as Cell 1, this line goes to a toolbox — this time the `anthropic` library — and pulls out a specific tool called `Anthropic`. Notice the letter casing: `anthropic` (all lowercase) is the name of the toolbox, and `Anthropic` (capital A) is the name of the specific tool inside it.

The `Anthropic` tool is a **class**, which you can think of as a blueprint or mold for creating objects. An **object** in Python is a bundle that combines data and actions together. We use this class in the next line to create a fully configured connection to Anthropic's AI service. A **library** (also called an **SDK**, which stands for Software Development Kit) is a pre-packaged collection of code that Anthropic wrote and published so developers don't have to build the connection logic from scratch.

---

**Line 2: `client = Anthropic(api_key=api_key)`**

This is one of the most important lines in the notebook. It creates what's called a **client** — an object that handles all communication with Anthropic's API (their AI service).

Think of it this way: if Anthropic's AI service is a restaurant kitchen, then this `client` is the waiter assigned specifically to your table. Every time you want to ask Claude a question, you give the request to your waiter (the `client`), who carries it to the kitchen (Anthropic's servers), waits for the response, and brings it back to you.

`Anthropic(api_key=api_key)` is calling the `Anthropic` blueprint to create one of these waiter objects. The `api_key=api_key` part is how we hand the waiter your credentials — the API key we retrieved in Cell 1. The waiter will attach this key to every request they carry, so Anthropic's servers know the requests are coming from an authorized account.

`client =` stores this newly created waiter object in a variable named `client`, so we can use it repeatedly throughout the notebook.

---

**Line 3: `print("Anthropic client ready")`**

This simply prints the message `Anthropic client ready` to the screen. It's a confirmation for the human running the notebook — if this line prints without any error message appearing above it, we know the object was created successfully.

---

### What the output means

```
Anthropic client ready
```

This single line of output is a green light. It tells you the connection to Anthropic's service has been configured and is ready to use. If something had gone wrong (for example, if the `anthropic` library wasn't installed, or the `api_key` variable was empty), Python would have shown an error message instead of this confirmation.

---

## Cell 3: The Smoke Test — Sending Your First Message to Claude

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

---

**Lines 1–5 together: `response = client.messages.create(...)`**

This block sends a real message to Claude and stores the response. Let's break it apart:

`client.messages.create(...)` uses the `client` waiter object we created in Cell 2. The `.messages` part navigates to a specific section of the client — the section that handles conversations. The `.create(...)` part tells that section to create a new message exchange. Together, `client.messages.create(...)` is the instruction that says "send this conversation to Claude and return what Claude says back."

Everything inside the parentheses — spread across multiple lines for readability — is a set of **arguments**: the specific details of our request. Python doesn't care if arguments are on one line or many lines; as long as the opening parenthesis `(` hasn't been closed yet, it knows the statement is still going.

The entire result of this operation is stored in the variable `response`. Think of `response` as a box that now contains Claude's entire reply, wrapped up with metadata.

---

**Line 2: `model="claude-haiku-4-5"`**

This is our first argument. It tells Anthropic which AI model we want to handle our request. Anthropic offers several versions of Claude, each with different speed, cost, and capability trade-offs.

`"claude-haiku-4-5"` refers to Claude Haiku — a version optimized to be fast and inexpensive. The name "haiku" reflects its design philosophy: brief, quick, efficient. For a **smoke test** (a quick check that everything is working, like lighting a match to see if there's a gas leak before turning on the stove), Haiku is the right choice. We're not asking for deep thinking here — we just want proof that the API connection works.

---

**Line 3: `#    model="claude-sonnet-4-5"`**

This entire line is a comment — it starts with `#`, so Python ignores it completely. It's a note to the human saying: if you want a more capable model, you could replace the line above with this one. `claude-sonnet-4-5` refers to Claude Sonnet, a more powerful (and more expensive) model. This pattern — commenting out an alternative — is a common programmer technique for leaving a reminder without breaking the working code.

---

**Line 4: `max_tokens=50`**

This argument sets a hard limit on how long Claude's response can be, measured in **tokens**.

A **token** is the basic unit Claude uses to process and generate text — roughly one word or one fragment of a word. For example, the word "unhappiness" might be split into the tokens `un`, `happi`, and `ness`. The word "cat" is a single token. Longer or rarer words tend to split into more tokens.

`max_tokens=50` tells Claude: "Use no more than 50 tokens in your response." Since we asked Claude to say hello in one short sentence, 50 tokens is more than enough — and this limit prevents accidentally generating a very long (and expensive) response if something behaved unexpectedly.

---

**Line 5: `messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This argument provides the actual conversation we want Claude to respond to. Let's unwrap it layer by layer:

The outer structure is `messages=[...]`. The square brackets `[...]` define a **list** — an ordered collection of items, like a numbered list on paper. In this case, the list represents the conversation history.

Inside the list, there is one item: `{"role": "user", "content": "Say hello in one short sentence."}`. The curly braces `{...}` define a **dictionary** — a lookup table where every piece of data has a label (called a **key**) and a value. Think of it like a form with labeled fields: the label `"role"` has the value `"user"`, and the label `"content"` has the value `"Say hello in one short sentence."`.

The `"role"` field tells Claude who is speaking. In the Messages API, conversations alternate between two roles: `"user"` (the person asking) and `"assistant"` (Claude answering). By marking this message as `"user"`, we're saying this is the human's opening message.

The `"content"` field contains the actual text of that message — our prompt to Claude.

So in plain English, this entire argument says: "Here is a conversation. So far, only one thing has been said: the user asked Claude to say hello in one short sentence."

---

**Line 6: `print(response.content[0].text)`**

After Claude responds, everything comes back inside the `response` variable. But that variable contains a lot of structured information — not just the text. This line navigates to the actual words Claude wrote.

`response.content` accesses the `content` portion of the response — which is a list of **content blocks**. Claude's responses are structured this way because, in more advanced use cases, a response might contain multiple blocks (text, tool results, etc.).

`[0]` picks the first item in that list. In Python, lists are numbered starting from zero, so `[0]` means "the first one." This is called **indexing** — specifying a position in a list by its number.

`.text` accesses the `text` field of that first content block — the actual string of words Claude wrote.

Put together, `response.content[0].text` means: "Go into the response, find the list of content blocks, take the first one, and give me its text."

Then `print(...)` displays that text on screen.

---

### What the output means

You'll see something like:

```
Hello! It's a pleasure to meet you today.
```

This is Claude's actual reply. Seeing this line means everything is working end-to-end: your `.env` file loaded correctly, your API key authenticated successfully, the request traveled to Anthropic's servers, Claude processed it, and the response came back and was displayed. The entire pipeline is operational.

---

## Cell 4: Installing and Confirming Required Tools

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

---

**Line 1: `import sys`**

This brings in Python's `sys` module (short for "system"). The **sys module** is a toolbox that gives your program information about the Python environment it's running inside — things like which version of Python is installed, where Python looks for files, and how the program can exit. We're importing it here so we can read and display the Python version number.

---

**Line 2: `import json`**

This brings in Python's `json` module. **JSON** stands for JavaScript Object Notation — but don't let that name mislead you, it has nothing to do with JavaScript in practice. JSON is a standardized way of writing structured data that both humans and computers can read. It looks like Python dictionaries and lists, which is why Python handles it so naturally.

For example, a JSON object looks like this:
```
{"name": "Alice", "score": 95, "passed": true}
```

In this lab, Claude will be asked to return responses formatted as JSON, and the `json` module gives us tools to parse (read and interpret) those responses — converting them from raw text into Python dictionaries and lists that we can work with.

---

**Line 3: `import re`**

This brings in Python's `re` module, which stands for **regular expressions**. A regular expression is a pattern-matching language — a way of describing text patterns so Python can search for, find, or extract pieces of text that match those patterns.

For example, a regular expression could describe "any sequence of digits" or "the word 'score' followed by a colon and a number." In this lab, `re` will be used in the scoring rubric to find specific patterns in Claude's responses. Think of it as a very powerful search tool — far more flexible than a simple word search.

---

**Line 5: `print("Python version:", sys.version)`**

This prints the version of Python currently installed. `sys.version` is a piece of stored information inside the `sys` toolbox — it's a pre-written string containing the version number and some additional details. The lab requires Python version 3.9 or higher (written as `≥ 3.9`) because some features used later in the notebook aren't available in older versions.

---

**Lines 8–10: The three `print` statements for module confirmation**

`print("json module ready:", json.__version__ if hasattr(json, '__version__') else 'built-in')`

This line is doing something slightly more complex. Let's work through it:

`json.__version__` would be the version number of the `json` module, if it has one. The double underscores around `__version__` are a Python convention meaning "this is a built-in property."

`hasattr(json, '__version__')` checks whether the `json` module even *has* a `__version__` property. `hasattr` stands for "has attribute" — it asks the question "does this object have a property by this name?" and returns `True` or `False`.

The `if ... else ...` structure here is a compact decision: "If `json` has a `__version__` property, use it; otherwise, print the word `'built-in'`." This is called a **ternary expression** — a one-line way of making a choice.

Since `json` is a module built directly into Python (it ships with the language), it doesn't have a separate version number — so this will print `built-in`.

`print("re module ready: built-in")` is a plain confirmation that `re` was imported without error. Since `re` is also built into Python, this line doesn't need any version-checking logic.

`print("Environment check complete ✅")` is a human-readable signal that all three imports succeeded. If any of the imports above had failed, Python would have crashed with an error before reaching this line — so seeing this message means the imports all worked.

---

### What the output means

You'll see something like:

```
Python version: 3.10.0 (default, Oct  4 2021, 17:00:00)
json module ready: built-in
re module ready: built-in
Environment check complete ✅
```

The first line confirms your Python version — make sure it starts with `3.9` or higher. The remaining lines confirm that the tools needed for the rest of the lab are available and ready. The checkmark emoji is a visual cue that everything passed.

---

## Glossary

**API (Application Programming Interface)**
A defined way for one piece of software to talk to another. In this notebook, the API is Anthropic's service that lets our Python code send messages to Claude and receive responses. Think of it as a waiter who takes your order (request) to the kitchen (Anthropic's servers) and brings back what you asked for (Claude's response).

**API key**
A unique secret code that proves to a service (like Anthropic) that you are an authorized user. It functions like a password. It must be kept private and never shared publicly.

**Argument**
A specific value you provide when calling a function, to tell it exactly what to work with. In `os.getenv("ANTHROPIC_API_KEY")`, the text `"ANTHROPIC_API_KEY"` is the argument.

**Boolean**
A value that can only be `True` or `False`. Named after the mathematician George Boole. Used to represent yes/no or on/off conditions.

**Cell**
In JupyterLab, a single block of code (or text) that can be run independently. Cells run in order, and variables created in one cell are available in later cells.

**Class**
A blueprint or mold for creating objects. When you call a class (like `Anthropic(...)`), Python creates a new object built from that blueprint.

**Client**
In this notebook, the `client` object is the configured connection to Anthropic's API. All requests to Claude are sent through it.

**Comment**
Text in Python code that begins with `#`. The computer ignores it entirely. Comments are notes for human readers explaining what the code does or why.

**Dictionary**
A Python data structure that stores pairs of labels (keys) and values, like a phone book where you look up a name to get a number. Written with curly braces: `{"key": "value"}`.

**dotenv / .env file**
A plain text file that stores sensitive values like API keys outside of your main code. The `dotenv` library reads this file and loads its values into the program's environment.

**Environment variable**
A named value stored at the operating system level, accessible by any program running on the computer. Used to pass configuration (like API keys) into programs without hardcoding them.

**Function**
A named, reusable set of instructions. You call a function by writing its name followed by parentheses. Think of it as a saved recipe you can run any time by saying its name.

**Import**
The act of bringing a library or specific tool from a library into your current working environment. Like pulling a tool off a warehouse shelf and placing it on your workbench.

**Indexing**
Accessing a specific item in a list by its position number. Python lists start counting at zero, so `[0]` is the first item, `[1]` is the second, and so on.

**JSON (JavaScript Object Notation)**
A standardized text format for representing structured data. Looks like Python dictionaries and lists. Used to exchange data between programs in a way both humans and computers can read.

**Library / Module / SDK**
Pre-written collections of Python code that you can import and use. A **library** is the general term; a **module** is often a single file of code; an **SDK** (Software Development Kit) is a library specifically designed to help developers interact with a particular service.

**List**
A Python data structure that holds an ordered collection of items, like a numbered list on paper. Written with square brackets: `["item one", "item two", "item three"]`.

**`None`**
A special Python value meaning "nothing" or "no value here." Functions return `None` when they have nothing meaningful to hand back.

**Object**
A bundle in Python that combines data and actions (functions) together. The `client` in Cell 2 is an object — it holds your API credentials and provides functions for making API calls.

**Regular expression (regex)**
A pattern-matching language used to search for, find, or extract pieces of text that match a described pattern. The `re` module in Python handles regular expressions.

**Response**
In this notebook, the complete reply that comes back from Claude when you send a message. It's stored as a structured object containing the text, metadata, and token usage information.

**Smoke test**
A quick, minimal check to confirm that a system works at all before doing serious work with it. Named after the electrical engineering practice of powering up a circuit and watching for smoke — a simple pass/fail test.

**String**
A piece of text in Python. Strings are written inside quotation marks, either single (`'hello'`) or double (`"hello"`). Python treats everything inside the quotes as literal text.

**Token**
The basic unit of text that AI models like Claude read and generate. Roughly corresponds to a word or word-fragment. Models charge for API usage in tokens, so tracking token counts helps manage costs.

**Variable**
A named storage location in a program. Like a labeled box — you put a value in, give it a name, and later retrieve it using that name.