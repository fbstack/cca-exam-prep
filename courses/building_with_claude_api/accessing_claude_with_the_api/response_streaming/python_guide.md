# Python Guide: Response Streaming with the Claude API

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving a computer a precise set of written instructions. Think of it like writing a very detailed recipe — the computer follows each step exactly, in order, without guessing what you meant. A **JupyterLab notebook** organizes those instructions into separate chunks called **cells**, which you can run one at a time. This is helpful when learning, because you can see the result of each step before moving on to the next.

---

## Cell 1: Load Environment Variables

This cell's job is to find your secret API key — stored safely in a separate file — and make it available to the rest of the notebook without ever printing it on screen for someone to see.

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

**Line 1: `# ── Starter Cell 1: Load environment variables ──────────────────────────`**

This line begins with a `#` symbol, which makes it a **comment**. Comments are notes written for humans — the computer ignores them completely when running the code. Think of them like sticky notes attached to the recipe explaining what the next section is for. This comment tells us the cell's purpose: loading environment variables.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack three concepts here.

A **module** (also called a **library** or **package**) is a collection of pre-written Python tools someone else built and made available for you to use. Think of it like a hardware store — you don't build your own hammer, you go to the store and borrow one.

**`dotenv`** is the name of one such toolbox. Its specialty is reading a special file called `.env` (pronounced "dot env") — a plain text file that lives on your computer and stores sensitive information like passwords and secret keys. Keeping secrets in a `.env` file means they never end up embedded in your notebook where someone might accidentally see them.

**`import`** is the act of reaching into that toolbox and bringing a specific tool into your workspace. Here, the specific tool we're importing is called **`load_dotenv`** — a **function** (think: a named recipe) that knows how to read the `.env` file.

The phrase `from dotenv import load_dotenv` means: *"Go to the dotenv toolbox, and bring in specifically the `load_dotenv` tool."*

---

**Line 3: `import os`**

This brings in another toolbox called **`os`**, which stands for **operating system**. Your operating system is the software that runs your entire computer (Windows, macOS, or Linux). The `os` module gives Python the ability to talk to your operating system — asking it things like "what folder are we in?" or "is there a secret value stored for this key?"

The reason we need `os` here is that, after the `.env` file is read, its contents get stored in a place called the **environment** — a kind of system-level memory where your computer keeps settings and secrets. The `os` module includes a tool (`os.getenv`) that can reach into that memory and retrieve a value by name.

---

**Line 5: `load_dotenv()`**

This line actually *calls* (meaning: runs, activates, executes) the `load_dotenv` function we imported two lines ago. The empty parentheses `()` are how Python knows you want to *run* the function right now, as opposed to just referring to it.

When `load_dotenv()` runs, it looks in your current working directory (the folder where this notebook lives) for a file named `.env`. It reads that file and loads any values it finds into the operating system's environment memory — making them available for the next line to retrieve.

---

**Line 6: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

Now we retrieve the secret key that `load_dotenv()` just loaded into memory.

**`os.getenv("ANTHROPIC_API_KEY")`** calls the `getenv` function from the `os` toolbox, asking it: *"Is there a value stored under the name `ANTHROPIC_API_KEY`?"* If yes, it returns that value (the secret key string). If no, it returns a special Python value called **`None`**, which means "nothing" or "absent."

**`api_key =`** is how we store that result. In Python, a **variable** is a labeled container that holds a value — like a box with a name written on the outside. Here, we're creating a box named `api_key` and putting the retrieved key inside it. Every time we write `api_key` later in the notebook, Python looks in that box and uses whatever's stored there.

---

**Line 7: `print("API key loaded:", api_key is not None)`**

This line produces a visible message in the notebook output so you know whether the key was found.

**`print()`** is a built-in Python function that displays text in the output area below the cell. Whatever you put between the parentheses gets shown on screen.

**`"API key loaded:"`** is a **string** — a piece of text, always wrapped in quotation marks so Python knows it's text and not a command.

**`api_key is not None`** is a **comparison expression** — it asks a yes/no question: "Is `api_key` something other than nothing?" If the key was found, this expression evaluates to **`True`**. If the key was missing, it evaluates to **`False`**.

