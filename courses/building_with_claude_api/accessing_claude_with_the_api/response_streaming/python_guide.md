# Python Guide: Response Streaming with the Claude API

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (one paragraph for complete beginners)

Python is a language for giving instructions to a computer. Think of it like writing a very precise recipe: you list every step in order, and the computer follows each one exactly. Unlike a recipe written for a human — where you can say "add some salt" and the cook figures it out — Python needs every instruction spelled out completely. The notebook you are working through is divided into **cells**, which are chunks of instructions you run one at a time, in order, like turning the pages of that recipe and executing each step before moving to the next.

---

## Cell 1: Load Environment Variables (Getting Your Secret API Key Safely)

```python
# ── Starter Cell 1: Load environment variables ──────────────────────────────
from dotenv import load_dotenv   # load_dotenv reads .env file into os.environ
import os                         # os.getenv safely retrieves env vars

load_dotenv()                     # parse .env in current working directory
api_key = os.getenv("ANTHROPIC_API_KEY")  # fetch the key; None if missing
print("API key loaded:", api_key is not None)  # confirm without printing secret
```

### What this code does

**Line 1: `# ── Starter Cell 1: Load environment variables ──────────────────────────────`**

This line is a **comment** — a note written for humans, not the computer. In Python, any line that starts with a `#` symbol is completely ignored when the code runs. Comments exist purely to explain what is happening to anyone reading the code. This one tells us the purpose of this entire cell: loading environment variables.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack three ideas here.

First, a **module** (also called a **library** or **package**) is a pre-written collection of tools someone else built and made available for you to use — like a specialist toolbox you can borrow. You do not have to build these tools yourself; you just reach into the box and grab what you need.

Second, **`dotenv`** is the name of one such toolbox. Its specialty is reading a special file on your computer called `.env` (pronounced "dot env"). A `.env` file is a plain text file where you store secret information — like passwords and API keys — that you do not want to accidentally share or hardcode into your notebook. Think of it as a locked drawer where you keep sensitive notes, separate from the main document.

Third, **`import`** is the action of bringing a tool from a toolbox into your current workspace so you can use it. The word **`from`** narrows it down: instead of bringing the entire `dotenv` toolbox, you are only bringing one specific tool from it — the tool called **`load_dotenv`**.

So this whole line says: *"From the dotenv toolbox, bring me the load_dotenv tool."*

---

**Line 3: `import os`**

This brings in another toolbox called **`os`**, which stands for **operating system**. Your operating system is the software that runs your computer — Windows, macOS, or Linux. The `os` toolbox gives Python the ability to talk to the operating system and ask it questions, like "what environment variables do you have stored?" We need this toolbox in a moment to retrieve our API key.

Notice that this time we used `import os` without `from` — this brings in the entire `os` toolbox at once, rather than just one tool from it.

---

**Line 4: *(blank line)***

A blank line in Python is just breathing room — it makes the code easier to read by grouping related lines together visually. The computer skips it entirely.

---

**Line 5: `load_dotenv()`**

Here we are **calling** (which means "using" or "executing") the `load_dotenv` tool we imported on Line 2. The parentheses `()` at the end are how you call a tool in Python — they are like pressing the "go" button on it.

A **function** is a named set of instructions that performs a task when called. Think of it as a named recipe: `load_dotenv` is the recipe name, and the `()` says "run this recipe now."

What does this recipe do? It looks for a file named `.env` in your current working folder (the folder your notebook is sitting in), opens it, reads the secret key-value pairs stored inside, and loads them into something called **environment variables** — a kind of temporary memory that your operating system holds while your program is running. After this line runs, your API key is sitting in that temporary memory, ready to be retrieved.

---

**Line 6: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

Several things are happening here, so let's take them in order.

