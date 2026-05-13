# Python Guide: Temperature — Controlling Creativity and Determinism

A plain-English walkthrough of every line of code in the JupyterLab lab.
No coding background required.

---

## What Python is (one paragraph for complete beginners)

Python is a way of giving precise, step-by-step instructions to a computer using plain-looking text. Think of it like writing a very detailed recipe: the computer reads each line from top to bottom and does exactly what it says. Just as a recipe can call for tools from your kitchen (a whisk, a mixing bowl), Python can call for pre-built tools that other programmers have already written — so you do not have to build everything from scratch. The JupyterLab notebook you are reading runs one small chunk of code at a time, called a **cell**, and shows you the result immediately below it. This guide explains every line in every cell, so you always know what is happening and why.

---

## Cell 1: Load Environment Variables

This cell is the very first thing the lab does. Its job is to find your secret API key — a password that proves to Anthropic's servers that you are allowed to use Claude — and make it available to the rest of the code without ever writing the key directly into the notebook (which would be a security risk).

```python
# ── Starter Cell 1: Load environment variables ──────────────────────────────
from dotenv import load_dotenv   # imports load_dotenv to read the .env file
import os                        # imports os so we can call os.getenv()

load_dotenv()                    # reads .env in the current directory and sets env vars
api_key = os.getenv("ANTHROPIC_API_KEY")  # retrieves the key; returns None if missing

print("API key loaded:", api_key is not None)  # prints True when the key was found
```

### What this code does

---

**Line 1: `# ── Starter Cell 1: Load environment variables ──────────────────────────────`**

This line is a **comment**. In Python, any line that starts with the `#` symbol is completely ignored by the computer — it is a note written by the programmer for human readers. Think of it like a sticky note attached to the side of a recipe card. This particular comment just labels what this block of code is for.

---

**Line 2: `from dotenv import load_dotenv`**

Let's unpack three things happening here.

*What is "importing"?* Python comes with many tools built in, and even more tools can be downloaded and added. **Importing** is the act of reaching into a toolbox and bringing a specific tool into your current workspace. Until you import a tool, Python does not know it exists — even if it is installed on the computer.

*What is `dotenv`?* It is a third-party toolbox (called a **library** or **package**) whose entire purpose is to read a special file called `.env`. A `.env` file is a plain text file that sits in your project folder and stores secret values — like passwords and API keys — in a format like `ANTHROPIC_API_KEY=abc123`. Storing secrets in this separate file means you can share your notebook code with others without accidentally sharing your key.

*What is `load_dotenv`?* It is one specific tool inside the `dotenv` toolbox. The `from ... import ...` pattern means "go to the `dotenv` toolbox and bring out only the `load_dotenv` tool." You could also bring out the whole toolbox at once, but it is cleaner to take only what you need.

---

**Line 3: `import os`**

This imports a different toolbox called `os`. The name stands for **operating system**, and this built-in Python toolbox gives your code the ability to interact with the computer's operating system — the software layer (like Windows or macOS) that sits between your code and the hardware.

One specific thing the operating system manages is **environment variables** — a system-level way of storing named values, like key-value pairs, that any program running on the computer can read. After `load_dotenv()` runs (in a moment), your API key will be stored as one of these environment variables, and `os` is the tool we use to read it back out.

---

**Line 5: `load_dotenv()`**

This line actually *calls* (runs) the `load_dotenv` tool we imported on line 2. The parentheses `()` at the end are what tell Python "do this now." Without the parentheses, you would just be referring to the tool without using it — like picking up a whisk but not whisking anything.

What does `load_dotenv()` do when it runs? It looks in the current folder for a file named `.env`, reads every line in that file, and registers each name-value pair as an environment variable. After this line runs, the operating system "knows about" your `ANTHROPIC_API_KEY` value — even though it was just sitting in a text file a moment ago.

---

**Line 6: `api_key = os.getenv("ANTHROPIC_API_KEY")`**

This line does two things: it *retrieves* a value, and it *stores* that value in a labeled box.

*Retrieving the value:* `os.getenv("ANTHROPIC_API_KEY")` asks the operating system: "What is the value of the environment variable named `ANTHROPIC_API_KEY`?" If `load_dotenv()` worked correctly, this will return your actual key — a long string of characters like `sk-ant-...`. If something went wrong and the key was never loaded, this returns a special Python value called `None`, which means "nothing" or "absent."

*Storing the value in a variable:* The `api_key =` part on the left creates a **variable**. Think of a variable as a labeled box: you write a name on the outside (`api_key`) and put a value inside (your actual key string). From this point forward, any time the code says `api_key`, Python opens that box and uses whatever is inside. This is important because the rest of the notebook will need to present your key to Anthropic's servers, and using the name `api_key` is much easier (and safer) than typing the raw key string over and over.

