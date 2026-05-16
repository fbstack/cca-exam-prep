# Python Guide: Structured Data Extraction with Claude's API

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (one paragraph for complete beginners)

Python is a programming language — a way of giving precise, step-by-step instructions to a computer. Think of it like writing a very detailed recipe. The computer follows each instruction exactly, in order, without guessing at your meaning. In this lab, we're using Python to talk to Claude (Anthropic's AI) through something called an **API** (explained below). Each "cell" in a JupyterLab notebook is a small group of instructions you can run independently, like separate recipe cards that build on each other.

---

## Cell 1: Load Environment Variables

This cell's job is to find your secret API key and make it available to the rest of the program — without writing the key directly into your code, which would be a security risk.

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

This line starts with a `#` symbol, which means it is a **comment**. Comments are notes written by the programmer for humans to read — the computer ignores them completely. This comment simply labels what this cell is doing, like a sticky note at the top of a recipe card.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack this carefully, because it introduces two important ideas at once.

**What is "importing"?** Importing is how Python brings in tools that aren't built in by default. Think of Python's core abilities as a basic kitchen with only a stove and a knife. To do more specialized cooking, you need to bring in extra equipment from a supply room. That supply room is called a **library** (or **module** or **package** — these terms are used loosely as synonyms). The `import` command is how you carry a specific tool from that supply room into your kitchen.

**What is `dotenv`?** `dotenv` is the name of a third-party library — a collection of tools someone else wrote that we're borrowing. Its specific job is to read a special file called `.env` (pronounced "dot env") that lives in the same folder as your notebook. A `.env` file is where you store secret values like passwords and API keys, formatted like this:
```
ANTHROPIC_API_KEY=sk-ant-abc123...
```
By keeping secrets in a separate `.env` file, you avoid accidentally publishing them when you share your code.

**What is `load_dotenv`?** This is a specific **function** inside the `dotenv` library. A function is like a named recipe — a set of instructions packaged under a single name that you can call (execute) whenever you need it. `load_dotenv` is the function we need from that library.

**The `from ... import ...` pattern** means "go to the `dotenv` library, find specifically the `load_dotenv` function, and bring only that one thing into my workspace." This is more precise than grabbing the entire library.

---

**Line 3: `import os`**

This line imports a library called `os`. The name stands for "operating system." Think of the operating system as the manager of your computer — it controls files, folders, running programs, and a special storage area called **environment variables**.

**What are environment variables?** Environment variables are like small labeled sticky notes that your operating system keeps on a corkboard. Any program running on the computer can read these notes. When `load_dotenv()` runs (line 5), it reads the `.env` file and pins its contents onto that corkboard. Then the `os` library gives us a way to read those sticky notes back.

The `import os` pattern (without `from`) means we're importing the entire `os` library, and we'll refer to its tools using the prefix `os.` (like `os.getenv()` on line 6).

---

**Line 5: `load_dotenv()`**

This line **calls** (executes) the `load_dotenv` function we imported on line 2. The parentheses `()` at the end are how you tell Python "run this function now." Think of it like pressing the "start" button on a machine you've already plugged in.

What does it actually do? It looks in the current folder for a file named `.env`, reads every line in that file, and places each key-value pair into the operating system's environment variable storage (the corkboard). After this line runs, the computer "knows" your API key — it's been pinned to the board.

Notice there's nothing inside the parentheses. That's fine — `load_dotenv` doesn't need any additional information to do its job. It just goes and does it.

---

**Line 6: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line has several moving parts. Let's break them down:

**`os.getenv(...)`** — This is a function from the `os` library. `getenv` is short for "get environment variable." It reaches into the operating system's corkboard and reads the sticky note with the label you provide. We're asking it: "What is the value stuck to the note labeled `ANTHROPIC_API_KEY`?"