The `print()` function combines both parts with a comma, so the output will read either:
- `API key loaded: True` — meaning the key was found, and we're good to continue.
- `API key loaded: False` — meaning the key is missing, and we need to fix the `.env` file before continuing.

Crucially, we never print the *actual key value* — only whether it exists. This keeps the secret from appearing in your notebook output.

### What the output means

You'll see one line of output, something like:

```
API key loaded: True
```

This confirms that the `.env` file was found, read, and the key was retrieved successfully. If you see `False`, stop here and check that your `.env` file exists in the right folder and contains a line like `ANTHROPIC_API_KEY=your-key-here`.

---

## Cell 2: Initialize the Anthropic Client

This cell creates the main "connection point" your notebook will use to communicate with Claude's AI. Think of it like dialing a phone number and establishing a connection — you do it once, then use that same connection for every conversation.

```python
# ── Starter Cell 2: Initialise Anthropic client ─────────────────────────────
from anthropic import Anthropic   # main SDK class that wraps all API calls

client = Anthropic(api_key=api_key)  # create one shared client for the session
print("Anthropic client ready")       # quick confirmation that init succeeded
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise Anthropic client ─────────────────────────────`**

Another comment — a human-readable label telling us this cell's purpose. The computer skips it entirely.

---

**Line 2: `from anthropic import Anthropic`**

This line imports a tool from the **`anthropic`** library — a toolbox that Anthropic (the company that makes Claude) has built specifically for Python developers.

The specific tool we're importing is called **`Anthropic`** (with a capital A). In Python, tools with capital letters are typically **classes** — think of a class as a *blueprint* for creating an object. The `Anthropic` class is a blueprint for creating a **client**, which is a piece of software that knows how to talk to the Anthropic API.

An **API** (Application Programming Interface) is like a waiter in a restaurant. You (your Python code) are the customer. Claude is the kitchen. The API is the waiter who takes your message to Claude and brings back Claude's response. The Anthropic client is your way of communicating with that waiter.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line builds the actual client object using the `Anthropic` blueprint.

**`Anthropic(api_key=api_key)`** calls the `Anthropic` class like a function (the parentheses mean "run this now"). Inside the parentheses, we pass **`api_key=api_key`** — we're telling the client: "Here is the secret key you'll need to prove to Anthropic's servers that I have permission to use this API."

Notice that `api_key` appears twice here. The first `api_key` (before the `=`) is the name that the `Anthropic` class expects — it's like filling out a form with a field labeled "API Key." The second `api_key` (after the `=`) is our variable from Cell 1 — the box containing the actual key value.

**`client =`** stores the newly created client in a variable named `client`. From this point forward, whenever we want to send a message to Claude, we'll use this `client` variable.

---

**Line 5: `print("Anthropic client ready")`**

This prints a simple confirmation message. If the client was created without errors, this message appears. If something went wrong (for example, an invalid API key), Python would have thrown an **error** before reaching this line, and you'd see an error message instead. Seeing "Anthropic client ready" is your green light to proceed.

### What the output means

```
Anthropic client ready
```

This single line of output tells you the client was set up successfully with your API key. The notebook is now ready to send messages to Claude.

---

## Cell 3: Smoke Test (Verify Everything Works End-to-End)

A **smoke test** is a quick, minimal check — the name comes from electronics, where you power on a new circuit for the first time just to see if it smokes (a bad sign) or doesn't (a good sign). Here, we send the smallest possible meaningful message to Claude just to confirm the entire pipeline works before we do anything more complex.

```python
# ── Starter Cell 3: Smoke test ──────────────────────────────────────────────
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

**Line 1: `# ── Starter Cell 3: Smoke test ──────────────────────────────────────────────`**

A comment labeling the cell. No effect on execution.

---

**Lines 2–6: `response = client.messages.create(...)`**

This is the core API call — the moment we actually reach out to Claude over the internet. Let's examine the structure first, then each piece.

The entire block from line 2 through the closing `)` on line 6 is a single Python instruction that spans multiple lines. Python allows you to spread a function call across several lines when you have many arguments (inputs) to provide — this makes the code easier to read. Everything between the outer parentheses belongs to one `create()` call.

