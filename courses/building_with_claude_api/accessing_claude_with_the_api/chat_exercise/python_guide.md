# Python Guide: Prompt Engineering & Evaluation Fundamentals

A plain-English walkthrough of every line of code in the JupyterLab notebook.
No coding background required.

---

## What Python is (for complete beginners)

Python is a way of giving precise, step-by-step instructions to a computer. Think of it like writing a very detailed recipe. Each line is one instruction. The computer reads from top to bottom and does exactly what each line says — no more, no less. If you leave out a step or misspell a word, the computer stops and tells you something went wrong. The notebook you are reading runs those instructions one "cell" (a box of code) at a time, so you can see results after each group of steps instead of waiting for everything to finish at once.

---

## Cell 1: Loading Your Secret API Key from a Safe Place

```python
from dotenv import load_dotenv
import os
load_dotenv()                              # reads .env file into environment
api_key = os.getenv("ANTHROPIC_API_KEY")   # retrieves the key string
print("API key loaded:", api_key is not None)
```

### What this code does

**Line 1: `from dotenv import load_dotenv`**

This line brings a specific tool into your working session. To understand it, think about a physical toolbox sitting in a storage room. The toolbox is called `dotenv` — it is a pre-written collection of Python tools that someone else wrote and made available for anyone to use. The specific tool you want from that toolbox is called `load_dotenv`. The word `from` tells Python which toolbox to open, and the word `import` tells Python which tool to lift out and bring to your workbench.

What is `dotenv`? It is a tool designed to help you keep secret information — like passwords or API keys — out of your code. Instead of writing your secret key directly in the notebook (where someone who reads your code could steal it), you store it in a separate, private file called `.env`. The `dotenv` tool knows how to read that private file.

What is an **API key**? It is a unique secret password that identifies you to a service — in this case, Anthropic's Claude AI. Without it, Anthropic has no way of knowing who is sending requests, and it cannot charge the right account for usage. Guard it like a password.

**Line 2: `import os`**

This brings in another toolbox called `os`. The name stands for "operating system." The operating system is the software that runs your computer — Windows, macOS, or Linux. The `os` toolbox gives Python the ability to talk to the operating system and ask it questions, like "do you have a piece of information stored under this name?"

Notice that this time you just wrote `import os` rather than `from os import something`. That means you are bringing in the entire `os` toolbox rather than just one tool from it. You will reach into it in the next line.

**Line 3: `load_dotenv()`**

This actually runs (or "calls") the `load_dotenv` tool you brought in on line 1. The parentheses `()` are how you tell Python "run this now." Without the parentheses, you are just referring to the tool; with the parentheses, you are actually using it.

What does it do when it runs? It looks for that private `.env` file on your computer, opens it, reads the secret values stored inside, and loads them into something called the **environment** — a temporary memory area that the operating system maintains while your program is running. Think of the environment as a bulletin board that any running program can post notes to and read notes from.

The comment after the `#` symbol (`# reads .env file into environment`) is a human-readable note that Python ignores completely. Comments exist purely for people reading the code, not for the computer.

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

There is a lot happening on this single line. Let's unpack it from right to left.

`os.getenv("ANTHROPIC_API_KEY")` — you are reaching into the `os` toolbox and using a tool called `getenv`, which stands for "get environment variable." An **environment variable** is one of those notes posted on the bulletin board mentioned above. You are asking: "Is there a note on the bulletin board labeled `ANTHROPIC_API_KEY`? If so, please read what it says and give it to me." That note was posted by `load_dotenv()` in the previous line when it read your `.env` file.

`api_key =` — the `=` sign here is not asking whether two things are equal (that would be `==`). Instead, it is an instruction to store whatever is on the right side into a **variable** on the left side. A variable is a labeled box that holds a value. You are creating a box, labeling it `api_key`, and putting the key string inside it. Anywhere you later write `api_key` in the code, Python will reach into that box and use whatever is stored there.

**Line 5: `print("API key loaded:", api_key is not None)`**

`print()` is a built-in Python tool that displays text in the output area below the cell — it is how the code talks back to you.

Inside the parentheses, there are two things separated by a comma. Python will display both of them, with a space in between.

The first thing is `"API key loaded:"` — plain text wrapped in quotation marks. In Python, any text wrapped in quotation marks is called a **string**, which just means "a sequence of characters treated as text rather than as code."

The second thing is `api_key is not None`. This is a question that produces either `True` or `False`. **None** is Python's special way of representing "nothing" or "no value was found." If `load_dotenv()` found your key, `api_key` holds the actual key string and is not None, so this evaluates to `True`. If the `.env` file was missing or the key name was misspelled, `api_key` would be None and this would print `False` — alerting you that something is wrong before you go any further.

### What the output means

You will see something like:

```
API key loaded: True
```

