# Python Guide: Prompt Engineering & Evaluation Fundamentals

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (one paragraph for complete beginners)

Python is a programming language — a way of writing instructions that a computer can follow. Think of it like a very precise recipe. A chef follows a recipe step by step; a computer follows Python code step by step. Each line of code is one instruction. Python is popular because its instructions read almost like plain English, which makes it a great starting point for learning to communicate with computers. In this lab, you are writing instructions that will talk to Claude, Anthropic's AI, through something called an API — think of the API as a waiter who takes your request to Claude's "kitchen" and brings back the response.

---

## Cell 1: Loading Your Secret API Key

```python
from dotenv import load_dotenv
import os
load_dotenv()                              # reads .env file in current directory
api_key = os.getenv("ANTHROPIC_API_KEY")  # pulls key into a variable
print("API key loaded:", api_key is not None)
```

### What this code does

**Line 1: `from dotenv import load_dotenv`**

This line brings a specific tool into your workspace. Let's unpack the terms:

- **Module / library**: Think of Python as a toolbox. The toolbox comes with some basic tools built in, but you can also snap in additional tool trays that other people have built. Each of these tool trays is called a *module* or *library*.
- **`dotenv`**: This is a third-party library (a tool tray someone else built) whose one job is to read a special file called `.env` (pronounced "dot env") and make the secret information stored in it available to your program.
- **`import`**: This word means "bring this tool into my current workspace so I can use it." Before you import something, Python doesn't know it exists.
- **`from ... import ...`**: This is a more targeted version of `import`. Instead of bringing in the entire `dotenv` tool tray, you're reaching in and pulling out just one specific tool called `load_dotenv`. It's like opening a toolbox and taking out only the screwdriver, rather than carrying the whole box to your workbench.
- **`load_dotenv`**: This is a *function* — a named recipe you can call by name to execute. This particular function, when called, will read a file called `.env` on your computer and load whatever is inside it.

Why do we need this? Your Anthropic API key is a secret password. You don't want to type it directly into your code, because if you ever shared your code with someone else, they'd see your key. Instead, you store it in a hidden `.env` file, and this tool reads it for you privately.

---

**Line 2: `import os`**

- **`os`**: This is a built-in Python module (one of the tools that comes with Python itself). "os" stands for "operating system." It gives your Python program the ability to talk to the computer's operating system — the underlying system (like Windows or macOS) that manages files, folders, and system settings.
- **`import os`**: You're bringing the entire `os` tool tray into your workspace. You'll use one specific tool from it in a moment: the ability to read *environment variables*.
- **Environment variable**: Think of an environment variable as a sticky note that your operating system is holding. It has a name on one side and a value on the other. Programs can ask the operating system "hey, do you have a sticky note called X?" and the operating system will hand back whatever is written on it. This is a common, safe way to pass secret information to programs without writing those secrets directly in the code.

---

**Line 3: `load_dotenv()`**

- The parentheses `()` after a function name mean "run this function now." When you just write the name without parentheses, Python recognizes the function but doesn't execute it. Adding `()` is like pressing the "go" button.
- This line calls the `load_dotenv` function you imported on line 1. It reads your `.env` file (which lives in the same folder as this notebook) and takes any secret keys stored there and registers them as environment variables — those sticky notes held by the operating system.
- The comment after the `#` symbol is just a note for humans reading the code. Python completely ignores everything after a `#` on the same line. Comments are like margin notes in a textbook.

---

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

Several new concepts here — let's go one at a time.