**`"ANTHROPIC_API_KEY"`** — This is the label we're looking for. Text surrounded by quotation marks in Python is called a **string** — it's just a sequence of characters treated as data, not as code. The label must match exactly what's written in your `.env` file.

**`api_key =`** — This stores the result in a **variable**. A variable is a labeled box that holds a value. The `=` sign here doesn't mean "equals" in the mathematical sense — it means "take what's on the right side and put it into the box named on the left side." So whatever string `os.getenv` finds on that corkboard, it gets placed into a box labeled `api_key`. We can now refer to that key anywhere in the notebook just by writing `api_key`.

If `ANTHROPIC_API_KEY` was not found on the corkboard (maybe the `.env` file is missing or spelled wrong), `os.getenv` returns a special value called `None` — Python's way of saying "nothing is here."

---

**Line 7: `print("API key loaded:", api_key is not None)`**

This line sends a message to the screen so you can see what's happening.

**`print(...)`** is a built-in Python function that displays whatever you give it. Think of it as the computer reading something out loud to you.

**`"API key loaded:"`** is a string — the text that will appear first.

**`api_key is not None`** — This is a **logical test** (also called a boolean expression). Python evaluates it and gets either `True` or `False`. It's asking: "Is the value in the `api_key` box something other than nothing?" If your key was found, the answer is `True`. If the key wasn't found, `api_key` contains `None`, and the answer is `False`.

The comma between the two items in `print()` just means "print both of these, separated by a space."

---

### What the output means

You will see one of two things:

- **`API key loaded: True`** — Your `.env` file was found, the key was inside it, and it's now stored in the `api_key` variable. You're ready to proceed.
- **`API key loaded: False`** — Something went wrong. Either the `.env` file doesn't exist, or it doesn't contain a line starting with `ANTHROPIC_API_KEY=`. The lab will not work until this is fixed.

This kind of quick check is called a **sanity check** — a fast, simple test to confirm your setup is working before doing anything more complicated.

---

## Cell 2: Initialise the Anthropic Client

This cell takes your API key and uses it to create a ready-to-use "connection object" that knows how to talk to Anthropic's servers. Think of this like logging in before you can use a service.

```python
# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────
from anthropic import Anthropic   # the official Python SDK class

client = Anthropic(api_key=api_key)  # creates an authenticated client instance
print("Anthropic client ready")       # confirms object was constructed without error
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise the Anthropic client ─────────────`**

Another comment — just a label for the human reader. The computer skips it.

---

**Line 2: `from anthropic import Anthropic`**

This imports a specific tool from the `anthropic` library (the official software package that Anthropic — the company that makes Claude — provides for Python developers).

**What are we importing?** Something called `Anthropic`, which is a **class**. A class is like a blueprint or a template. You don't use the blueprint itself — you use it to create a specific, functional **instance** (a built version of the blueprint). Think of a class like the design plans for a car, and an instance like the actual car you drive.

Notice the pattern: `from anthropic import Anthropic` — the library (package) is named `anthropic` in all lowercase, and the class inside it is also named `Anthropic` but with a capital A. This is a naming convention — classes in Python traditionally start with a capital letter.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line creates your actual working connection to Claude's API.