**`client.messages.create(...)`** — breaking this down:
- **`client`** is our variable from Cell 2, the object that knows how to talk to the Anthropic API.
- **`.messages`** accesses the "messages" feature of the client — the part that handles back-and-forth conversation messages.
- **`.create(...)`** is the function that actually sends a request to Claude and waits for a response. Think of it as handing a written note to the waiter (the API), who carries it to Claude (the kitchen) and brings back a reply.

**`response =`** stores whatever Claude sends back in a variable named `response`. This will be a structured object containing Claude's reply text, token usage, and other information.

---

**Line 3: `model="claude-haiku-4-5",`**

This tells the API *which version* of Claude to use. Anthropic offers several models, and each has a different balance of speed, cost, and capability — like choosing between express delivery and standard delivery.

**`"claude-haiku-4-5"`** is a faster and less expensive model, making it well-suited for testing. The name "Haiku" suggests brevity and speed.

The `model=` part is a **keyword argument** — a way of saying "here is a specific named input for this function." Think of filling out a form: you're writing `model` in the field labeled "model."

The trailing **comma** after `"claude-haiku-4-5"` indicates that more arguments are coming. Python uses commas to separate items in a list of inputs.

---

**Line 4: `# model="claude-sonnet-4-5",      # swap here for higher quality`**

This entire line is a comment — it starts with `#`, so Python ignores it completely. The developer has left this as a reminder: if you want a more powerful (but slower and more expensive) model, you can remove the `#` from this line and add `#` to the line above, effectively "swapping" which model is active. This technique of turning code on and off with `#` is called **commenting out**.

---

**Line 5: `max_tokens=50,`**

This sets a hard upper limit on how long Claude's response can be.

A **token** is the unit Claude uses to measure text. Roughly speaking, one token equals about three-quarters of a word in English — so 50 tokens is approximately 35–40 words. This tiny limit is intentional: for a smoke test, we just need to confirm Claude responds *at all*, not that it can write a long essay. A small `max_tokens` value also keeps costs low during testing.

**`max_tokens=50`** is another keyword argument saying: "Don't let Claude write more than 50 tokens."

---

**Line 6: `messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual conversation we're sending to Claude.

**`messages=`** is the keyword argument that specifies the conversation history. The Claude API works by receiving a list of conversation turns — every message has a "role" (who sent it) and "content" (what they said).

**`[...]`** — the square brackets create a **list**. Think of a list like a numbered list on paper — an ordered collection of items. Here, our list contains just one item: a single message.

**`{...}`** — the curly braces create a **dictionary**. A dictionary is a lookup table of paired labels and values — like a mini form. Each pair is written as `"label": value`. Our dictionary has two pairs:
- **`"role": "user"`** — this message was sent by the user (you).
- **`"content": "Say hello in one short sentence."`** — this is the actual text of your message to Claude.

Putting it together: we're sending Claude a conversation that contains one user message saying "Say hello in one short sentence."

---

**Line 7: `print(response.content[0].text)`**

This extracts Claude's reply from the `response` object and prints it.

**`response`** is the object that came back from the API — it contains Claude's reply along with other information.

**`.content`** accesses the "content" part of the response. Claude's API wraps its reply in a **list** of content blocks (there could theoretically be multiple blocks — for example, if Claude returned both text and an image). Even when there's only one reply, it's still wrapped in a list.

**`[0]`** retrieves the *first item* from that list. In Python, lists are numbered starting from zero, not one. So `[0]` means "give me item number zero" — which is the first (and usually only) content block.

**`.text`** accesses the actual text string within that content block — Claude's written reply.

**`print(...)`** displays that text in the output area.

### What the output means

You'll see something like:

```
Hello! I'm doing well and excited to assist you today.
```

(The exact wording will vary — Claude generates responses freshly each time.) What matters is that you see *something* — a short, coherent sentence. This confirms:
1. Your API key is valid and accepted by Anthropic's servers.
2. The client is correctly configured.
3. Messages are being sent and received successfully.
4. You're ready to move into the main lab exercises.

If instead you see an error message (Python errors appear in red or orange), that indicates a problem — most commonly an invalid API key or a network issue.

---

## Glossary

**API (Application Programming Interface):** A defined way for two pieces of software to talk to each other. Like a waiter who carries your order to the kitchen and brings back your food, an API lets your code make requests to another service (like Claude) and receive structured responses.

**Argument:** A value you pass into a function to tell it what to work with. In `os.getenv("ANTHROPIC_API_KEY")`, the string `"ANTHROPIC_API_KEY"` is an argument.

**Class:** A blueprint for creating objects. Like an architectural plan for a house — you can build many houses from one plan, each its own independent structure. `Anthropic` is a class; `client` is an object built from that blueprint.

**Client:** In programming, a client is a piece of software that connects to a server to make requests. The Anthropic client is the object in your code that communicates with Anthropic's servers.

**Comment:** A line in code that begins with `#` in Python. The computer ignores it entirely. Comments are human-readable notes that explain what the code is doing.