---

**Line 8: `print("API key loaded:", api_key is not None)`**

This line produces a visible message in the notebook so you can confirm everything worked.

*What is `print()`?* It is a built-in Python function (a named set of instructions) that displays text as output beneath the cell. Think of it like a "display board" — whatever you put inside the parentheses gets shown to you.

*What is `api_key is not None`?* This is a **comparison expression** that evaluates to either `True` or `False`. It is asking: "Is `api_key` something other than `None`?" If your key was found and loaded successfully, `api_key` contains a real string value, so `api_key is not None` is `True`. If the key was missing, `api_key` is `None`, and this expression is `False`.

The `print()` function receives two things separated by a comma: the text `"API key loaded:"` and the result of that comparison. Python displays them side by side.

### What the output means

When this cell runs, you will see one of two things:
- **`API key loaded: True`** — Your `.env` file was found, the key was inside it, and the key is now stored in the `api_key` variable. You are ready to proceed.
- **`API key loaded: False`** — Something went wrong. Either the `.env` file does not exist, it is in the wrong folder, or the line `ANTHROPIC_API_KEY=...` is missing or misspelled inside it. You should fix this before continuing, because every subsequent cell depends on having a valid key.

---

## Cell 2: Initialise the Anthropic Client

Now that we have our API key, this cell uses it to create a **client** — a Python object that knows how to talk to Anthropic's servers on our behalf. Every API call we make for the rest of the notebook will go through this client.

```python
# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────
from anthropic import Anthropic   # imports the Anthropic SDK client class

client = Anthropic(api_key=api_key)  # creates the client; api_key authenticates every request
print("Anthropic client ready")       # confirms the object was constructed without error
```

### What this code does

---

**Line 1: `# ── Starter Cell 2: Initialise the Anthropic client ─────────────────────────`**

Another comment labeling the cell. The computer ignores this completely — it is purely for human readers.

---

**Line 2: `from anthropic import Anthropic`**

This imports a tool from a library called `anthropic`. This library is Anthropic's official **SDK** (Software Development Kit) — a pre-built set of Python tools that handle all the behind-the-scenes complexity of sending requests to the Claude API and receiving responses.

The specific tool we are pulling out is called `Anthropic` (capital A). In Python, when a name starts with a capital letter, it is usually a **class** — which you can think of as a blueprint or mold. Just as a cookie cutter is a mold that you press into dough to produce a cookie, a class is a blueprint that you use to create an **object** — a fully formed thing that has both data and abilities built into it. We use this blueprint in the very next line.

---

**Line 4: `client = Anthropic(api_key=api_key)`**

This line creates the client object and stores it in a variable named `client`.

*`Anthropic(api_key=api_key)` on the right:* We are using the `Anthropic` blueprint (class) like a mold. Calling it with parentheses — `Anthropic(...)` — means "create a new object from this blueprint." Inside the parentheses, we pass `api_key=api_key`, which gives the new object our secret key so it can authenticate with Anthropic's servers.

Let's look at `api_key=api_key` carefully, because it looks confusing at first. The part on the *left* of the `=` (`api_key`) is the *name of the parameter* that the `Anthropic` blueprint is expecting — it literally wants something labeled `api_key`. The part on the *right* (`api_key`) is our *variable from Cell 1*, which holds the actual key string. So we are saying: "Create an Anthropic client object, and for its `api_key` slot, use the value stored in our `api_key` variable."

*`client =` on the left:* The resulting object — now a fully configured, key-holding client — gets stored in a variable named `client`. From this point forward, whenever the notebook wants to call Claude, it will say `client.messages.create(...)`.

