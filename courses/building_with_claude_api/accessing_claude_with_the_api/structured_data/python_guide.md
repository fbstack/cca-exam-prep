# Python Guide: Structured Data via Assistant Prefilling and Stop Sequences

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (one paragraph for complete beginners)

Python is a programming language — a way of giving precise, step-by-step instructions to a computer. Think of it like writing a recipe: each line is one instruction, and the computer follows them in order, top to bottom. Unlike a recipe written for a human, though, Python has to be completely unambiguous — every comma, bracket, and quotation mark matters. The good news is that Python was designed to read almost like plain English, which makes it one of the friendliest languages to learn. In this lab, you are using Python to talk to Claude (Anthropic's AI) through its API — sending it questions and receiving structured answers back.

---

## Cell 1: Loading Your Secret API Key

This cell handles something very important before anything else: finding your secret password (called an **API key**) that proves to Anthropic's servers that you are allowed to use Claude. It reads that key from a hidden file on your computer so you never have to type it directly into your code — which would be a security risk.

```python
from dotenv import load_dotenv  # import dotenv to read .env file
import os                        # import os to access environment variables

load_dotenv()                              # read key=value pairs from .env into os.environ
api_key = os.getenv("ANTHROPIC_API_KEY")   # retrieve the API key string (None if missing)
print("API key loaded:", api_key is not None)  # confirm key presence without printing secret
```

### What this code does

---

**Line 1: `from dotenv import load_dotenv`**

This line brings a specific tool into your workspace. To understand it, you need to know two things:

- **What a module/library is:** Python comes with many built-in capabilities, but thousands of extra tools exist that you can add. These are called *modules* or *libraries* — pre-written collections of code that someone else wrote and packaged up for you to use. Think of them like kitchen appliances: you don't build a blender from scratch every time you want a smoothie — you just take one off the shelf and plug it in.

- **What `dotenv` is:** `dotenv` is a library whose one job is to read a special hidden file called `.env` (pronounced "dot env") that lives on your computer. That file stores sensitive information like passwords and API keys in a simple format: `KEY=value`. Keeping secrets in a separate file means you never accidentally share them when you share your code.

- **What `from ... import ...` means:** This is the syntax (the grammar) Python uses when you want to bring in a *specific tool* from a library, rather than the whole library. Here, you're reaching into the `dotenv` library and pulling out just the `load_dotenv` function (a function is a named set of instructions you can run by calling its name — more on functions later).

In plain English: *"Go to the `dotenv` library and get me the `load_dotenv` tool so I can use it here."*

---

**Line 2: `import os`**

- **What `os` is:** `os` stands for "operating system." It is a built-in Python module (meaning it comes with Python automatically — no installation needed) that lets Python talk to the underlying computer system: reading files, checking folder paths, and crucially here, reading **environment variables**.

- **What an environment variable is:** Think of your computer as an office building. The environment variables are like a shared notice board in the hallway — a list of key-value pairs (name=value) that any program running on the computer can read. Your API key will be posted on this notice board by `load_dotenv()` in the next line, and then `os` will read it from there.

- **What `import os` means:** Unlike the first line which grabbed one specific tool, this line imports the entire `os` module under the name `os`. You'll then use `os.getenv(...)` to access its tools (the dot means "look inside `os` for this specific function").

In plain English: *"Bring in Python's built-in operating system toolkit so I can read the notice board of system variables."*

---

**Line 3: `load_dotenv()`**

This line actually *runs* (executes) the `load_dotenv` function you imported in line 1.

- **What parentheses mean after a name:** In Python, putting `()` after a name is how you *call* (run) a function. The parentheses are like pressing the "start" button on that kitchen appliance. Without the parentheses, you'd just be pointing at the appliance without turning it on.

- **What it does:** `load_dotenv()` opens the `.env` file on your computer, reads every `KEY=value` line inside it, and posts each one onto the operating system's notice board (the environment variables) so other parts of your code can find them.

- **Why this is better than typing the key directly:** If you wrote `api_key = "sk-ant-abc123..."` directly in your notebook, that secret would be visible to anyone who sees your code. By keeping it in a `.env` file (which you never share), your code stays shareable and your secret stays secret.

In plain English: *"Open the `.env` file and copy everything in it onto the system's shared notice board."*

---

**Line 4: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line reads your API key from the notice board and saves it for later use.

- **What a variable is:** A variable is a labeled box that holds a value. When you write `api_key = something`, you are creating a box labeled `api_key` and putting `something` inside it. Any time you write `api_key` later in your code, Python opens that box and uses whatever is inside.

- **What `os.getenv(...)` does:** This is a function inside the `os` module. It looks on the system's notice board for an entry with the name you specify (in this case, `"ANTHROPIC_API_KEY"`) and returns whatever value is stored there. If it cannot find that name, it returns a special value called `None` — Python's way of saying "nothing."

- **What `"ANTHROPIC_API_KEY"` is:** This is a **string** — a piece of text, always wrapped in quotation marks in Python. Quotation marks tell Python "this is literal text, not a variable name or command." It is the exact name of the entry you stored in your `.env` file.

In plain English: *"Look on the system notice board for the entry called `ANTHROPIC_API_KEY` and put whatever value you find into a box labeled `api_key`."*

---

**Line 5: `print("API key loaded:", api_key is not None)`**

This line prints a message to the screen to confirm that the key was found — without accidentally revealing what the key actually is.

- **What `print(...)` does:** `print` is one of Python's most basic built-in functions. Whatever you put between its parentheses, Python displays on the screen below the cell. It is your window into what the code is doing.

- **What `api_key is not None` does:** This is a **logical test** — a question Python evaluates to either `True` or `False`. It asks: "Is the thing in the `api_key` box something other than `None`?" If the `.env` file was found and the key was loaded correctly, `api_key` will contain your actual key (a long string of characters), so the answer will be `True`. If the key was missing, `api_key` would be `None`, and the answer would be `False`. This is called a **boolean expression** — one that evaluates to either `True` or `False`.

- **Why not just print `api_key` directly?** Because that would print your actual secret key to the screen. Anyone looking over your shoulder — or at a screenshot of your notebook — could steal it. Printing `True` or `False` tells you what you need to know (did it load?) without exposing the secret.

In plain English: *"Print the message 'API key loaded:' followed by the answer to the question 'did we actually find a key?' (True or False)."*

### What the output means

You should see: `API key loaded: True`

This means Python successfully found your `.env` file, found the `ANTHROPIC_API_KEY` entry inside it, and loaded it into memory. If you see `API key loaded: False`, the key was not found — most likely your `.env` file is missing, in the wrong folder, or the key is spelled differently inside it.

---

## Cell 2: Creating the Connection to Claude

Now that you have your API key, this cell uses it to set up a live connection to Anthropic's servers — the equivalent of logging in. After this cell runs, you have an object (a self-contained package of tools and data) ready to send messages to Claude.

```python
from anthropic import Anthropic  # import the official Anthropic SDK client class

client = Anthropic(api_key=api_key)  # instantiate the client; all API calls go through this object
print("Anthropic client ready")       # confirm the client was created without exception
```

### What this code does

---

**Line 1: `from anthropic import Anthropic`**

- **What `anthropic` is:** `anthropic` (lowercase) is a Python library — a collection of pre-written tools — published by Anthropic, the company that makes Claude. It is the official way to communicate with Claude from Python. Think of it as an official telephone kit that Anthropic gave you, complete with all the dialing instructions pre-loaded.

- **What `Anthropic` (uppercase) is:** Inside that library, there is a **class** called `Anthropic`. A class is a blueprint — a template for creating objects. An **object** is like a custom-built tool that knows both some information (like your API key) and some actions it can perform (like sending messages to Claude). You will use this class to build your actual connection tool in the next line.

- **Why the same word appears twice with different capitalizations:** In Python, `anthropic` (lowercase) refers to the library package, while `Anthropic` (uppercase) refers to the specific class inside it. Python is case-sensitive — these are treated as completely different things.

In plain English: *"From Anthropic's official Python toolkit, get me the `Anthropic` connection blueprint."*

---

**Line 2: `client = Anthropic(api_key=api_key)`**

This is where you actually build your connection object — your personal, key-holding, Claude-communicating tool.

- **What `Anthropic(api_key=api_key)` does:** Calling a class with parentheses (like `Anthropic(...)`) creates a new object from that blueprint — this is called **instantiation** (creating an instance of the class). It is like using a cookie cutter (the class/blueprint) to make an actual cookie (the object). The thing inside the parentheses, `api_key=api_key`, passes your secret key into the object so it can use it in every request it makes.

- **What `api_key=api_key` means:** This looks confusing because the same word appears twice. The first `api_key` (before the `=`) is the *name of the slot* the `Anthropic` blueprint expects to receive your key in — that name is built into the library. The second `api_key` (after the `=`) is your *variable* — the box you filled in Cell 1 with your actual key. In plain English: "Put what's in my `api_key` box into the slot labeled `api_key`."

- **What `client = ...` does:** The newly created object is stored in a variable (labeled box) called `client`. Every time you want to talk to Claude later in this lab, you will go through this `client` object.

In plain English: *"Use the Anthropic blueprint to build a connection tool, give it my API key, and store it in a box labeled `client`."*

---

**Line 3: `print("Anthropic client ready")`**

- This simply prints the text `Anthropic client ready` to the screen.

- **Why this matters:** If the previous line had failed — for example, if the `anthropic` library was not installed, or if the API key was in the wrong format — Python would have crashed with an error message and never reached this line. The fact that you see this printed message means everything in this cell ran successfully.

In plain English: *"Print a confirmation message so I know the connection object was built without any errors."*

### What the output means

You should see: `Anthropic client ready`

This tells you that the `anthropic` library is installed, the `Anthropic` object was created successfully with your key, and you are ready to make real API calls. If instead you see a red error message, it usually means either the library is not installed (try running `pip install anthropic` in a terminal) or the API key stored in Cell 1 was `None` (False).

---

## Cell 3: The Smoke Test — Sending Your First Message to Claude

This cell sends a small, simple message to Claude just to confirm that the end-to-end connection works — from your computer, through the internet, to Anthropic's servers, and back. In software development, a quick test like this is called a **smoke test** (from the old engineering practice: turn something on and check if smoke appears — if it doesn't, it's probably working).

```python
response = client.messages.create(        # call the Messages API endpoint
    model="claude-haiku-4-5",             # use Haiku for fast, low-cost smoke test
    #    model="claude-sonnet-4-5",
    max_tokens=50,                        # cap tokens so the smoke test stays cheap
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]  # minimal valid request
)
print(response.content[0].text)  # content is a list of blocks; [0] is the first (text) block
```

### What this code does

---

**Line 1: `response = client.messages.create(`**

This begins the function call that actually sends a message to Claude. Because it has many ingredients to specify, this single logical instruction is spread across multiple lines for readability — Python knows it is not finished until the closing parenthesis `)` appears.

- **What `client.messages.create(...)` means:** You are reaching into your `client` object (built in Cell 2), then into a section of it called `messages`, then calling a function called `create`. The dots are like navigating folders: `client` → `messages` → `create`. The `create` function is the one that actually sends your request to Anthropic's servers and waits for Claude's response.

- **What `response = ...` does:** Claude's answer will come back as a complex object containing not just the text, but also information about how many tokens were used, why generation stopped, etc. All of that is stored in a variable (labeled box) called `response`.

- **What an API is:** API stands for **Application Programming Interface**. Think of it as a waiter in a restaurant: you (your code) tell the waiter (the API) what you want, the waiter takes your order to the kitchen (Anthropic's servers), Claude prepares the response, and the waiter brings it back to your table. You never go directly to the kitchen — the API handles all that for you.

In plain English: *"Send a message to Anthropic's servers via the `messages.create` function and store whatever comes back in a box labeled `response`."*

---

**Line 2: `model="claude-haiku-4-5",`**

This specifies *which* Claude model you want to handle your request.

- **What a model is:** Claude is not a single program — Anthropic offers different versions (models) with different trade-offs between speed, cost, and intelligence. Think of them like different service tiers: economy, business, and first class.

  - **Haiku** is the smallest and fastest — like a quick text message response. It is used here because smoke tests should be fast and cheap.
  - **Sonnet** (shown commented out on the next line) is larger and more capable — better for complex tasks.

- **What the trailing comma means:** In Python, when you are listing multiple ingredients inside a function call, each one is separated by a comma. The comma after `"claude-haiku-4-5"` simply means "more ingredients follow on the next lines."

In plain English: *"Use the Haiku version of Claude — the fast, inexpensive one — to answer this request."*

---

**Line 3: `#    model="claude-sonnet-4-5",`**

This line is a **comment** — it is completely ignored by Python.

- **What a comment is:** Any line in Python that begins with `#` is a comment. Python skips it entirely. Comments exist for human readers, not the computer. They are notes, explanations, or — as in this case — lines of code that have been "turned off" temporarily.

- **Why this line is here:** The developer has left an alternative option (using Sonnet instead of Haiku) commented out, so you can easily switch by removing the `#` from this line and adding `#` to the line above. It is like having a backup option written in pencil.

In plain English: *"This line is a human note — a disabled alternative. Python ignores everything after the `#`."*

---

**Line 4: `max_tokens=50,`**

This sets a hard limit on how long Claude's response can be.

- **What a token is:** When Claude reads and writes text, it does not process individual letters or whole words — it works in chunks called **tokens**. Roughly speaking, one token is about three-quarters of an average English word. The word "hamburger" might be one token; "antidisestablishmentarianism" might be several. A limit of 50 tokens means Claude will stop writing after approximately 35–40 words.

- **Why set a limit?** Anthropic charges based on the number of tokens used. Without a limit, a misbehaving prompt could cause Claude to generate thousands of tokens and create an unexpectedly large bill. Setting `max_tokens=50` for a smoke test keeps the cost to fractions of a cent.

In plain English: *"Stop Claude's response after at most 50 word-chunks (roughly 35–40 words), so this test stays quick and cheap."*

---

**Line 5: `messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This is the actual message you are sending to Claude — the conversation history.

- **What a list is:** The square brackets `[...]` create a **list** — an ordered collection of items, like a numbered list on paper. In this case, the list contains just one item: one message.

- **What a dictionary is:** The curly braces `{...}` create a **dictionary** — a lookup table made of key-value pairs, like a contact card. Each entry has a name (key) and a value, separated by a colon. Here, the dictionary has two entries:
  - `"role": "user"` — the "role" slot contains the value "user," meaning this message comes from the human.
  - `"content": "Say hello in one short sentence."` — the "content" slot contains the actual text of the message.

- **Why the `role` field?** Claude's API is designed around multi-turn conversations (back-and-forth exchanges). To keep track of who said what, each message must be labeled: `"user"` for the human's messages and `"assistant"` for Claude's previous responses. Even if there's only one message (like here), the format requires the `role` label.

In plain English: *"Send one message, labeled as coming from the user, with the text 'Say hello in one short sentence.'"*

---

**Line 6: `)`**

This closing parenthesis ends the `client.messages.create(...)` function call that began on line 1. Python collects everything between the opening `(` and this closing `)` as the complete set of ingredients for that function.

In plain English: *"That's everything I want to pass into the `create` function — done."*

---

**Line 7: `print(response.content[0].text)`**

This line extracts Claude's actual reply text from the response object and prints it to the screen.

- **What `response.content` is:** The `response` object that came back from the API contains several pieces of information. One of them, accessed via `.content`, is a **list** of content blocks. Think of it like a returned package that might contain multiple items inside. Claude can technically return multiple blocks (for example, if it uses tools), but usually there is just one.

- **What `[0]` means:** Because `response.content` is a list, you access specific items in it using square brackets and a number. Python counts from zero, not one — so `[0]` means "the first item." This is called **indexing**. Think of it like apartment numbers where the ground floor is apartment 0.

- **What `.text` means:** Each content block is an object. Accessing `.text` retrieves the actual text string (Claude's written reply) from inside that block.

- **Putting it together:** `response.content[0].text` means: "Go into `response`, find the `content` list, take the first item in that list, and from that item get the `text` field."

In plain English: *"From the response that came back from Claude, dig into the first content block and print the actual text of what Claude said."*

### What the output means

You should see a single short sentence from Claude — something like: `Hello! It's wonderful to connect with you today.`

This confirms the entire chain works: your key is valid, the `anthropic` library is properly installed, the internet connection to Anthropic's servers is functioning, and Claude is responding. If you see an error instead, it usually means the API key is invalid or expired, or there is a network problem.

---

## Cell 4: The `ask()` Helper Function and Usage Logger

*(Note: The notebook's source was cut off mid-function definition. This guide explains every line that was provided, and describes the full pattern for context.)*

This is the most complex cell in the lab. It builds two things: a **session-wide log** to track how many tokens every API call uses, and a reusable **helper function** called `ask()` that wraps all the repetitive details of calling Claude into one convenient, labeled tool.

```python
usage_log = []  # session-level list; every API call appends one dict for telemetry

def ask(
    messages,            # the full conversation list (user + assistant turns)
    system="",           # optional system prompt; defaults to empty string
    max_tokens=512,      # generous default; callers can override for large outputs
    stop_sequences=None, # None means no stop sequences; pass a list to activate
    label="call"         # human-readable label for the usage log entry
```

### What this code does

---

**Line 1: `usage_log = []`**

- **What `[]` means:** Square brackets with nothing inside them create an empty **list** — like an empty numbered notepad with no entries yet.

- **What `usage_log` is:** This is a variable (a labeled box) called `usage_log`. It will act as the lab's running record — every time a call is made to Claude through the `ask()` function, one entry will be added to this list recording how many tokens were used. By the end of the lab, you can look at `usage_log` to see a complete history of every API call and its cost.

- **What "session-level" means:** This list is created once, outside of any function, which means it exists for the entire time the notebook is running. Functions defined later can add to it, and it keeps growing. Think of it as a shared accounting ledger for the whole session.

In plain English: *"Create an empty list called `usage_log` that will grow throughout the session as a record of every API call made."*

---

**Line 2: `def ask(`**

This line begins the definition of a new function called `ask`.

- **What a function is:** A function is a named recipe — a block of instructions that you write once and can run (call) as many times as you want just by using its name. Instead of typing out all the details of a Claude API call every time you need one, you write those details once inside this function, and then call `ask(...)` whenever you need it.

- **What `def` means:** `def` is short for "define." It is Python's keyword that signals: "I am about to create a new function. Everything indented below this line (until the indentation stops) is the body of this function."

- **Why the parenthesis is open but not closed yet:** The function's ingredients (called **parameters**) are listed inside parentheses after the name. Because there are many parameters, they are written one per line for readability. Python knows the parameter list continues until it sees the closing `)`.

In plain English: *"Start defining a reusable recipe called `ask`. Here are the ingredients it needs..."*

---

**Line 3: `messages,`**

This is the first **parameter** (ingredient) of the `ask` function.

- **What a parameter is:** A parameter is a placeholder — a named slot that will be filled in with an actual value every time the function is called. Think of function parameters like blanks in a form: the form is defined once, but every time someone fills it out, they put in their own specific values.

- **What `messages` represents here:** This is the conversation history that the caller will provide — a list of dictionaries in the same format as in Cell 3 (each with a `role` and `content`). Unlike the parameters below, this one has no default value, which means the caller *must* provide it every time — it is required.

In plain English: *"The first required ingredient: the list of messages (the conversation to send to Claude)."*

---

**Line 4: `system="",`**

This is the second parameter — the **system prompt** — with a **default value**.

- **What a system prompt is:** In Claude's API, besides the conversation messages, you can provide a separate "system" instruction that sets the context or persona for Claude before the conversation begins. It is like a briefing you give an assistant before they take a customer call: "You are a customer service agent for Acme Corp. Be polite and brief." The system prompt is applied silently — the user does not see it.

- **What `=""` means:** The `=` here sets a **default value** for this parameter. If the caller does not provide a system prompt, Python automatically uses `""` (an empty string — text with nothing in it). This makes the parameter optional. The caller only has to mention it if they want something other than blank.

In plain English: *"The optional system prompt (background instructions for Claude). If the caller doesn't specify one, use an empty string — no special instructions."*

---

**Line 5: `max_tokens=512,`**

This is the third parameter — the maximum response length — with a default value.

- **What `512` means here:** Unlike the smoke test in Cell 3 (where 50 tokens was enough), the `ask()` helper is designed for more substantial responses throughout the lab. 512 tokens is roughly 375–400 words — enough for most detailed responses without being excessively costly.

- **Why provide a default?** Most calls to `ask()` throughout this lab will not need to specify `max_tokens` explicitly — the 512 default is fine. But if a specific call needs a longer (or shorter) response, the caller can override it by passing in a different number.

In plain English: *"The optional token limit for Claude's response. Default to 512 (roughly 375 words) if the caller doesn't specify a different limit."*

---

**Line 6: `stop_sequences=None,`**

This is the fourth parameter — the list of **stop sequences** — with a default of `None`.

- **What a stop sequence is:** A stop sequence is a string of text that, when Claude is about to generate it, causes Claude to immediately stop writing. It is like a "stop here" sign planted in the text. For example, if you set the stop sequence to `"```"` (a code fence), Claude will stop generating the moment it would write those three backtick characters.

- **What `None` means:** `None` is Python's special value for "nothing" or "not applicable." Using `None` as the default here means: "If the caller doesn't specify stop sequences, don't use any — let Claude keep writing until it naturally finishes or hits `max_tokens`."

- **Why stop sequences matter for this lab:** The whole lab is about extracting structured data (like JSON or code) cleanly. Stop sequences are one of the key techniques — you can tell Claude to stop the moment it finishes the structured block, before it adds any extra commentary.

In plain English: *"The optional list of stop signals. If the caller doesn't provide any, use `None` (meaning: no stops — let Claude finish naturally)."*

---

**Line 7: `label="call"`**

This is the fifth and final visible parameter — a human-readable name for this API call — with a default value.

- **What this is for:** Every time `ask()` makes an API call, it will record an entry in `usage_log`. The `label` is a short descriptive name for that entry — like writing "Section 2 test" or "JSON extraction attempt" next to a line in the accounting ledger. This makes it easy to look back at `usage_log` and understand which call is which.

- **What `"call"` as default means:** If the caller doesn't bother providing a custom label, the log entry will just say `"call"` — generic, but better than nothing.

In plain English: *"An optional nickname for this API call, used when logging token usage. Defaults to the generic label 'call' if not specified."*

### What the output means

This cell produces no visible output when it runs — no `print()` statement is called. Instead, running this cell loads the `usage_log` list and the `ask()` function definition into Python's memory, making them available for all subsequent cells. Think of it as preparing your tools before you start cooking: you set out your cutting board and knives, but you haven't sliced anything yet.

If the cell runs without error, you know the function was defined successfully and is ready to use.

---

## Glossary

**API (Application Programming Interface):** A structured way for two software systems to communicate. Like a waiter who takes your order to the kitchen and brings back the result — you don't go directly to the kitchen yourself.

**Boolean:** A value that is either `True` or `False` and nothing else. Named after mathematician George Boole. Used for yes/no decisions in code.

**Class:** A blueprint or template for creating objects. Like a cookie cutter — it defines the shape, but each cookie made from it is a separate instance.

**Comment:** A line in Python code that starts with `#` and is completely ignored by Python. Used to leave notes for human readers.

**Default value:** A value that a function parameter automatically uses when the caller doesn't provide one. Makes parameters optional.

**Dictionary:** A data structure made of key-value pairs. Like a contact card where each field (key) has a value. Written with curly braces `{...}` in Python.

**dotenv:** A Python library whose job is to read a `.env` file and load its contents as environment variables. Keeps secrets out of your code.

**.env file:** A hidden configuration file (the name starts with a dot) that stores sensitive values like API keys in `KEY=value` format. Never shared publicly.

**Environment variable:** A named value stored at the operating system level, accessible to any program running on the computer. Like a shared notice board in an office hallway.

**Function:** A named, reusable block of code — like a recipe you write once and can run as many times as you want by calling its name.

**Haiku (Claude model):** Anthropic's smallest, fastest, and least expensive Claude model. Best for quick tasks where speed and cost matter more than maximum capability.

**Import:** Bringing a module or specific tool from a library into your current workspace so you can use it. Like taking an appliance off the shelf and plugging it in.

**Index / Indexing:** Accessing a specific item in a list by its position number. Python counts from zero, so `[0]` is the first item, `[1]` is the second, etc.

**Instantiation:** Creating an actual object from a class blueprint. Like using a cookie cutter to make an actual cookie.

**JSON (JavaScript Object Notation):** A structured way of writing data that both humans and machines can read, using keys and values. Like a very organized, nested shopping list.

**Library / Module:** A pre-written collection of Python code — tools someone else built that you can import and use. Saves you from building everything from scratch.

**List:** An ordered, numbered collection of items. Written with square brackets `[...]` in Python. Like a numbered list on paper.

**max_tokens:** An API parameter that sets a hard limit on how many tokens Claude can generate in its response. Prevents unexpectedly long (and expensive) outputs.

**messages:** The conversation history sent to the Claude API. A list of dictionaries, each with a `role` ("user" or "assistant") and `content` (the text of the message).

**Model:** A specific version of Claude (e.g., Haiku, Sonnet, Opus) with different trade-offs in speed, capability, and cost.

**None:** Python's special value meaning "nothing" or "no value." Often used as a default when something is optional and absent.

**Object:** A self-contained package that bundles together both data (attributes) and actions (methods/functions).