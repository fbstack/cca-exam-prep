# Python Guide: System Prompts Exercise

A plain-English walkthrough of every line of code in this JupyterLab notebook.
No coding background required.

---

## What Python is (for complete beginners)

Python is a language for giving instructions to a computer. Think of it like writing a very precise recipe. The computer follows each instruction exactly, in order, one step at a time. The instructions are written in plain-ish English words, but they follow strict grammar rules so the computer can understand them. A JupyterLab notebook organises those instructions into separate sections called **cells** — you run each cell one at a time, and the results appear directly below it.

---

## Cell 1: Loading Your Secret API Key

```python
from dotenv import load_dotenv  # loads variables from .env file into os.environ
import os                        # gives access to environment variables

load_dotenv()                            # reads .env in current directory
api_key = os.getenv("ANTHROPIC_API_KEY") # retrieves the key value (None if missing)
print("API key loaded:", api_key is not None)  # True = key found, False = check .env
```

### What this code does

---

**Line 1: `from dotenv import load_dotenv`**

This line brings a specific tool into your workspace so you can use it.

Let's unpack the vocabulary first:

- A **module** (also called a **library** or **package**) is a collection of pre-written Python tools someone else built, which you can borrow. Think of it like a toolbox sitting on a shelf. You don't have to build a hammer yourself — you borrow one from the toolbox.
- **`dotenv`** is the name of one of those toolboxes. Its job is to read a special hidden file on your computer called `.env` (pronounced "dot env") that stores secret values, like passwords, without you ever having to type them directly into your code.
- **`import`** means "bring this tool into my workspace." But here, instead of importing the entire `dotenv` toolbox, we're using `from ... import ...`, which means "go to that toolbox and bring me just this one specific tool." We only want the tool called `load_dotenv`.
- **`load_dotenv`** is a **function** — think of a function as a named recipe. When you call it by name later, the computer follows all the instructions inside that recipe. This particular recipe's job is to find your `.env` file and read its contents into the computer's memory.

Why do we need this? Because your secret API key (the password that lets you talk to Claude) should never be written directly in your code. If you shared your notebook with someone, they'd see your password. Instead, the key lives in a separate hidden file, and `load_dotenv` fetches it safely.

---

**Line 2: `import os`**

This brings in another toolbox — this one called `os`.

- **`os`** stands for "operating system." It's a standard Python toolbox (it comes built into Python — you don't need to install it separately) that lets your code talk to the computer's operating system. The operating system is the underlying software that runs your computer, like Windows or macOS.
- One of the things the operating system manages is something called **environment variables** — these are named values that your computer stores behind the scenes, like a set of sticky notes the computer keeps for itself. After `load_dotenv()` runs, it puts your API key on one of those sticky notes. The `os` toolbox lets your Python code read those sticky notes.

---

**Line 4: `load_dotenv()`**

This line actually *calls* (meaning: runs, executes) the `load_dotenv` recipe we imported on Line 1.

- The empty parentheses `()` after a name are how you tell Python "run this recipe right now." Without the parentheses, you're just referring to the recipe — you're not actually cooking anything.
- When this runs, it looks in the current folder for a file named `.env`, opens it, reads any lines inside it that look like `VARIABLE_NAME=some_value`, and quietly loads them into the computer's environment variables — those sticky notes the operating system keeps.
- You don't see any output from this line. It just does its work silently.

---

**Line 5: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line reads one of those sticky notes and saves its value into a labeled box in your code.