**`os.getenv("ANTHROPIC_API_KEY")`** — The dot `.` between `os` and `getenv` means "use the tool called `getenv` that lives inside the `os` toolbox." The `getenv` tool asks the operating system: *"Do you have an environment variable with this exact name?"* The name we are asking about is `"ANTHROPIC_API_KEY"` — this must match exactly what is written in your `.env` file. If it finds it, it hands back the value (your actual secret key). If it does not find it, it hands back a special Python value called **`None`**, which simply means "nothing" or "not found."

**`api_key =`** — The `=` sign in Python does not mean "equals" the way it does in math. It means **"store this value in a labeled box called `api_key`."** That labeled box is called a **variable** — a name you choose that holds a value for later use. From this point forward, whenever the code says `api_key`, Python knows to look inside that box and use whatever is stored there.

Putting it together: this line says *"Ask the operating system for the value of ANTHROPIC_API_KEY, and store whatever it gives back in a variable called api_key."*

---

**Line 7: `print("API key loaded:", api_key is not None)`**

**`print()`** is a built-in Python function that displays output — whatever you put inside its parentheses appears as text below the cell when you run it. Think of it as Python's way of showing you a message.

**`"API key loaded:"`** is a **string** — a piece of text. In Python, text is always wrapped in quotation marks so the computer knows it is meant to be read as words, not as code instructions.

**`api_key is not None`** is the interesting part. This is a **logical expression** that evaluates to either `True` or `False`. It asks: *"Is the value stored in api_key something other than nothing?"* If `load_dotenv()` successfully found your key, `api_key` contains your actual key text, and `api_key is not None` becomes `True`. If the key was not found, `api_key` is `None`, and this expression becomes `False`.

Why not just print the key itself? Because your API key is a secret — like a password. Printing it directly would expose it in your notebook output, which could be dangerous if you share the notebook. This line is a clever compromise: it tells you whether the key was found without revealing what the key actually is.

### What the output means

When this cell runs, you will see one of two things printed below it:

- **`API key loaded: True`** — Your `.env` file was found, the key was loaded successfully, and you are ready to proceed. Good news.
- **`API key loaded: False`** — Something went wrong. Either the `.env` file does not exist, or the key inside it is not named exactly `ANTHROPIC_API_KEY`. You would need to fix this before continuing.

---

## Cell 2: Initialise the Anthropic Client (Opening a Connection to Claude)

```python
# ── Starter Cell 2: Initialise Anthropic client ──────────────────────────────
from anthropic import Anthropic   # main SDK class that wraps all API calls

client = Anthropic(api_key=api_key)  # create one shared client for the session
print("Anthropic client ready")       # quick confirmation that init succeeded
```

### What this code does

**Line 1: `# ── Starter Cell 2: Initialise Anthropic client ──────────────────────────────`**

Another comment for human readers — the computer ignores it. It tells us this cell's job is to set up the Anthropic client. "Initialise" means "set up and prepare for use."

---

**Line 2: `from anthropic import Anthropic`**

This follows the same pattern as Cell 1's import lines. **`anthropic`** (lowercase) is the name of a toolbox — specifically, the official **SDK** (Software Development Kit) that Anthropic provides. An SDK is a pre-built set of tools that makes it easier to communicate with a service — in this case, the Claude AI. Without an SDK, talking to Claude would require writing a lot of complicated low-level networking code yourself. The SDK handles all of that for you.

From inside that `anthropic` toolbox, we are importing a specific tool called **`Anthropic`** (uppercase). This particular tool is a **class** — a blueprint for creating objects. Think of a class like a cookie cutter: the cutter itself is the class, and each cookie you stamp out is an **object** (or **instance**) created from that blueprint. In a moment, we will use this blueprint to create our working connection to Claude.

---

**Line 3: *(blank line)***

Visual breathing room only — ignored by Python.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line uses the `Anthropic` blueprint to create a working object, and stores it in a variable called `client`.

