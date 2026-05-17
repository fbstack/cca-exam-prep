# Python Guide: Prompt Evaluation Lab

A plain-English walkthrough of every line of code in the JupyterLab notebook.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving instructions to a computer. Think of it like writing a very precise recipe — the computer follows each instruction exactly, in order, with no guesswork. The instructions are written in plain-looking text that humans can read, but the computer can also execute. A **JupyterLab notebook** is a special environment where you write those instructions in chunks called **cells**, run each chunk one at a time, and see the results appear right below each chunk. This makes it ideal for learning, because you can see exactly what each piece of code does before moving on.

---

## Cell 1: Loading Your Secret API Key

This cell handles the very first thing any program that talks to an external service needs to do: find and load the secret password (the API key) that proves you are allowed to use that service.

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

This line brings a specific tool into your workspace so you can use it.

To understand what this means, think of Python as a giant toolbox warehouse. The warehouse holds thousands of specialized tools (called **libraries** or **modules**). Your workspace starts empty — you only bring in the tools you actually need for the job. The act of bringing a tool in is called **importing**.

The tool being imported here is called `load_dotenv`. It comes from a library called `dotenv` (pronounced "dot-env"). The `dotenv` library's entire purpose is to read a special file on your computer called `.env` (literally a file named with just a dot and the letters "env"). That file is where you store secret information — like passwords and API keys — without having to write those secrets directly in your code. Writing secrets directly in code is dangerous because code often gets shared; the `.env` file stays private on your machine.

The word `from` tells Python *which* library to reach into. The word `import` tells Python *what specific tool* to pull out of that library.

---

**Line 2: `import os`**

This imports another tool — this one called `os`, which stands for "operating system."

Your **operating system** is the software that runs your computer (Windows, macOS, Linux). The `os` module gives Python a way to talk to the operating system — to ask it questions like "what files are in this folder?" or, in our case, "what is stored in the environment variables?"

**Environment variables** are like sticky notes that your operating system keeps posted in its memory. Programs can read these sticky notes to get configuration information — things like where to find a database, what mode to run in, or what the API key is. They are called "environment" variables because they describe the *environment* your program is running in, not the program itself.

We need `os` because, after `load_dotenv` reads the `.env` file, it pastes the contents of that file onto those operating system sticky notes. Then `os` lets us reach up and read those sticky notes.

---

**Line 3: `load_dotenv()`**

This actually *runs* (or **calls**) the `load_dotenv` tool we imported in Line 1.

A **function** is like a named recipe. When you write just the name followed by parentheses `()`, you are saying "execute this recipe now." The parentheses are the trigger — without them, you are just referring to the recipe by name but not cooking anything.

What this function does behind the scenes: it looks for a file called `.env` in the current folder, reads it line by line, and for each line that looks like `KEY=VALUE`, it takes the key and value and posts them as environment variables (those sticky notes we talked about). After this line runs, the API key that was sitting quietly in your `.env` file is now accessible to the rest of your program.

---

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line reads one specific sticky note from the operating system's memory and saves what it finds into a **variable** called `api_key`.

A **variable** is like a labeled box. You put something inside it and give the box a name, so you can find that thing again later just by using the name. Here, the box is named `api_key`.

The `os.getenv(...)` part is calling a function called `getenv` that lives inside the `os` tool. The dot (`.`) between `os` and `getenv` means "look inside `os` and find the tool called `getenv`." Think of it like saying "in the `os` toolbox, use the `getenv` wrench."

The text inside the parentheses — `"ANTHROPIC_API_KEY"` — is the **argument**, meaning the specific information we are passing to the function. We are telling `getenv`: "go look for the sticky note labeled `ANTHROPIC_API_KEY` and bring back whatever is written on it."

The `=` sign here does not mean "equals" in a math sense. It means "take whatever is on the right side and store it in the box named on the left side." So after this line runs, the box called `api_key` contains your actual API key string (a long string of random characters like `sk-ant-abc123...`). If the key was not found, the box contains a special Python value called `None`, which means "nothing is here."

---

**Line 5: `print("API key loaded:", api_key is not None)`**