Think of the client as a personal assistant you have just hired and briefed. You gave the assistant your credentials (the API key) so they can get into the building (Anthropic's servers). From now on, instead of going yourself every time, you hand requests to the assistant and they handle all the logistics of delivering your request and bringing back the answer.

---

**Line 5: `print("Anthropic client ready")`**

This prints a simple confirmation message. There is no condition or check here — if the `Anthropic(...)` call on the previous line had failed (for instance, because the `anthropic` library was not installed), Python would have crashed with an error before reaching this line. The fact that this message appears means the client object was created successfully.

### What the output means

You should see: **`Anthropic client ready`**

This confirms that the `anthropic` library is installed correctly and that the client object exists in memory. It does *not* yet confirm that the API key is valid — that will be tested when you actually make a request to the API in Cell 3.

---

## Cell 3: Smoke Test — Verify End-to-End Connectivity

A "smoke test" is a term borrowed from hardware engineering: when you power on a new circuit for the first time, if smoke comes out, something is obviously wrong. In software, a smoke test is the simplest possible test to confirm that everything is connected and working end to end. This cell sends the shortest possible message to Claude and prints the reply.

```python
# ── Starter Cell 3: Smoke test — verifies end-to-end connectivity ────────────
response = client.messages.create(   # calls the Messages API
    model="claude-haiku-4-5",        # fastest/cheapest model — ideal for smoke tests
    #    model="claude-sonnet-4-5",  # uncomment for higher quality during development
    max_tokens=50,                   # short cap — we only need one sentence back
    messages=[{"role": "user", "content": "Say hello in one short sentence."}]  # minimal prompt
)
print(response.content[0].text)  # content is a list; [0] is the first (only) block; .text is the string
```

### What this code does

---

**Line 1: `# ── Starter Cell 3: Smoke test — verifies end-to-end connectivity ────────────`**

A comment labeling and explaining the purpose of this cell. Ignored by Python.

---

**Lines 2–7: `response = client.messages.create(...)`**

This is a single Python **statement** spread across multiple lines. Python allows this when the content is inside parentheses — everything between the opening `(` on line 2 and the closing `)` on line 7 is considered one instruction. Let's break it into its parts.

**`client.messages.create(...)`** — the actual API call

- `client` is the assistant object we created in Cell 2.
- `.messages` is a sub-section of the client that deals specifically with sending messages to Claude. The dot (`.`) is Python's way of saying "go inside this object and find this part of it." Think of it like opening a filing cabinet (`client`) and pulling out a specific drawer labeled "messages."
- `.create(...)` is a **method** — a function that lives inside an object. Calling `.create()` is the act of actually sending your request to Anthropic's servers over the internet. When this line runs, your computer reaches out to Anthropic, hands over your message, and waits for Claude to respond.

**`response =`** stores whatever comes back from the API. The entire reply from Claude — not just the text, but also information about which model was used, how many tokens were consumed, and more — gets packaged into an object and placed in this variable named `response`. Think of it as the full envelope that comes back from the postal service: the letter is inside, but the envelope also has postmarks and other metadata.

---

**Line 3: `model="claude-haiku-4-5",`**

This is the first **argument** (a value passed into a function) inside the `.create()` call. It tells Claude which specific AI model to use for this request.

Anthropic offers several Claude models that differ in speed, cost, and capability. `claude-haiku-4-5` is the fastest and least expensive model — perfect for quick tests where you just want to confirm connectivity, not produce a polished response. The comma at the end tells Python that more arguments are coming.

---

**Line 4: `#    model="claude-sonnet-4-5",`**

This is a **commented-out line** — the `#` at the beginning means Python ignores it entirely. It is left there as a reminder that you could swap to a more capable model by removing the `#` (and commenting out line 3). This is a common developer technique for leaving yourself a quick alternative without deleting anything.

---

**Line 5: `max_tokens=50,`**

This argument sets a hard upper limit on how long Claude's response can be, measured in **tokens**.

A **token** is the unit of text that AI language models read and write. It is roughly equivalent to one short word or part of a longer word. For example, the word "hello" is one token, the word "extraordinary" might be two or three tokens, and a punctuation mark is often one token. The number 50 means Claude is not allowed to generate more than approximately 50 word-chunks in its reply.

Why set a limit? Without one, Claude might write a very long response when a short one is all you need — and longer responses cost more money (API calls are billed partly by token count) and take more time. For this smoke test, one sentence is plenty, so 50 tokens is a comfortable cap.

---

**Line 6: `messages=[{"role": "user", "content": "Say hello in one short sentence."}]`**

This argument is the actual conversation you are sending to Claude. Let's unpack its structure carefully, because this pattern appears in every API call throughout the lab.

*`messages=`* — This parameter tells Claude: here is the conversation history.

*`[...]`* — The square brackets mean this is a **list** — an ordered collection of items, like a numbered list on paper. Conversations can have many back-and-forth exchanges, so the `messages` parameter accepts a list of them. Here there is only one item in the list.

*`{"role": "user", "content": "Say hello in one short sentence."}`* — This is a **dictionary**. Think of a dictionary as a lookup table or a form with labeled fields. This particular dictionary has two fields:
- `"role"`: `"user"` — This tells Claude that this message comes from the human user (as opposed to the AI assistant itself, which would have the role `"assistant"`). Claude uses these role labels to understand the shape of the conversation.
- `"content"`: `"Say hello in one short sentence."` — This is the actual text of the message — what you are asking Claude to do.

So the full structure reads: "Here is a conversation containing one message. That message was sent by the user, and it says: 'Say hello in one short sentence.'"

---

**Line 8: `print(response.content[0].text)`**

This line digs into the `response` object to extract Claude's actual reply text and print it.

*`response.content`* — The `response` object has several named parts inside it. The part called `content` holds Claude's actual output. However, it is not just a plain piece of text — it is a **list** (remember, square bracket territory). This is because Claude can technically return multiple content blocks in one response (for example, text plus a tool call). Even when there is only one block, it is still wrapped in a list.

*`[0]`* — This is how Python accesses one specific item in a list by its position. Lists in Python are **zero-indexed**, which means counting starts at 0 rather than 1. So `[0]` means "give me the first item in this list." Since our response has only one content block, `[0]` gets us that single block.

*`.text`* — The content block object has a part called `text` which holds the actual reply string — the words Claude generated. This is the string we want to display.

Putting it together: "Go inside `response`, find `content`, get the first item in that list, and then get its `text` part. Print that text."

### What the output means

You should see a short, friendly greeting from Claude — something like: **`Hello! It's wonderful to connect with you today.`**

If you see this, congratulations: your API key is valid, your internet connection is working, the `anthropic` library is installed correctly, and Claude is responding. Every component of the chain is confirmed working. If instead you see an error message, the error text will tell you what went wrong — most commonly, an invalid API key or a network problem.

---

## Glossary

**API (Application Programming Interface):** A defined way for one piece of software to talk to another. Like a waiter who takes your order to the kitchen (a server or another service) and brings back what you asked for. You do not need to know how the kitchen works — you just use the waiter's interface.

**API key:** A long, secret string of characters that acts like a password. It identifies who is making a request to the API and ensures only authorized users can access the service.

**Argument:** A value you pass into a function or method to give it the information it needs to do its job. Like specifying "chocolate" when you call the "make a cake" recipe.

**Class:** A blueprint or mold for creating objects. Defines what data an object will hold and what actions it can perform. Named with a capital letter by convention in Python.

**Client (API client):** A pre-built object that handles the details of communicating with an external service. Like a personal assistant who knows the protocol for dealing with a particular organization.

**Comment:** A line in code that starts with `#`. Python ignores it completely. Used to leave notes for human readers.

**Dictionary:** A data structure that stores key-value pairs — like a form with labeled fields. Written with curly braces `{}`. You look up a value by providing its key, similar to looking up a word in a dictionary to find its definition.

**Environment variable:** A named value stored at the operating system level, accessible to any program running on the computer. Used to pass configuration and secrets to programs without hardcoding them.

**Function:** A named, reusable set of instructions. You call a function by writing its name followed by parentheses. Like a recipe you can invoke by name whenever you need it.

**Import:** The act of bringing a tool or library into your current code file so you can use it. Like reaching into a toolbox and placing a specific tool on your workbench.

**Library / Package:** A collection of pre-written Python tools that you can import and use. Someone else wrote and tested the code — you just use it.

**List:** An ordered collection of items, written with square brackets `[]`. Items are accessed by their position (index), starting from 0.

**Method:** A function that lives inside an object. Called using dot notation: `object.method()`.

**`None`:** A special Python value meaning "nothing" or "absent." Used when a variable has no meaningful value to hold.

**Object:** A self-contained unit that bundles together data and the functions that operate on that data. Created from a class blueprint.

**Parameter:** A named slot in a function or method that accepts an argument. Like a labeled field on a form waiting to be filled in.

**`print()`:** A built-in Python function that displays text as output in the notebook. Whatever you put in the parentheses gets shown beneath the cell when it runs.

**SDK (Software Development Kit):** A collection of pre-built tools, libraries, and documentation provided by a service (like Anthropic) to make it easier to build software that uses that service.

**Smoke test:** The simplest possible test run to verify that a system works end to end. Not a thorough test — just enough to confirm nothing is obviously broken.

**Token:** The unit of text that AI language models process. Roughly equivalent to one short word or part of a longer word. Both the input you send and the output Claude generates are measured and billed in tokens.

**Variable:** A named container that holds a value. Like a labeled box — you write the name on the outside and store a value inside. Whenever Python sees the variable's name, it retrieves the value stored in it.

**Zero-indexed:** A counting system where positions start at 0 rather than 1. In Python lists, the first item is at position `[0]`, the second at `[1]`, and so on.

**.env file:** A plain text file (literally named `.env`) in your project folder that stores secret values like API keys as `NAME=value` pairs. Kept private and never shared or committed to version control.