- **Variable**: A variable is a labeled box that holds a value. When you write `api_key = ...`, you're creating a box, labeling it `api_key`, and putting something inside it. Later in the code, whenever you write `api_key`, Python opens that box and uses whatever is inside.
- **`=` (the assignment operator)**: In Python, a single `=` doesn't mean "equals" in the math sense. It means "take the value on the right and store it in the box on the left." Think of it as an arrow pointing left: take this → put it here.
- **`os.getenv(...)`**: The dot `.` here is like saying "reach into the `os` tool tray and use the tool called `getenv`." `getenv` stands for "get environment variable." It asks the operating system: "Do you have a sticky note called the name I'm giving you?" and returns whatever value is written on that note.
- **`"ANTHROPIC_API_KEY"`**: The text in quotes is a *string* — a piece of text data. The quotes tell Python "this is text, not a command." This particular string is the name of the sticky note you're asking the operating system to look up. It matches the name written in your `.env` file.
- Putting it together: this line asks the operating system for the secret API key, and stores whatever it finds in a box labeled `api_key`.

---

**Line 5: `print("API key loaded:", api_key is not None)`**

- **`print(...)`**: This is one of Python's most basic built-in functions. It displays output on the screen below the code cell, so you can see what's happening inside your program.
- **`"API key loaded:"`**: Another string (text in quotes) that will appear exactly as written in the output.
- **`api_key is not None`**: This is a question Python is asking itself: "Is the `api_key` box holding something real, or is it empty?"
  - **`None`**: In Python, `None` is a special value that means "nothing" or "empty." If `os.getenv` couldn't find the sticky note you asked for, it returns `None`.
  - **`is not None`**: This checks whether `api_key` holds an actual value (not nothing). Python will evaluate this and return either `True` or `False`.
  - **`True` / `False`**: These are called *Boolean* values — they represent a yes/no, on/off state.
- The comma inside `print()` lets you print multiple things side by side, separated by a space.

### What the output means

```
API key loaded: True
```

The word `True` tells you that `api_key` is not empty — your `.env` file was found, the key inside it was read successfully, and it's now stored in the `api_key` variable. If you saw `False`, it would mean the key wasn't found, and later steps would fail. This line is a safety check: a quick confirmation that the plumbing works before you try to turn on the tap.

---

## Cell 2: Creating the Anthropic Client

```python
from anthropic import Anthropic
client = Anthropic(api_key=api_key)  # instantiate SDK client; reused throughout
print("Anthropic client ready")
```

### What this code does

**Line 1: `from anthropic import Anthropic`**

- **`anthropic`**: This is another third-party library — a package of tools built by Anthropic specifically to make it easy to send messages to Claude's API from Python.
- **`Anthropic`** (with a capital A): Inside the `anthropic` library, there is a specific tool called `Anthropic`. This is a *class* — think of a class as a blueprint or a template for creating an object.
- **Object**: An object is like a smart device. It holds data (settings) and knows how to do things (has built-in functions). When you create an object from a class, you get a fully configured, ready-to-use tool.
- This line reaches into the `anthropic` library and pulls out just the `Anthropic` blueprint, ready to be used.

---

**Line 2: `client = Anthropic(api_key=api_key)`**

- **`Anthropic(...)`**: Writing a class name followed by parentheses creates a new object from that blueprint — like using a cookie cutter to stamp out an actual cookie. This is called *instantiating* (hence the comment "instantiate SDK client").
- **`api_key=api_key`**: This is a *keyword argument* — a named piece of information you're passing to the object as it's being created. The part on the left (`api_key=`) is the name of the setting. The part on the right (`api_key`) is the variable (the labeled box from Cell 1) whose value is being passed in. You're telling the Anthropic object: "here is my secret key; use it to authenticate my requests."
- **`client`**: This is the variable (labeled box) that holds the newly created Anthropic object. The name `client` is chosen because this object acts as your personal representative — your "client" — when communicating with Anthropic's servers.
- **SDK**: The comment mentions "SDK client." SDK stands for *Software Development Kit* — a pre-built set of tools that makes it easier to work with a service. Anthropic's SDK handles the complicated behind-the-scenes work of formatting requests, handling errors, and communicating over the internet, so you don't have to do it manually.
- From this point forward, whenever the notebook wants to talk to Claude, it will use this `client` object. That's why the comment says "reused throughout."