**What is an API?** API stands for Application Programming Interface. Think of it as a waiter in a restaurant. You (your code) tell the waiter what you want. The waiter carries your request to the kitchen (Anthropic's servers running Claude). The kitchen prepares a response and the waiter brings it back to you. The API is the defined system for placing those orders and receiving those results.

**`Anthropic(api_key=api_key)`** — The parentheses after `Anthropic` mean "build me an instance of this blueprint." Inside the parentheses, we're passing in a piece of information the blueprint needs: `api_key=api_key`. 

This might look confusing at first. The left side (`api_key=`) is the name of the slot in the blueprint — it's saying "fill in the slot labeled `api_key`." The right side (`api_key`) is our variable from Cell 1 — the actual key we retrieved. Python distinguishes them by position: left is the slot name, right is the value going in.

The result is stored in a variable named `client` — a box that now holds your authenticated, ready-to-use API connection. We'll use `client` throughout the rest of the lab whenever we want to talk to Claude.

**What does "authenticated" mean?** Authentication is proving you are who you say you are — like showing your ID to get into a building. The API key is your ID. By passing it into the `Anthropic()` blueprint, every request the `client` makes will automatically include that key, so Anthropic's servers know your requests are authorized.

---

**Line 5: `print("Anthropic client ready")`**

Prints a confirmation message to the screen. This is simply the programmer's way of saying "the previous line ran without crashing." If the `Anthropic()` call on line 4 had failed (for example, because you passed in an invalid key format), Python would have thrown an error and this line would never have been reached.

---

### What the output means

- **`Anthropic client ready`** — The `client` object was successfully created. You now have a working connection handle. Note: this doesn't prove the key is *valid* with Anthropic's servers yet — that test happens in Cell 3. It only proves the object was constructed without a code error.

---

## Cell 3: Smoke Test — Verify End-to-End Connectivity

This cell sends a tiny, real message to Claude and prints the response. It's the first true test that everything — your key, your internet connection, and the API — is working together.

```python
# ── Starter Cell 3: Smoke test — verify end-to-end connectivity ──────────────
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

A comment. The term **smoke test** is a metaphor from engineering — the idea that if you turn on a new machine and it doesn't literally smoke, it's probably working well enough to test further. In software, a smoke test is the simplest possible check that a system is functional end-to-end.

---

**Lines 2–7: `response = client.messages.create(...)`**

This is a **function call** spread across multiple lines. Python allows you to spread a long function call across several lines for readability — it's still one single instruction. Everything from `client.messages.create(` down to the closing `)` on line 7 is one call.

Let's understand the structure first, then each argument:

**`client.messages.create(...)`** — This is how we use the API. Breaking it down by the dots:
- `client` — our connection object from Cell 2
- `.messages` — a section of that client specifically for working with message-based conversations (Claude uses a chat-style "messages" format)
- `.create(...)` — the specific action: "create a new conversation turn and send it to Claude"

Think of it like: `connection → message-handling-department → create-new-request`

The result of this entire call (Claude's response) is stored in the variable `response`.

---

**`model="claude-haiku-4-5",`**

This is the first **argument** (a piece of information) being passed into the `.create()` function.

**What is a model?** A model is a specific version of the AI. Different models have different speeds, costs, and capability levels. `"claude-haiku-4-5"` is a faster, lighter, less expensive model — suitable for quick tests. The commented-out line `#    model="claude-sonnet-4-5",` shows an alternative (a more capable model) — but since lines starting with `#` are ignored, only `claude-haiku-4-5` is actually used.

**`model=`** is the name of the slot we're filling. **`"claude-haiku-4-5"`** is the string value going into it.

---

**`max_tokens=50,`**

**What is a token?** When Claude reads or writes text, it doesn't work letter-by-letter or word-by-word. It works in **tokens** — chunks of text that are roughly one word or part of a word. The word "hello" is one token. The word "unbelievable" might be two tokens. A rough rule of thumb: 100 tokens ≈ 75 words.

**`max_tokens=50`** sets a hard ceiling: Claude is not allowed to write more than 50 tokens in its response. For a smoke test asking for "one short sentence," 50 tokens is more than enough. This argument is **required** by the API — you must always specify an upper limit to prevent unexpectedly long (and expensive) responses.

---

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual content of the conversation. Let's unpack it layer by layer.

**The outer `messages=`** — This is the slot we're filling. The value is everything inside the `[...]`.

**`[...]`** — Square brackets in Python denote a **list** — an ordered collection of items, like a numbered list on paper. This list will hold the conversation history (each message is one item).

**`{...}`** — Curly braces in Python denote a **dictionary** — a lookup table where each item has a label (called a **key**) and a value. Like a mini-database row. For example: `{"name": "Alice", "age": 30}` — you look up `"name"` and get `"Alice"`.

**`"role": "user"`** — The `role` key tells Claude who is speaking. The value `"user"` means this is the human's turn. (The other option would be `"assistant"` for Claude's turns.)