`True` means your `.env` file was found, your key was read successfully, and it is sitting in the `api_key` variable ready to use. If you see `False`, stop and check that your `.env` file exists in the right folder and that `ANTHROPIC_API_KEY` is spelled correctly inside it.

---

## Cell 2: Creating a Reusable Connection to the Anthropic API

```python
from anthropic import Anthropic
client = Anthropic(api_key=api_key)   # creates a reusable API client
print("Anthropic client ready")
```

### What this code does

**Line 1: `from anthropic import Anthropic`**

Again, you are opening a toolbox (`anthropic` — this is the official Python library provided by Anthropic) and pulling out a specific tool called `Anthropic`. Note that the toolbox and the tool have the same name but play different roles: the lowercase `anthropic` is the library (the whole toolbox), and the capitalized `Anthropic` is a specific tool inside it.

What kind of tool is `Anthropic`? It is a **class** — a blueprint for creating objects. Think of a class like a cookie cutter: the cutter itself is not a cookie, but you use it to stamp out actual cookies. In the next line, you will stamp out an actual object using this blueprint.

**Line 2: `client = Anthropic(api_key=api_key)`**

This line uses the `Anthropic` blueprint to create an actual working object, and stores it in a variable called `client`.

`Anthropic(api_key=api_key)` — the parentheses here stamp out a new object from the `Anthropic` blueprint. Inside the parentheses, you are passing in a piece of information it needs: `api_key=api_key`. The left side (`api_key=`) is the name of the slot you are filling in — a slot that the `Anthropic` blueprint has pre-defined. The right side (`api_key`) is your variable from the previous cell, which holds the actual secret key string.

What is a **client** in this context? Think of the API as a kitchen in a restaurant, and you are a customer. The client is your personal waiter. Whenever you want to send a message to Claude, you tell the waiter (the client), and the waiter handles all the details of walking to the kitchen, placing the order correctly, waiting for the reply, and bringing it back to you. You create the client once and then reuse it for every request in this session.

`client =` stores this fully configured waiter object in the variable named `client`.

**Line 3: `print("Anthropic client ready")`**

A simple confirmation message. This tells you the object was created without any errors. If something went wrong — for example, the `anthropic` library was not installed — Python would have already thrown an error before reaching this line.

### What the output means

```
Anthropic client ready
```

This is purely a reassurance message. It confirms that the `client` object was created successfully and is ready to send requests to Claude's API.

---

## Cell 3: The Smoke Test — Making Your First Real API Call

```python
response = client.messages.create(
    model="claude-haiku-4-5",          # fast, cheap model for smoke test
    #    model="claude-sonnet-4-5",
    max_tokens=50,                     # hard ceiling on output tokens
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]
)
print(response.content[0].text)        # content is a list; [0] is the first block
```

### What this code does

This cell makes your first actual request to the Claude AI and prints the reply. It is called a **smoke test** — a quick, simple check to verify that everything is connected and working before you try anything complicated. (The term comes from electronics: when you power on a new circuit for the first time, if no smoke appears, the basic connections are good.)

**Lines 1–5: `response = client.messages.create(...)`**

This is one big instruction spread across multiple lines for readability. Python knows it is not finished until it sees the closing parenthesis `)`, so it reads all five lines as a single command.

`client.messages.create(...)` — you are reaching into your `client` waiter object, then into its `messages` section, and calling a tool called `create`. This is the core action: it creates a new message exchange with Claude and sends it to Anthropic's servers.

Everything inside the parentheses is a **parameter** — a specific piece of information the `create` tool needs in order to do its job. Think of parameters like fields on an order form: the waiter needs to know what dish you want, how big a portion, etc.

**Parameter: `model="claude-haiku-4-5"`**

This tells Claude which version of itself to use. Anthropic offers several models with different trade-offs between speed, capability, and cost. `claude-haiku-4-5` is fast and inexpensive — perfect for a quick smoke test where you just want to verify the connection works. Think of models like different staff members: Haiku is the quick-reply assistant; Sonnet (the commented-out option below) is the more thorough analyst. You pay more for more capable models, so you choose based on what the task actually needs.

**Line 3: `#    model="claude-sonnet-4-5",`**

The `#` at the start makes this entire line a **comment** — Python ignores it completely. It is there to show you an alternative: if you deleted the `#` and added a `#` to the line above, you would switch to the Sonnet model instead. This is a common technique for keeping alternatives visible in code without activating them.

**Parameter: `max_tokens=50`**

This sets a hard upper limit on how long Claude's reply can be. A **token** is the unit Claude uses to measure text — roughly equivalent to one word or part of a word (for example, "hello" is one token; "unbelievable" might be two or three). Setting `max_tokens=50` means Claude will stop writing after approximately 50 word-chunks, even if it hasn't finished a thought. This prevents accidentally running up a large bill on a simple test.

