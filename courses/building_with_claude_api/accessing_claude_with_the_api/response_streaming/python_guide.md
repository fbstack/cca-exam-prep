# Python Guide: Response Streaming with the Claude API

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving instructions to a computer in plain-ish language. You write a series of steps — one per line, roughly — and the computer carries them out in order. Think of it like writing a very precise recipe: the computer follows each step exactly as written, without guessing what you meant. The notebook you are reading is organized into **cells** — small, runnable chunks of instructions. You run one cell at a time, and each cell builds on what the previous ones have done.

---

## Cell 1: Loading Your Secret API Key From a Safe Place

```python
# ── Starter Cell 1: Load environment variables ──────────────────────────────
from dotenv import load_dotenv   # load_dotenv reads .env file into os.environ
import os                         # os.getenv safely retrieves env vars

load_dotenv()                     # parse .env in current working directory
api_key = os.getenv("ANTHROPIC_API_KEY")  # fetch the key; None if missing
print("API key loaded:", api_key is not None)  # confirm without printing secret
```

### What this code does

---

**Line 1: `# ── Starter Cell 1: Load environment variables ──────────────────────────────`**

This line does nothing at all — the computer ignores it completely. It is a **comment**, which is any line that starts with the `#` symbol. Comments are notes written for human readers. They explain *why* the code exists, not *what* the computer is doing. Think of them as sticky notes attached to the code.

---

**Line 2: `from dotenv import load_dotenv`**

This line brings a specific tool into your workspace.

Let's unpack the words one at a time:

- **`dotenv`** is a pre-written collection of code (called a **library** or **package**) that someone else wrote and shared. Its whole job is to read a special file called `.env` (pronounced "dot env") where secret values — like passwords and API keys — are stored. Instead of typing your secret key directly into your code (which would be risky if anyone saw your code), you store it in this separate `.env` file, and `dotenv` fetches it for you.

- **`import`** means "bring this tool into my workspace so I can use it." Think of a toolbox in a shed — `import` is the act of carrying a specific tool from the shed to your workbench.

- **`from ... import ...`** is a more specific version of `import`. Instead of bringing the entire `dotenv` toolbox, you're reaching in and grabbing just one tool from it: the tool named `load_dotenv`.

- **`load_dotenv`** is a **function** — a named set of instructions that you can run on demand, like a named recipe. This particular function, when called, finds your `.env` file and reads its contents.

The comment at the end (`# load_dotenv reads .env file into os.environ`) is a reminder that when this function runs, it places the secret values into a kind of temporary memory area called `os.environ` — which we'll explain in the next line.

---

**Line 3: `import os`**

This brings in another tool — this time the entire `os` library.

- **`os`** stands for "operating system." It is a library that comes built into Python (no installation needed) and lets your Python code talk to the underlying computer — things like reading files, checking what folder you're in, and, crucially for us, reading **environment variables**.

- **Environment variables** are pieces of information stored in your computer's memory (not in any file) that programs can read. They are commonly used to hold secrets and settings. Think of them like small whiteboards in your computer's back office — each whiteboard has a name and a value written on it. Programs can walk in, look at a whiteboard by name, and read what's written there.

The comment (`# os.getenv safely retrieves env vars`) previews how we'll use this library on the next line.

---

**Line 5: `load_dotenv()`**

This line actually *runs* (calls) the `load_dotenv` function we brought in on Line 2.

- The parentheses `()` are what tell Python "run this function right now." Without the parentheses, Python would just know the function exists but wouldn't execute it. With the parentheses, Python runs all the instructions inside that function.

- What `load_dotenv()` does when it runs: it looks in your current folder for a file named `.env`, reads any name-and-value pairs it finds there (for example, `ANTHROPIC_API_KEY=abc123xyz`), and writes those values onto the "environment variable whiteboards" inside your computer's memory. After this line runs, those secret values are available to the rest of your code — without anyone having to type them out directly.

---

**Line 6: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line reads one specific value from the environment variable whiteboards and stores it in a labeled box for later use.