**`"content": "Say hello in one short sentence."`** — The `content` key holds what was actually said. Here, the user is asking Claude to say hello briefly.

So in plain English, `messages` is: a list containing one item, that item being a dictionary describing a user message saying "Say hello in one short sentence."

The comment below this line explains: *"messages is a list of dicts — the minimum required field besides model/max_tokens."* A **dict** is just short for dictionary.

---

**Line 8: `print(response.content[0].text)`**

This line prints Claude's actual reply. Let's decode the chain of references:

**`response`** — the variable holding Claude's full response object from the `.create()` call above.

**`.content`** — Claude's response isn't just a plain string. It's a structured object with multiple fields. `.content` is the field that holds the actual text blocks of the reply. It's a **list** (because in theory, Claude could return multiple content blocks — for example, a mix of text and other data types).

**`[0]`** — This accesses the **first item** in the list. In Python, lists are counted starting from 0, not 1. So `[0]` means "the first item," `[1]` would mean the second, and so on. Since our simple request will produce a single text block, `[0]` is the one we want.

**`.text`** — The content block has multiple fields of its own. `.text` is the field that holds the actual readable text string of Claude's reply.

Putting it all together: "From Claude's response, get the content list, take the first item in that list, and get the text field from that item — then print it."

---

### What the output means

You will see a single sentence from Claude — something like:

`Hello! I'm Claude, and I'm delighted to meet you today.`