---

**Line 3: `print("Anthropic client ready")`**

- Prints the text `Anthropic client ready` to the screen.
- This is another confirmation message, purely for your benefit. It tells you that the client object was created without errors and is ready to be used.

### What the output means

```
Anthropic client ready
```

This simple confirmation tells you that the `client` object now exists, is properly authenticated with your API key, and is ready to send messages to Claude. Think of it as your phone successfully connecting to the network — you're now online and ready to make calls.

---

## Cell 3: The Smoke Test — Sending Your First Message to Claude

```python
response = client.messages.create(
    model="claude-haiku-4-5",
    #    model="claude-sonnet-4-5",   # swap to Sonnet for harder tasks
    max_tokens=50,
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]
)
print(response.content[0].text)
```

### What this code does

This cell sends a real message to Claude and prints the reply. It's called a "smoke test" — a quick, minimal check to confirm everything is working before you build anything more complex. (The term comes from electronics: if you turn on a new circuit and it doesn't start smoking, it probably works.)

---

**Lines 1–6: `response = client.messages.create(...)`**

This is one instruction that spans multiple lines. Python allows you to break a single command across several lines when it's inside parentheses — it's purely for readability, like writing a long sentence across two lines of a form.

- **`client.messages.create(...)`**: You're reaching into the `client` object, then into a part of it called `messages`, and calling a function called `create`. This function sends a message to the Claude API and waits for a response. Think of it as: *pick up the phone* (`client`) → *go to the messaging feature* (`.messages`) → *compose and send* (`.create(...)`).
- **`response =`**: Whatever Claude sends back is stored in a variable (labeled box) called `response`. This box will hold a complex object containing Claude's reply and other information like token counts.

Now let's look at each parameter (a *parameter* is a named setting you pass into a function):

---

**`model="claude-haiku-4-5"`**

- **`model`**: This tells the API *which version of Claude* you want to talk to. Anthropic offers different models with different trade-offs between speed, cost, and capability.
- **`"claude-haiku-4-5"`**: Claude Haiku is Anthropic's fastest, most lightweight model — great for simple tasks and quick responses. It's named after the short Japanese poem form, which is fitting.
- The line below it (starting with `#`) is a commented-out alternative. The `#` turns it into a note rather than active code. If you wanted to use Claude Sonnet (a more powerful model) instead, you could remove the `#` from that line and add a `#` to the line above. This is called "swapping" models.

---

**`max_tokens=50`**

- **Token**: A token is a word-chunk that the AI reads and writes — roughly one word or part of a word. The word "fantastic" might be one token; "unbelievable" might be two. Tokens are the unit Claude uses to measure input and output length.
- **`max_tokens`**: This is a hard ceiling on how many tokens Claude is allowed to write in its response. Setting it to `50` means Claude will stop after approximately 50 word-chunks, even if it wasn't finished. This is a required parameter — you must always tell Claude the maximum length it's allowed to write.
- For a "say hello" request, 50 tokens is more than enough. This parameter protects against unexpectedly long (and expensive) responses.

---