**`Anthropic(api_key=api_key)`** — The parentheses after `Anthropic` mean we are creating a new instance (a new cookie from the cutter). Inside the parentheses, `api_key=api_key` is passing in a piece of information the new object needs: our API key. The part on the left of the `=`, `api_key`, is the name that the `Anthropic` tool expects to receive — it is looking for a piece of information it calls `api_key`. The part on the right, `api_key`, is the variable we created in Cell 1 that holds our actual key. So we are saying: *"Here is the key; please use it."*

What does **`client`** represent? Think of it as a personal assistant who has already verified your identity (using the API key) and is now ready to carry messages back and forth between your notebook and Claude. Every time you want to ask Claude something, you will speak to `client`, and `client` handles the communication for you.

**Why store it in a variable?** Because we only want to do this setup once. By storing the ready-to-use object in `client`, we can use it in every subsequent cell without having to set up the connection all over again each time.

---

**Line 5: `print("Anthropic client ready")`**

This simply prints the message `Anthropic client ready` to confirm that the previous line did not produce any errors. If something went wrong — for example, if the API key was invalid — Python would have shown an error message instead of reaching this line. Seeing this message is your confirmation that the client was created successfully.

### What the output means

You should see: **`Anthropic client ready`**

This means the Anthropic SDK accepted your API key and created a working client object. You now have an open line of communication to Claude, ready to be used in the cells that follow.

---

## Cell 3: Smoke Test (Sending a First Message to Confirm Everything Works)

```python
# ── Starter Cell 3: Smoke test ────────────────────────────────────────────────
response = client.messages.create(
    model="claude-haiku-4-5",         # fast/cheap model ideal for tests
    # model="claude-sonnet-4-5",      # swap here for higher quality
    max_tokens=50,                    # tiny limit — just checking connectivity
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]
)
print(response.content[0].text)       # content is a list; [0] is the first block
```

### What this code does

**Line 1: `# ── Starter Cell 3: Smoke test ──────────────────────────────────────────────`**

Another human-readable comment. A **smoke test** is a term borrowed from electronics: when you first power on a new circuit, you watch to see if smoke appears, which would indicate something is badly wrong. In software, a smoke test is a minimal check — the simplest possible thing you can do to confirm the system is working before you try anything more complex.

---

**Lines 2–6: `response = client.messages.create(...)`**

This is a single instruction spread across multiple lines for readability. Python allows this — if a line opens a parenthesis `(`, Python knows the instruction is not finished until the matching closing parenthesis `)` appears.

The entire block says: *"Use the client to send a message to Claude and store Claude's response in a variable called `response`."*

Let's look at each piece:

**`client.messages.create(...)`** — This calls a function that lives inside your `client` object. The `.messages.create` part navigates to the right tool: `client` is your assistant, `.messages` is the section of the assistant's capabilities that deals with message-based conversations, and `.create` is the specific action — create a new message exchange. Everything inside the parentheses tells this function what to do.

**`model="claude-haiku-4-5"`** — This tells the API which version of Claude to use. Think of Claude as a family of AI models with different capabilities and costs. **Claude Haiku** is the lightest and fastest member of the family — ideal for quick tests because it responds quickly and costs less. The format `parameter_name=value` is how you pass named pieces of information into a function. These are called **keyword arguments** — named slots you fill in.

**`# model="claude-sonnet-4-5"`** — This entire line is commented out (starts with `#`), so Python ignores it completely. It is left here as a helpful note: if you wanted higher-quality responses, you could remove the `#` from this line and add a `#` to the line above to swap to the more capable Sonnet model. Only one `model=` line can be active at a time.

