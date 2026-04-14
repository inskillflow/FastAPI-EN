<a id="top"></a>

# Understanding `main.py` — Every Line Explained
## Task Manager API — FastAPI Deep Dive for Absolute Beginners

> Every concept is explained from scratch.
> No prior knowledge of FastAPI, classes, or decorators is assumed.
> Each section can be read independently.

---

## Table of Contents

| # | Concept |
|---|---|
| 1 | [The Big Picture — What does this file do?](#s1) |
| 2 | [The imports — What is `from X import Y`?](#s2) |
| 3 | [The `app` object — What is `FastAPI()`?](#s3) |
| 4 | [Classes — What is a `class`?](#s4) |
| 5 | [Pydantic and `BaseModel` — What is data validation?](#s5) |
| 6 | [Class `TaskCreate` — Line by line](#s6) |
| 7 | [Class `TaskUpdate` — What is `Optional`?](#s7) |
| 8 | [Class `Task` — The full response model](#s8) |
| 9 | [The in-memory database — `tasks_db` and `next_id`](#s9) |
| 10 | [Decorators — What is `@`?](#s10) |
| 11 | [Route 1 — `@app.get("/")` — The root endpoint](#s11) |
| 12 | [Route 2 — `@app.get("/tasks")` — List with filters](#s12) |
| 13 | [Route 3 — `@app.get("/tasks/{task_id}")` — Get one task](#s13) |
| 14 | [Route 4 — `@app.post("/tasks")` — Create a task](#s14) |
| 15 | [Route 5 — `@app.put("/tasks/{task_id}")` — Replace a task](#s15) |
| 16 | [Route 6 — `@app.patch("/tasks/{task_id}")` — Partial update](#s16) |
| 17 | [Route 7 — `@app.delete("/tasks/{task_id}")` — Delete a task](#s17) |
| 18 | [HTTPException — How errors are handled](#s18) |
| 19 | [The full annotated file — everything at once](#s19) |

---

<a id="s1"></a>

<details>
<summary>1 — The Big Picture — What does this file do?</summary>

<br/>

### One sentence

`main.py` is a **web server** that listens for HTTP requests and responds with JSON data about tasks.

---

### The restaurant analogy

Imagine a restaurant:

| Restaurant | API |
|---|---|
| The restaurant building | The server (uvicorn) |
| The menu | The list of endpoints (GET, POST, PUT...) |
| A waiter | A route function (def get_tasks, def create_task...) |
| An order form | A Pydantic model (TaskCreate, TaskUpdate) |
| The kitchen storage | The in-memory dictionary (tasks_db) |
| A dish returned to the customer | A JSON response |

When you run `uvicorn main:app --reload`:
- The restaurant opens its doors
- Waiters (route functions) are standing by
- The storage (tasks_db) is pre-stocked with 4 sample tasks

When you send a request (GET /tasks):
- A customer walks in and says "I want to see the menu of all tasks"
- The waiter (get_tasks function) goes to the storage
- The waiter brings back the list as a JSON response

---

### The flow of a request — step by step

```
Your browser / Postman / curl
         │
         │  sends HTTP request:  GET http://127.0.0.1:8000/tasks
         ▼
   uvicorn (the server)
         │
         │  receives the request, looks at the URL and method
         ▼
   FastAPI (the router)
         │
         │  finds the matching function: get_tasks()
         ▼
   get_tasks() function runs
         │
         │  reads tasks_db, applies filters, builds the list
         ▼
   FastAPI converts the result to JSON
         │
         ▼
   Response sent back: HTTP 200 + JSON body
         │
         ▼
Your browser / Postman / curl receives the response
```

---

### What is JSON?

JSON (JavaScript Object Notation) is just **text formatted as a dictionary**.

Python dictionary:
```python
{"title": "Learn FastAPI", "completed": False, "priority": "high"}
```

JSON (what travels over the internet):
```json
{"title": "Learn FastAPI", "completed": false, "priority": "high"}
```

They look almost identical. The only difference:
- Python uses `False` (capital F) — JSON uses `false` (lowercase)
- Python uses `True` — JSON uses `true`
- Python uses `None` — JSON uses `null`

FastAPI automatically converts between Python dictionaries and JSON.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s2"></a>

<details>
<summary>2 — The imports — What is `from X import Y`?</summary>

<br/>

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional, List
from datetime import datetime
```

---

### What is an import?

An **import** tells Python: "Go find this tool in another file (or library) and bring it here so I can use it."

Think of it like a toolbox. Instead of building a hammer from scratch, you import one from a hardware store.

---

### Line by line

#### Line 1
```python
from fastapi import FastAPI, HTTPException
```

- `from fastapi` → go to the FastAPI library (installed with `pip install fastapi`)
- `import FastAPI` → bring in the main class that creates the web application
- `import HTTPException` → bring in the tool that creates HTTP error responses (like 404)

> **FastAPI** is the name of both the library AND the class inside it. This is common in Python.

---

#### Line 2
```python
from pydantic import BaseModel
```

- `from pydantic` → go to the Pydantic library (installed automatically with FastAPI)
- `import BaseModel` → bring in the base class for creating data models (more on this in section 5)

> **Pydantic** is a data validation library. It checks that your data has the right types and values.

---

#### Line 3
```python
from typing import Optional, List
```

- `from typing` → go to Python's built-in `typing` module
- `import Optional` → a type hint that means "this field can be `None` OR a value"
- `import List` → a type hint that means "a list of items"

> **Type hints** are labels you put on variables to say what type of data they hold. Python does not enforce them at runtime, but FastAPI and Pydantic use them to validate data.

Examples:
```python
Optional[str]   # this can be a string OR None
Optional[bool]  # this can be True/False OR None
List[Task]      # this is a list of Task objects
```

---

#### Line 4
```python
from datetime import datetime
```

- `from datetime` → go to Python's built-in `datetime` module
- `import datetime` → bring in the `datetime` class

This is used in the `_now()` function to get the current date and time:

```python
datetime.now()                          # current date and time as an object
datetime.now().strftime("%Y-%m-%d %H:%M:%S")  # formatted as a string: "2026-03-30 14:22:05"
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>3 — The `app` object — What is `FastAPI()`?</summary>

<br/>

```python
app = FastAPI(
    title="Task Manager API",
    description="Mini CRUD API — GET, POST, PUT, PATCH, DELETE",
    version="1.0.0",
)
```

---

### What is this doing?

`FastAPI()` creates a **web application object** and stores it in the variable `app`.

Think of it as:
> "I am building a new restaurant. I name it 'Task Manager API', I write a description on the sign, and I say it is version 1.0."

From this moment, `app` is the central hub of your API. Every route (endpoint) will be attached to it.

---

### The three parameters

| Parameter | What it does | Where it appears |
|---|---|---|
| `title` | Name of the API | Top of the Swagger UI page at `/docs` |
| `description` | Short explanation of what the API does | Under the title in `/docs` |
| `version` | Version number | Shown in `/docs` and in the OpenAPI JSON at `/openapi.json` |

Go to `http://127.0.0.1:8000/docs` — you will see these values at the top of the page.

---

### Why `app`?

The variable name `app` is a convention — you could name it anything:

```python
my_api = FastAPI(title="Task Manager API")
server = FastAPI(title="Task Manager API")
app = FastAPI(title="Task Manager API")  # ← the convention everyone uses
```

But you must use the same name when you start the server:

```bash
uvicorn main:app --reload
#         ^^^^
#         this must match the variable name in main.py
```

If you renamed it `my_api`, you would start the server with `uvicorn main:my_api --reload`.

---

### What happens internally when you write `FastAPI()`?

`FastAPI()` is a **class being instantiated** (creating an object from a blueprint).

```python
app = FastAPI(...)
```

This is equivalent to saying:
> "Follow the FastAPI blueprint and build me one specific application. Store the result in `app`."

We will explain classes in detail in section 4.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>4 — Classes — What is a `class`?</summary>

<br/>

### The concept in one sentence

A **class** is a **blueprint** for creating objects.
An object created from a class is called an **instance**.

---

### The house analogy

```
Blueprint (class)          House (instance / object)
─────────────────          ─────────────────────────
"A house has:              house1 = House(color="blue", rooms=3)
  - a color                house2 = House(color="red", rooms=5)
  - number of rooms
  - a door"
```

The blueprint is defined once. You can create as many houses (instances) as you want.

---

### A simple Python class example

```python
class Person:
    name: str
    age: int

# Create two instances (two "persons") from the same blueprint
alice = Person()
alice.name = "Alice"
alice.age = 30

bob = Person()
bob.name = "Bob"
bob.age = 25
```

Both `alice` and `bob` follow the same blueprint (`Person`) but have their own data.

---

### Classes in this API

In `main.py`, classes are used in two ways:

**1 — Pydantic models (data blueprints):**

```python
class TaskCreate(BaseModel):
    title: str
    description: str = ""
    completed: bool = False
    priority: str = "medium"
```

This says: "A TaskCreate object always has these 4 fields, with these types."

**2 — FastAPI itself is a class:**

```python
app = FastAPI(title="Task Manager API")
```

`FastAPI` is a class. `app` is one instance of that class.

---

### What does `class TaskCreate(BaseModel)` mean?

```python
class TaskCreate(BaseModel):
```

The `(BaseModel)` part is called **inheritance**.
It means: "TaskCreate is a class that inherits everything from BaseModel."

Think of it as:
> "I want to build a special type of form. I start with the standard form template (`BaseModel`) and add my own fields on top."

`BaseModel` comes from Pydantic. By inheriting from it, `TaskCreate` automatically gets:
- Automatic data validation (checks types)
- Automatic JSON conversion
- Automatic error messages when data is wrong

---

### Instantiating a class

When FastAPI receives a POST request with this JSON body:

```json
{"title": "Study for exam", "priority": "high"}
```

FastAPI automatically creates an instance of `TaskCreate`:

```python
task = TaskCreate(title="Study for exam", priority="high")
# task.title       = "Study for exam"
# task.description = ""       ← default value applied
# task.completed   = False    ← default value applied
# task.priority    = "high"
```

This instance is then passed to your function:

```python
def create_task(task: TaskCreate):  # ← task is the instance
    print(task.title)   # "Study for exam"
    print(task.priority)  # "high"
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s5"></a>

<details>
<summary>5 — Pydantic and `BaseModel` — What is data validation?</summary>

<br/>

### The problem Pydantic solves

Imagine someone sends this JSON to your POST endpoint:

```json
{
  "title": 12345,
  "completed": "maybe",
  "priority": true
}
```

All the types are wrong:
- `title` should be a string — they sent a number
- `completed` should be `true`/`false` — they sent a string
- `priority` should be a string — they sent a boolean

Without Pydantic, you would have to manually check every field. With Pydantic, it is automatic.

---

### What Pydantic does

When you define:

```python
class TaskCreate(BaseModel):
    title: str
    completed: bool = False
    priority: str = "medium"
```

Pydantic automatically:
1. **Checks the type** of each field when data arrives
2. **Converts** if possible (e.g., the string `"true"` becomes `True`)
3. **Raises a 422 error** with a clear message if conversion is impossible
4. **Applies defaults** for missing optional fields

---

### Type annotations — what they mean

```python
title: str          # title must be a string
completed: bool     # completed must be True or False
priority: str       # priority must be a string
id: int             # id must be an integer
```

The `:` after the field name means "this field has this type".

---

### Default values

```python
description: str = ""         # if not provided, use empty string
completed: bool = False        # if not provided, use False
priority: str = "medium"       # if not provided, use "medium"
```

The `= value` after the type means "use this as the default if the field is not sent".

Fields **without** a default value are **required**:

```python
title: str   # NO default → REQUIRED → 422 error if missing
```

---

### What `BaseModel` gives you for free

By writing `class TaskCreate(BaseModel)`, your class automatically gets:

| Feature | What it means |
|---|---|
| `.model_dump()` | Converts the object to a Python dictionary |
| `.model_json()` | Converts the object to a JSON string |
| Automatic validation | Checks types when you create an instance |
| Automatic 422 errors | FastAPI sends detailed error if data is invalid |
| Swagger documentation | FastAPI reads your fields and shows them in `/docs` |

---

### Seeing validation in action

Send this broken JSON to POST /tasks:

```json
{"title": 12345}
```

Pydantic tries to convert `12345` (integer) to a string. It succeeds — Pydantic is forgiving about this specific conversion.

Send this:

```json
{"title": "My task", "completed": "maybe"}
```

Pydantic cannot convert `"maybe"` to a boolean. You get:

```json
{
  "detail": [{
    "type": "bool_parsing",
    "loc": ["body", "completed"],
    "msg": "Input should be a valid boolean..."
  }]
}
```

This is Pydantic catching the error before it even reaches your function.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>6 — Class `TaskCreate` — Line by line</summary>

<br/>

```python
class TaskCreate(BaseModel):
    """Fields required to create a task."""
    title: str
    description: str = ""
    completed: bool = False
    priority: str = "medium"   # low | medium | high
```

---

### Line 1 — The class declaration

```python
class TaskCreate(BaseModel):
```

| Part | Meaning |
|---|---|
| `class` | Python keyword — "I am defining a blueprint" |
| `TaskCreate` | Name of this blueprint |
| `(BaseModel)` | It inherits from Pydantic's BaseModel — gets validation for free |
| `:` | Starts the class body (everything indented below belongs to this class) |

> **Why is it called `TaskCreate`?**
> Because it is used when **creating** a task. It contains the fields a user must send when they want to add a new task.

---

### Line 2 — The docstring

```python
"""Fields required to create a task."""
```

A **docstring** is a description of the class written in triple quotes.
It appears in the Swagger UI documentation automatically.
It is for humans to read — Python ignores it when running the code.

---

### Line 3 — The `title` field

```python
title: str
```

| Part | Meaning |
|---|---|
| `title` | The name of this field |
| `:` | "this field has the type..." |
| `str` | "...a string (text)" |
| *(no `= default`)* | No default → **REQUIRED** — must be provided |

This is the only **required** field. If a client sends a POST request without `title`, the server returns 422.

---

### Line 4 — The `description` field

```python
description: str = ""
```

| Part | Meaning |
|---|---|
| `description` | Field name |
| `str` | Must be a string |
| `= ""` | Default value is an **empty string** — if not provided, it is `""` |

This field is **optional**. You can send it or not.

---

### Line 5 — The `completed` field

```python
completed: bool = False
```

| Part | Meaning |
|---|---|
| `completed` | Field name |
| `bool` | Must be a **boolean** — `true` or `false` in JSON, `True` or `False` in Python |
| `= False` | Default is `False` — a new task is not completed by default |

---

### Line 6 — The `priority` field

```python
priority: str = "medium"   # low | medium | high
```

| Part | Meaning |
|---|---|
| `priority` | Field name |
| `str` | Must be a string |
| `= "medium"` | Default is `"medium"` |
| `# low \| medium \| high` | A comment for developers — the accepted values |

> Note: the comment `# low | medium | high` is just a hint for humans. Python does NOT enforce these values.
> If someone sends `priority: "pizza"`, it will be accepted.
> To enforce allowed values, you would use a Python `Enum` class.

---

### Summary — what `TaskCreate` accepts

| Field | Required? | Type | Default |
|---|---|---|---|
| `title` | **YES** | string | *(none)* |
| `description` | No | string | `""` |
| `completed` | No | boolean | `false` |
| `priority` | No | string | `"medium"` |

Minimum valid body:
```json
{"title": "My task"}
```

Full body:
```json
{
  "title": "My task",
  "description": "Details here",
  "completed": false,
  "priority": "high"
}
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s7"></a>

<details>
<summary>7 — Class `TaskUpdate` — What is `Optional`?</summary>

<br/>

```python
class TaskUpdate(BaseModel):
    """All fields optional — used for PATCH (partial update)."""
    title: Optional[str] = None
    description: Optional[str] = None
    completed: Optional[bool] = None
    priority: Optional[str] = None
```

---

### Why a separate class for updates?

When you send a PATCH request, you only want to change **some** fields — not all of them.

For example, to mark a task as completed:
```json
{"completed": true}
```

You only send ONE field. The other three (`title`, `description`, `priority`) are not in the body.

If you used `TaskCreate` for PATCH, Python would complain: "`title` is required but not provided!"

`TaskUpdate` solves this by making **every field optional**.

---

### What is `Optional[str]`?

```python
title: Optional[str] = None
```

`Optional[str]` means: "this field can be either a **string** OR **None**."

None is Python's way of saying "nothing" or "not provided".

```python
title: str           # MUST be a string — cannot be None
title: Optional[str] # CAN be a string OR None
```

---

### Why `= None` instead of `= ""`?

```python
title: Optional[str] = None
```

Setting the default to `None` (instead of `""`) is a trick used in the PATCH endpoint:

```python
for field, value in update.model_dump(exclude_none=True).items():
    tasks_db[task_id][field] = value
```

`exclude_none=True` means: "when converting to a dict, skip any field that is `None`."

So if you send `{"completed": true}`:
- `title` → `None` → **skipped** (not updated)
- `description` → `None` → **skipped** (not updated)
- `completed` → `True` → **included** (updated)
- `priority` → `None` → **skipped** (not updated)

If we had used `= ""` as the default:
- `title` → `""` → **NOT skipped** — the title would be overwritten with an empty string!

`None` is the only value that `exclude_none=True` skips. This is the key design choice.

---

### Visual comparison

| Scenario | `TaskCreate` | `TaskUpdate` |
|---|---|---|
| `title` not sent | ❌ 422 error (required) | ✅ accepted — stays `None`, skipped |
| `completed` not sent | ✅ defaults to `False` | ✅ stays `None`, skipped |
| Use case | POST and PUT | PATCH only |

---

### The difference in practice

**POST with TaskCreate — all fields or error:**
```json
{}   →   422: title is required
{"title": "x"}   →   201: works, others default to "" / false / "medium"
```

**PATCH with TaskUpdate — any subset is fine:**
```json
{}                        →   200: nothing changes (all None, all skipped)
{"completed": true}       →   200: only completed changes
{"title": "x", "priority": "low"}   →   200: only title and priority change
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s8"></a>

<details>
<summary>8 — Class `Task` — The full response model</summary>

<br/>

```python
class Task(BaseModel):
    """Full task object returned by the API."""
    id: int
    title: str
    description: str
    completed: bool
    priority: str
    created_at: str
```

---

### What is this class for?

`Task` is the **response model** — it defines what a task looks like **when the API sends it back to the client**.

It is used as `response_model=Task` or `response_model=List[Task]` in the route decorators:

```python
@app.get("/tasks/{task_id}", response_model=Task)
@app.get("/tasks", response_model=List[Task])
```

This tells FastAPI: "When this endpoint returns data, it must look like a `Task` object."

---

### Why is it different from `TaskCreate`?

`TaskCreate` is what the **client sends to the server** (input).
`Task` is what the **server sends to the client** (output).

| Field | In `TaskCreate`? | In `Task`? | Who sets it? |
|---|---|---|---|
| `title` | ✅ | ✅ | Client |
| `description` | ✅ | ✅ | Client |
| `completed` | ✅ | ✅ | Client |
| `priority` | ✅ | ✅ | Client |
| `id` | ❌ | ✅ | **Server** (auto-assigned) |
| `created_at` | ❌ | ✅ | **Server** (auto-set) |

`id` and `created_at` are **never sent by the client**. The server adds them automatically. That is why they are not in `TaskCreate`.

---

### Field types in `Task`

```python
id: int           # integer — 1, 2, 3, 4, 5...
title: str        # string — "Learn FastAPI"
description: str  # string — "Read the docs"
completed: bool   # boolean — True or False
priority: str     # string — "low", "medium", "high"
created_at: str   # string — "2026-03-30 14:22:05"
```

All fields are **required** — no defaults, no `Optional`. This makes sense because `Task` represents a complete record. You should never return an incomplete task.

---

### What `response_model` does

When you write:

```python
@app.get("/tasks/{task_id}", response_model=Task)
def get_task(task_id: int):
    return tasks_db[task_id]  # returns a raw dict
```

FastAPI takes the raw dict from `tasks_db`, validates it against the `Task` model, and converts it to JSON. If the dict has extra fields that are not in `Task`, they are **filtered out** automatically.

This protects you from accidentally returning sensitive internal data that you did not intend to expose.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>9 — The in-memory database — `tasks_db` and `next_id`</summary>

<br/>

```python
def _now() -> str:
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")

tasks_db: dict[int, dict] = {
    1: {"id": 1, "title": "Learn FastAPI", ...},
    2: {"id": 2, "title": "Build a REST API", ...},
    3: {"id": 3, "title": "Write unit tests", ...},
    4: {"id": 4, "title": "Deploy to production", ...},
}

next_id = 5
```

---

### The `_now()` helper function

```python
def _now() -> str:
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")
```

| Part | Meaning |
|---|---|
| `def _now()` | Define a function named `_now` — the `_` prefix is a convention meaning "this is a private helper, only used internally" |
| `-> str` | This function **returns** a string — this is a type hint on the return value |
| `datetime.now()` | Get the current date and time as a `datetime` object |
| `.strftime(...)` | Format the datetime as a string in a specific pattern |
| `"%Y-%m-%d %H:%M:%S"` | The format pattern: year-month-day hour:minute:second |

Example output:
```
"2026-03-30 14:22:05"
```

This is called every time a new task is created to stamp the creation time.

---

### The `tasks_db` dictionary

```python
tasks_db: dict[int, dict] = {
    1: {
        "id": 1,
        "title": "Learn FastAPI",
        "description": "Read the official FastAPI documentation",
        "completed": False,
        "priority": "high",
        "created_at": "2026-03-19 08:00:00",
    },
    ...
}
```

**What is a dictionary?**

A dictionary is a collection of **key: value** pairs.

```python
my_dict = {
    "name": "Alice",   # key: "name", value: "Alice"
    "age": 30,         # key: "age",  value: 30
}
```

**What is `dict[int, dict]`?**

This is a type hint:
```python
tasks_db: dict[int, dict]
```
- The **key** is an `int` (the task ID: 1, 2, 3, 4...)
- The **value** is a `dict` (the task data: title, description, etc.)

Think of it as an **index in a book**:
- Key 1 → points to task 1's data
- Key 2 → points to task 2's data
- Key 99 → does not exist → triggers a 404 error

**Why a dictionary and not a list?**

With a list, to find task ID 3 you would have to loop through all tasks:
```python
for task in tasks_list:
    if task["id"] == 3:
        return task  # slow for large data
```

With a dictionary, access is instant:
```python
tasks_db[3]  # instantly returns task 3 — no loop needed
```

---

### Why does the data reset when the server restarts?

Because `tasks_db` is a **Python variable in memory** — it only exists while the Python process is running.

When you restart the server (`Ctrl+C` then `uvicorn main:app --reload`), Python starts from scratch and `tasks_db` is re-created with the original 4 tasks.

A real application would use a **database** (PostgreSQL, MySQL, SQLite) which persists data to disk. This project uses an in-memory dict to keep things simple.

---

### The `next_id` counter

```python
next_id = 5  # auto-increment counter
```

This is a simple counter. Every time a task is created:
1. The new task gets `id = next_id` (currently 5)
2. `next_id` increases by 1 (`next_id += 1`)
3. The next task will get ID 6, then 7, then 8...

This simulates how a real database uses **auto-increment primary keys**.

The `global` keyword (seen later in `create_task`) is needed to modify this counter from inside a function:

```python
def create_task(task: TaskCreate):
    global next_id          # "I want to modify the variable outside this function"
    new_task = {"id": next_id, ...}
    next_id += 1            # increment for next time
```

Without `global next_id`, Python would think you are creating a **new local variable** named `next_id` inside the function, and the original counter would never change.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>10 — Decorators — What is `@`?</summary>

<br/>

### The most important concept in this file

Every route in this file starts with a line like:

```python
@app.get("/tasks")
@app.post("/tasks")
@app.put("/tasks/{task_id}")
@app.delete("/tasks/{task_id}")
```

These lines starting with `@` are called **decorators**.

---

### What is a decorator in plain English?

A decorator is a line that **modifies or enhances the function below it**.

Think of it as a **label on a box**:
- The box is the function (`def get_tasks(...)`)
- The label is the decorator (`@app.get("/tasks")`)
- The label tells FastAPI: "This function handles GET requests to `/tasks`"

---

### The doorbell analogy

Imagine a large office building with many doors:

```
Door 1: GET /tasks         → rings the get_tasks() function
Door 2: GET /tasks/1       → rings the get_task() function
Door 3: POST /tasks        → rings the create_task() function
Door 4: DELETE /tasks/4    → rings the delete_task() function
```

The decorator `@app.get("/tasks")` above `get_tasks()` is like **installing a doorbell on door 1 and connecting it to the get_tasks function**.

When someone arrives at `GET /tasks` (knocks on door 1), FastAPI rings the doorbell and `get_tasks()` runs.

---

### The anatomy of a decorator

```python
@app.get("/tasks", response_model=List[Task], summary="Get all tasks")
def get_tasks(...):
```

Let's break down `@app.get("/tasks", response_model=List[Task], summary="Get all tasks")`:

| Part | Meaning |
|---|---|
| `@` | "This is a decorator" |
| `app` | The FastAPI application object |
| `.get` | The HTTP method — this handles GET requests |
| `("/tasks"` | The URL path this handles |
| `response_model=List[Task]` | "Validate and format the response as a list of Task objects" |
| `summary="..."` | Short description shown in Swagger UI |
| `)` | End of the decorator |

---

### The HTTP methods and their decorators

| HTTP Method | FastAPI Decorator | What it does |
|---|---|---|
| GET | `@app.get(...)` | Read data — never modifies anything |
| POST | `@app.post(...)` | Create a new record |
| PUT | `@app.put(...)` | Replace an existing record entirely |
| PATCH | `@app.patch(...)` | Update specific fields of a record |
| DELETE | `@app.delete(...)` | Remove a record |

---

### What happens WITHOUT a decorator?

```python
def get_tasks():
    return list(tasks_db.values())
```

This is just a regular Python function. FastAPI does not know it exists. It will never be called when someone sends a request.

```python
@app.get("/tasks")
def get_tasks():
    return list(tasks_db.values())
```

NOW FastAPI knows: "When someone sends `GET /tasks`, call `get_tasks()`."

The decorator **registers** the function in FastAPI's routing table.

---

### The `response_model` parameter

```python
@app.get("/tasks", response_model=List[Task])
```

`response_model` tells FastAPI:
1. Validate that the returned data matches the `Task` structure
2. Filter out any extra fields not in `Task`
3. Convert to JSON with the correct types
4. Show the expected response structure in the Swagger UI

---

### The `status_code` parameter

```python
@app.post("/tasks", response_model=Task, status_code=201)
@app.delete("/tasks/{task_id}", status_code=204)
```

By default, FastAPI returns `200 OK`. 
- `status_code=201` changes it to `201 Created` — appropriate for POST
- `status_code=204` changes it to `204 No Content` — appropriate for DELETE

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>11 — Route 1 — `@app.get("/")` — The root endpoint</summary>

<br/>

```python
@app.get("/", summary="Root — API info")
def root():
    return {
        "message": "Task Manager API is running",
        "docs": "http://127.0.0.1:8000/docs",
        "total_tasks": len(tasks_db),
    }
```

---

### The decorator

```python
@app.get("/", summary="Root — API info")
```

- `@app.get` → handles GET requests
- `"/"` → the URL is just the root — `http://127.0.0.1:8000/`
- `summary=` → text shown in Swagger UI as the endpoint description
- No `response_model` → FastAPI will not validate the response — whatever the function returns is sent as-is

---

### The function

```python
def root():
```

- `def` → defines a function
- `root` → name of the function (could be anything — but `root` makes sense for the root URL)
- `()` → no parameters — this function takes no input

---

### The return value

```python
return {
    "message": "Task Manager API is running",
    "docs": "http://127.0.0.1:8000/docs",
    "total_tasks": len(tasks_db),
}
```

This returns a **Python dictionary**. FastAPI automatically converts it to JSON.

| Key | Value | What it does |
|---|---|---|
| `"message"` | "Task Manager API is running" | A friendly confirmation message |
| `"docs"` | "http://127.0.0.1:8000/docs" | Reminder of where the Swagger UI is |
| `"total_tasks"` | `len(tasks_db)` | Counts how many tasks are currently in memory |

**What is `len(tasks_db)`?**

`len()` is a Python built-in function that counts elements.

```python
len(tasks_db)  # returns the number of key-value pairs in the dictionary
               # = 4 at startup, more if tasks were added, fewer if deleted
```

---

### The full flow

```
Request:  GET http://127.0.0.1:8000/
              ↓
FastAPI finds: @app.get("/") matches → calls root()
              ↓
root() runs: builds the dict
              ↓
FastAPI converts dict to JSON
              ↓
Response: HTTP 200 OK
Body: {"message": "Task Manager API is running", "docs": "...", "total_tasks": 4}
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s12"></a>

<details>
<summary>12 — Route 2 — `@app.get("/tasks")` — List with filters</summary>

<br/>

```python
@app.get("/tasks", response_model=List[Task], summary="Get all tasks")
def get_tasks(completed: Optional[bool] = None, priority: Optional[str] = None):
    """
    Returns all tasks.
    - Filter by status:   GET /tasks?completed=true
    - Filter by priority: GET /tasks?priority=high
    - Combine filters:    GET /tasks?completed=false&priority=high
    """
    tasks = list(tasks_db.values())

    if completed is not None:
        tasks = [t for t in tasks if t["completed"] == completed]

    if priority is not None:
        tasks = [t for t in tasks if t["priority"] == priority]

    return tasks
```

---

### The decorator

```python
@app.get("/tasks", response_model=List[Task], summary="Get all tasks")
```

- `response_model=List[Task]` → "the response is a **list** of `Task` objects"
- `List[Task]` = a list where every item must be a `Task`

---

### The function parameters — query parameters

```python
def get_tasks(completed: Optional[bool] = None, priority: Optional[str] = None):
```

These two parameters become **query parameters** automatically.

FastAPI rule: **if a function parameter is not in the URL path, it becomes a query parameter.**

| Parameter | Type | Default | Query string |
|---|---|---|---|
| `completed` | `Optional[bool]` | `None` | `?completed=true` or `?completed=false` |
| `priority` | `Optional[str]` | `None` | `?priority=high` |

When someone sends `GET /tasks?completed=true`, FastAPI:
1. Sees `completed=true` in the URL
2. Finds the `completed` parameter in `get_tasks()`
3. Converts the string `"true"` to the Python boolean `True`
4. Calls `get_tasks(completed=True, priority=None)`

When someone sends `GET /tasks` (no filters):
- FastAPI calls `get_tasks(completed=None, priority=None)`

---

### Line 1 — Get all tasks as a list

```python
tasks = list(tasks_db.values())
```

- `tasks_db.values()` → returns all the values (the task dicts) from the dictionary
- `list(...)` → converts them from a `dict_values` object into a regular Python list

Before this line:
```python
tasks_db = {1: {"id":1,...}, 2: {"id":2,...}, 3: {"id":3,...}, 4: {"id":4,...}}
```

After this line:
```python
tasks = [{"id":1,...}, {"id":2,...}, {"id":3,...}, {"id":4,...}]
```

Now `tasks` is a plain list — easy to filter.

---

### Lines 2-3 — The completed filter

```python
if completed is not None:
    tasks = [t for t in tasks if t["completed"] == completed]
```

**Line 1:**
```python
if completed is not None:
```

- If `completed` was not provided in the URL, it is `None`
- `is not None` checks: "was a filter actually provided?"
- If `completed=None` → skip this block — do not filter
- If `completed=True` or `completed=False` → enter this block and filter

**Line 2:**
```python
tasks = [t for t in tasks if t["completed"] == completed]
```

This is a **list comprehension** — a compact way to filter a list.

Longhand version:
```python
filtered = []
for t in tasks:
    if t["completed"] == completed:
        filtered.append(t)
tasks = filtered
```

Short version (same result):
```python
tasks = [t for t in tasks if t["completed"] == completed]
```

Read it as: "Give me all tasks `t` from `tasks` where `t["completed"]` equals the requested value."

---

### Lines 4-5 — The priority filter

```python
if priority is not None:
    tasks = [t for t in tasks if t["priority"] == priority]
```

Same logic — filters tasks by priority if the filter was provided.

If both filters are provided (e.g., `?completed=false&priority=high`):
- First the completed filter runs → reduces the list
- Then the priority filter runs on the already-reduced list → reduces further

---

### The return

```python
return tasks
```

Returns the filtered list. FastAPI validates each item against `Task` (via `response_model=List[Task]`) and converts to JSON.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s13"></a>

<details>
<summary>13 — Route 3 — `@app.get("/tasks/{task_id}")` — Get one task</summary>

<br/>

```python
@app.get("/tasks/{task_id}", response_model=Task, summary="Get one task by ID")
def get_task(task_id: int):
    """Returns a single task. Returns 404 if the task does not exist."""
    if task_id not in tasks_db:
        raise HTTPException(
            status_code=404,
            detail=f"Task with ID {task_id} not found",
        )
    return tasks_db[task_id]
```

---

### The URL path parameter `{task_id}`

```python
@app.get("/tasks/{task_id}", ...)
```

The `{task_id}` in the URL is a **path parameter** — a variable part of the URL.

When a request comes in for `GET /tasks/3`:
- FastAPI sees the URL matches `/tasks/{task_id}`
- It extracts `3` from the URL
- It assigns `task_id = 3`
- It calls `get_task(task_id=3)`

The curly braces `{}` in the route decorator tell FastAPI: "this part of the URL is variable — capture it."

---

### The function parameter

```python
def get_task(task_id: int):
```

- `task_id` → the name must match the `{task_id}` in the decorator
- `: int` → FastAPI automatically converts the URL string `"3"` to the integer `3`

If someone sends `GET /tasks/abc`:
- FastAPI tries to convert `"abc"` to an `int`
- It fails → FastAPI returns `422 Unprocessable Entity` automatically

---

### The `if` check — does the task exist?

```python
if task_id not in tasks_db:
    raise HTTPException(
        status_code=404,
        detail=f"Task with ID {task_id} not found",
    )
```

**`task_id not in tasks_db`:**

The `in` operator checks if a key exists in a dictionary:
```python
1 in tasks_db   # True  — task 1 exists
99 in tasks_db  # False — task 99 does not exist
```

`not in` is the opposite:
```python
99 not in tasks_db  # True — task 99 does NOT exist
```

If the task does not exist, we `raise HTTPException`.

**What is `raise`?**

`raise` tells Python to stop the function immediately and throw an error.
Instead of crashing with an ugly Python error, `HTTPException` creates a clean HTTP error response.

**What is `f"Task with ID {task_id} not found"`?**

This is an **f-string** (formatted string). The `f` before the quotes allows you to embed variables directly:

```python
task_id = 99
f"Task with ID {task_id} not found"
# Result: "Task with ID 99 not found"
```

The `{task_id}` inside the string is replaced by the actual value of the variable.

---

### The return

```python
return tasks_db[task_id]
```

`tasks_db[task_id]` looks up the task by its ID in the dictionary and returns the raw dict.

FastAPI validates it against `response_model=Task` and converts it to JSON.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s14"></a>

<details>
<summary>14 — Route 4 — `@app.post("/tasks")` — Create a task</summary>

<br/>

```python
@app.post("/tasks", response_model=Task, status_code=201, summary="Create a new task")
def create_task(task: TaskCreate):
    """
    Creates a new task and returns it with its assigned ID.
    Required field: **title**
    Optional fields: description, completed, priority
    """
    global next_id
    new_task = {
        "id": next_id,
        **task.model_dump(),
        "created_at": _now(),
    }
    tasks_db[next_id] = new_task
    next_id += 1
    return new_task
```

---

### The decorator

```python
@app.post("/tasks", response_model=Task, status_code=201, ...)
```

- `@app.post` → handles POST requests
- `status_code=201` → overrides the default `200` — a successful creation returns `201 Created`

---

### The function parameter — the request body

```python
def create_task(task: TaskCreate):
```

FastAPI rule: **if a parameter type is a Pydantic model, it reads the JSON body.**

When the client sends:
```json
{"title": "Study for exam", "priority": "high"}
```

FastAPI:
1. Reads the JSON body
2. Creates a `TaskCreate` instance with those values
3. Calls `create_task(task=TaskCreate(title="Study for exam", priority="high"))`

Inside the function, `task` is a `TaskCreate` object:
- `task.title` → `"Study for exam"`
- `task.description` → `""` (default)
- `task.completed` → `False` (default)
- `task.priority` → `"high"`

---

### Line 1 — `global next_id`

```python
global next_id
```

This tells Python: "The `next_id` I am using inside this function is the one declared at the module level (outside all functions), not a new local variable."

Without this line, the `next_id += 1` on the last line would create a local variable and the global counter would never increment.

---

### Lines 2-6 — Building the new task dict

```python
new_task = {
    "id": next_id,
    **task.model_dump(),
    "created_at": _now(),
}
```

This builds a Python dictionary for the new task.

**`"id": next_id`** → assign the current counter value as the ID

**`**task.model_dump()`** → this is the most interesting line:

- `task.model_dump()` converts the `TaskCreate` Pydantic object into a plain Python dictionary:
  ```python
  task.model_dump()
  # Result: {"title": "Study for exam", "description": "", "completed": False, "priority": "high"}
  ```

- `**` before it is the **dictionary unpacking operator** — it "spreads" all key-value pairs into the new dict:
  ```python
  {
      "id": 5,
      **{"title": "Study for exam", "description": "", "completed": False, "priority": "high"},
      "created_at": "2026-03-30 14:22:05"
  }
  ```
  Is exactly equivalent to:
  ```python
  {
      "id": 5,
      "title": "Study for exam",
      "description": "",
      "completed": False,
      "priority": "high",
      "created_at": "2026-03-30 14:22:05"
  }
  ```

**`"created_at": _now()`** → calls the `_now()` helper to get the current timestamp

---

### Line 7 — Save to the database

```python
tasks_db[next_id] = new_task
```

Adds the new task to the in-memory dictionary with `next_id` as its key.

---

### Line 8 — Increment the counter

```python
next_id += 1
```

`next_id += 1` is shorthand for `next_id = next_id + 1`.

The counter increases so the next task gets the next number.

---

### Line 9 — Return the new task

```python
return new_task
```

Returns the newly created task (including the assigned `id` and `created_at`).

FastAPI validates it against `response_model=Task` and sends it with status `201 Created`.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s15"></a>

<details>
<summary>15 — Route 5 — `@app.put("/tasks/{task_id}")` — Replace a task</summary>

<br/>

```python
@app.put("/tasks/{task_id}", response_model=Task, summary="Replace a task entirely (PUT)")
def replace_task(task_id: int, task: TaskCreate):
    """
    Replaces the entire task with the provided data.
    All fields are overwritten — title is required.
    Returns 404 if the task does not exist.
    """
    if task_id not in tasks_db:
        raise HTTPException(
            status_code=404,
            detail=f"Task with ID {task_id} not found",
        )
    updated_task = {
        "id": task_id,
        **task.model_dump(),
        "created_at": tasks_db[task_id]["created_at"],
    }
    tasks_db[task_id] = updated_task
    return updated_task
```

---

### Two parameters: path + body

```python
def replace_task(task_id: int, task: TaskCreate):
```

FastAPI handles two kinds of parameters here at the same time:

| Parameter | Where it comes from | Type |
|---|---|---|
| `task_id` | URL path `/tasks/{task_id}` | `int` |
| `task` | JSON body | `TaskCreate` (Pydantic model) |

FastAPI is smart enough to know:
- `task_id` matches `{task_id}` in the URL → path parameter
- `task: TaskCreate` is a Pydantic model → read it from the JSON body

---

### The 404 check

```python
if task_id not in tasks_db:
    raise HTTPException(status_code=404, detail=f"Task with ID {task_id} not found")
```

Same pattern as `get_task`. Cannot replace something that does not exist.

---

### Building the updated task

```python
updated_task = {
    "id": task_id,
    **task.model_dump(),
    "created_at": tasks_db[task_id]["created_at"],
}
```

Almost the same as `create_task` but with two differences:

1. **`"id": task_id`** → uses the existing ID from the URL (not the counter)
2. **`"created_at": tasks_db[task_id]["created_at"]`** → preserves the original creation date

Why preserve `created_at`?
- The task was created at a specific time
- You are only **replacing** the content — not creating a new task
- The creation date should remain the original one

`tasks_db[task_id]["created_at"]` reads the original `created_at` value from the current task before overwriting it.

---

### Saving and returning

```python
tasks_db[task_id] = updated_task  # overwrites the old dict with the new one
return updated_task
```

The entire old task is replaced. Nothing from the old task survives except `id` and `created_at`.

---

### PUT vs POST — the key difference

| | POST | PUT |
|---|---|---|
| URL | `/tasks` | `/tasks/{task_id}` |
| ID | Assigned by server | Provided in URL |
| `created_at` | Generated now | Preserved from original |
| Counter | `next_id += 1` | Not touched |
| Purpose | Create NEW | Replace EXISTING |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s16"></a>

<details>
<summary>16 — Route 6 — `@app.patch("/tasks/{task_id}")` — Partial update</summary>

<br/>

```python
@app.patch("/tasks/{task_id}", response_model=Task, summary="Partially update a task (PATCH)")
def update_task(task_id: int, update: TaskUpdate):
    """
    Updates only the provided fields. Unspecified fields are left unchanged.
    Returns 404 if the task does not exist.
    """
    if task_id not in tasks_db:
        raise HTTPException(
            status_code=404,
            detail=f"Task with ID {task_id} not found",
        )
    for field, value in update.model_dump(exclude_none=True).items():
        tasks_db[task_id][field] = value
    return tasks_db[task_id]
```

---

### The `TaskUpdate` parameter

```python
def update_task(task_id: int, update: TaskUpdate):
```

Uses `TaskUpdate` (not `TaskCreate`) — because with PATCH, every field is optional.

If the client sends `{"completed": true}`:
- `update.title` → `None`
- `update.description` → `None`
- `update.completed` → `True`
- `update.priority` → `None`

---

### The most important line — the PATCH logic

```python
for field, value in update.model_dump(exclude_none=True).items():
    tasks_db[task_id][field] = value
```

Let's break this down step by step.

**Step 1: `update.model_dump()`**

Converts the `TaskUpdate` object to a dictionary:
```python
# If client sent: {"completed": true}
update.model_dump()
# Result: {"title": None, "description": None, "completed": True, "priority": None}
```

**Step 2: `update.model_dump(exclude_none=True)`**

The `exclude_none=True` argument removes all keys whose value is `None`:
```python
update.model_dump(exclude_none=True)
# Result: {"completed": True}
```

Only the fields that were actually provided survive.

**Step 3: `.items()`**

`.items()` on a dictionary returns a list of `(key, value)` pairs:
```python
{"completed": True}.items()
# Result: [("completed", True)]
```

**Step 4: `for field, value in ...`**

Loop through each (key, value) pair:
```python
for field, value in [("completed", True)]:
    # field = "completed"
    # value = True
```

**Step 5: `tasks_db[task_id][field] = value`**

Update only that specific field in the stored task:
```python
tasks_db[3]["completed"] = True  # only this field changes
```

**The full effect:**

If the current task 3 is:
```python
{"id": 3, "title": "Write unit tests", "completed": False, "priority": "medium", ...}
```

After the loop with `{"completed": true}`:
```python
{"id": 3, "title": "Write unit tests", "completed": True, "priority": "medium", ...}
```

Only `completed` changed. The loop touched only the fields that were in the body.

---

### Return

```python
return tasks_db[task_id]
```

Returns the updated task **from the database** (not from `update`) — so you see the complete task with all its fields.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s17"></a>

<details>
<summary>17 — Route 7 — `@app.delete("/tasks/{task_id}")` — Delete a task</summary>

<br/>

```python
@app.delete("/tasks/{task_id}", status_code=204, summary="Delete a task")
def delete_task(task_id: int):
    """
    Deletes a task permanently.
    Returns 204 No Content on success.
    Returns 404 if the task does not exist.
    """
    if task_id not in tasks_db:
        raise HTTPException(
            status_code=404,
            detail=f"Task with ID {task_id} not found",
        )
    del tasks_db[task_id]
```

---

### The decorator

```python
@app.delete("/tasks/{task_id}", status_code=204, summary="Delete a task")
```

- `@app.delete` → handles DELETE requests
- `status_code=204` → success response is `204 No Content` (empty body)
- No `response_model` → nothing is returned after a delete

---

### The function

```python
def delete_task(task_id: int):
```

Only one parameter: `task_id` from the URL path.
No body parameter needed — you just need the ID of what to delete.

---

### The 404 check

```python
if task_id not in tasks_db:
    raise HTTPException(status_code=404, detail=f"Task with ID {task_id} not found")
```

Cannot delete something that does not exist.

---

### The deletion

```python
del tasks_db[task_id]
```

`del` is a Python keyword that **removes** a key-value pair from a dictionary.

```python
tasks_db = {1: {...}, 2: {...}, 3: {...}, 4: {...}}
del tasks_db[4]
tasks_db = {1: {...}, 2: {...}, 3: {...}}   # key 4 is gone
```

After this line, task 4 no longer exists in memory.

---

### Why no `return` statement?

```python
def delete_task(task_id: int):
    ...
    del tasks_db[task_id]
    # no return
```

There is nothing to return after a deletion — the task no longer exists.

FastAPI sees the function returns nothing and the status code is `204 No Content` — it sends an empty response body.

This is intentional and correct. `204 No Content` + empty body = successful deletion.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s18"></a>

<details>
<summary>18 — HTTPException — How errors are handled</summary>

<br/>

```python
raise HTTPException(
    status_code=404,
    detail=f"Task with ID {task_id} not found",
)
```

---

### What is `HTTPException`?

`HTTPException` is a special Python exception (error class) provided by FastAPI.

When you `raise` it inside a route function:
1. The function stops immediately — no more code runs
2. FastAPI catches the exception
3. FastAPI builds a JSON error response and sends it to the client

Without `HTTPException`, your only option would be to return a normal response with a 200 status code but include an error message inside — which is wrong. Status codes exist precisely to communicate success or failure.

---

### `raise` vs `return`

```python
# WRONG way to handle errors:
def get_task(task_id: int):
    if task_id not in tasks_db:
        return {"error": "not found"}  # returns 200 OK — misleading!

# CORRECT way:
def get_task(task_id: int):
    if task_id not in tasks_db:
        raise HTTPException(status_code=404, detail="not found")  # returns 404
```

`return` sends a successful response and keeps going (well, it stops the function but the request is marked as successful).
`raise` stops the function AND signals that something went wrong.

---

### The two parameters

```python
raise HTTPException(
    status_code=404,
    detail=f"Task with ID {task_id} not found",
)
```

| Parameter | Value | Meaning |
|---|---|---|
| `status_code` | `404` | The HTTP error code to return |
| `detail` | `f"Task with ID {task_id} not found"` | The error message included in the JSON body |

The client receives:

```json
{
    "detail": "Task with ID 99 not found"
}
```

---

### HTTP error codes used in this API

| Code | Name | When it is raised |
|---|---|---|
| `404` | Not Found | The task ID does not exist in `tasks_db` |
| `422` | Unprocessable Entity | Pydantic validation failed — raised automatically by FastAPI |

---

### FastAPI's automatic 422

You never write `raise HTTPException(status_code=422, ...)` in this file.
FastAPI raises 422 automatically when a Pydantic model validation fails.

For example, if someone sends:
```json
{"title": "My task", "completed": "yes"}
```

Pydantic tries to convert `"yes"` to a boolean → fails → FastAPI raises 422 automatically with a detailed message. You do not need to write any code for this.

---

### Summary of all status codes in this API

| Code | Name | Trigger | Manual or automatic? |
|---|---|---|---|
| 200 | OK | GET, PUT, PATCH success | Automatic (default) |
| 201 | Created | POST success | Manual (`status_code=201` in decorator) |
| 204 | No Content | DELETE success | Manual (`status_code=204` in decorator) |
| 404 | Not Found | ID not in `tasks_db` | Manual (`raise HTTPException(404)`) |
| 422 | Unprocessable Entity | Pydantic validation error | Automatic (FastAPI handles it) |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s19"></a>

<details>
<summary>19 — The full annotated file — every line commented</summary>

<br/>

> This is the complete `main.py` file with a comment above every meaningful line.
> Read it from top to bottom as a recap of everything covered in this document.

```python
# ─────────────────────────────────────────────────────────────────────────────
# IMPORTS — bring in external tools
# ─────────────────────────────────────────────────────────────────────────────

# FastAPI  → the web framework (creates the app, handles routing)
# HTTPException → tool to send HTTP error responses (404, etc.)
from fastapi import FastAPI, HTTPException

# BaseModel → base class for Pydantic data models (automatic validation)
from pydantic import BaseModel

# Optional → "this field can be None OR a value"
# List     → "a list of items"
from typing import Optional, List

# datetime → used to get the current date and time
from datetime import datetime


# ─────────────────────────────────────────────────────────────────────────────
# APP — create the FastAPI application object
# ─────────────────────────────────────────────────────────────────────────────

# FastAPI() creates the application — like opening a restaurant
# All routes (endpoints) will be registered on this object
# title, description, version appear in Swagger UI at /docs
app = FastAPI(
    title="Task Manager API",
    description="Mini CRUD API — GET, POST, PUT, PATCH, DELETE",
    version="1.0.0",
)


# ─────────────────────────────────────────────────────────────────────────────
# PYDANTIC MODELS — blueprints for data validation
# ─────────────────────────────────────────────────────────────────────────────

# TaskCreate: what the CLIENT sends when CREATING a task (POST and PUT)
class TaskCreate(BaseModel):
    # (BaseModel) = inherit data validation from Pydantic

    title: str                  # required — no default — 422 error if missing
    description: str = ""       # optional — default is empty string
    completed: bool = False     # optional — default is False (not done)
    priority: str = "medium"    # optional — default is "medium"


# TaskUpdate: what the CLIENT sends when PATCHING (partial update)
class TaskUpdate(BaseModel):

    # ALL fields are Optional[type] = None
    # This means: each field can be None (not provided) or a real value
    # exclude_none=True in the PATCH route will skip the None ones
    title: Optional[str] = None
    description: Optional[str] = None
    completed: Optional[bool] = None
    priority: Optional[str] = None


# Task: what the SERVER sends BACK to the client (the full task object)
class Task(BaseModel):

    id: int           # assigned by server — never sent by client
    title: str
    description: str
    completed: bool
    priority: str
    created_at: str   # set by server using _now() — never sent by client


# ─────────────────────────────────────────────────────────────────────────────
# IN-MEMORY DATABASE — a Python dictionary acting as storage
# ─────────────────────────────────────────────────────────────────────────────

# Helper function: returns the current date+time as a formatted string
# -> str means this function returns a string
# _name convention: the _ prefix means "private/internal helper"
def _now() -> str:
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    # strftime formats the datetime object into a readable string
    # %Y = 4-digit year, %m = month, %d = day, %H = hour, %M = minute, %S = second


# tasks_db: the in-memory "database"
# Type hint: dict[int, dict] = a dictionary where:
#   - key is an int (the task ID: 1, 2, 3, ...)
#   - value is a dict (the task data)
# This resets every time the server restarts (not persistent)
tasks_db: dict[int, dict] = {
    1: {
        "id": 1,
        "title": "Learn FastAPI",
        "description": "Read the official FastAPI documentation",
        "completed": False,
        "priority": "high",
        "created_at": "2026-03-19 08:00:00",
    },
    2: {
        "id": 2,
        "title": "Build a REST API",
        "description": "Create a CRUD API with GET, POST, PUT, DELETE",
        "completed": True,       # this task is already done
        "priority": "high",
        "created_at": "2026-03-19 09:00:00",
    },
    3: {
        "id": 3,
        "title": "Write unit tests",
        "description": "Use pytest and FastAPI TestClient",
        "completed": False,
        "priority": "medium",
        "created_at": "2026-03-19 10:00:00",
    },
    4: {
        "id": 4,
        "title": "Deploy to production",
        "description": "Use Docker and a cloud provider",
        "completed": False,
        "priority": "low",
        "created_at": "2026-03-19 11:00:00",
    },
}

# Auto-increment counter: the ID that will be assigned to the next new task
# The 4 sample tasks used IDs 1-4, so the next one gets ID 5
next_id = 5


# ─────────────────────────────────────────────────────────────────────────────
# ROUTES — the 7 endpoints
# ─────────────────────────────────────────────────────────────────────────────

# ── ROUTE 1 ──────────────────────────────────────────────────────────────────

# @app.get("/") = decorator: "when someone sends GET /, call root()"
# summary = short description in Swagger UI
@app.get("/", summary="Root — API info")
def root():
    # Returns a simple status dict — no response_model needed
    return {
        "message": "Task Manager API is running",
        "docs": "http://127.0.0.1:8000/docs",
        "total_tasks": len(tasks_db),  # len() counts keys in the dict
    }


# ── ROUTE 2 ──────────────────────────────────────────────────────────────────

# response_model=List[Task] = validate and serialize as a list of Task objects
@app.get("/tasks", response_model=List[Task], summary="Get all tasks")
def get_tasks(completed: Optional[bool] = None, priority: Optional[str] = None):
    # completed and priority are QUERY PARAMETERS (not in the URL path)
    # They appear in the URL as: /tasks?completed=true&priority=high
    # Default is None = not provided = no filter applied

    # Get all tasks from the dict as a plain list
    tasks = list(tasks_db.values())

    # Apply the completed filter ONLY if it was provided (not None)
    if completed is not None:
        # List comprehension: keep only tasks where completed matches
        tasks = [t for t in tasks if t["completed"] == completed]

    # Apply the priority filter ONLY if it was provided (not None)
    if priority is not None:
        tasks = [t for t in tasks if t["priority"] == priority]

    # Return the (possibly filtered) list
    # FastAPI validates each item against Task and converts to JSON
    return tasks


# ── ROUTE 3 ──────────────────────────────────────────────────────────────────

# {task_id} in the URL is a PATH PARAMETER — a variable part of the URL
# Example: GET /tasks/3 → task_id = 3
@app.get("/tasks/{task_id}", response_model=Task, summary="Get one task by ID")
def get_task(task_id: int):
    # : int tells FastAPI to convert the URL string "3" to the integer 3
    # If it cannot (e.g. /tasks/abc), FastAPI returns 422 automatically

    # Check if task_id exists as a key in tasks_db
    if task_id not in tasks_db:
        # raise stops the function and sends an HTTP error response
        # HTTPException builds the JSON {"detail": "..."} body
        raise HTTPException(
            status_code=404,                               # 404 = Not Found
            detail=f"Task with ID {task_id} not found",   # f-string: embeds the variable
        )

    # task_id exists — return the task dict
    # FastAPI validates against Task and converts to JSON
    return tasks_db[task_id]


# ── ROUTE 4 ──────────────────────────────────────────────────────────────────

# status_code=201: successful POST returns 201 Created (not 200)
@app.post("/tasks", response_model=Task, status_code=201, summary="Create a new task")
def create_task(task: TaskCreate):
    # task: TaskCreate = FastAPI reads the JSON body and creates a TaskCreate instance
    # Pydantic validates the data — 422 error if title is missing or types are wrong

    # global: allows modifying the module-level variable next_id from inside this function
    global next_id

    # Build the new task dictionary
    new_task = {
        "id": next_id,          # assign the current counter value as the ID
        **task.model_dump(),    # unpack all TaskCreate fields into this dict
        # ** is the dict unpacking operator: spreads {"title":..., "completed":...} inline
        "created_at": _now(),   # server stamps the creation time
    }

    # Save to the in-memory database
    tasks_db[next_id] = new_task

    # Increment the counter so the next task gets the next ID
    next_id += 1   # shorthand for: next_id = next_id + 1

    # Return the new task (with id and created_at added by the server)
    # FastAPI sends it with status 201 Created
    return new_task


# ── ROUTE 5 ──────────────────────────────────────────────────────────────────

@app.put("/tasks/{task_id}", response_model=Task, summary="Replace a task entirely (PUT)")
def replace_task(task_id: int, task: TaskCreate):
    # task_id: from the URL path  (/tasks/1 → task_id=1)
    # task: TaskCreate            from the JSON body
    # FastAPI automatically distinguishes: path param vs body param

    # Cannot replace something that does not exist
    if task_id not in tasks_db:
        raise HTTPException(
            status_code=404,
            detail=f"Task with ID {task_id} not found",
        )

    # Build the replacement task
    updated_task = {
        "id": task_id,                              # keep the same ID
        **task.model_dump(),                        # overwrite all client-sent fields
        "created_at": tasks_db[task_id]["created_at"],  # preserve original creation date
    }
    # tasks_db[task_id]["created_at"] reads the OLD task's created_at before overwriting it

    # Overwrite the old task in the database
    tasks_db[task_id] = updated_task

    return updated_task


# ── ROUTE 6 ──────────────────────────────────────────────────────────────────

@app.patch("/tasks/{task_id}", response_model=Task, summary="Partially update a task (PATCH)")
def update_task(task_id: int, update: TaskUpdate):
    # update: TaskUpdate — all fields are Optional, default None
    # Only the fields that were actually sent will have non-None values

    if task_id not in tasks_db:
        raise HTTPException(
            status_code=404,
            detail=f"Task with ID {task_id} not found",
        )

    # The PATCH magic:
    for field, value in update.model_dump(exclude_none=True).items():
        # update.model_dump()               → {"title": None, "completed": True, "priority": None, ...}
        # update.model_dump(exclude_none=True) → {"completed": True}   (Nones removed)
        # .items()                          → [("completed", True)]
        # for field, value                  → field="completed", value=True
        tasks_db[task_id][field] = value   # update ONLY this field — nothing else changes

    # Return the complete updated task from the database
    return tasks_db[task_id]


# ── ROUTE 7 ──────────────────────────────────────────────────────────────────

# status_code=204: successful DELETE returns 204 No Content (empty body)
# No response_model: nothing is returned after deletion
@app.delete("/tasks/{task_id}", status_code=204, summary="Delete a task")
def delete_task(task_id: int):

    # Cannot delete something that does not exist
    if task_id not in tasks_db:
        raise HTTPException(
            status_code=404,
            detail=f"Task with ID {task_id} not found",
        )

    # del removes the key-value pair from the dictionary permanently
    del tasks_db[task_id]
    # No return statement — nothing to send back
    # FastAPI sends an empty 204 No Content response
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>










## Annex — How to Run and Test This API Step by Step

> This annex is a practical guide.
> It shows how to start the FastAPI server, open the documentation, and test every route one by one.
> You can read it after finishing the explanation of `main.py`, or use it while practicing.

---

## Table of Contents

| #   | Section                                            |
| --- | -------------------------------------------------- |
| A1  | [What you need before starting](#a1)               |
| A2  | [How to start the server](#a2)                     |
| A3  | [How to open the automatic API documentation](#a3) |
| A4  | [How to test the root endpoint](#a4)               |
| A5  | [How to test `GET /tasks`](#a5)                    |
| A6  | [How to test `GET /tasks/{task_id}`](#a6)          |
| A7  | [How to test `POST /tasks`](#a7)                   |
| A8  | [How to test `PUT /tasks/{task_id}`](#a8)          |
| A9  | [How to test `PATCH /tasks/{task_id}`](#a9)        |
| A10 | [How to test `DELETE /tasks/{task_id}`](#a10)      |
| A11 | [How to test query parameters](#a11)               |
| A12 | [Common beginner mistakes](#a12)                   |

---

<a id="a1"></a>

<details>
<summary>A1 — What you need before starting</summary>

<br/>

Before running this API, you need:

* Python installed
* FastAPI installed
* Uvicorn installed
* The file `main.py` saved in your project folder

If FastAPI and Uvicorn are not installed yet, run:

```bash
pip install fastapi uvicorn
```

If you use a virtual environment, activate it first, then install the packages.

Example:

```bash
python -m venv venv
venv\Scripts\activate
pip install fastapi uvicorn
```

On macOS or Linux:

```bash
python3 -m venv venv
source venv/bin/activate
pip install fastapi uvicorn
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a2"></a>

<details>
<summary>A2 — How to start the server</summary>

<br/>

Make sure your terminal is opened in the folder that contains `main.py`.

Then run:

```bash
uvicorn main:app --reload
```

---

### What this command means

```bash
uvicorn main:app --reload
```

| Part       | Meaning                                                 |
| ---------- | ------------------------------------------------------- |
| `uvicorn`  | Starts the ASGI web server                              |
| `main`     | The name of the Python file: `main.py`                  |
| `app`      | The FastAPI object inside `main.py`                     |
| `--reload` | Automatically restarts the server when you save changes |

So this command means:

> "Start Uvicorn, open the file `main.py`, find the object called `app`, and run it as a web application."

---

### What you should see

If everything is working, the terminal should display something similar to:

```bash
INFO:     Will watch for changes in these directories: [...]
INFO:     Uvicorn running on http://127.0.0.1:8000
INFO:     Started reloader process [...]
INFO:     Started server process [...]
INFO:     Application startup complete.
```

This means the API is now running locally on:

```text
http://127.0.0.1:8000
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a3"></a>

<details>
<summary>A3 — How to open the automatic API documentation</summary>

<br/>

Once the server is running, open your browser and go to:

```text
http://127.0.0.1:8000/docs
```

This opens the **Swagger UI**, which is the interactive documentation automatically generated by FastAPI.

---

### What you can do in `/docs`

In this page, you can:

* see all endpoints
* expand each route
* read its description
* click **Try it out**
* send requests directly from the browser
* see the JSON response
* see the status code returned by the server

This is one of FastAPI’s strongest features.

You can also open:

```text
http://127.0.0.1:8000/openapi.json
```

This shows the raw OpenAPI schema in JSON format.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a4"></a>

<details>
<summary>A4 — How to test the root endpoint</summary>

<br/>

The root endpoint is:

```http
GET /
```

Full URL:

```text
http://127.0.0.1:8000/
```

Open it directly in your browser, or test it in Swagger UI.

Expected response:

```json
{
  "message": "Task Manager API is running",
  "docs": "http://127.0.0.1:8000/docs",
  "total_tasks": 4
}
```

---

### What this confirms

If this endpoint works, it means:

* the server is running
* FastAPI loaded correctly
* `main.py` was read correctly
* the `app` object is valid
* the `root()` function is reachable

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a5"></a>

<details>
<summary>A5 — How to test <code>GET /tasks</code></summary>

<br/>

This endpoint returns all tasks:

```http
GET /tasks
```

Full URL:

```text
http://127.0.0.1:8000/tasks
```

Expected result: a JSON array containing all tasks.

Example:

```json
[
  {
    "id": 1,
    "title": "Learn FastAPI",
    "description": "Read the official FastAPI documentation",
    "completed": false,
    "priority": "high",
    "created_at": "2026-03-19 08:00:00"
  }
]
```

In reality, you should get all available tasks, not just one.

---

### What this tests

This verifies that:

* the `/tasks` route exists
* FastAPI correctly returns a list
* `response_model=List[Task]` works
* the dictionary values are being converted to JSON properly

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a6"></a>

<details>
<summary>A6 — How to test <code>GET /tasks/{task_id}</code></summary>

<br/>

This endpoint returns one specific task by ID.

Example:

```http
GET /tasks/1
```

Full URL:

```text
http://127.0.0.1:8000/tasks/1
```

Expected response:

```json
{
  "id": 1,
  "title": "Learn FastAPI",
  "description": "Read the official FastAPI documentation",
  "completed": false,
  "priority": "high",
  "created_at": "2026-03-19 08:00:00"
}
```

---

### Test an ID that does not exist

Try:

```http
GET /tasks/999
```

Expected response:

```json
{
  "detail": "Task with ID 999 not found"
}
```

Expected status code:

```text
404 Not Found
```

---

### What this tests

This verifies that:

* path parameters are working
* FastAPI converts the URL value to `int`
* the `if task_id not in tasks_db` check works
* `HTTPException(status_code=404)` works properly

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a7"></a>

<details>
<summary>A7 — How to test <code>POST /tasks</code></summary>

<br/>

This endpoint creates a new task.

```http
POST /tasks
```

In Swagger UI:

* open `POST /tasks`
* click **Try it out**
* enter JSON in the request body
* click **Execute**

Use this example body:

```json
{
  "title": "Practice FastAPI",
  "description": "Create and test API endpoints",
  "completed": false,
  "priority": "high"
}
```

Expected response:

```json
{
  "id": 5,
  "title": "Practice FastAPI",
  "description": "Create and test API endpoints",
  "completed": false,
  "priority": "high",
  "created_at": "2026-03-30 14:22:05"
}
```

The exact `id` and `created_at` will vary.

Expected status code:

```text
201 Created
```

---

### Minimum valid body

You can also test with the smallest valid JSON body:

```json
{
  "title": "Simple task"
}
```

Because:

* `title` is required
* all other fields have defaults

Expected result:

* `description` becomes `""`
* `completed` becomes `false`
* `priority` becomes `"medium"`

---

### What this tests

This verifies that:

* the request body is read correctly
* `TaskCreate` validation works
* default values are applied
* `model_dump()` works
* `next_id` increments properly
* `_now()` adds a timestamp
* status code `201` is returned

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a8"></a>

<details>
<summary>A8 — How to test <code>PUT /tasks/{task_id}</code></summary>

<br/>

This endpoint replaces an existing task entirely.

Example:

```http
PUT /tasks/1
```

Use this JSON body:

```json
{
  "title": "Learn FastAPI deeply",
  "description": "Study routes, models, and validation",
  "completed": true,
  "priority": "high"
}
```

Expected behavior:

* task 1 is fully replaced
* all fields from the body overwrite the old ones
* `id` remains `1`
* `created_at` stays unchanged

Expected response:

```json
{
  "id": 1,
  "title": "Learn FastAPI deeply",
  "description": "Study routes, models, and validation",
  "completed": true,
  "priority": "high",
  "created_at": "2026-03-19 08:00:00"
}
```

---

### Important rule about PUT

PUT means:

> "Replace the full resource."

So if you forget a field, the default from `TaskCreate` may be used.

For example, if you send only:

```json
{
  "title": "New title"
}
```

Then:

* `description` becomes `""`
* `completed` becomes `false`
* `priority` becomes `"medium"`

That is why PUT is a full replacement, not a partial edit.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a9"></a>

<details>
<summary>A9 — How to test <code>PATCH /tasks/{task_id}</code></summary>

<br/>

This endpoint updates only the fields that you send.

Example:

```http
PATCH /tasks/1
```

Use this body:

```json
{
  "completed": true
}
```

Expected response:

* only `completed` changes
* all other fields remain exactly the same

Example result:

```json
{
  "id": 1,
  "title": "Learn FastAPI",
  "description": "Read the official FastAPI documentation",
  "completed": true,
  "priority": "high",
  "created_at": "2026-03-19 08:00:00"
}
```

---

### Another PATCH example

```json
{
  "priority": "low",
  "title": "Updated task title"
}
```

This changes only:

* `priority`
* `title`

Everything else stays unchanged.

---

### Why PATCH is different from PUT

PUT replaces the whole object.
PATCH updates only specific fields.

That is why PATCH uses:

```python
TaskUpdate
```

instead of:

```python
TaskCreate
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a10"></a>

<details>
<summary>A10 — How to test <code>DELETE /tasks/{task_id}</code></summary>

<br/>

This endpoint deletes a task permanently.

Example:

```http
DELETE /tasks/1
```

Expected status code:

```text
204 No Content
```

Expected response body:

* empty

---

### How to verify the deletion worked

After deleting task 1, test:

```http
GET /tasks/1
```

Expected result:

```json
{
  "detail": "Task with ID 1 not found"
}
```

Status code:

```text
404 Not Found
```

This confirms that the task was removed from `tasks_db`.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a11"></a>

<details>
<summary>A11 — How to test query parameters</summary>

<br/>

The endpoint:

```http
GET /tasks
```

supports two query parameters:

* `completed`
* `priority`

---

### Filter by completed status

```http
GET /tasks?completed=true
```

This returns only completed tasks.

```http
GET /tasks?completed=false
```

This returns only incomplete tasks.

---

### Filter by priority

```http
GET /tasks?priority=high
```

This returns only tasks with high priority.

---

### Combine filters

```http
GET /tasks?completed=false&priority=high
```

This returns only tasks that are both:

* not completed
* high priority

---

### Important note

Query parameters are not part of the route path itself.

These two are different:

```http
GET /tasks/1
```

This uses a **path parameter**

```http
GET /tasks?priority=high
```

This uses a **query parameter**

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="a12"></a>

<details>
<summary>A12 — Common beginner mistakes</summary>

<br/>

### Mistake 1 — Running the wrong command

Wrong:

```bash
python main.py
```

Why it is wrong:

* `main.py` defines the API
* but it does not directly start the web server

Correct:

```bash
uvicorn main:app --reload
```

---

### Mistake 2 — Using the wrong object name

If your file is:

```python
app = FastAPI()
```

Then the command must be:

```bash
uvicorn main:app --reload
```

If you write:

```bash
uvicorn main:api --reload
```

it will fail, because there is no object called `api`.

---

### Mistake 3 — Forgetting that data resets after restart

This project uses an in-memory dictionary:

```python
tasks_db = {...}
```

That means:

* created tasks disappear when the server stops
* deleted tasks come back after restart
* updates are not permanently saved

This is normal in this project.

---

### Mistake 4 — Confusing PUT and PATCH

* **PUT** = replace everything
* **PATCH** = update only some fields

If a beginner uses PUT with only one field, they may accidentally reset the other fields to default values.

---

### Mistake 5 — Sending invalid JSON types

Example:

```json
{
  "title": "My task",
  "completed": "maybe"
}
```

This fails because `completed` must be a boolean.

Correct values:

```json
true
false
```

not:

```json
"true"
"false"
"maybe"
```

---

### Mistake 6 — Forgetting the required field `title`

This body is invalid:

```json
{}
```

Because `title` is required in `TaskCreate`.

You must send at least:

```json
{
  "title": "My task"
}
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<p align="center">
  <strong>End of Annex — How to Run and Test This API</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>











---

<p align="center">
  <strong>End of Code Explained — main.py</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>