**Parameter: `messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual conversation you are sending. Let's break it down from the outside in:

`messages=` — the slot on the order form where you write the conversation.

`[...]` — the square brackets create a **list** — an ordered collection of items, like a numbered list on paper. A conversation can have many turns, so it is stored as a list of turns.

`{...}` — the curly braces create a **dictionary** — a lookup table made of pairs, where each pair has a label (called a **key**) and a value. Like a phone book where you look up a name to get a number. Here, the dictionary has two key-value pairs:

- `"role": "user"` — the label `role` maps to the value `"user"`, which tells Claude that this message is coming from the human side of the conversation (as opposed to `"assistant"`, which would be Claude's own previous replies).
- `"content": "Say hello in one short sentence."` — the label `content` maps to the actual text of the message you are sending.

`response =` — whatever comes back from Claude is stored in a variable called `response`. This will be a complex object containing Claude's reply text, information about how many tokens were used, the model that was used, and more.

**Line 6: `print(response.content[0].text)`**

Now you retrieve and display Claude's actual reply. Let's unpack from the inside out:

`response` — the variable holding everything that came back from the API.

`response.content` — you are reaching into the `response` object to find its `content` section, which holds the actual reply. Claude's API returns content as a **list** because some advanced use cases can produce multiple content blocks (for example, a block of text and a block of structured data). Even when there is just one reply, it is still wrapped in a list.

`response.content[0]` — the `[0]` reaches into the list and pulls out the item at position zero. In Python (and most programming languages), counting starts at zero, not one. So the first item is at position `[0]`, the second would be at `[1]`, and so on. The comment in the code notes this: `# content is a list; [0] is the first block`.

`response.content[0].text` — finally, you reach into that first content block and pull out just the text portion of it.

`print(...)` — displays that text in the output area below the cell.

### What the output means

You will see something like:

```
Hello! I hope you're having a wonderful day.
```

This is Claude's actual reply, proving that the entire pipeline works: your key was valid, the client connected to Anthropic's servers, the request was formatted correctly, Claude processed it, and the reply traveled back to your notebook successfully. If this works, you can trust that more complex cells later in the notebook will also be able to reach Claude.

---

## Cell 4: The `ask()` Helper Function — Making API Calls Easier

```python
usage_log = []   # accumulates token counts for every API call this session

def ask(prompt, system="", model="claude-haiku-4-5", max_tokens=512, temperature=0):
    """
    Send a single-turn message and return the assistant reply string.

    Parameters
    ----------
    prompt      : str   — the user message text
    system      : str   — persistent instructions (optional)
    ...
    """
```

### What this code does

This cell defines a **helper function** — a reusable, named recipe that bundles up all the steps of making an API call so you can trigger the whole sequence by writing just one short line later. Instead of typing out `client.messages.create(...)` with all its parameters every single time you want to ask Claude something, you will be able to write `ask("your question here")` and the helper will handle everything else for you.

**Line 1: `usage_log = []`**

This creates a variable called `usage_log` and stores an empty **list** inside it. The square brackets `[]` with nothing between them mean "an empty list — no items yet."