**`messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the most information-dense part, so let's break it down carefully.

- **`messages=`**: This parameter is the actual conversation you're sending to Claude. It's always a list of turns in the conversation.
- **List `[...]`**: A list is an ordered collection of items, like a numbered list on paper. The square brackets `[` and `]` mark the beginning and end of a list. This list contains one item.
- **Dictionary `{...}`**: A dictionary is a lookup table — like a two-column spreadsheet where the left column has labels (called *keys*) and the right column has values. The curly braces `{` and `}` mark a dictionary. Each entry is written as `"key": value`.
- **`{"role": "user", "content": "Say hello in one short sentence."}`**: This dictionary has two entries:
  - `"role": "user"` — This says the speaker for this turn is the *user* (you). Claude's API tracks conversations as a series of turns, alternating between `"user"` and `"assistant"` (Claude's role). This structure allows multi-turn conversations.
  - `"content": "Say hello in one short sentence."` — This is the actual text of the message being sent. It's the prompt — the instruction or question you're giving Claude.
- Putting it all together: `messages` is a list containing one conversation turn, where the user says "Say hello in one short sentence."

---

**Line 7: `print(response.content[0].text)`**

- **`response`**: The box holding everything Claude sent back.
- **`.content`**: The `response` object has several parts. `.content` is the part that holds Claude's actual reply. Using a dot to reach into an object is like opening a filing cabinet and going to a specific drawer.
- **`[0]`**: `.content` is a list (multiple items are possible in some cases). The `[0]` means "get the first item." In Python, counting always starts at zero, not one. So `[0]` means "item number one" (the first one), `[1]` would mean the second, and so on.
- **`.text`**: The first content item is itself an object. Its `.text` part holds the actual string of text Claude wrote.
- Putting it together: "Reach into the response, find the content list, take the first item, and get its text — then print it on screen."

### What the output means

```
Hello!
```

Claude responded to the prompt "Say hello in one short sentence." with `Hello!` This confirms that:
1. Your API key is valid and accepted
2. The `client` object is successfully connecting to Anthropic's servers
3. Messages are being sent and received correctly
4. You can extract and display Claude's reply

Everything is working. The foundation is solid. The rest of the lab builds on this pattern.

---

## Cell 4: The `ask()` Helper Function

```python
import re  # standard-library regex; used later in the rubric

# ── usage log ────────────────────────────────────────────────────────────
usage_log = []  # global list; one dict appended after every API call

# ── helper ───────────────────────────────────────────────────────────────
def ask(prompt, system=None, max_tokens=512, temperature=1.0):
```

> **Note:** The notebook input provided was cut off mid-function. The explanation below covers every visible line thoroughly and explains the patterns involved so you can follow any continuation of the function.

### What this code does

This cell sets up two things: a *usage tracker* (to keep count of API costs) and a *helper function* called `ask()` that makes sending messages to Claude much more convenient throughout the rest of the lab.

---

**Line 1: `import re`**

- **`re`**: This is Python's built-in *regular expressions* module. A regular expression (regex for short) is a powerful mini-language for finding patterns in text. For example, you can use regex to search for "any number in this sentence" or "any word that ends in -ing."
- This tool is being imported now so it's ready for later in the lab when the rubric (scoring system) needs to find specific patterns in Claude's responses.
- The comment tells you it will be used in the rubric section. It doesn't do anything yet — importing a tool just puts it on your workbench; it doesn't use it.

---

**Line 3 (comment line): `# ── usage log ──...`**

- This line is entirely a comment (everything after `#` is ignored by Python). It's a visual divider that the author added to make the code easier to read — like a section header in a document. The `──` dashes are just decoration.

---

**Line 4: `usage_log = []`**

- **`usage_log`**: A variable (labeled box) being created with the name `usage_log`.
- **`[]`**: Empty square brackets create an empty list — an ordered collection with nothing in it yet, like a blank notepad waiting for entries.
- **Why a list?**: Every time the notebook makes an API call to Claude, the code will *append* (add) a record to this list. By the end of the lab, `usage_log` will contain one entry for every API call made, recording how many tokens were used each time.
- **`global list`**: The comment says this is a "global list." *Global* means it's created here, outside of any function, and will be accessible from anywhere in the notebook — any function or cell can add to it or read from it.
- Think of `usage_log` as a running tab at a restaurant — every time you order something (make an API call), the waiter adds it to the tab.

---

**Line 6 (comment line): `# ── helper ──...`**

Another visual divider comment to mark where the helper function definition begins.

---

**Line 7: `def ask(prompt, system=None, max_tokens=512, temperature=1.0):`**

This is the beginning of a *function definition*. Let's break it down word by word.

