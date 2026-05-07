# Python Guide: Getting an API Key — Building with the Claude API

A plain-English walkthrough of every line of code in this JupyterLab lab.
No coding background required. Every concept is explained from scratch.

---

## What Python is (for complete beginners)

Python is a language for giving instructions to a computer. You write those instructions as plain-looking text — no buttons, no menus — and the computer carries them out one line at a time, top to bottom. Think of it like writing a very precise recipe: the computer will follow each step exactly as written, in order, without skipping anything or making assumptions.

A **JupyterLab notebook** organises those instructions into chunks called **cells**. You run one cell at a time, and the result appears immediately below it. This makes it easy to build up a program in pieces and see what's happening at each stage — which is exactly what this lab does.

---

## Cell 1: Load Your Secret API Key from a File

```python
# ── Starter Cell 1: Load environment variables ────────────────────────────────
from dotenv import load_dotenv   # imports load_dotenv to read .env file from disk
import os                         # imports os module to access environment variables

load_dotenv()                     # reads the .env file and injects variables into os.environ
api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the key by name; returns None if missing

print("API key loaded:", api_key is not None)  # True = key found; False = check your .env file
```

### What this code does

---

**Line 1: `# ── Starter Cell 1: Load environment variables ──...`**

This line is a **comment** — a note written for human readers only. In Python, any line that starts with a `#` symbol is completely ignored by the computer. It will never be executed. Comments exist purely to explain what the surrounding code is doing. Think of them like margin notes in a textbook. This one tells us that the purpose of this cell is to load "environment variables" — a term we'll define shortly.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack this carefully, because this kind of line appears constantly in Python.

**What is a module/library?**
Python comes with a set of built-in abilities, but thousands of extra tools have been created by other developers and made freely available. These tools are packaged into **libraries** (also called **modules** or **packages**). Think of it like a toolbox: Python ships with some basic tools, but you can borrow specialised tools from a shared community workshop whenever you need them.

**What is `dotenv`?**
`dotenv` is the name of a library specifically designed to read a special file called `.env` (pronounced "dot-env") from your project folder. A `.env` file is where you store secret information — like passwords or API keys — so they don't end up written directly in your code for everyone to see.

**What does `from ... import ...` mean?**
The `from dotenv import load_dotenv` pattern is how you bring a specific tool from a library into your current workspace. `from dotenv` says "go to the dotenv library," and `import load_dotenv` says "bring in specifically the tool called `load_dotenv`." After this line runs, you can use `load_dotenv` anywhere in this cell.

**What is `load_dotenv`?**
It's a **function** — a named set of instructions that performs a specific task. Think of a function like a named recipe: once you have the recipe card in hand, you can "call" it (use it) whenever you need it. `load_dotenv` is a recipe for opening your `.env` file and reading the secrets inside it.

---

**Line 3: `import os`**

**What is `os`?**
`os` is a library that ships with Python itself — you don't need to install it separately. Its name stands for "operating system," and it provides tools for talking to the operating system your computer runs (Windows, macOS, or Linux). Among other things, it can read **environment variables**.

**What are environment variables?**
Environment variables are like a bulletin board your operating system maintains, where programs can leave and read named pieces of information. When `load_dotenv` reads your `.env` file (next line), it posts the secrets from that file onto this bulletin board. Then `os` gives you a way to read those posted values.