- **`os.getenv("ANTHROPIC_API_KEY")`** — The dot (`.`) here means "go inside the `os` toolbox and use the tool called `getenv`." The word `getenv` is short for "get environment variable." The value inside the quotation marks — `"ANTHROPIC_API_KEY"` — is the *name* of the sticky note we want to read.
- **`api_key`** is a **variable** — a labeled box that holds a value. You're telling Python: "read the sticky note named `ANTHROPIC_API_KEY` and store whatever value is written on it in a box I'm calling `api_key`."
- The **`=`** sign here doesn't mean "is equal to" the way it does in maths. In Python, `=` means "take the thing on the right, and put it into the box named on the left." It's an *assignment*.
- If the `.env` file was missing, or the key wasn't in it, `os.getenv()` gives back a special value called **`None`** — Python's way of saying "there's nothing here." The `api_key` box would then hold `None` as its value.

---

**Line 6: `print("API key loaded:", api_key is not None)`**

This line displays a message so you can see whether the key was found successfully.

- **`print()`** is a built-in Python function that displays things in the output area below the cell. Whatever you put inside the parentheses gets shown on screen.
- **`"API key loaded:"`** is a **string** — a piece of text. In Python, text is always wrapped in quotation marks so the computer knows it's not a command.
- **`api_key is not None`** is a question Python asks and answers for you. It checks whether the `api_key` box holds an actual value (your key) or the empty signal `None`. `is not None` literally means "is this thing something real, not empty?" Python answers this question with either **`True`** or **`False`** — these are called **booleans**, and they represent yes/no answers.
- The comma between the two items inside `print()` tells Python to display them one after the other, separated by a space.

### What the output means

You'll see one of two things:
- **`API key loaded: True`** — Your `.env` file was found, the key was read successfully, and everything is ready to go.
- **`API key loaded: False`** — Something went wrong. Either the `.env` file doesn't exist, it's in the wrong folder, or the key isn't named `ANTHROPIC_API_KEY` inside it. You'd need to fix this before going further.

This check matters because if the key is missing, every attempt to talk to Claude later will fail with an authentication error — so it's worth catching the problem here, early.

---

## Cell 2: Creating the Connection to Claude

```python
from anthropic import Anthropic  # imports the official Anthropic Python SDK client class

client = Anthropic(api_key=api_key)  # instantiates client; api_key authenticates every request
print("Anthropic client ready")       # confirms object creation succeeded
```

### What this code does

---

**Line 1: `from anthropic import Anthropic`**

This line brings in the official tool for communicating with Claude.