This line displays a message to the screen so you can see whether the previous step worked.

`print(...)` is one of Python's most fundamental built-in functions. Whatever you put inside the parentheses gets displayed as text output below the cell.

Here we are printing two things separated by a comma:
- The text `"API key loaded:"` — this is a **string** (any sequence of characters wrapped in quotes), and it is just a label to make the output readable.
- `api_key is not None` — this is a **comparison expression**. Python evaluates it and produces either `True` or `False`. It is asking: "is the thing inside the `api_key` box something other than nothing?" If your API key was found, this evaluates to `True`. If it was not found, it evaluates to `False`.

### What the output means

You will see either:
- `API key loaded: True` — your `.env` file was found, it contained the key `ANTHROPIC_API_KEY`, and its value was successfully loaded. You are ready to proceed.
- `API key loaded: False` — something went wrong. Either the `.env` file does not exist, or it does not contain a line with exactly `ANTHROPIC_API_KEY=your_key_here`. You would need to fix that before continuing.

This output matters because everything else in the lab depends on having a valid API key. Checking it here, at the very start, saves you from confusing error messages later.

---

## Cell 2: Creating the API Client

This cell takes the API key you just loaded and uses it to create an **authenticated client** — a dedicated connection object through which all your conversations with Claude will flow.

```python
from anthropic import Anthropic  # imports the Anthropic SDK client class

client = Anthropic(api_key=api_key)  # creates an authenticated client; all API calls go through this object
print("Anthropic client ready")       # confirms client was created without error
```

### What this code does

---

**Line 1: `from anthropic import Anthropic`**

This imports the main tool from the `anthropic` library — a tool called `Anthropic` (capital A).

The `anthropic` library (lowercase) is Anthropic's official **SDK**. An **SDK** stands for "Software Development Kit" — it is a pre-built package of tools that makes it easier to talk to a specific service (in this case, Claude). Without the SDK, you would have to write a lot of complicated code yourself just to send a message and receive a reply. The SDK handles all that complexity for you.

Inside the `anthropic` library is a tool called `Anthropic` (capital A). This is a **class** — think of a class as a blueprint or a template for creating a specific kind of object. In this case, the `Anthropic` class is the blueprint for creating a "client" — a connection manager that knows how to talk to Anthropic's servers.

---

**Line 2: `client = Anthropic(api_key=api_key)`**

This line uses the `Anthropic` blueprint to create an actual client object and stores it in a variable (labeled box) called `client`.

When you write `Anthropic(...)` with parentheses, you are **instantiating** the class — meaning you are using the blueprint to manufacture a real, working object. Think of the class as a cookie cutter and this line as pressing it into the dough to make an actual cookie.

Inside the parentheses, `api_key=api_key` is passing a **named argument**. The part before the `=` (`api_key`) is the name of the slot you are filling in. The part after the `=` (`api_key`) is the variable (labeled box) that holds your actual API key string from Cell 1. The client needs this key so that when it contacts Anthropic's servers, it can prove your identity and that you have permission to use the service.

After this line runs, `client` is a fully configured object that can send messages to Claude on your behalf. Every future API call in this notebook will go through this `client` object.

---

**Line 3: `print("Anthropic client ready")`**

This prints a confirmation message to the screen. If the client creation had failed (for example, due to a network problem or invalid setup), Python would have thrown an **error** before reaching this line, and you would never see this message. Seeing it means everything worked.

### What the output means

You will see: `Anthropic client ready`

This tells you the client was created without errors. The key was accepted in the sense that the client object was built successfully. Note that the actual API key is not verified against Anthropic's servers until you make your first real request in Cell 3.

---

## Cell 3: Smoke Test — Sending Your First Message to Claude

This cell sends a simple test message to Claude to confirm that the entire connection — from your computer, through the internet, to Anthropic's servers, and back — is working correctly. In software development, this kind of minimal first test is called a **smoke test** (the name comes from electronics: if you plug something in and it doesn't smoke, the basic circuitry works).

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

**Lines 1–5: `response = client.messages.create(...)`**

This entire block is one statement that sends a message to Claude and waits for a reply. The reply is stored in a variable (labeled box) called `response`.