- **`os.getenv("ANTHROPIC_API_KEY")`** — Here we use the `os` library we imported. The `getenv` part is a function inside `os` (hence the dot connecting them — `os.getenv` means "the `getenv` function that lives inside `os`"). We call it with the name `"ANTHROPIC_API_KEY"` in parentheses, meaning: "Go look at the whiteboard named `ANTHROPIC_API_KEY` and tell me what's written on it." If the whiteboard exists and has something written on it, `getenv` returns that value. If the whiteboard doesn't exist (because `load_dotenv` didn't find the `.env` file, or the key wasn't in it), `getenv` returns a special value called `None`, which in Python means "nothing" or "absent."

- **`api_key =`** — The `=` sign in Python is not checking equality (it's not asking "is this equal?"). It is an **assignment** — it means "take whatever is on the right side and store it in a labeled box named `api_key`." A **variable** is exactly this: a labeled box that holds a value. The label is `api_key`, and whatever `os.getenv` returns gets placed inside it.

- An **API key** is a unique password that proves to the Anthropic service that you are an authorized user. Every time you send a request to Claude, you include this key so Anthropic knows who to bill and whether to allow the request.

The comment (`# fetch the key; None if missing`) confirms the two possible outcomes.

---

**Line 7: `print("API key loaded:", api_key is not None)`**

This line prints a message to the screen — but it's carefully designed to confirm your key was loaded *without* accidentally showing the secret key itself.

- **`print(...)`** is a built-in Python function that displays whatever you put inside the parentheses as output below the cell. Think of it as Python's way of talking back to you.

- **`"API key loaded:"`** is a **string** — a piece of text that Python treats as a literal value to display, not as code to run. Strings are always wrapped in quote marks so Python knows "this is text, not an instruction."

- **`api_key is not None`** — This is a **comparison expression** that produces either `True` or `False`.
  - `None` is Python's way of saying "there is no value here."
  - `is not None` means "does this variable actually contain something?"
  - So `api_key is not None` asks: "Does the `api_key` box contain a real value (not nothing)?" If yes, the result is `True`. If the key was missing and `api_key` holds `None`, the result is `False`.

- The comma between the string and the expression tells `print` to display both items side by side, separated by a space.

The cleverness here: you get confirmation that the key loaded successfully (`True`) or that something went wrong (`False`) — without ever printing the actual secret key to the screen where someone might see it.

---

### What the output means

When this cell runs, you will see one line of output, like:

```
API key loaded: True
```

**`True`** means everything worked — the `.env` file was found, the `ANTHROPIC_API_KEY` value was inside it, and it has been stored in the `api_key` variable, ready for use in later cells.

**`False`** would mean the key was not found. If you see `False`, check that your `.env` file exists in the same folder as this notebook and contains the line `ANTHROPIC_API_KEY=your_key_here`.

---

## Cell 2: Creating the Connection to Claude

```python
# ── Starter Cell 2: Initialise Anthropic client ──────────────────────────────
from anthropic import Anthropic   # main SDK class that wraps all API calls

client = Anthropic(api_key=api_key)  # create one shared client for the session
print("Anthropic client ready")       # quick confirmation that init succeeded
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise Anthropic client ──────────────────────────────`**

Another comment — a human-readable label telling us what this cell is for. The computer skips it. "Initialise" means "set up and prepare for use."

---

**Line 2: `from anthropic import Anthropic`**

This brings in the main tool from Anthropic's official Python library.

- **`anthropic`** (lowercase) is the name of the Python package — the collection of code that Anthropic has published to make it easier to use their API. You would have installed this package before running the notebook (typically with a command like `pip install anthropic`).

- **`Anthropic`** (uppercase, the specific thing we're importing) is a **class**. A class is a blueprint or template for creating objects. Think of a class like a cookie cutter: it defines the shape and features, and you use it to stamp out individual cookies (called **instances**).

- **`from anthropic import Anthropic`** means: "From the `anthropic` package, bring in specifically the `Anthropic` class/blueprint so I can use it to create a connection object."

The comment confirms this is the "main SDK class that wraps all API calls." **SDK** stands for Software Development Kit — it's a ready-made set of tools a company provides so developers don't have to write connection code from scratch.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This is the most important line in this cell. It creates a live, ready-to-use connection to the Anthropic API.

- **`Anthropic(api_key=api_key)`** — Here we use the `Anthropic` blueprint (class) to create one specific instance of it. The parentheses with values inside are how you "stamp out a cookie" from the cookie cutter. We're passing in one piece of information: `api_key=api_key`.

  - The *left side* `api_key=` is the name of the parameter the `Anthropic` class expects — it's asking, "What's your API key?"
  - The *right side* `api_key` is our variable from Cell 1 — the actual secret key we loaded from the `.env` file.
  - It might look a little odd that both sides say `api_key`, but they are different things: the left is the parameter *name* (defined by Anthropic's code), and the right is our *variable* (defined by us in Cell 1).

- **`client =`** stores the resulting connection object in a variable named `client`. Think of `client` as a telephone handset that is already dialed into Anthropic's service and authenticated with your key. Every time you want to speak to Claude, you pick up this handset.

- The word **client** reflects a common pattern: in software, a **client** is the thing that makes requests, and a **server** is the thing that responds. Your code is the client; Anthropic's computers are the server.

---

**Line 5: `print("Anthropic client ready")`**

This prints a simple confirmation message so you know the previous line ran without errors.

- If the `Anthropic(...)` call had failed (for example, if the `api_key` variable was `None`), Python would have shown an error (called an **exception**) and never reached this line. Seeing "Anthropic client ready" in the output is your signal that everything was set up successfully.

---

### What the output means

```
Anthropic client ready
```

This single line of text confirms that the `client` object was created without errors. The `client` variable now exists and is ready to be used in subsequent cells to send messages to Claude. You only need to create this client once — all later cells will reuse the same `client` variable.

---

## Cell 3: Smoke Test — Sending Your First Message to Claude

```python
# ── Starter Cell 3: Smoke test ───────────────────────────────────────────────
response = client.messages.create(
    model="claude-haiku-4-5",         # fast/cheap model ideal for tests
    # model="claude-sonnet-4-5",      # swap here for higher quality
    max_tokens=50,                    # tiny limit — just checking connectivity
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]
)
print(response.content[0].text)       # content is a list; [0] is the first block
```

### What this code does

---

**Line 1: `# ── Starter Cell 3: Smoke test ───────────────────────────────────────────────`**

Another comment label. A **smoke test** is a term borrowed from engineering — it refers to the most basic possible test: just turn it on and see if anything catches fire. Here it means: "Let's send one tiny message to Claude just to confirm the whole connection is working end to end."

---

**Lines 2–7: `response = client.messages.create(...)`**

This block is one single instruction spread across multiple lines for readability. Together, these lines send a message to Claude and store its reply.

Let's read it as one unit first, then examine each part:

> "Using the `client` connection, call the `messages.create` function, configure it with these settings, and store whatever Claude sends back in a variable called `response`."

- **`client.messages.create(...)`** — The dot notation here means "go inside `client`, find `messages`, and inside that find `create`, and run it." Think of it like a filing system: `client` is the cabinet, `messages` is the drawer, and `create` is the specific folder you pull out and use. `create` is the function that actually sends your request to the Anthropic API and waits for a reply.

- **`response =`** — The reply that comes back from Claude will be stored in a variable named `response`. This is a complex object (like a package with multiple compartments) containing the reply text, information about how many words were used, which model replied, and so on.

Now let's look at each argument (setting) inside the parentheses:

---

**`model="claude-haiku-4-5",`**

This tells the API which version of Claude to use.

- **`model`** is the parameter name — Anthropic's code expects you to tell it which AI model to use.
- **`"claude-haiku-4-5"`** is the model identifier. Anthropic offers several models at different price/quality trade-offs. "Haiku" is fast and inexpensive, making it perfect for testing. The comment confirms this: `# fast/cheap model ideal for tests`.
- The trailing comma after the value is required because more settings follow — commas separate items in a list of settings.

---

**`# model="claude-sonnet-4-5",      # swap here for higher quality`**

This entire line is a comment (it starts with `#`) — the computer ignores it. It's a note telling you that if you want a more capable (but slower and more expensive) model, you can replace the line above with this one. Commenting out code like this is a common technique for leaving "alternative options" in place without running them.

---

**`max_tokens=50,`**

This sets a hard limit on how long Claude's reply can be.

- A **token** is roughly equivalent to one word, though it can be a fragment of a word or a punctuation mark. AI language models read and write in tokens rather than letters or words.
- **`max_tokens=50`** tells Claude: "Don't write more than 50 tokens in your response." For a smoke test, we want a tiny reply — just enough to confirm connectivity. There's no need to generate a long response just to verify the connection works.
- The comment notes this is a "tiny limit — just checking connectivity." It's worth knowing: if you omit `max_tokens` entirely, the API will return an error — it is required. This is mentioned in the reference table in the markdown section of the notebook.

---

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual conversation you are sending to Claude.

Let's break it into layers:

- **`messages=`** is the parameter that holds the conversation history. The API expects a list of turns in the conversation — each turn is labeled with who said it and what was said.

- **`[...]`** — The square brackets create a **list**. Think of a list as a numbered list on paper: an ordered collection of items. Here the list contains just one item (one message), but in a real multi-turn conversation it would contain many messages.

- **`{"role": "user", "content": "Say hello in one short sentence."}`** — The curly braces create a **dictionary**. A dictionary is a lookup table made of paired entries, like a phone book where you look up a name to get a number. Here the dictionary has two entries:
  - `"role": "user"` — the key `"role"` maps to the value `"user"`, meaning this message was written by the human side of the conversation.
  - `"content": "Say hello in one short sentence."` — the key `"content"` maps to the actual text of the message.

- The Anthropic API requires this specific structure because it supports multi-turn conversations where messages alternate between `"user"` (the human) and `"assistant"` (Claude). By labeling each message, the API knows who said what.

So the full message structure here says: "Here is a list of one conversation turn: the user said 'Say hello in one short sentence.'"

---

**Line 8: `print(response.content[0].text)`**

This line reaches into the reply object and prints the actual text Claude wrote.

- **`response`** is the variable holding everything Claude sent back — not just the text, but also metadata like token counts and model information.

- **`response.content`** — The dot accesses the `content` part of the response. `content` is a **list** (remember: an ordered collection). Why a list? Because Claude can theoretically return multiple content blocks (for example, text plus tool-use results). In most basic cases, there will be just one block.

- **`[0]`** — Square brackets after a list name let you pick a specific item by its position. Positions in Python start at **zero**, not one. So `[0]` means "the first item." This is called **zero-based indexing** and it surprises many beginners — just remember: first item is `[0]`, second is `[1]`, and so on. The comment confirms this: `# content is a list; [0] is the first block`.

- **`.text`** — Accesses the `text` part of that first content block, which is where Claude's actual written reply lives.

All together: "Get the first content block from Claude's response, and print its text."

---

### What the output means

You will see something like:

```
Hello! I hope you're having a wonderful day.
```

The exact wording will vary because Claude generates text with some natural variation. What matters is:

1. **Any coherent sentence appearing here** = success. The full chain worked: your `.env` file loaded the key → `load_dotenv` pushed it into memory → `os.getenv` retrieved it → `Anthropic(api_key=...)` authenticated your client → `client.messages.create(...)` reached Anthropic's servers → Claude generated a reply → it came back to your notebook → `print` displayed it.

2. **An error message instead** = something in the chain broke. The most common causes are a missing or incorrect API key, no internet connection, or a typo in the model name.

This smoke test cell is intentionally kept simple. Its only job is to verify end-to-end connectivity before you move on to the more complex streaming code in later cells.

---

## Markdown Cells: Section Headers and Reference Tables

The notebook also contains cells written in **Markdown** — a simple formatting language for text. These cells don't run as Python code; they render as formatted text (headings, tables, bullet points) to guide the reader. You can recognize them because they contain prose and formatting rather than Python instructions.

The two markdown cells in this notebook include:

- A **title and objectives block** explaining what the lab covers (streaming, event types, token tracking, failure modes).
- A **reference table** listing every API parameter you can configure on a streaming call, along with a second table showing the different SDK interfaces available for streaming.

These cells require no code explanation — they are documentation. However, a few terms from those tables are worth understanding for context:

- **`stream=True`**: A setting you can pass to the API that, instead of waiting for Claude to finish and sending the whole reply at once, immediately starts sending text as Claude generates it — word by word. This is what makes chat interfaces feel live and responsive.
- **`temperature`**: A number between 0 and 1 that controls how "creative" vs. "predictable" Claude's responses are. At 0, responses are highly deterministic (Claude picks the most likely next word every time). At 1, there's more randomness and variety.
- **`stop_sequences`**: A list of text strings that, if Claude generates any of them, will cause Claude to stop immediately — like a signal flare telling it "you're done."
- **`get_final_message()`**: A function available after streaming completes that returns the fully assembled reply, including token usage statistics.

---

## Glossary

**API (Application Programming Interface)**
A defined way for two software programs to talk to each other. Like a waiter who takes your order to the kitchen and returns with your food — you don't need to know how the kitchen works, just how to place an order with the waiter.

**API key**
A unique secret code that identifies and authenticates you when you make requests to an API. Like a membership card that proves you have an account.

**Assignment (`=`)**
In Python, the single equals sign does not mean "is equal to" — it means "store this value in this variable." `x = 5` means "put the value 5 in the box labeled `x`."

**Class**
A blueprint or template for creating objects in Python. Like a cookie cutter: you define the shape once, then use it to stamp out many individual instances.

**Client**
In software, the program that makes requests. Your Python code acts as a client, and Anthropic's servers act as the server that responds.

**Comment**
A line in Python code that starts with `#`. The computer ignores it entirely. Used to leave notes for human readers.

**Dictionary**
A Python data structure made of paired entries: each entry has a key and a value, like entries in a phone book. Written with curly braces: `{"key": "value"}`.

**`.env` file**
A plain text file (literally named `.env`) used to store secret configuration values like API keys. Each line has the format `VARIABLE_NAME=value`. The leading dot is a convention meaning "hidden/configuration file."

**Environment variable**
A piece of information stored in your computer's operating memory (not a file) that programs can read. Commonly used to share configuration and secrets between your computer's environment and a running program.

**Exception**
Python's way of signaling that something went wrong while running code. When an exception occurs, Python stops running the current code and displays an error message.

**Function**
A named set of instructions you can run on demand by writing its name followed by parentheses. Like a named recipe: `bake_cake()` would run all the steps in the bake_cake recipe.

**Import**
Bringing a library or specific tool from a library into your current workspace so you can use it. Like carrying a tool from a shed to your workbench.

**Instance**
One specific object created from a class (blueprint). If `Anthropic` is the cookie cutter, then `client = Anthropic(...)` creates one specific cookie — `client` is the instance.

**Library / Package**
A pre-written collection of Python code that someone else wrote and made available for others to use. Saves you from having to write common functionality from scratch.

**List**
A Python data structure that holds an ordered collection of items. Written with square brackets: `[item1, item2, item3]`. Items are accessed by position number starting at zero.

**`None`**
A special Python value meaning "nothing" or "no value." When a function can't find what it's looking for, it often returns `None` to signal absence.

**Object**
A self-contained unit in Python that bundles data and functions together. The `client` variable is an object — it holds connection settings and functions like `messages.create`.

**Parameter**
A named input to a function. When you call `Anthropic(api_key=...)`, `api_key` is the parameter name, and the actual key is the value you're passing in.

**`print()`**
A built-in Python function that displays text or values as output below a code cell. Python's way of communicating results back to you.

**SDK (Software Development Kit)**
A set of pre-built tools provided by a company to make it easier to use their service. Anthropic's Python SDK handles the low-level details of connecting to their API so you can focus on what you want to do, not how the connection works.

**Smoke test**
The simplest possible test of a system — just enough to confirm nothing is obviously broken. Term originates from electronics: power up a circuit and watch for smoke.

**Stream / Streaming**
Instead of waiting for Claude to finish generating an entire response and then sending it all at once, streaming sends the text in small pieces as it is generated — word by word or chunk by chunk. This makes interfaces feel faster and more responsive.

**String**
A piece of text that Python treats as a literal value rather than code. Always wrapped in quote marks: `"like this"` or `'like this'`.

**Token**
A chunk of text that AI models process. Roughly equivalent to a word, though it can be a word fragment or a punctuation mark. AI models have limits measured in tokens (both for input and output).

**Variable**
A labeled box that holds a value. You create one with an assignment: `api_key = "abc123"` creates a box labeled `api_key` containing the text `"abc123"`.

**Zero-based indexing**
Python counts positions in a list starting from zero, not one. The first item is at position `[0]`, the second at `[1]`, and so on. This surprises beginners but becomes second nature quickly.