**Why use `import os` instead of `from os import ...`?**
When you write `import os`, you're bringing in the entire `os` toolbox at once. You'll access individual tools from it using the format `os.toolname` (as you'll see on line 5). Both patterns (`import X` and `from X import Y`) are common — they're just two ways of fetching tools.

---

**Line 5: `load_dotenv()`**

Now we're actually *using* the `load_dotenv` function we brought in on line 2. Notice the **parentheses** `()` at the end — in Python, parentheses are how you *call* (activate/run) a function. Without the parentheses, you'd just be referring to the function without running it, like holding a recipe card but never cooking.

What this line actually does: it looks for a file named `.env` in your current project folder, opens it, reads the `NAME=VALUE` pairs inside it, and posts each one onto the operating system's environment variable bulletin board. After this line runs, your secret API key is available to be read — but it's still safely tucked away in a file, not written in your code.

**Why does this matter?**
If you wrote your API key directly in your code — like `api_key = "sk-ant-abc123..."` — anyone who could see your code (a colleague, a version control system like Git, anyone you share your file with) could also see your secret key. The `.env` approach keeps the secret in a separate file that you never share or publish.

---

**Line 6: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line has two things happening — let's take them in order.

**What is `os.getenv(...)`?**
This is a function from the `os` library (note the `os.` prefix, which means "use the `getenv` tool from the `os` toolbox"). `getenv` stands for "get environment variable." It reads one specific value from the operating system's bulletin board — whichever one you name inside the parentheses.

**What is `"ANTHROPIC_API_KEY"`?**
This is the *name* of the environment variable we're looking for — the label on the bulletin board entry. When you set up your `.env` file, you'd have a line that reads:
```
ANTHROPIC_API_KEY=sk-ant-your-actual-key-here
```
The `load_dotenv()` call on the previous line posted that to the bulletin board. Now `os.getenv("ANTHROPIC_API_KEY")` reads it back.

If the variable doesn't exist on the bulletin board (perhaps because the `.env` file is missing or the variable is misspelled), `getenv` returns a special value called `None` — Python's way of saying "there's nothing here."

**What is `api_key = ...`?**
The `=` sign here is not asking a question ("are these equal?"). It's an *assignment* — it creates a **variable** named `api_key` and stores the result of `os.getenv(...)` inside it. Think of a variable as a labeled box: you're creating a box labeled `api_key` and placing your retrieved API key string inside it. From this point on in the program, whenever you write `api_key`, Python will look up what's in that box and use it.

---

**Line 8: `print("API key loaded:", api_key is not None)`**

**What is `print(...)`?**
`print` is one of Python's most fundamental built-in functions. It displays information in the output area below the cell — it's how your program talks back to you. Whatever you put inside the parentheses gets displayed on screen.

**What is `"API key loaded:"`?**
This is a **string** — a piece of text enclosed in quotation marks. Strings are how Python represents human-readable text. This particular string is just a label that will appear at the start of the output.

**What is `api_key is not None`?**
This is a **boolean expression** — a question that evaluates to either `True` or `False`.
- `None` is Python's way of representing "nothing" or "no value."
- `is not None` asks: "does this variable contain an actual value, rather than nothing?"
- If your API key was successfully loaded, `api_key` holds a string like `"sk-ant-..."`, so `api_key is not None` evaluates to `True`.
- If the key was missing, `api_key` holds `None`, so the expression evaluates to `False`.

So the full print statement outputs something like:
```
API key loaded: True
```
or
```
API key loaded: False
```

### What the output means and why it matters

If you see `API key loaded: True`, everything worked: the `.env` file was found, the key was read, and it's stored and ready to use.

If you see `API key loaded: False`, something went wrong — either the `.env` file doesn't exist, it's in the wrong folder, or the variable is named differently inside it. This is a deliberately helpful checkpoint: it catches problems at the very start before you try to make real API calls and get confusing error messages later.

---

## Cell 2: Create Your Connection to the Claude API

```python
# ── Starter Cell 2: Initialise the Anthropic client ───────────────────────────
from anthropic import Anthropic   # imports the top-level SDK class

client = Anthropic(api_key=api_key)  # creates a reusable client bound to our key
print("Anthropic client ready")       # confirms the object was created without errors
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise the Anthropic client ──...`**

Another comment, ignored by Python, telling us this cell's purpose: to set up (initialise) the Anthropic client. "Initialise" just means "create and prepare for first use."

---

**Line 2: `from anthropic import Anthropic`**

**What is `anthropic`?**
`anthropic` is the official Python library (also called an **SDK** — Software Development Kit) provided by Anthropic, the company that makes Claude. It gives you pre-built tools for communicating with Claude's API without having to write all the low-level networking code yourself.

**What is `Anthropic` (capital A)?**
Within that library, `Anthropic` (capital A) is a **class** — a blueprint for creating a particular type of object. In our case, it's the blueprint for a "client" — a program-side representative that knows how to talk to the Anthropic API on your behalf. Think of it like calling a phone company to set up a business account: the class is the phone company's process, and the object you create from it is your active account connection.

The pattern `from anthropic import Anthropic` says: "from the anthropic library, bring in the `Anthropic` class so I can use it."

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line *uses* the `Anthropic` class blueprint to create an actual working object and stores it in a variable named `client`.

**What happens when you call `Anthropic(...)`?**
Calling a class with parentheses (like calling a function) creates an **instance** of that class — a real, usable object built from the blueprint. Think of the class as a cookie cutter and the instance as the actual cookie. You can make many cookies from one cutter; here we're making one client.

**What is `api_key=api_key`?**
This is how you pass information *into* the object as it's being created. `api_key=` is the **parameter name** — the label the `Anthropic` class uses to identify this piece of information. `api_key` (after the `=`) is our variable from Cell 1 — the box holding our actual API key string. So we're telling the client: "here is my API key — use it to authenticate all requests you make on my behalf."

The `client` variable now holds a fully configured connection object that knows which API key to present every time you ask it to contact Claude.

**Why store it in `client`?**
By saving the connection object in a variable named `client`, you can reuse it across many API calls without having to set up the connection from scratch each time. `client` will appear in every subsequent API call in this notebook.

---

**Line 5: `print("Anthropic client ready")`**

A simple confirmation message. If this line prints without an error, it means the `Anthropic(...)` call on the previous line succeeded — the client was created without any problem (for example, it didn't crash because of an invalid API key format). It's a small but useful signal that you're ready to move forward.

### What the output means and why it matters

You should see:
```
Anthropic client ready
```
This confirms your `client` object exists and is ready to send messages to Claude. If this cell throws an error instead, the most common cause is that the `anthropic` library hasn't been installed (the fix is to run `pip install anthropic` in your terminal).

---

## Cell 3: Send Your First Test Message to Claude

```python
# ── Starter Cell 3: Smoke test — verifies end-to-end connectivity ─────────────
response = client.messages.create(         # calls the Messages API
    model="claude-haiku-4-5",              # fast, low-cost model — good for smoke tests
    # model="claude-sonnet-4-5",           # uncomment for a more capable model
    max_tokens=50,                          # caps the reply length; required by the API
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]
    # messages is a list of turn dicts; at minimum one user turn is required
)
print(response.content[0].text)            # content is a list of blocks; [0] is the first block
```

### What this code does

---

**Line 1: `# ── Starter Cell 3: Smoke test — verifies end-to-end connectivity ──`**

Another explanatory comment. A **smoke test** is a term borrowed from engineering — when you first wire up a new electronic device, you turn it on briefly to see if smoke appears (indicating something is badly wrong). In software, a smoke test is a quick, minimal check that everything is connected and working before you do anything more serious.

---

**Lines 2–7: The `client.messages.create(...)` call (read as one unit)**

This entire block — from `response = client.messages.create(` all the way to the closing `)` — is a single instruction split across multiple lines for readability. Python allows you to spread a long instruction across multiple lines when it's inside parentheses. Let's understand the structure first, then go line by line.

**What is `client.messages.create(...)`?**
This is how you send a message to Claude and ask for a reply.
- `client` is the connection object you created in Cell 2
- `.messages` is a sub-section of that client dedicated to the Messages API
- `.create(...)` is the function that actually sends your request

Think of it like using a phone (the `client`) to call a specific department (`.messages`) and placing an order (`.create(...)`).

**What is an API?**
An **API** (Application Programming Interface) is a defined way for one piece of software to talk to another. Claude lives on Anthropic's servers, not on your computer. The API is the formal "door" you knock on to reach Claude. Think of a waiter at a restaurant: you (your code) tell the waiter (the API) what you want, the waiter takes your request to the kitchen (Claude's servers), and brings back what was prepared (the response).

**What is `response = ...`?**
The `=` stores the result of the entire `client.messages.create(...)` call into a variable named `response`. When Claude answers, the API sends back a structured package of information — not just the text, but also metadata like how many tokens were used. All of that gets stored in `response` so you can examine it.

---

**Line 3: `model="claude-haiku-4-5",`**

**What is `model`?**
This is a **parameter** — a named piece of information you're passing to the `create` function to configure how it works. `model` tells the API *which* Claude model (version) you want to use.

**What is `"claude-haiku-4-5"`?**
This is a string identifying a specific Claude model. Anthropic offers different models with different trade-offs:
- **Haiku** models are fast and inexpensive — good for quick tests and high-volume tasks where speed matters more than maximum capability
- **Sonnet** models are more capable and more thoughtful, at higher cost

For a smoke test — just checking that the connection works — Haiku is the right choice.

**Why the comma at the end?**
When you're passing multiple parameters to a function, each one is separated by a comma. This comma tells Python "there are more parameters coming."

---

**Line 4: `# model="claude-sonnet-4-5",`**

This is a **commented-out line** — the `#` at the start means Python ignores it entirely. It's left here as a convenience: if you want to switch to the more capable Sonnet model, you remove the `#` from this line and add a `#` to the line above. This is a common pattern called "toggling" between options by commenting/uncommenting.

---

**Line 5: `max_tokens=50,`**

**What are tokens?**
AI models like Claude don't read text the way humans do — they break text into chunks called **tokens**. A token is roughly 3–4 characters, or about 75% of an average English word. So 50 tokens is roughly 35–40 words. The word "tokenization" might be split into two tokens; the word "cat" might be one.

**What does `max_tokens` do?**
This parameter sets a hard limit on how long Claude's reply can be. The API *requires* you to set this — it won't accept a request without it. This protects both you (from unexpectedly long, expensive responses) and the API (from runaway requests).

For a smoke test where you just want Claude to say hello, 50 tokens is more than enough.

---

**Lines 6–7: `messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the most important parameter — it's the actual conversation you're sending to Claude. Let's break it apart carefully.

**What is `messages=`?**
This parameter is where you provide the conversation history — every turn of the dialogue, in order.

**What is `[...]`?**
The square brackets create a **list** — an ordered collection of items, like a numbered list on paper. Here, the list contains our conversation turns.

**What is `{"role": "user", "content": "..."}`?**
The curly braces `{...}` create a **dictionary** — a lookup table made of paired labels and values, like a mini-form with fields filled in. Think of it like a form at a doctor's office: `name: Jane`, `age: 34`. In this case:
- `"role": "user"` — the "role" field says who is speaking. In the Claude API, roles can be `"user"` (that's you, the human) or `"assistant"` (that's Claude). This tells Claude which side of the conversation each message comes from.
- `"content": "Say hello in one short sentence."` — the "content" field contains the actual text of that turn. This is your question or instruction to Claude.

So the full `messages` parameter is: *a list containing one item, which is a dictionary describing one turn of conversation — the user asking Claude to say hello in one short sentence.*

This is the minimum required structure for an API call: at least one message, with a role and content specified.

---

**Line 9: `print(response.content[0].text)`**

When the API call completes, Claude's reply is stored in the `response` variable. But `response` is a complex, structured object — not just a single string. This line digs into it to extract just the text of the reply.

**What is `response.content`?**
The `.content` part accesses a specific section of the response object — the list of content blocks that Claude returned. In most cases this list has just one item: the text of Claude's reply.

**What is `[0]`?**
The `[0]` after `response.content` selects the *first* item from that list. In Python (and most programming languages), counting starts at zero, not one. So `[0]` means "the first item," `[1]` would mean "the second item," and so on. Since we're doing a simple one-question call, there's only one content block, and it's at position `[0]`.

**What is `.text`?**
Within that first content block, `.text` accesses the actual text string — the words Claude wrote. So the full chain `response.content[0].text` means: "from the response, get the content list, take the first block, and give me its text."

**Why is it structured this way?**
The API returns a list of content blocks rather than a single string because Claude can theoretically return multiple kinds of content (text, tool calls, etc.) in a single response. The list structure accommodates that flexibility.

### What the output means and why it matters

You should see something like:
```
Hello! It's wonderful to meet you today.
```
(The exact wording will vary — Claude doesn't give identical responses every time.)

If you see this, you've just confirmed the entire chain works:
1. Your API key is valid ✓
2. The Anthropic client connected successfully ✓
3. Your request reached Claude's servers ✓
4. Claude processed your message and replied ✓
5. Your code correctly extracted the text from the response ✓

If you see an error instead, the most common causes are: an invalid API key, no internet connection, or the `anthropic` library not being installed.

---

## Glossary

**API (Application Programming Interface):** A formal way for one piece of software to request services from another. Like a waiter taking your order to the kitchen and returning with food.

**Boolean:** A value that can only be `True` or `False`. Named after mathematician George Boole. Used for yes/no questions in code.

**Call (a function):** To actually run/execute a function by writing its name followed by parentheses. "Calling" `load_dotenv()` means "run these instructions now."

**Cell:** A chunk of code (or text) in a JupyterLab notebook. Each cell can be run independently, and its output appears below it.

**Class:** A blueprint for creating objects. Like a cookie cutter that defines the shape; the objects created from it are the actual cookies.

**Client:** In this context, a Python object that manages your connection to a remote service (the Claude API). It knows your credentials and how to format requests.

**Comment:** A line in code that starts with `#` and is completely ignored by Python. Written for human readers to explain what the surrounding code does.

**Dictionary:** A Python data structure that stores paired labels (keys) and values, like a lookup table or a filled-in form. Written with curly braces `{}`.

**dotenv / python-dotenv:** A library for reading `.env` files and making their contents available as environment variables.

**Environment variable:** A named piece of information stored on the operating system's "bulletin board," accessible by any program running on that computer.

**Function:** A named, reusable set of instructions that performs a specific task. You "call" it by writing its name followed by parentheses.

**Import:** Bringing a tool or library into your current workspace so you can use it. Like fetching a tool from a toolbox.

**Instance:** A specific object created from a class blueprint. If `Anthropic` is the cookie cutter, `client = Anthropic(...)` creates one cookie (instance).

**JupyterLab:** An interactive coding environment that lets you write and run Python code in cells, seeing results immediately below each cell.

**Library / Module / Package:** A collection of pre-written Python tools that you can import and use. Saves you from writing common functionality from scratch.

**List:** An ordered collection of items in Python, written with square brackets `[]`. Like a numbered list on paper. Counting starts at zero.

**max_tokens:** A parameter that limits how long Claude's response can be, measured in tokens.

**Model:** A specific version of an AI system with particular characteristics (speed, capability, cost). `claude-haiku-4-5` and `claude-sonnet-4-5` are two different Claude models.

**None:** Python's way of representing "no value" or "nothing." Returned by functions that find