- **`anthropic`** (lowercase) is the name of a Python package — a toolbox created by Anthropic, the company that makes Claude. This toolbox handles all the technical details of sending messages to Claude and receiving responses.
- **`Anthropic`** (uppercase, the specific tool we're importing) is a **class** — think of a class as a *blueprint* or *template* for creating a specific type of object. In this case, it's the blueprint for creating a "connection object" — something that knows how to talk to Anthropic's servers.
- The distinction between the lowercase package name and the uppercase class name can be confusing at first. Just remember: the lowercase one is the toolbox; the uppercase one is the specific tool inside it.
- An **SDK** (Software Development Kit) is a ready-made toolbox a company provides so developers can use their service without having to build the technical plumbing themselves. Anthropic's SDK handles things like formatting your requests correctly, managing errors, and processing responses.

---

**Line 3: `client = Anthropic(api_key=api_key)`**

This line uses the `Anthropic` blueprint to create an actual working connection object, and stores it in a variable called `client`.

- **`Anthropic(api_key=api_key)`** — The parentheses after `Anthropic` tell Python to build a new object using that blueprint. This process is called **instantiation** — you're creating one specific *instance* (one particular copy) of the thing the blueprint describes.
- **`api_key=api_key`** — This is how you pass information into the object you're creating. The part on the left (`api_key=`) is the name of the setting you're providing. The part on the right (`api_key`) is the variable from Cell 1 that holds your actual key value. So you're saying: "build this connection object, and tell it to use my API key for authentication."
- **Authentication** means proving to a server that you are who you claim to be, and that you have permission to use the service. Your API key is like a membership card — every request you send will automatically include it.
- **`client`** is the variable (labeled box) that holds this connection object. You'll use `client` in every future cell whenever you want to talk to Claude.
- An **API** (Application Programming Interface) is like a waiter at a restaurant. You (the customer) tell the waiter what you want. The waiter goes to the kitchen (Anthropic's servers), the kitchen prepares your order (Claude generates a response), and the waiter brings it back to you. You never interact with the kitchen directly — the waiter (API) handles the communication.

---

**Line 4: `print("Anthropic client ready")`**

This prints a simple confirmation message.

- This is a plain text message with no logic attached. It only appears if the line above it succeeded without an error. If `Anthropic(api_key=api_key)` had failed — for example, due to an invalid key format — Python would have stopped and shown an error message instead of reaching this `print` line.

### What the output means

You should see: **`Anthropic client ready`**

This tells you the connection object was created without errors. It doesn't yet confirm your key is *valid* — that only gets verified when you make your first actual request to Claude. But it confirms the object exists and is ready to use.

---

## Cell 3: Sending a Test Message to Claude (The "Smoke Test")

```python
response = client.messages.create(       # calls the Messages API endpoint
    model="claude-haiku-4-5",            # fastest/cheapest model — good for smoke tests
    #    model="claude-sonnet-4-5",      # swap here for higher-quality responses
    max_tokens=50,                       # hard cap on output length; required by the API
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]  # minimal valid messages list
)
print(response.content[0].text)  # content is a list of blocks; [0] is the first text block
```

### What this code does

---

**Lines 1–5: `response = client.messages.create(...)`**

This is the heart of the operation: sending a message to Claude and receiving a reply. Let's go through it carefully.

The whole block from `client.messages.create(` down to the closing `)` is one single instruction that happens to span multiple lines. Python allows this when the instruction is inside parentheses — it keeps reading until it finds the matching closing parenthesis. This is done purely for readability.

- **`client.messages.create()`** — Starting from the left: `client` is the connection object you created in Cell 2. The dot means "go inside this object." `messages` is a section of that object that handles the message-sending functionality. Another dot, and then `create` is the specific action: "create a new message exchange." The parentheses trigger it to actually run.
- **`response =`** — The reply that comes back from Claude gets stored in a variable (labeled box) called `response`. This box ends up holding a complex object containing Claude's text, plus metadata like how many tokens were used.
- A **token** is roughly a word or word-fragment. AI models like Claude don't read character by character or word by word exactly — they break text into chunks called tokens. "Hello" is one token. "Unbelievable" might be two. Tokens matter because the API charges based on how many tokens are sent and received.

---

**`model="claude-haiku-4-5"`**

This tells the API which version of Claude to use.

- Anthropic offers several Claude models, each with different trade-offs between speed, cost, and capability. Think of them like ordering a car: economy model vs. luxury model.
- **`claude-haiku-4-5`** is the fastest and least expensive model — good for quick tests where you just want to confirm everything is working, rather than getting the highest-quality response.
- The value is a **string** (text in quotes) because model names are just text labels that the API uses to route your request to the right system.
- The commented-out line directly below (`# model="claude-sonnet-4-5"`) shows an alternative. In Python, any line starting with `#` is a **comment** — the computer ignores it entirely. Comments are notes for human readers. This one is telling you: "if you want better quality responses, remove the `#` from this line and add `#` to the line above it."

---

**`max_tokens=50`**

This sets a hard limit on how long Claude's response can be.

- **`max_tokens`** is a required parameter — the API will refuse your request if you leave it out. It forces you to think about and declare an upper bound on response length.
- Setting it to `50` means: "stop generating after 50 tokens, even if you're mid-sentence." For this smoke test, a short response is all we need.
- This protects you from accidentally generating (and being charged for) an unexpectedly long response.

---

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual conversation you're sending to Claude. Let's unpack this carefully, because the structure might look strange at first.

- **`messages=`** — This is the parameter that carries the conversation history.
- The value is a **list**, indicated by the square brackets `[...]`. A list is an ordered collection of items — like a numbered list on paper. Here, the list contains just one item.
- That one item is a **dictionary**, indicated by the curly braces `{...}`. A dictionary is a lookup table made of paired entries — each entry has a *key* (a label) and a *value* (what that label points to), separated by a colon. Think of it like a form with labeled fields.
- This dictionary has two fields:
  - `"role": "user"` — The label `"role"` has the value `"user"`, meaning this message is coming from the human side of the conversation.
  - `"content": "Say hello in one short sentence."` — The label `"content"` has the value `"Say hello in one short sentence."`, which is the actual text of the message.
- The API requires messages in this exact structure: a list of dictionaries, each with a `"role"` and `"content"`. The role can be `"user"` (human speaking) or `"assistant"` (Claude speaking). This format lets you send multi-turn conversations by including multiple dictionaries in the list.

---

**Line 6: `print(response.content[0].text)`**

This line extracts Claude's actual reply text from the response and displays it.

- **`response`** — The variable holding everything Claude sent back.
- **`.content`** — Inside the response object, there's a section called `content` that holds the actual reply. The dot means "go inside `response` and look at the `content` part."
- **`[0]`** — The content section is a list (because in theory a response could have multiple parts). The `[0]` means "give me the first item in this list." In Python, lists are numbered starting from `0`, not `1`. So the first item is always at position `[0]`.
- **`.text`** — Inside that first content item, `.text` is where the actual reply text lives.
- **`print(...)`** — Displays the extracted text in the output area.

### What the output means

You'll see something like: **`Hello! I'm doing well and happy to assist you today.`**

This is Claude's actual reply to your "Say hello in one short sentence" message. If this appears, your entire setup is working: the API key is valid, the connection is live, and Claude is responding. This is called a **smoke test** — a minimal check just to see if the system is working before running more complex code. (The term comes from electronics: you power a new circuit on and check whether smoke appears before investing more effort.)

---

## Cell 4: The `ask()` Helper Function

```python
# --- Token usage log — appended after every API call for Section 9 analysis ---
usage_log = []  # list of dicts; each entry records one API call's token counts

def ask(prompt: str, system: str = None, model: str = "claude-haiku-4-5", max_tokens: int = 512) -> str:
    """
    Single-turn convenience wrapper around client.messages.create().

    Parameters
    ----------
    prompt     : The user message text to send.
    system     : Optional system prompt string (omitted entirely when None).
    model      : Claude model ID string.
    max_tokens : Hard upper bound on response length in tokens.

    Returns
    -------
    str  — The assistant's reply text.
    """
    # Build the keyword arguments dict dynamically so we never pass system=None.
    # The API interprets system=None as [explanation continues in original]
```

> **Note:** The notebook source appears to be truncated after the comment about `system=None`. The explanation below covers all visible lines fully, and describes what the truncated section would logically contain.

### What this code does

---

**Line 1: `# --- Token usage log — appended after every API call for Section 9 analysis ---`**

This is a **comment** — a note for human readers. The `#` character tells Python to ignore everything after it on that line. Comments like this one (surrounded by dashes) are a common way to create a visible divider in code, making it easier to see where one section ends and another begins.

---

**Line 2: `usage_log = []`**

This creates an empty list and stores it in a variable called `usage_log`.

- **`[]`** — Empty square brackets create an empty list — like a blank notebook with no entries yet. A list is an ordered collection of items that can grow over time.
- **`usage_log`** — The variable name tells you its purpose: it will log (record) usage data. After each call to Claude, the code will add an entry to this list describing how many tokens were used. This lets you track and analyse API usage later.
- The list starts empty because no calls have been made yet.

---

**Line 4: `def ask(prompt: str, system: str = None, model: str = "claude-haiku-4-5", max_tokens: int = 512) -> str:`**

This is the most complex line so far. It *defines* a reusable function called `ask`. Let's take it piece by piece.

- **`def`** — Short for "define." This keyword tells Python: "I'm about to create a new named recipe." Everything indented below this line is the contents of the recipe.
- **`ask`** — The name of the function. You chose this name. After this cell runs, you can call `ask(...)` anywhere in the notebook to trigger this recipe.
- **The parentheses `(...)`** contain the **parameters** — the pieces of information the function needs to do its job. Think of parameters as the blank lines on a form you fill out when you call the function. This function has four parameters:

  - **`prompt: str`** — The user's message to send to Claude. The `: str` part is a **type hint** — it's a note saying "this should be text (a string)." Python doesn't enforce type hints, but they help humans reading the code understand what kind of value is expected.
  - **`system: str = None`** — An optional system prompt. The `= None` part means this parameter has a **default value** of `None`. If you call `ask()` without providing a system prompt, it automatically uses `None` (meaning: don't send a system prompt). You only need to provide it when you want it.
  - **`model: str = "claude-haiku-4-5"`** — Which Claude model to use. The default is `"claude-haiku-4-5"`, so if you don't specify, it uses the fast, economical model automatically.
  - **`max_tokens: int = 512`** — The maximum response length in tokens. Default is 512. The `: int` hint says this should be a whole number (integer).

- **`-> str:`** — This is the **return type hint**, indicating that this function will hand back a string (text) when it's done. The arrow `->` points to what comes out. The colon `:` at the very end marks the start of the function's body (everything indented below).

A **system prompt** is a special set of instructions sent to Claude *before* the conversation begins. It's like a briefing you give an assistant before a meeting: "You are a Python engineer. Be concise. Use code examples." It shapes Claude's behaviour for the entire conversation without the user seeing it.

---

**Lines 5–17: The docstring (the text in triple quotes)**

```
"""
Single-turn convenience wrapper around client.messages.create().
...
"""
```

- The text between the triple quotation marks (`"""`) is called a **docstring** — short for "documentation string." It's a built-in way to write a description of what a function does, directly inside the function.
- Python treats the content as a string of text, not as instructions to execute. It's purely informational — for human readers and tools that generate documentation.
- This docstring explains: what the function does, what each parameter means, and what the function returns when it's done.

---

**The comment about building arguments dynamically (truncated section)**

```python
# Build the keyword arguments dict dynamically so we never pass system=None.
# The API interprets system=None as [...]
```

Although the rest of this cell's code was cut off in the source, the comment tells us exactly what the next section does. Here's what it would logically contain and why it matters:

The Anthropic API treats `system=None` differently from simply *not including* a system parameter at all. Passing `system=None` can cause an error or unexpected behaviour — it's like handing the waiter a blank order form versus not handing them a form at all. One confuses them; the other is simply an omission.

To handle this safely, the function builds a dictionary of arguments to pass to `client.messages.create()`, and only *adds* the `system` key if a real value was provided. This is called **dynamic construction** — building something piece by piece based on conditions. If `system` is `None`, the key is simply left out of the dictionary entirely, which is the correct behaviour.

### What the output means

Defining a function with `def` produces no visible output. Nothing appears below the cell. This is normal — you're just creating the recipe. The recipe doesn't run until you call it by name later. Think of it as writing the instructions on a card and setting it aside for later use.

---

## Glossary

Every technical term used in this guide, defined in plain English.

---

**API (Application Programming Interface)**
A waiter who takes your order to the kitchen (a server or service) and brings back the result. It's the agreed-upon way two pieces of software talk to each other. You don't interact with the kitchen directly; you use the API.

**Authentication**
Proving to a server that you are who you say you are and that you have permission to use the service. Your API key is your authentication credential — like a membership card.

**Boolean**
A value that is either `True` or `False`. Python's way of representing yes/no answers to questions.

**Cell (in JupyterLab)**
A section of a notebook that contains either code (which you can run) or text (which is formatted and displayed). Cells run one at a time.

**Class**
A blueprint or template for creating objects. Like an architectural drawing — you use it to construct actual buildings (objects), each of which follows the same design.

**Comment**
A note in code that starts with `#`. The computer ignores everything after the `#` on that line. Comments are written for human readers.

**Default value**
A value a parameter uses automatically if you don't provide one when calling a function. It's the pre-filled answer on a form — you can change it, but if you leave it blank, the default is used.

**Dictionary**
A lookup table made of paired entries. Each entry has a key (a label) and a value (what that label points to). Like a phone book: look up a name (key) to get a number (value). In Python, written with curly braces: `{"key": "value"}`.

**Docstring**
A description written inside a function using triple quotation marks. Python doesn't execute it — it's documentation for human readers and tools.

**Dot notation**
Using a `.` to go inside an object and access something within it. `client.messages.create` means: inside `client`, find `messages`, and inside that, find `create`.

**Environment variable**
A named value stored by the operating system, available to any program running on the computer. Like a sticky note the OS keeps. Used to store configuration values (like API keys) outside of code files.

**Function**
A named recipe. You define it once with `def`, and then call it by name whenever you want to run those instructions. Functions can accept input (parameters) and return output.

**Instantiation**
The act of creating one specific object from a class (blueprint). If the class is the architectural drawing, instantiation is when you actually build the house.

**Integer**
A whole number with no decimal point (e.g., 50, 512, 0). In Python, type-hinted as `int`.

**Library / Module / Package**
A collection of pre-written Python tools you can import and use. The terms overlap somewhat: a module is a single file of tools; a package is a collection of modules; a library is a general term for any reusable collection of code.

**List**
An ordered collection of items, like a numbered list on paper. In Python, written with square brackets: `["item1", "item2"]`. Items are numbered starting from 0.

**`None`**
Python's way of representing "nothing" or "no value." It's not zero, it's not an empty string — it's the explicit absence of a value.

**Object**
A self-contained bundle of data and functionality created from a class blueprint. The `client` variable holds an object that knows how to communicate with Anthropic's servers.

**Parameter**
A piece of information a function needs to do its job. Defined in the function's parentheses when you use `def`. When you call the function, you fill in the parameters with actual values.

**Return value**
What a function hands back to you after it runs. In the `ask()` function, the return value is Claude's reply text.

**SDK (Software Development Kit)**
A ready-made toolbox a company provides so you can use their service without building all the technical plumbing yourself. Anthropic's Python SDK handles formatting requests, managing errors, and parsing responses.

**Smoke test**
A quick, minimal check to see if a system is fundamentally working before investing time in more complex testing. The term comes from electronics: you power a device on and check whether smoke appears.

**String**
A piece of text in Python, always wrapped in quotation marks. `"Hello"` is a string. `"claude-haiku-4-5"` is a string.

**System prompt**
A special set of instructions sent to Claude *before* the conversation begins. It's a briefing that shapes Claude's behaviour — its tone, role, and format — without the user seeing it.

**Token**
A chunk of text that an AI model processes as a unit. Roughly equivalent to a word or part of a word. AI models are billed and limited based on token counts, not character or word counts.

**Type hint**
A note in a function definition indicating what kind of value a parameter expects or a function returns. Python doesn't enforce them, but they help human readers understand the code.

**Variable**
A labeled box that holds a value. You create a variable by giving it a name and assigning a value with `=`. The box can hold text, numbers, lists, objects, or other types of data.

**`=` (assignment)**
In Python, a single `=` does not mean "is equal to." It means "take the value on the right and store it in the variable named on the left."

**`==` (equality check)**
Two equals signs check whether two values are the same. `api_key == "abc"` asks "is the value in `api_key` exactly `'abc'`?" This is different from assignment.

**`[0]` (list indexing)**
A way to retrieve one specific item from a list. Python counts from zero, so `[0]` means "the first item," `[1]` means "the second item," and so on.