- **`def`**: Short for "define." This keyword tells Python: "I'm about to describe a reusable recipe. Give it a name and remember it for later." The code inside the function doesn't run yet — it's just being defined and saved.
- **`ask`**: The name given to this function. You chose the name `ask` because the function's job is to "ask" Claude something.
- **`(prompt, system=None, max_tokens=512, temperature=1.0)`**: These are the *parameters* — the inputs the function accepts when it's called. Think of them as blanks on a form that you fill in differently each time.
  - **`prompt`**: A required parameter. Every time you call `ask()`, you must provide a prompt — the message or question you want to send Claude.
  - **`system=None`**: An *optional* parameter with a *default value* of `None` (nothing). If you call `ask()` without specifying a `system` value, it will default to `None` (no system prompt). A system prompt is a set of persistent instructions that shapes how Claude behaves for the whole conversation — like briefing an assistant before they start a task.
  - **`max_tokens=512`**: Another optional parameter with a default of `512`. If you don't specify a maximum token limit, the function will automatically use 512. This is more generous than the `50` in the smoke test, appropriate for longer responses.
  - **`temperature=1.0`**: Another optional parameter with a default of `1.0`. *Temperature* controls how creative or deterministic Claude's responses are. A temperature of `0` makes responses very consistent and predictable (the same prompt gives nearly the same answer every time). A temperature of `1` allows more variation and creativity. Think of it like a dial from "strictly by the book" to "improvise freely."
- **The colon `:`**: In Python, a colon at the end of certain lines means "what follows is the body of this block." Everything *indented* below this line is part of the `ask()` function.
- **Indentation**: Python uses spaces at the beginning of lines to show that those lines belong inside something (like a function, loop, or condition). This is Python's way of grouping code — unlike some other languages that use curly braces.

### What the output means

```
ask() helper defined ✅
```

The `✅` (green checkmark emoji) and the message confirm that the function was defined successfully without errors. Note that the function hasn't *run* yet — it's been defined and saved into memory, ready to be called later. This output was produced by a `print("ask() helper defined ✅")` statement that appears later in the full cell (after the function definition), confirming the whole cell executed cleanly.

---

## Glossary

**API (Application Programming Interface)**
A defined way for two pieces of software to talk to each other. Think of it as a waiter who takes your order (request) to the kitchen (server) and brings back what you asked for (response). Anthropic's API lets your Python code send messages to Claude and receive replies.

**Argument**
A value you pass into a function when you call it. In `print("hello")`, the string `"hello"` is the argument.

**Boolean**
A value that is either `True` or `False` — a yes/no, on/off value. Named after mathematician George Boole.

**Class**
A blueprint or template for creating objects. Like a cookie cutter — the class defines the shape, and each object created from it is a distinct cookie.

**Comment**
A note in code for human readers, marked with `#`. Python ignores everything after `#` on a line. Comments explain why code works the way it does.

**Default value**
A value that a function parameter uses automatically if you don't specify one. In `def ask(..., max_tokens=512)`, the default for `max_tokens` is `512`.

**Dictionary**
A lookup table structure in Python, written with curly braces `{}`. Each entry has a key (label) and a value, separated by a colon. Like a phone book where you look up a name to get a number.

**dotenv / `.env` file**
A simple text file used to store secret configuration values (like API keys) separately from your code. The `dotenv` library reads this file and makes its contents available to your program.

**Environment variable**
A named value stored by the operating system, accessible to programs running on that computer. Like a sticky note the OS holds for you. Used to pass secret information to programs without writing it in the code.

**Function**
A named, reusable recipe of code. You define it once with `def`, then call it by name (with parentheses) whenever you want to run it.

**`import`**
The Python keyword that brings a module or library into your current workspace, making its tools available to use.

**Indentation**
Spaces at the beginning of a line in Python. Python uses indentation to determine which lines belong inside a block of code (like a function or loop).

**Instance / instantiate**
Creating a specific object