Why does this exist? Every time you call the API, Anthropic tells you how many tokens were used (both in your request and in Claude's reply). This log will collect that information from every single API call made during this session, so that at the end you can look back and see a running total of your usage. Think of it as starting a fresh receipt at the beginning of a shopping trip.

The comment `# accumulates token counts for every API call this session` explains the purpose in plain English — again, Python ignores it, but it is valuable for anyone reading the code.

**Lines 3–10: `def ask(prompt, system="", model="claude-haiku-4-5", max_tokens=512, temperature=0):`**

`def` is a Python keyword that means "I am about to define a new function." A **function** is a named recipe — a sequence of steps that you write once, give a name to, and can then trigger at any time by calling that name. Everything indented below this `def` line is part of the recipe.

`ask` is the name you are giving this function. You chose this name — it could have been anything, but `ask` is clear and descriptive.

The items in parentheses after `ask` are the **parameters** of the function — the pieces of information you can pass in when you call the recipe. Think of them like blanks on a form:

- `prompt` — the question or instruction you want to send to Claude. No default value is given here, meaning you *must* provide this every time you call `ask()`.

- `system=""` — optional background instructions that stay consistent for every turn (like telling Claude "you are a professional writing coach" before the conversation starts). The `=""` means if you don't provide this, it defaults to an empty string — no system instructions.

- `model="claude-haiku-4-5"` — which Claude model to use. Defaults to the fast, economical Haiku model if you don't specify otherwise.

- `max_tokens=512` — the maximum length of Claude's reply, measured in tokens. Defaults to 512 if you don't specify otherwise.

- `temperature=0` — a number between 0 and 1 that controls how creative or unpredictable Claude's replies are. At `0`, Claude gives the most consistent, predictable answer — the same question will get essentially the same answer every time. At `1`, Claude is more experimental and varied. For evaluation and testing work, `0` is preferred because you want reliable, repeatable results.

The colon `:` at the end of the `def` line tells Python that the function's body (all its steps) will follow on the next lines, indented.

**Lines 4–10: `""" ... """`**

The triple quotation marks `"""` mark what is called a **docstring** — a special comment written immediately inside a function to explain what it does, what its parameters mean, and what it gives back. Python can display this documentation automatically when someone asks for help about the function. It is a professional habit to include one. Everything between the opening `"""` and the closing `"""` is ignored by Python as code but preserved as documentation.

### What the output means

This cell produces no output when you run it. Defining a function does not run it — it just teaches Python the recipe. The recipe only executes when you later call `ask(...)` somewhere else in the notebook.

---

## Glossary

**API (Application Programming Interface):** A defined way for two pieces of software to talk to each other. Like a waiter who carries orders between you (your code) and the kitchen (Claude's servers). You never see the kitchen; you only interact with the waiter's menu of available requests.

**API key:** A long, unique secret string that identifies who is making requests to an API. Functions like a password. Anthropic uses it to know which account to charge and to prevent unauthorized access.

**Class:** A blueprint for creating objects. Like a cookie cutter that can stamp out many individual cookies, each with the same structure but potentially different specific values.

**Client (API client):** An object that manages the connection to an API on your behalf. You create it once and reuse it, rather than re-establishing the connection for every individual request.

**Comment:** Text in a code file that Python completely ignores, written for humans who read the code. In Python, comments begin with a `#` symbol.

**Default value:** A fallback value that a function uses for a parameter when the caller doesn't provide one. Written as `parameter=value` in the function definition.

**def:** A Python keyword used to define (create) a new function. Everything indented below a `def` line is part of that function.

**Dictionary:** A lookup table made of key-value pairs. Written with curly braces `{}`. Like a phone book where you look up a label (the key) to retrieve an associated piece of information (the value).

**Docstring:** A special multi-line comment placed immediately inside a function, written between triple quotes `"""`. It documents what the function does and how to use it.

**dotenv / .env file:** A system for storing secret values (like API keys) in a separate private file rather than directly in code. The `.env` file stays on your machine and is never shared. The `dotenv` library reads it and makes its contents available to your program.

**Environment variable:** A named piece of information stored in the operating system's temporary memory while a program is running. Programs can read and write these notes. `load_dotenv()` posts your API key here so your code can retrieve it without the key ever appearing directly in the code.

**Function:** A named, reusable recipe — a sequence of steps that you define once and can trigger at any time by calling its name with parentheses. Functions can accept input (parameters) and can produce output (a return value).

**Import:** Bringing a tool or toolbox into your current working session so you can use it. Like carrying a specific wrench from a toolbox into the room where you're working.

**Indentation:** The spaces at the beginning of a line. Python uses indentation to understand which lines belong to a function, loop, or other structure. Lines indented under a `def` statement are part of that function.

**List:** An ordered, numbered collection of items. Written with square brackets `[]`. Items are separated by commas. The first item is at position `[0]`, the second at `[1]`, and so on.

**max_tokens:** A parameter that sets the maximum number of tokens Claude is allowed to produce in a single reply. A hard ceiling to control length and cost.

**Model:** A specific version of Claude with different capability, speed, and cost characteristics. Like choosing between different staff members for a task based on complexity and budget.

**None:** Python's special built-in value meaning "nothing" or "no value." When a variable holds `None`, it means the expected value was not found or was never assigned.

**Object:** A self-contained bundle of data and tools created from a class blueprint. For example, the `client` variable holds an object that contains your API key and all the tools needed to send requests to Anthropic.

**Parameter:** A piece of information passed into a function when you call it. Like filling in blanks on a form. Parameters let the same function behave differently depending on what you give it.

**print():** A built-in Python function that displays text or values in the output area below a notebook cell. It is the primary way Python code communicates results to you.

**String:** A sequence of characters treated as text rather than as code. In Python, strings are wrapped in either single quotes `'...'` or double quotes `"..."`.

**Smoke test:** A quick, simple check run before anything complex to verify that the basic connections work. If the simplest possible thing works, you can be reasonably confident the more complex things will too.

**Temperature:** A parameter (a number from 0 to 1) that controls how creative or random Claude's replies are. Zero means consistent and deterministic; one means more varied and experimental.

**Token:** The unit Claude uses to measure and process text. Roughly equivalent to a word or part of a word. Both your input and Claude's output are counted in tokens for billing and limit purposes.

**True / False:** Python's two **boolean** values — the result of any yes/no question in code. `True` means yes or correct; `False` means no or incorrect.

**Variable:** A labeled storage box that holds a value. You create one by writing a name, then `=`, then the value you want to store. Later, writing just the name retrieves whatever is in the box.