Let's understand the structure before diving into the individual parts. The `client.messages.create(...)` is calling a function that lives deep inside the client object. The dots are like navigating a filing cabinet: go into `client`, then into the `messages` drawer, then use the `create` tool inside that drawer. The parentheses at the end trigger the function.

Because this function call spans multiple lines (for readability), Python knows it is all one statement because the opening parenthesis `(` on the first line has not been closed yet — Python keeps reading until it finds the matching `)`.

The function sends your request to Anthropic's servers over the internet and **blocks** (pauses and waits) until Claude has generated a response. Then it packages that response into an object and puts it in the `response` variable.

---

**`model="claude-haiku-4-5"`**

This is a named argument telling the API *which version of Claude* to use.

Anthropic offers several models, each with different trade-offs between speed, cost, and capability:
- **Claude Haiku** is the fastest and cheapest — ideal for quick tests and high-volume tasks where speed matters more than maximum quality.
- **Claude Sonnet** is more capable and produces higher-quality responses, but is slower and costs more per request.

For a smoke test (just checking that the connection works), Haiku is the right choice — there is no point spending extra tokens just to verify the plumbing works.

---

**`#    model="claude-sonnet-4-5",`**

Lines that start with `#` are **comments**. Python completely ignores them when running the code — they exist only for humans to read. This commented-out line is showing you an *alternative* you could use instead: if you wanted higher-quality responses, you could un-comment this line (remove the `#`) and comment out the Haiku line above it.

---

**`max_tokens=50`**

This sets a hard ceiling on how long Claude's response can be.

A **token** is the unit Claude uses to measure text — roughly one word or part of a word. The sentence "Hello, how are you?" is about 5 tokens. Setting `max_tokens=50` tells Claude: "stop generating text after 50 tokens, even if you are mid-sentence." This is important for two reasons:
1. **Cost control** — you are charged based on how many tokens Claude generates. Without a cap, a poorly-worded prompt could cause Claude to generate a very long (and expensive) response.
2. **Predictability** — in an evaluation pipeline, you want responses of roughly consistent length.

For this smoke test, 50 tokens is plenty for a simple greeting.

---

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This defines the conversation you are sending to Claude.

The `messages` parameter expects a **list** — an ordered collection of items, like a numbered list on paper. In Python, lists are written with square brackets `[...]`.

Inside the list is one item: `{"role": "user", "content": "Say hello in one short sentence."}`. This is a **dictionary** — a lookup table made of key-value pairs, like a mini-form where each field has a label and a value. In Python, dictionaries are written with curly braces `{...}`.