**Dictionary:** A Python data structure that stores paired labels (called **keys**) and values. Written with curly braces `{}`. Like a phone book where you look up a name to find a number: `{"role": "user", "content": "Hello"}`.

**Environment variable:** A value stored at the operating system level — outside your code — that programs can read. Used for secrets like API keys so they don't appear directly in your code.

**Error / Exception:** A message Python displays when something goes wrong during execution. Python stops running the code and tells you what failed and on which line.

**Function:** A named, reusable set of instructions. Like a recipe with a name — you can call it whenever you need it. `print()`, `load_dotenv()`, and `os.getenv()` are all functions.

**Import:** The act of bringing a tool from an external library into your current workspace. Like pulling a specific tool out of a toolbox so you can use it. Done with the `import` or `from ... import` keywords.

**Keyword argument:** A way of passing an input to a function by naming which parameter you're filling in: `model="claude-haiku-4-5"`. This is like filling out a labeled form field, as opposed to just guessing what order inputs go in.

**Library / Module / Package:** A collection of pre-written Python tools that extend what Python can do. `dotenv`, `os`, and `anthropic` are all libraries. The terms are often used interchangeably.

**List:** An ordered collection of items in Python, written with square brackets `[...]`. Items are numbered starting from zero. Like a numbered list on paper.

**`max_tokens`:** A parameter that caps how many tokens (word-chunks) Claude can produce in a single response. A guard against unexpectedly long (and costly) outputs.

**Method:** A function that belongs to an object. `client.messages.create()` calls the `create` method on the `messages` property of the `client` object. Methods use dot notation.

**Model:** A specific version of an AI system. Different models have different capabilities, speeds, and costs. `claude-haiku-4-5` is a fast, economical model; `claude-sonnet-4-5` is more capable.

**`None`:** A special Python value meaning "nothing" or "no value." When `os.getenv()` can't find a variable, it returns `None` instead of crashing.

**Object:** An instance created from a class blueprint. `client` is an object — a specific, usable instance built from the `Anthropic` class blueprint.

**`print()`:** A built-in Python function that displays text in the output area of a notebook or terminal. Commonly used to show results, check values, or confirm steps completed.

**Response:** The data that comes back from an API after you make a request. In this lab, the response is an object containing Claude's reply text, token counts, and metadata.

**Smoke test:** A quick, minimal check to verify that a system is basically working before doing anything complex. Named after the electronics practice of powering on a new circuit to see if anything literally smokes.

**String:** A piece of text in Python, always enclosed in quotation marks — either single `'...'` or double `"..."`. Python treats everything inside the quotes as literal text, not as a command.

**Token:** The unit AI models use to measure and process text. Roughly three-quarters of a word in English. AI models have limits on how many tokens they can read (input) or write (output) in one call.

**Variable:** A named container that stores a value in Python. Written as `name = value`. Like a labeled box — you put something in it and retrieve it later by name. `api_key`, `client`, and `response` are all variables in this notebook.

**`.env` file:** A plain text file (literally named `.env`) that stores environment variables as lines of `KEY=value` pairs. Used to keep secrets like API keys out of your code.