**`max_tokens=50`** — A **token** is the unit of text that AI models work with internally. It is not exactly a word — it is more like a word-chunk. On average, 100 tokens is roughly 75 words. `max_tokens=50` sets a hard ceiling: Claude is not allowed to generate more than 50 tokens in its response. For this smoke test, we only want a short sentence, so 50 is plenty. This parameter is also critical for cost control — without it, Claude could potentially generate a very long response, which costs more and takes longer.

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`** — This is the actual conversation you are sending to Claude. Let's unpack it carefully because it uses two important data structures:

- A **list** is an ordered collection of items, written with square brackets `[...]`. Think of it like a numbered list on paper. Here, the list contains one item — a single message.
- A **dictionary** is a lookup table of paired information, written with curly brackets `{...}`. Each entry has a **key** (a label) and a **value** (the information). Think of it like a form with labeled fields. Here, the dictionary has two fields: `"role"` and `"content"`.

So `{"role": "user", "content": "Say hello in one short sentence."}` is one message in the conversation, structured as a form with two fields:
- `"role": "user"` — identifies who is speaking. In Claude's conversation format, messages alternate between `"user"` (that's you) and `"assistant"` (that's Claude). This message is from the user.
- `"content": "Say hello in one short sentence."` — the actual text of the message.

The whole `messages=[...]` says: *"Here is the conversation history — a list containing one message from the user."*

**`response =`** — Whatever Claude sends back gets stored in this variable. The response is a complex object containing Claude's reply text, token usage statistics, and other information.

---

**Line 7: `print(response.content[0].text)`**

Now we reach into the response object to get the actual text of Claude's reply.

**`response.content`** — The `response` object has several sections (called **attributes**). The `.content` section holds the actual content of Claude's reply. This is returned as a **list** — a collection of content blocks — because Claude can theoretically respond with multiple blocks (for example, a text block and an image block in advanced use cases).

**`[0]`** — Square brackets with a number inside are used to access a specific item from a list by its **index** — its position number. Importantly, Python starts counting at zero, not one. So `[0]` means "the first item." This gives us the first (and in this case only) content block in Claude's response.

**`.text`** — This accesses the `text` attribute of that first content block — the actual words Claude generated.

Putting it all together: `response.content[0].text` means *"go into the response, find the content list, take the first item from that list, and get its text."* Then `print(...)` displays that text below the cell.

### What the output means

You should see a short, friendly sentence from Claude — something like:

**`Hello! It's a pleasure to meet you today.`**

The exact wording will vary because AI responses have some natural variation. What matters is that a sentence appears at all. If you see text, it means:
- Your API key is valid and accepted
- Your internet connection is working
- The `client` object is communicating with Claude correctly
- You are ready to proceed with the actual streaming lessons

If you see an error instead, something in the setup went wrong and needs to be fixed before continuing.

---

## Markdown Cells (Non-Code Explanatory Content)

The notebook also contains two **markdown cells** — these are not code and do not run. Markdown is a lightweight formatting language for writing human-readable text with headings, bullet points, bold text, and tables. JupyterLab renders these cells as formatted text. They serve as documentation and course material embedded directly in the notebook.

**The title cell** (`# CCA Lab: Response Streaming`) introduces the lab topic, lists what you will learn, identifies which CCA (Claude Certification Associate) exam domain the lab addresses, and states five learning objectives. Reading this cell before running the code cells tells you the purpose and scope of what follows.

**The Section 1 header cell** (`## Section 1: Prerequisites, Environment Setup & API Glossary`) contains two reference tables:

- The first table documents every parameter you can pass to the Claude API's messages endpoint — its name, data type, whether it is required, and what it does. The ✅ symbol means required; ❌ means optional.
- The second table explains three different ways the SDK lets you work with streaming responses: the raw event approach, the high-level context manager approach, and a method for retrieving the complete message after streaming finishes.

These cells require no explanation of Python syntax because they contain no Python — they are reference material to consult as you work through the streaming code cells that follow in the lab.

---

## Glossary

**API (Application Programming Interface)** — A defined way for two pieces of software to communicate. Like a waiter who takes your order to the kitchen and brings back what you requested, the API accepts your request, delivers it to a service (Claude), and returns the result.

**API key** — A unique secret string of characters that identifies you to a service and proves you have permission to use it. Treat it like a password — never share it or display it publicly.

**Attribute** — A piece of data that belongs to an object. Accessed with a dot: `object.attribute`. Like a field on a form that belongs to that form.