This particular dictionary has two keys:
- `"role"` — who is speaking. The value is `"user"`, meaning this message is coming from the human side of the conversation. (The other possible role is `"assistant"`, which would be Claude's side.)
- `"content"` — what is being said. The value is the actual message text: `"Say hello in one short sentence."`

The API requires messages to be formatted this way so it can handle multi-turn conversations (where user and assistant take turns). Even for a single message, you still wrap it in this structure.

---

**Line 6: `print(response.content[0].text)`**

This line extracts the actual text of Claude's reply from the response object and prints it.

After the API call completes, `response` is a complex object containing lots of information — not just the text, but also usage statistics, the model name, a stop reason, and more. The text of the reply is nested inside it.

Let's decode `response.content[0].text` from left to right:
- `response` — the labeled box holding Claude's full reply object
- `.content` — go inside `response` and find the part called `content`. This is a **list** (ordered collection) of content blocks. Claude can technically return multiple blocks (for example, text plus an image description), so the API always uses a list even when there is only one block.
- `[0]` — get the item at position 0 in that list. **Python counts starting from zero**, not one. So `[0]` means "the first item." This is the first (and in this case only) content block.
- `.text` — go inside that block and get the actual text string.

### What the output means

You will see something like: `Hello! It's great to meet you — I hope you're having a wonderful day!`

The specific wording will vary slightly each time you run it (because Claude has some natural variation in its responses), but you should see a short, friendly greeting. This confirms that:
- Your API key is valid and accepted by Anthropic's servers
- The network connection is working
- The `client` object is properly configured
- Claude is reachable and responding

If something is wrong, you will see a Python error message instead — usually something about authentication (bad key), network connectivity, or an invalid parameter.

---

## Cell 4: Installing and Checking Prerequisites

This cell verifies that all the Python tools the rest of the lab depends on are available and working. Think of it as a pre-flight checklist before takeoff.

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

This imports the `sys` module, which gives Python access to information about the Python interpreter itself.

The **Python interpreter** is the program on your computer that reads your Python code and executes it. The `sys` module lets you ask it questions like "what version of Python are you?" or "what operating system are you running on?" Here, we just want to check the version number.

---

**Line 2: `import json`**

This imports Python's built-in `json` module.

**JSON** stands for JavaScript Object Notation. Despite the name mentioning JavaScript, JSON is a universal format used everywhere in modern software. It is a structured way of writing data that both humans and computers can read — like a very organized, nested shopping list. It looks like this:

```json
{
  "name": "Alice",
  "score": 9,
  "passed": true
}
```

Later in this lab, you will ask Claude to respond in JSON format, and you will use this `json` module to parse (read and interpret) those responses.

---

**Line 3: `import re`**

This imports Python's built-in `re` module. The `re` stands for **regular expressions**.

A **regular expression** is a special pattern-matching language. Think of it like a very powerful "find" function — instead of searching for exactly the word "score", you can search for patterns like "any number between 1 and 10" or "any text that appears between curly braces." Later in the lab, `re` will be used to extract scores and JSON blocks from Claude's text responses.

---

**Line 5: `print("Python version:", sys.version)`**

This prints the Python version to the screen.

`sys.version` is like checking a label on the interpreter that says which version it is. The lab requires Python 3.9 or higher (written as `≥ 3.9`). Older versions might not support some of the features used in the code.

---

**Lines 8–10: The three `print` statements**

These lines confirm that each import succeeded.

The most interesting one is the middle line:
`print("json module ready:", json.__version__ if hasattr(json, '__version__') else 'built-in')`

This uses a **conditional expression** (also called a ternary expression) — a compact way of writing "if this is true, use this value; otherwise, use that value."

Let's break it apart:
- `hasattr(json, '__version__')` — this checks whether the `json` module has a version attribute. `hasattr` means "has attribute" — it returns `True` or `False`. An **attribute** is a piece of data stored inside an object (accessed with a dot, like `json.__version__`).
- `if hasattr(json, '__version__') else 'built-in'` — if `json` has a version number, use it; otherwise, use the text `'built-in'`.
- The whole expression produces whichever value applies, and `print` displays it.

The reason for this complexity: `json` is a **built-in module**, meaning it comes pre-installed with Python and does not have a traditional version number attribute. The code gracefully handles both possibilities.

**Line 10:** `print("re module ready: built-in")` is straightforward — it just confirms `re` is available by printing a message. (Since both `re` and `json` are built-in to Python, if they were missing, the `import` statements above would have already crashed with an error — so reaching these `print` lines means they loaded fine.)

**Line 11:** `print("Environment check complete ✅")` prints a final confirmation with a checkmark emoji. Emojis are valid text in Python strings.

### What the output means

You will see something like:
```
Python version: 3.10.0 (default, Oct 5 2021, ...)
json module ready: built-in
re module ready: built-in
Environment check complete ✅
```

The Python version number should start with `3.9` or higher (3.10.0 in this example is fine). The other lines just confirm that the core tools the lab needs are present and importable. If any `import` statement had failed, Python would have stopped with an error message before reaching the print statements.

---

## Glossary

**Argument** — A value you pass into a function when you call it, telling the function what specific thing to work on. In `os.getenv("ANTHROPIC_API_KEY")`, the argument is `"ANTHROPIC_API_KEY"`.

**API (Application Programming Interface)** — A defined way for one program to talk to another. Think of it like a waiter: you tell the waiter (API) what you want, the waiter takes your order to the kitchen (the service), and brings back what you asked for. The Anthropic API is how your code talks to Claude.

**API Key** — A secret string of characters (like a password) that proves to an API that you are authorized to use it. Without it, the service will refuse your requests.

**Attribute** — A piece of data or a function that belongs to an object, accessed using a dot. For example, `response.content` accesses the `content` attribute of the `response` object.

**Block** — In the context of Claude's API response, a content block is one unit of returned content (usually text). A response can contain multiple blocks.

**Boolean** — A value that is either `True` or `False`. Named after mathematician George Boole. Used in conditions and comparisons.

**Built-in module** — A module that comes pre-installed with Python, requiring no additional download. Examples include `os`, `sys`, `json`, and `re`.

**Call (a function)** — To execute or run a function by writing its name followed by parentheses `()`.

**Class** — A blueprint or template for creating objects. Like a cookie cutter that can produce many identical-but-separate cookies. `Anthropic` is a class; `client` is the object created from it.

**Client** — In programming, a client is a program (or object) that connects to a service on your behalf. The `client` variable in this lab is the object through which all your API calls flow.

**Comment** — Text in code that Python ignores, written for human readers. In Python, comments start with `#`.

**Conditional expression** — A compact way to choose between two values based on a condition. Structure: `value_if_true if condition else value_if_false`.

**Dictionary** — A lookup table made of key-value pairs. Like a phone book where you look up a name (key) to get a number (value). Written with curly braces `{}` in Python.

**Environment variable** — A piece of configuration data stored by your operating system, like a sticky note in the computer's memory. Programs can read these without having the data written directly in their code.

**Function** — A named, reusable set of instructions that you can trigger by calling it. Like a recipe: give it a name, call the name, and the steps execute.

**Import** — Bringing a tool (library or module) into your current workspace so you can use it. Like carrying a tool from the warehouse to your workbench.

**Instantiate** — To create an actual object from a class blueprint. Like pressing a cookie cutter into dough to make a specific cookie.

**JSON (JavaScript Object Notation)** — A structured text format for representing data, readable by both humans and computers. Uses curly braces for objects `{}`, square brackets for lists `[]`, colons to separate keys and values, and commas to separate items.

**Library / Module / Package** — A collection of pre-written Python tools that you can import and use in your code. The terms are often used interchangeably, though technically: a module is a single file, a package is a folder of modules, and a library is a general term for any reusable code collection.

**List** — An ordered collection of items, like a numbered list on paper. Written with square brackets `[]` in Python. Items are accessed by their position, with counting starting at 0.

**Named argument** — An argument passed to a function using the format `name=value`, making the code more readable and allowing arguments to be given in any order.

**None** — A special Python value that means "nothing" or "no value." Like an empty box. Used when a variable has no meaningful value to hold.

**Object** — A self-contained unit that combines data (attributes) and functionality (methods/functions). The `client` variable is an object. The `response` variable is an object.

**Operating system** — The software that manages your computer's hardware and provides services to programs (Windows, macOS, Linux).

**Parsing** — Reading a structured string (like JSON text) and converting it into a usable Python object like a dictionary or list.

**Regular expression** — A pattern-matching language used to search for, extract, or replace text based on patterns rather than exact strings. Provided by Python's `re` module.

**SDK (Software Development Kit)** — A pre-built package of tools designed to make working with a specific service easier. The `anthropic` library is Anthropic's SDK for Python.

**Smoke test** — A quick, minimal test to verify that the basic functionality works before investing time in more complex testing. From electronics: if you power something on and it doesn't smoke, the basic circuitry is intact.

**String** — A sequence of characters (text) wrapped in quotes. `"Hello"` and `"ANTHROPIC_API_KEY"` are both strings.

**Token** — The unit Claude (and other AI language models) use to measure text. Roughly one word or part of a word. Costs, speed, and limits are all measured in tokens.

**Variable** — A labeled box that holds a value. You give it a name, store something in it, and refer to it later by that name. In Python, you create a variable by writing `name = value`.

**`=` (assignment operator)** — In Python, a single equals sign means "store the value on the right into the variable on the left." It does *not* mean mathematical equality. To *compare* two values for equality, Python uses `==` (double equals).

**`.env` file** — A plain text file (with no name before the dot) used to store secret configuration values like API keys. Read by the `dotenv` library. Should never be shared or committed to version control.