The exact wording will vary (Claude doesn't give identical answers every time), but it should be a short greeting in one sentence. 

**Why this matters:** If this cell runs and you see a greeting, your API key is valid, Anthropic's servers accepted your request, and the response came back successfully. Your entire setup is confirmed working. If you see an error instead — such as `AuthenticationError` — it means your API key is invalid or wasn't loaded correctly.

---

## A Note on the Markdown Cells (The Reference Tables)

The remaining content in this notebook consists of **Markdown cells** — cells that contain formatted text, headings, and tables rather than executable code. They don't run like code cells; they just display information. Think of them as the textbook pages between the exercises.

Here's what those cells explain, translated into plain English:

---

## The API Parameter Reference Table (Markdown Cell)

This is a reference guide explaining the six key "settings" you can pass to Claude's API. Here's what each one means in plain English:

---

**`model` (Required)**
Selects which version of Claude to use. Like choosing between a sports car and a family sedan — different speeds and capabilities. Examples used in this lab: `"claude-haiku-4-5"` (fast and economical) and `"claude-sonnet-4-5"` (more capable but slower and more expensive).

---

**`max_tokens` (Required)**
Sets the maximum length of Claude's response, measured in tokens (word-chunks). This is a safety valve — it prevents Claude from generating an unexpectedly long (and costly) response. You must always provide this.

---

**`messages` (Required)**
The conversation history, formatted as a **list of dictionaries**. Each dictionary represents one turn in the conversation and must have two keys:
- `"role"` — either `"user"` (the human speaking) or `"assistant"` (Claude speaking)
- `"content"` — what was actually said in that turn

The turns should alternate: user, assistant, user, assistant... This is how Claude understands the flow of the conversation.

---

**`messages[-1]` with `role="assistant"` — The Prefill Technique**

This is a more advanced technique that's central to this lab. Here's the concept:

Normally, the last message in your list is from the user, and Claude generates what comes next (its reply). But you can also put an **assistant message at the end** of the list — an incomplete message — and Claude will continue writing from exactly where that message left off.

Think of it like this: imagine you're ghostwriting a speech for someone. You hand them a script that starts "Dear friends, tonight I want to talk about..." and they continue from that exact point, in that exact style. The pre-written beginning **locks in the format before they start improvising**.

In this lab, that "pre-written beginning" will often be something like:
```json
{
```
...which forces Claude to continue in valid JSON format, because it's already committed to having started a JSON structure.

This technique is called **prefilling** — you fill in the beginning of Claude's reply before Claude generates anything. The table entry `messages[-1]` uses Python list notation: `[-1]` means "the last item in the list" (Python lets you count backwards from the end using negative numbers).

---

**`stop_sequences` (Optional)**

This is a list of strings that act as "stop signs" for Claude's generation. The moment Claude produces any string in this list, it stops immediately — and critically, **the stop string itself is NOT included in the response**.

Example: If you set `stop_sequences=["```"]` (a code fence marker), Claude will stop the moment it would write ` ``` `, and that marker won't appear in what gets returned to you.

**Why is this useful?** When combined with prefilling, you can get clean, boundary-free structured output. You tell Claude to start with `{` (by prefilling it), and you tell it to stop at `}` (via stop_sequences). What you get back is the content in between — no extra text, no markdown decorations, no "Here is the JSON you asked for:" preamble.

---

**`system` (Optional)**

A set of instructions that frames Claude's entire behavior for the conversation, placed before any user messages. Think of it as a job briefing you give Claude before the conversation starts: "You are a technical assistant. You respond only in JSON. You do not explain your reasoning." The system prompt shapes how Claude approaches every message that follows.

---

## The Stop Sequence Exclusion Table (Markdown Cell)

This table answers a specific question that appears on the CCA (Claude Certification Assessment): **What exactly gets included in Claude's response, and what gets left out?**

| Scenario | Included in response? |
|---|---|
| Text Claude generates *before* hitting the stop sequence | ✅ Yes |
| The stop sequence string itself | ❌ No — it is excluded |
| Text Claude would have generated *after* the stop sequence | ❌ No — generation halts |

**In plain English:** If you set `stop_sequences=["END"]` and Claude generates the text `Hello, world!END This is extra`, you will receive only `Hello, world!` — the word `END` and everything after it is stripped away.

This is a precise, reliable boundary — which is why this pattern is so useful for structured data extraction. You know exactly where the output will end, and you don't need to write code to clean up the response afterward.

---

## Glossary

Here is every technical term used in this guide, defined in plain English.

**API (Application Programming Interface)** — A defined system for one piece of software to request something from another. Like a waiter taking your order to the kitchen and bringing back the result. In this lab, the API lets your Python code send messages to Claude and receive responses.

**API key** — A secret string of characters (like a password) that proves you have an authorized account with Anthropic. You include it with every request so Anthropic knows who is asking.

**Argument** — A piece of information you pass into a function so it can do its job. Like ingredients you hand to a recipe. In `max_tokens=50`, the number `50` is the argument.

**Authentication** — The process of proving your identity to a system. Showing your API key is how your code authenticates with Anthropic.

**Boolean** — A value that is either `True` or `False`. The result of a logical test like `api_key is not None`.

**Class** — A blueprint or template for creating objects. The `Anthropic` class is a template for creating API connection objects. You build an instance from it by calling it with parentheses: `Anthropic(...)`.

**Client (in this context)** — An object that knows how to make requests to a server. Our `client` variable holds an `Anthropic` object that handles all communication with Claude's API.

**Comment** — A line of code starting with `#` that the computer ignores. Written for humans to explain what the code is doing.

**Content block** — The unit that Claude's API uses to return its response. A response may contain multiple blocks (e.g., text, tool use). In this lab, we access the first block with `[0]` and read its `.text` field.

**Dictionary (dict)** — A Python data structure that stores key-value pairs. Like a lookup table or a phone book. Written with curly braces: `{"role": "user", "content": "Hello"}`. You look up a key to get its value.

**Environment variable** — A named value stored by the operating system that any running program can read. Like a sticky note pinned to a corkboard. Used to store secrets (like API keys) outside of your code.

**`.env` file** — A plain text file in your project folder that stores secret values in the format `KEY=value`. The `dotenv` library reads this file and loads its contents as environment variables.

**Function** — A named set of instructions that you can call (execute) by writing its name followed by parentheses. Like a named recipe: `load_dotenv()` means "run the recipe called load_dotenv."

**Import** — The act of bringing a library's tools into your current Python workspace. Like carrying a specialized tool from a supply room into your kitchen.

**Instance** — A specific, working object built from a class blueprint. `client = Anthropic(...)` creates an instance of the `Anthropic` class.

**JSON (JavaScript Object Notation)** — A structured format for writing data that both humans and computers can read. Looks like Python dictionaries and lists. Widely used for sending data between systems. Example: `{"name": "Alice", "age": 30}`.

**Library (also: module, package)** — A collection of pre-written Python tools you can import and use. `dotenv`, `os`, and `anthropic` are all libraries used in this lab.

**List** — A Python data structure that holds an ordered collection of items. Written with square brackets: `["apple", "banana", "cherry"]`. Items are accessed by position, starting at 0.

**Markdown** — A simple formatting language used to write styled text (headings, bold, tables) without HTML. JupyterLab notebooks use Markdown cells for explanatory text between code cells.

**`max_tokens`** — The maximum number of tokens (word-chunks) Claude is allowed to generate in a response. A required parameter that prevents runaway output.

**Model** — A specific trained version of an AI system. Different models have different capability levels, speeds, and costs. Examples: `claude-haiku-4-5` (fast/cheap) and `claude-sonnet-4-5` (more capable).

**`None`** — Python's special value meaning "nothing" or "no value." Used when a variable has been created but holds no meaningful data, or when a function found nothing to return.

**Object** — A self-contained bundle of data and capabilities, built from a class blueprint. The `client` variable holds an object that has the ability to make API calls.

**Prefill (assistant prefill)** — A technique where you place an incomplete assistant message at the end of the `messages` list. Claude continues writing from that exact starting point instead of beginning fresh. Used to lock in output format before generation starts.

**`print()`** — A built-in Python function that displays text or values on the screen. Your main window into what your program is doing.

**Role** — In the Claude messages format, `role` identifies who is speaking in a conversation turn. Either `"user"` (the human) or `"assistant"` (Claude).

**Sanity check** — A quick, simple test to confirm that a basic assumption is true before proceeding with more complex steps. Cell 1's `print("API key loaded:", api_key is not None)` is a sanity check.

**SDK (Software Development Kit)** — A pre-built set of tools that makes it easier to work with a specific service. The `anthropic` Python library is Anthropic's SDK — it handles the technical details of making API calls so you don't have to.

**Smoke test** — The simplest possible test that confirms a system is working end-to-end. Named after the idea that if you turn on electronics and nothing smokes, it's probably functioning. Cell 3 is a smoke test for the Claude API connection.

**Stop sequence** — A string of characters that, when Claude generates it, immediately halts further output. The stop sequence itself is NOT included in the returned response. Used to set clean, predictable boundaries on Claude's output.

**String** — A sequence of text characters in Python, enclosed in quotation marks. `"Hello"`, `"ANTHROPIC_API_KEY"`, and `"claude-haiku-4-5"` are all strings.

**System prompt** — A high-level instruction given to Claude before the conversation begins, using the `system` parameter. Sets Claude's role, constraints, and behavior for the entire conversation.

**Token** — The unit Claude uses to read and write text. Roughly one word or part of a word. The string `"Hello, world!"` is about 4 tokens. Used to measure and limit the length of inputs and outputs.

**Variable** — A named container for storing a value in Python. Written as `name = value`. The `=` sign means "store this value in this named box." Example: `api_key = os.getenv("ANTHROPIC_API_KEY")` stores the retrieved API key in a box labeled `api_key`.