**Call (a function)** — To execute a function by writing its name followed by parentheses, e.g. `load_dotenv()`. The parentheses are the trigger that makes it run.

**Class** — A blueprint for creating objects. Like a cookie cutter: the cutter is the class; each stamped-out cookie is an instance.

**Client** — In this notebook, the `client` variable holds an object that manages communication between your notebook and the Claude API. Your personal assistant for talking to Claude.

**Comment** — A line in Python that begins with `#`. The computer ignores it entirely. It exists for human readers only.

**Dictionary** — A data structure that stores paired information as key-value pairs, written with curly brackets `{}`. Like a form with labeled fields: `{"role": "user"}` means the field labeled "role" contains the value "user."

**Environment variable** — A piece of information stored in your operating system's temporary memory while a program runs. Used to pass sensitive data (like API keys) to programs without hardcoding them into the source code.

**`.env` file** — A plain text file (typically named `.env`) that stores environment variables as `KEY=value` pairs, one per line. The `dotenv` library reads this file and loads its contents into your environment.

**Function** — A named, reusable set of instructions that performs a task when called. Like a named recipe: you call it by name, and it executes its steps.

**Import** — Bringing a tool or toolbox from an external library into your current working environment so you can use it. Like pulling a specific tool from a toolbox into your workspace.

**Index** — The position number of an item in a list. Python counts from zero, so the first item is at index `[0]`, the second at `[1]`, and so on.

**Instance** — A specific object created from a class blueprint. If `Anthropic` is the cookie cutter (class), then `client = Anthropic(...)` creates one cookie (instance).

**Keyword argument** — A named piece of information you pass into a function, written as `name=value`. The name tells the function which slot you are filling in.

**Library / Module / Package** — Pre-written collections of tools that extend what Python can do. You bring them into your code using `import`. These terms are often used interchangeably.

**List** — An ordered collection of items, written with square brackets `[...]`. Like a numbered list on paper. Items are accessed by their index number.

**`max_tokens`** — A parameter that sets the maximum number of tokens (word-chunks) Claude is allowed to generate in a single response. Controls both cost and response length.

**Method** — A function that belongs to an object. Called using dot notation: `object.method()`. Like asking your assistant to do something: `client.messages.create()`.

**Model** — A specific trained version of an AI. In this notebook, `claude-haiku-4-5` is a fast, lightweight model; `claude-sonnet-4-5` is more capable but slower and more expensive.

**`None`** — A special Python value that means "nothing" or "no value." Used when a variable has no meaningful value to hold — for example, when `os.getenv()` cannot find the requested environment variable.

**Object** — A bundle of data and functions created from a class blueprint. The `client` variable in this notebook is an object — it holds both the connection information and the tools needed to use it.

**`os` module** — A Python toolbox for communicating with the operating system. Among other things, it can read environment variables with `os.getenv()`.

**Print** — A built-in Python function that displays output below a cell. `print("hello")` causes the word `hello` to appear in the notebook output area.

**SDK (Software Development Kit)** — A pre-built package of tools that makes it easier to work with a specific service or platform. The `anthropic` SDK handles the complex communication details so you can focus on writing your prompts.

**Smoke test** — A minimal check to confirm that a system is fundamentally working before attempting anything more complex. Named after the practice of checking if electronics emit smoke when first powered on.

**String** — A piece of text in Python, always wrapped in quotation marks: `"like this"`. The quotation marks tell Python to treat the contents as literal text rather than as code.

**Token** — The unit of text that AI language models process internally. Roughly one word or part of a word. Used to measure and limit the length of inputs and outputs.

**Variable** — A named container that holds a value. Like a labeled box: you put something in it once (`api_key = ...`) and refer to it by name later. The value inside can change over the course of a program.

**`True` / `False`** — Python's two **boolean** values, representing yes/no or on/off. The result of logical expressions like `api_key is not None`.