<a id="top"></a>

# Understanding `frontend.py` — Every Line Explained

## Task Manager UI — Streamlit Deep Dive, Clear and Practical

> Every concept is explained clearly from the ground up.
> No prior experience with Streamlit or `requests` is assumed, but the explanations stay technically serious.
> Each section can be read independently.
> This explanation is based on the provided `frontend.py` file. :contentReference[oaicite:0]{index=0}

---

```md
:::tip
This guide explains the Streamlit frontend used in this project:

- Frontend file: `frontend.py`
- Project repository: `demo_api_1_simple_fastapi_app`
- Backend file: `main.py`

Reference links:
- Frontend: `https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/frontend.py`
- Project: `https://github.com/inskillflow/demo_api_1_simple_fastapi_app/`
- Backend: `https://github.com/inskillflow/demo_api_1_simple_fastapi_app/blob/main/main.py`

This document focuses on explaining the frontend file step by step, while the backend logic is implemented in `main.py`.
:::
```


## Table of Contents

| # | Concept |
|---|---|
| 1 | [The Big Picture — What does this file do?](#s1) |
| 2 | [The imports — Why `streamlit` and `requests`?](#s2) |
| 3 | [The `API_URL` variable — What is it for?](#s3) |
| 4 | [Page configuration — What is `st.set_page_config()`?](#s4) |
| 5 | [The page title and caption — What do they do?](#s5) |
| 6 | [Helper function `server_is_up()` — Why check the backend first?](#s6) |
| 7 | [Stopping the app early — What do `st.error()` and `st.stop()` do?](#s7) |
| 8 | [Sidebar navigation — What is `st.sidebar.radio()`?](#s8) |
| 9 | [Page 1 — `View all tasks`](#s9) |
| 10 | [Filters — How the status and priority filters work](#s10) |
| 11 | [Sending a GET request with query parameters](#s11) |
| 12 | [Displaying tasks — icons, colors, expanders, and columns](#s12) |
| 13 | [Page 2 — `Create a task`](#s13) |
| 14 | [Forms — What is `st.form()`?](#s14) |
| 15 | [Building the POST payload — Creating a task step by step](#s15) |
| 16 | [Handling success and validation errors — `201`, `422`, and others](#s16) |
| 17 | [Page 3 — `Edit a task`](#s17) |
| 18 | [Loading all tasks into a selector](#s18) |
| 19 | [Showing the current task values](#s19) |
| 20 | [The update mode selector — `PATCH` vs `PUT`](#s20) |
| 21 | [PUT logic — Replacing a task from the frontend](#s21) |
| 22 | [PATCH logic — Updating only changed fields](#s22) |
| 23 | [Page 4 — `Delete a task`](#s23) |
| 24 | [Sending a DELETE request and refreshing the UI](#s24) |
| 25 | [The full annotated file — everything at once](#s25) |

---

<a id="s1"></a>

<details>
<summary>1 — The Big Picture — What does this file do?</summary>

<br/>

### One sentence

`frontend.py` is a **web interface** built with Streamlit that lets a user interact with the FastAPI Task Manager backend through forms, selectors, and buttons instead of manually crafting HTTP requests. :contentReference[oaicite:1]{index=1}

---

### The architectural role of this file

In this project, `main.py` is the backend API. It exposes endpoints such as:

- `GET /tasks`
- `POST /tasks`
- `PUT /tasks/{task_id}`
- `PATCH /tasks/{task_id}`
- `DELETE /tasks/{task_id}`

That backend is the system that actually owns the task data and applies the business logic.

`frontend.py` plays a different role. It does not store the authoritative data. It acts as a **client** of the API. Its job is to:
- collect input from the user
- send HTTP requests to the backend
- receive responses
- present the results in a readable interface

So the backend is the service layer, and the frontend is the interaction layer. :contentReference[oaicite:2]{index=2}

---

### A practical mental model

You can think of the system like this:

| Part | Role |
|---|---|
| `main.py` | The backend service that defines the API |
| `frontend.py` | The Streamlit client that consumes that API |
| `requests` | The bridge used by the frontend to call the backend |
| Browser | Where the user sees the interface |

The important point is that the frontend is not “doing the CRUD directly.”
It is **asking the backend** to do it.

---

### The runtime flow — step by step

```text
User opens the Streamlit page
         │
         │  selects a page or submits a form
         ▼
frontend.py runs the corresponding Streamlit block
         │
         │  builds an HTTP request with requests.get/post/put/patch/delete
         ▼
FastAPI backend receives the request
         │
         │  validates input, reads or updates tasks, returns JSON
         ▼
frontend.py receives the response
         │
         │  interprets status code and JSON body
         ▼
Streamlit renders success messages, errors, or task details
````

---

### Why this file matters

This file demonstrates a very common real-world pattern:

> A UI does not need to know how the data is stored internally. It only needs to know how to call the API correctly.

That separation is one of the most important ideas in modern application design.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s2"></a>

<details>
<summary>2 — The imports — Why `streamlit` and `requests`?</summary>

<br/>

```python
import streamlit as st
import requests
```

These imports appear at the top of the provided file. 

---

### What is an import?

An **import** tells Python to load tools from another module or library so they can be used in the current file.

Instead of writing your own UI framework or HTTP client from scratch, you import them.

---

### Line 1

```python
import streamlit as st
```

This imports the Streamlit library and gives it the short alias `st`.

That means:

```python
streamlit.title("Task Manager")
```

can be written more compactly as:

```python
st.title("Task Manager")
```

The alias is just a convenience. It does not change what the library is.

---

### What Streamlit provides in this file

Streamlit is used here to build the interface:

* page configuration
* title and caption
* sidebar navigation
* headers
* dropdowns
* text inputs
* forms
* buttons
* warnings, errors, and success messages
* layout elements such as columns and expanders

So Streamlit is responsible for everything visual and interactive on the page.

---

### Line 2

```python
import requests
```

This imports the `requests` library, which is one of the most common Python libraries for sending HTTP requests.

In this file, `requests` is used to call the FastAPI backend:

* `requests.get(...)`
* `requests.post(...)`
* `requests.put(...)`
* `requests.patch(...)`
* `requests.delete(...)`

Without `requests`, the Streamlit app would display a UI, but it would not be able to communicate with the API.

---

### The division of responsibility

| Library     | Responsibility                |
| ----------- | ----------------------------- |
| `streamlit` | Build the interface           |
| `requests`  | Talk to the backend over HTTP |

That separation is clean and intentional.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>3 — The `API_URL` variable — What is it for?</summary>

<br/>

```python
API_URL = "http://127.0.0.1:8000"
```

This constant appears near the top of the file. 

---

### What it represents

`API_URL` stores the base URL of the FastAPI backend.

Instead of repeating:

```python
"http://127.0.0.1:8000/tasks"
"http://127.0.0.1:8000/"
"http://127.0.0.1:8000/tasks/3"
```

the file stores the common base once and builds full URLs from it.

Examples later in the code:

```python
f"{API_URL}/"
f"{API_URL}/tasks"
f"{API_URL}/tasks/{selected_id}"
```

---

### Why this is good practice

If the backend address changes later, you update it in one place instead of many.

For example, if the backend were deployed elsewhere, you could change:

```python
API_URL = "https://my-api.example.com"
```

and the rest of the code would still work structurally.

---

### What is `127.0.0.1`?

`127.0.0.1` is the loopback address. It refers to the same machine the code is running on.

It is functionally equivalent to:

```text
localhost
```

So this URL means:

```text
http://127.0.0.1:8000
```

> “Talk to a web server running on this computer on port 8000.”

That matches the FastAPI server started with:

```bash
uvicorn main:app --reload
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>4 — Page configuration — What is `st.set_page_config()`?</summary>

<br/>

```python
st.set_page_config(
    page_title="Task Manager",
    page_icon="✓",
    layout="wide",
)
```

This block configures the Streamlit page before the rest of the interface is rendered. 

---

### What this function does

`st.set_page_config(...)` controls high-level page settings such as:

* browser tab title
* page icon
* page width behavior

It is not backend logic. It is presentation logic.

---

### Parameter by parameter

| Parameter                   | Meaning                                                    |
| --------------------------- | ---------------------------------------------------------- |
| `page_title="Task Manager"` | Sets the browser tab title                                 |
| `page_icon="✓"`             | Sets the browser tab icon                                  |
| `layout="wide"`             | Uses a wider layout instead of the default centered layout |

---

### Why `layout="wide"` matters

A task manager interface benefits from horizontal space:

* filters can sit side by side
* task details can be split into columns
* the UI feels less cramped

So `wide` is a practical choice for this kind of page.

---

### Why this line is placed early

This configuration should be declared near the top of the file because it defines how the page should behave before the rest of the content is displayed.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s5"></a>

<details>
<summary>5 — The page title and caption — What do they do?</summary>

<br/>

```python
st.title("Task Manager")
st.caption("Streamlit interface connected to the FastAPI backend")
```

These lines create the visible heading area at the top of the app. 

---

### `st.title(...)`

```python
st.title("Task Manager")
```

This creates the main page title, similar in spirit to a large heading in HTML.

It immediately tells the user what application they are looking at.

---

### `st.caption(...)`

```python
st.caption("Streamlit interface connected to the FastAPI backend")
```

A caption is smaller, supporting text.

It provides context:

* this is not just a standalone toy page
* it is connected to a backend
* the UI is a client of the API

---

### Why these two lines are useful

A good interface should establish context very quickly:

* What is this?
* What system am I interacting with?
* What role does this screen play?

These two lines answer those questions efficiently.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>6 — Helper function `server_is_up()` — Why check the backend first?</summary>

<br/>

```python
def server_is_up() -> bool:
    try:
        r = requests.get(f"{API_URL}/", timeout=2)
        return r.status_code == 200
    except Exception:
        return False
```

This helper function checks whether the FastAPI backend is reachable before the frontend tries to use it. 

---

### The purpose of this function

The entire frontend depends on the backend being available.

If the backend is down, later calls such as:

```python
requests.get(f"{API_URL}/tasks")
```

or

```python
requests.post(f"{API_URL}/tasks", json=payload)
```

will fail.

So this file performs an early health check.

---

### Line by line

#### Function definition

```python
def server_is_up() -> bool:
```

* `def` defines a function
* `server_is_up` is the name
* `-> bool` is a return type hint meaning the function is expected to return either `True` or `False`

---

#### The `try` block

```python
try:
```

This means:

> “Run the code below, but if it fails, handle the failure gracefully.”

That is important because network calls can fail for many reasons.

---

#### The HTTP request

```python
r = requests.get(f"{API_URL}/", timeout=2)
```

This sends a GET request to the root endpoint of the backend.

The full URL is:

```text
http://127.0.0.1:8000/
```

The response is stored in the variable `r`.

---

#### The timeout

```python
timeout=2
```

This prevents the frontend from waiting indefinitely.

It means:

> “If the server does not answer within 2 seconds, give up.”

That makes the check responsive.

---

#### The return condition

```python
return r.status_code == 200
```

This compares the HTTP status code against `200`.

If the backend responds successfully with `200 OK`, the function returns `True`.
Otherwise it returns `False`.

---

#### The exception handler

```python
except Exception:
    return False
```

If anything goes wrong:

* connection refused
* backend not started
* timeout
* DNS error
* another network-related issue

the function returns `False` instead of crashing the whole app.

---

### Why this is a helper function

This logic is separated into its own function because:

* it has a clear purpose
* it may be reused
* it keeps the rest of the code cleaner

That is what helper functions are for.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s7"></a>

<details>
<summary>7 — Stopping the app early — What do `st.error()` and `st.stop()` do?</summary>

<br/>

```python
if not server_is_up():
    st.error(
        "Cannot reach the FastAPI server at http://127.0.0.1:8000  \n"
        "Start it first:  `uvicorn main:app --reload`"
    )
    st.stop()
```

This block protects the rest of the UI from running when the backend is unavailable. 

---

### The condition

```python
if not server_is_up():
```

If `server_is_up()` returns `False`, this block runs.

So the meaning is:

> “If the backend is not reachable, do not continue.”

---

### `st.error(...)`

This displays a visible red error box in the Streamlit interface.

The message explains:

* what failed
* where the frontend expected the backend to be
* how to start it

That is helpful because it gives a corrective action, not just a generic error.

---

### Why the string has `\n`

```python
"Cannot reach ...  \n"
"Start it first:  `uvicorn main:app --reload`"
```

`\n` is a newline character.
It creates a line break in the displayed message.

So the user sees a two-line message instead of one long sentence.

---

### `st.stop()`

`st.stop()` is a Streamlit control function that halts execution of the script at that point.

It means:

> “Do not run any of the code below this line for this render.”

That is important because the rest of the file assumes the backend is available.

Without `st.stop()`, the interface would continue into sections that try to call the backend and would likely trigger more confusing errors.

---

### Why this is a good design choice

This is a classic example of **failing early and clearly**.

Instead of letting the app continue in a broken state, the code:

1. checks the dependency
2. explains the problem
3. stops immediately

That produces a much better user experience.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s8"></a>

<details>
<summary>8 — Sidebar navigation — What is `st.sidebar.radio()`?</summary>

<br/>

```python
st.sidebar.title("Navigation")
page = st.sidebar.radio(
    "Go to",
    ["View all tasks", "Create a task", "Edit a task", "Delete a task"],
)
```

This block creates the navigation menu on the left side of the app. 

---

### `st.sidebar.title(...)`

```python
st.sidebar.title("Navigation")
```

This creates a title inside the sidebar instead of in the main page area.

It labels the menu area clearly.

---

### `st.sidebar.radio(...)`

```python
page = st.sidebar.radio(
    "Go to",
    ["View all tasks", "Create a task", "Edit a task", "Delete a task"],
)
```

A radio widget is a single-choice selector.

The user can choose exactly one option at a time.

The selected value is stored in the variable `page`.

---

### Why store it in `page`?

Later in the file, the code checks:

```python
if page == "View all tasks":
```

or

```python
elif page == "Create a task":
```

and so on.

So `page` controls which block of the interface is displayed.

---

### Why a radio widget is appropriate here

This app is organized into mutually exclusive views:

* one view to read tasks
* one to create
* one to edit
* one to delete

A radio selector fits this structure well because only one mode is active at a time.

---

### The broader pattern

This is a common Streamlit pattern:

* a sidebar widget chooses a mode
* the selected value drives conditional rendering in the main area

It is a simple and effective way to structure small applications.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>9 — Page 1 — `View all tasks`</summary>

<br/>

```python
if page == "View all tasks":
    st.header("All Tasks")
```

This block starts the first page of the application: the page that reads and displays tasks from the backend. 

---

### What this page does

This page is the “read” view of the application.

It:

* displays filtering controls
* builds query parameters based on user choices
* sends a GET request to `/tasks`
* receives a list of tasks
* renders the results in a structured format

This corresponds to the **R** in CRUD:

* Create
* Read
* Update
* Delete

---

### `st.header(...)`

```python
st.header("All Tasks")
```

This creates a section heading in the main area of the page.

It signals that the content below belongs to the task listing view.

---

### Why this block is conditional

The code is inside:

```python
if page == "View all tasks":
```

So this part runs only when the user selected that specific sidebar option.

That is how one Streamlit script can behave like multiple pages.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>10 — Filters — How the status and priority filters work</summary>

<br/>

```python
col1, col2 = st.columns(2)
with col1:
    filter_completed = st.selectbox(
        "Filter by status",
        ["All", "Completed", "Not completed"],
    )
with col2:
    filter_priority = st.selectbox(
        "Filter by priority",
        ["All", "high", "medium", "low"],
    )
```

This block creates two side-by-side filter controls. 

---

### `st.columns(2)`

```python
col1, col2 = st.columns(2)
```

This creates two layout columns and returns them as objects.

That allows the UI to place content side by side instead of stacking everything vertically.

Here, the filters are arranged in two columns:

* left column for completion status
* right column for priority

---

### `with col1:` and `with col2:`

These `with` blocks tell Streamlit where to place the widgets.

So:

```python
with col1:
```

means:

> “Put the following widget inside the first column.”

And similarly for `col2`.

---

### The first selectbox

```python
filter_completed = st.selectbox(
    "Filter by status",
    ["All", "Completed", "Not completed"],
)
```

This creates a dropdown with three choices:

* All
* Completed
* Not completed

The selected option is stored in `filter_completed`.

---

### The second selectbox

```python
filter_priority = st.selectbox(
    "Filter by priority",
    ["All", "high", "medium", "low"],
)
```

This creates another dropdown for priority filtering.

The selected option is stored in `filter_priority`.

---

### Why the UI uses “All”

“All” is not a value the backend expects.
It is a UI-level choice meaning:

> “Do not filter on this field.”

Later in the code, “All” is interpreted as “do not send that query parameter.”

That distinction between UI language and API language is very common.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>11 — Sending a GET request with query parameters</summary>

<br/>

```python
params = {}
if filter_completed == "Completed":
    params["completed"] = "true"
elif filter_completed == "Not completed":
    params["completed"] = "false"
if filter_priority != "All":
    params["priority"] = filter_priority

response = requests.get(f"{API_URL}/tasks", params=params)
```

This block converts the UI filter state into query parameters and sends the GET request. 

---

### Start with an empty dictionary

```python
params = {}
```

This dictionary will hold the query parameters sent to the backend.

A query parameter dictionary like:

```python
{"completed": "false", "priority": "high"}
```

will later become a URL such as:

```text
/tasks?completed=false&priority=high
```

---

### Mapping the completion filter

```python
if filter_completed == "Completed":
    params["completed"] = "true"
elif filter_completed == "Not completed":
    params["completed"] = "false"
```

The Streamlit selectbox returns UI labels such as:

* `"Completed"`
* `"Not completed"`

But the backend expects query values such as:

* `"true"`
* `"false"`

So this block translates the UI choice into API-compatible values.

If the UI selection is `"All"`, nothing is added to `params`.

---

### Mapping the priority filter

```python
if filter_priority != "All":
    params["priority"] = filter_priority
```

If the priority is not `"All"`, the selected value is sent directly.

Examples:

* `"high"`
* `"medium"`
* `"low"`

If the selection is `"All"`, the priority parameter is omitted.

---

### The GET request

```python
response = requests.get(f"{API_URL}/tasks", params=params)
```

This sends an HTTP GET request to the backend.

The important part here is `params=params`.

The `requests` library automatically converts the dictionary into a query string.

Example:

```python
params = {"completed": "false", "priority": "high"}
```

becomes something like:

```text
http://127.0.0.1:8000/tasks?completed=false&priority=high
```

---

### Why this is clean design

The frontend does not manually build URL strings with concatenation.
It uses a structured dictionary and lets `requests` serialize it.

That is safer and cleaner.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s12"></a>

<details>
<summary>12 — Displaying tasks — icons, colors, expanders, and columns</summary>

<br/>

```python
if response.status_code == 200:
    tasks = response.json()

    if not tasks:
        st.info("No tasks match the selected filters.")
    else:
        st.write(f"**{len(tasks)} task(s) found**")

        for task in tasks:
            status_icon = "✅" if task["completed"] else "⬜"
            priority_color = {
                "high": "🔴",
                "medium": "🟡",
                "low": "🟢",
            }.get(task["priority"], "⚪")

            with st.expander(
                f"{status_icon} #{task['id']} — {task['title']}  {priority_color} {task['priority'].upper()}"
            ):
                col_a, col_b = st.columns(2)
                with col_a:
                    st.write(f"**Description:** {task['description'] or '—'}")
                    st.write(f"**Priority:** {task['priority']}")
                with col_b:
                    st.write(f"**Status:** {'Completed' if task['completed'] else 'In progress'}")
                    st.write(f"**Created:** {task['created_at']}")
                    st.write(f"**ID:** {task['id']}")
else:
    st.error(f"Error {response.status_code}: {response.text}")
```

This block processes and displays the backend response. 

---

### Step 1 — Check the status code

```python
if response.status_code == 200:
```

A successful GET request should return `200 OK`.

If it does, the code continues to process the JSON body.

If not, the code falls into the `else` branch and displays an error.

---

### Step 2 — Parse the JSON body

```python
tasks = response.json()
```

The backend returns JSON.
`response.json()` converts that JSON into Python data structures.

For `GET /tasks`, the backend returns a JSON array, so `tasks` becomes a Python list of dictionaries.

---

### Step 3 — Handle the empty case

```python
if not tasks:
    st.info("No tasks match the selected filters.")
```

If the list is empty, Streamlit shows an informational message.

This is better than showing a blank page because it explains that:

* the request worked
* there are simply no matching results

---

### Step 4 — Show the number of tasks

```python
st.write(f"**{len(tasks)} task(s) found**")
```

This gives immediate feedback about how many results were returned.

`len(tasks)` counts how many task objects are in the list.

---

### Step 5 — Loop through tasks

```python
for task in tasks:
```

Each `task` is a dictionary returned by the API, for example:

```python
{
    "id": 1,
    "title": "Learn FastAPI",
    "description": "...",
    "completed": False,
    "priority": "high",
    "created_at": "2026-03-19 08:00:00"
}
```

---

### Step 6 — Build a status icon

```python
status_icon = "✅" if task["completed"] else "⬜"
```

This is a conditional expression.

If `task["completed"]` is `True`, it uses a check mark.
Otherwise it uses an empty box.

It is a compact visual representation of task status.

---

### Step 7 — Build a priority marker

```python
priority_color = {
    "high": "🔴",
    "medium": "🟡",
    "low": "🟢",
}.get(task["priority"], "⚪")
```

This uses a dictionary as a mapping from priority to colored symbol.

| Priority | Symbol |
| -------- | ------ |
| high     | 🔴     |
| medium   | 🟡     |
| low      | 🟢     |

`.get(..., "⚪")` provides a default value if the priority is not one of the expected keys.

---

### Step 8 — Use an expander

```python
with st.expander(...):
```

An expander is a collapsible UI component.

The summary line shows:

* completion icon
* ID
* title
* colored priority marker
* uppercase priority text

Then the user can expand it to view more details.

This keeps the page compact when many tasks are displayed.

---

### Step 9 — Split details into two columns

Inside each expander:

```python
col_a, col_b = st.columns(2)
```

The details are split into two columns:

* left column for description and priority
* right column for status, creation date, and ID

This makes the content easier to scan than one long vertical block.

---

### Error branch

```python
else:
    st.error(f"Error {response.status_code}: {response.text}")
```

If the backend did not return `200`, the UI shows the raw status code and response text.

That is useful during development because it exposes what the backend actually returned.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s13"></a>

<details>
<summary>13 — Page 2 — `Create a task`</summary>

<br/>

```python
elif page == "Create a task":
    st.header("Create a New Task")
```

This block starts the task creation page. 

---

### What this page is responsible for

This page implements the **Create** operation in CRUD.

It:

* renders a form
* collects user input
* performs basic frontend validation
* sends a POST request to the backend
* displays the created task or any validation errors

---

### Why task creation belongs in its own page

Creation has a different interaction pattern from viewing, editing, or deleting:

* it needs empty input fields
* it needs a submit button
* it creates a new record instead of modifying an existing one

So separating it into its own page keeps the logic clear.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s14"></a>

<details>
<summary>14 — Forms — What is `st.form()`?</summary>

<br/>

```python
with st.form("create_form", clear_on_submit=True):
    title = st.text_input("Title *", placeholder="e.g. Learn FastAPI")
    description = st.text_area("Description", placeholder="Optional description...")
    priority = st.selectbox("Priority", ["medium", "high", "low"])
    completed = st.checkbox("Mark as completed immediately", value=False)

    submitted = st.form_submit_button("Create Task", type="primary")
```

This block defines the creation form. 

---

### Why use a form?

In Streamlit, widgets outside a form can trigger reruns immediately as the user interacts.

A form groups related inputs together so they are processed as a single submission event.

That is exactly what you want for a creation workflow:

* user fills fields
* user clicks submit
* code processes everything together

---

### The form identifier

```python
with st.form("create_form", clear_on_submit=True):
```

`"create_form"` is the form key or identifier.

It distinguishes this form from any other forms in the app.

---

### `clear_on_submit=True`

This means that once the form is successfully submitted, the widget values are cleared.

That is convenient if the user wants to create multiple tasks in sequence.

---

### The input widgets

#### Title field

```python
title = st.text_input("Title *", placeholder="e.g. Learn FastAPI")
```

* single-line input
* stored in `title`
* the asterisk in `"Title *"` signals that the field is required

#### Description field

```python
description = st.text_area("Description", placeholder="Optional description...")
```

* multi-line text area
* stored in `description`

#### Priority dropdown

```python
priority = st.selectbox("Priority", ["medium", "high", "low"])
```

* dropdown menu
* stored in `priority`

#### Completed checkbox

```python
completed = st.checkbox("Mark as completed immediately", value=False)
```

* boolean input
* default is `False`

---

### The submit button

```python
submitted = st.form_submit_button("Create Task", type="primary")
```

This button submits the form.

When clicked, `submitted` becomes `True`, and the code below can act on the form data.

`type="primary"` makes the button visually emphasized.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s15"></a>

<details>
<summary>15 — Building the POST payload — Creating a task step by step</summary>

<br/>

```python
if submitted:
    if not title.strip():
        st.warning("Title is required.")
    else:
        payload = {
            "title": title.strip(),
            "description": description.strip(),
            "priority": priority,
            "completed": completed,
        }
        response = requests.post(f"{API_URL}/tasks", json=payload)
```

This block validates the form and sends the POST request. 

---

### Step 1 — Check whether the form was submitted

```python
if submitted:
```

This ensures the logic runs only after the user clicked the submit button.

---

### Step 2 — Validate the title locally

```python
if not title.strip():
    st.warning("Title is required.")
```

`strip()` removes leading and trailing whitespace.

Examples:

```python
"  Learn FastAPI  ".strip()   # "Learn FastAPI"
"     ".strip()               # ""
```

This check prevents titles that are technically non-empty strings but contain only spaces.

It is frontend validation, performed before the request is sent.

---

### Step 3 — Build the payload

```python
payload = {
    "title": title.strip(),
    "description": description.strip(),
    "priority": priority,
    "completed": completed,
}
```

This Python dictionary is the JSON body that will be sent to the backend.

The keys correspond to the fields expected by the backend’s `TaskCreate` model:

* `title`
* `description`
* `priority`
* `completed`

---

### Step 4 — Send the POST request

```python
response = requests.post(f"{API_URL}/tasks", json=payload)
```

This sends an HTTP POST request to:

```text
http://127.0.0.1:8000/tasks
```

The `json=payload` argument tells `requests` to:

* serialize the dictionary as JSON
* set the appropriate content type

So the backend receives structured JSON, not plain text.

---

### Why this local validation is still useful even though the backend validates too

The backend is the ultimate authority and must validate input.
That is non-negotiable.

But frontend validation improves usability because it can catch obvious issues before the request is sent.

So the validation responsibilities are layered:

* frontend: convenience and immediate feedback
* backend: authoritative enforcement

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s16"></a>

<details>
<summary>16 — Handling success and validation errors — `201`, `422`, and others</summary>

<br/>

```python
if response.status_code == 201:
    created = response.json()
    st.success(
        f"Task created successfully!  \n"
        f"ID: **{created['id']}** — {created['title']}"
    )
    st.json(created)
elif response.status_code == 422:
    errors = response.json().get("detail", [])
    for err in errors:
        field = " → ".join(str(x) for x in err.get("loc", []))
        st.error(f"Validation error in {field}: {err.get('msg')}")
else:
    st.error(f"Error {response.status_code}: {response.text}")
```

This block interprets the backend response after a create operation. 

---

### Success case — `201 Created`

```python
if response.status_code == 201:
```

A successful POST should return `201 Created`.

The code then parses the created task:

```python
created = response.json()
```

and displays:

* a success message
* the assigned ID
* the task title
* the full JSON response

`st.json(created)` is especially useful during learning and debugging because it shows exactly what the backend returned.

---

### Validation error case — `422`

```python
elif response.status_code == 422:
```

FastAPI returns `422 Unprocessable Entity` when request validation fails.

This typically happens when:

* a required field is missing
* a field has the wrong type
* the JSON shape does not match the expected model

---

### Extracting the validation details

```python
errors = response.json().get("detail", [])
```

FastAPI validation errors usually include a `detail` field containing a list of error objects.

Using `.get("detail", [])` is safe because it provides an empty list if `detail` is missing for some reason.

---

### Converting the location into readable text

```python
field = " → ".join(str(x) for x in err.get("loc", []))
```

If the backend reports a location like:

```python
["body", "title"]
```

this becomes:

```text
body → title
```

That produces more readable UI messages.

---

### Displaying each validation error

```python
st.error(f"Validation error in {field}: {err.get('msg')}")
```

Instead of dumping raw JSON, the frontend extracts and formats the error cleanly.

That is a good example of translating backend detail into a better user-facing message.

---

### Generic error case

```python
else:
    st.error(f"Error {response.status_code}: {response.text}")
```

If the response is neither `201` nor `422`, the UI shows the raw status code and text.

That is useful for unexpected backend issues during development.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s17"></a>

<details>
<summary>17 — Page 3 — `Edit a task`</summary>

<br/>

```python
elif page == "Edit a task":
    st.header("Edit a Task")
```

This block starts the task editing page. 

---

### What this page needs to accomplish

Editing is more complex than creation because the UI must:

1. load existing tasks
2. let the user choose one
3. retrieve its current data
4. show the current values
5. let the user choose between `PUT` and `PATCH`
6. submit the correct request

This page therefore combines both read and update logic.

---

### Why editing is more involved than creation

Creation starts from empty fields.
Editing starts from an existing object.

That means the frontend must synchronize itself with the backend before it can render the correct editing controls.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s18"></a>

<details>
<summary>18 — Loading all tasks into a selector</summary>

<br/>

```python
all_tasks = requests.get(f"{API_URL}/tasks").json()

if not all_tasks:
    st.info("No tasks available. Create one first.")
else:
    task_options = {
        f"#{t['id']} — {t['title']}": t["id"] for t in all_tasks
    }
    selected_label = st.selectbox("Select a task to edit", list(task_options.keys()))
    selected_id = task_options[selected_label]

    current = requests.get(f"{API_URL}/tasks/{selected_id}").json()
```

This block loads tasks and builds the task selector. 

---

### Load all tasks

```python
all_tasks = requests.get(f"{API_URL}/tasks").json()
```

This sends a GET request to `/tasks` and immediately parses the JSON response.

The result is a Python list of task dictionaries.

---

### Handle the empty case

```python
if not all_tasks:
    st.info("No tasks available. Create one first.")
```

If there are no tasks, the editing workflow cannot continue meaningfully.

So the frontend informs the user and does not attempt to build a selector.

---

### Build a label-to-ID mapping

```python
task_options = {
    f"#{t['id']} — {t['title']}": t["id"] for t in all_tasks
}
```

This is a dictionary comprehension.

It creates a mapping such as:

```python
{
    "#1 — Learn FastAPI": 1,
    "#2 — Build a REST API": 2
}
```

This is clever because:

* the user sees readable labels
* the code still retains the actual numeric IDs

---

### Create the selector

```python
selected_label = st.selectbox("Select a task to edit", list(task_options.keys()))
```

The user selects a label such as:

```text
#2 — Build a REST API
```

Then the code retrieves the corresponding ID:

```python
selected_id = task_options[selected_label]
```

---

### Fetch the current task

```python
current = requests.get(f"{API_URL}/tasks/{selected_id}").json()
```

Once the task ID is known, the frontend fetches the full current version of that task from the backend.

This `current` dictionary becomes the reference point for the editing UI.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s19"></a>

<details>
<summary>19 — Showing the current task values</summary>

<br/>

```python
st.subheader("Current values")
col1, col2 = st.columns(2)
with col1:
    st.write(f"**Title:** {current['title']}")
    st.write(f"**Description:** {current['description'] or '—'}")
with col2:
    st.write(f"**Priority:** {current['priority']}")
    st.write(f"**Status:** {'Completed' if current['completed'] else 'In progress'}")

st.divider()
st.subheader("New values (leave blank to keep current)")
```

This block presents the existing task before the user edits it. 

---

### Why show current values first?

Editing is safer and more transparent when the user can see the current state before making changes.

It answers:

* Which task am I editing?
* What are its current values?
* What would change if I save?

---

### Use of `st.subheader(...)`

This structures the editing page into clear sections:

* current values
* new values

That makes the workflow easier to follow.

---

### Splitting current values into columns

Again, `st.columns(2)` is used to create a cleaner layout.

The left column shows:

* title
* description

The right column shows:

* priority
* status

This improves readability.

---

### The `or '—'` pattern

```python
current['description'] or '—'
```

If the description is an empty string, the UI shows a dash instead of blank space.

That small detail improves visual clarity.

---

### `st.divider()`

This draws a visual separator between:

* the existing task data
* the editing inputs below

It improves the page structure.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s20"></a>

<details>
<summary>20 — The update mode selector — `PATCH` vs `PUT`</summary>

<br/>

```python
mode = st.radio(
    "Update mode",
    ["PATCH — update specific fields only", "PUT — replace everything"],
    horizontal=True,
)
```

This widget lets the user choose how the update should be performed. 

---

### Why this is important

`PUT` and `PATCH` are often discussed abstractly, but this interface makes the distinction concrete.

The backend supports both.
The frontend exposes both deliberately.

---

### The two meanings

| Method  | Meaning                 |
| ------- | ----------------------- |
| `PUT`   | Replace the whole task  |
| `PATCH` | Update only some fields |

That difference affects:

* how the frontend prepares the form
* how the payload is built
* which fields are required
* what the backend does with missing fields

---

### `horizontal=True`

This causes the radio options to be displayed horizontally instead of vertically.

It is mostly a presentation choice, but it works well here because there are only two options and they are closely related.

---

### Why the UI text is explicit

The labels are not just `PUT` and `PATCH`.
They are expanded into:

* `PATCH — update specific fields only`
* `PUT — replace everything`

That is a smart choice because it embeds the meaning directly into the interface.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s21"></a>

<details>
<summary>21 — PUT logic — Replacing a task from the frontend</summary>

<br/>

Inside the form, the code handles PUT like this:

```python
if "PUT" in mode:
    if not new_title.strip():
        st.warning("Title is required for PUT.")
    else:
        payload = {
            "title": new_title.strip(),
            "description": new_description.strip(),
            "priority": new_priority,
            "completed": new_completed,
        }
        response = requests.put(f"{API_URL}/tasks/{selected_id}", json=payload)
        if response.status_code == 200:
            st.success("Task replaced successfully (PUT).")
            st.json(response.json())
        else:
            st.error(f"Error {response.status_code}: {response.text}")
```

This branch runs when the user selects PUT mode. 

---

### Why title is validated again

```python
if not new_title.strip():
    st.warning("Title is required for PUT.")
```

PUT corresponds to a full replacement of the resource.
The backend uses `TaskCreate` for PUT, and `TaskCreate` requires `title`.

So the frontend checks this before sending the request.

---

### The PUT payload

```python
payload = {
    "title": new_title.strip(),
    "description": new_description.strip(),
    "priority": new_priority,
    "completed": new_completed,
}
```

This is a complete task body.
It contains all relevant editable fields.

That matches the semantics of PUT:

> replace the resource with the provided representation

---

### The PUT request

```python
response = requests.put(f"{API_URL}/tasks/{selected_id}", json=payload)
```

This sends a PUT request to the specific task endpoint.

Example:

```text
PUT /tasks/3
```

with a full JSON body.

---

### Handling the response

If the backend returns `200`, the UI confirms success and displays the returned JSON.

If not, the UI shows the raw status code and response text.

---

### Why this is semantically correct

PUT is not being used as “save whatever changed.”
It is being used as:

> submit a complete new version of the task

That is exactly what PUT is supposed to mean.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s22"></a>

<details>
<summary>22 — PATCH logic — Updating only changed fields</summary>

<br/>

The PATCH branch is:

```python
else:
    payload = {}
    if new_title.strip():
        payload["title"] = new_title.strip()
    if new_description.strip() != current["description"]:
        payload["description"] = new_description.strip()
    if new_priority != current["priority"]:
        payload["priority"] = new_priority
    if new_completed != current["completed"]:
        payload["completed"] = new_completed

    if not payload:
        st.info("No changes detected.")
    else:
        response = requests.patch(
            f"{API_URL}/tasks/{selected_id}", json=payload
        )
        if response.status_code == 200:
            st.success(
                f"Task updated successfully (PATCH).  \n"
                f"Fields changed: {', '.join(payload.keys())}"
            )
            st.json(response.json())
        else:
            st.error(f"Error {response.status_code}: {response.text}")
```

This branch runs when the user selects PATCH mode. 

---

### Why start with an empty payload?

```python
payload = {}
```

PATCH should include only the fields that are intended to change.

So the payload starts empty and fields are added selectively.

---

### Title logic

```python
if new_title.strip():
    payload["title"] = new_title.strip()
```

If the title field contains non-whitespace text, it is included.

This supports the UI’s PATCH convention:

* blank title input means “leave current title unchanged”
* non-blank title input means “update the title”

---

### Description comparison

```python
if new_description.strip() != current["description"]:
    payload["description"] = new_description.strip()
```

This compares the new description to the current description.
The field is included only if it actually changed.

---

### Priority comparison

```python
if new_priority != current["priority"]:
    payload["priority"] = new_priority
```

Same idea for priority.

---

### Completion comparison

```python
if new_completed != current["completed"]:
    payload["completed"] = new_completed
```

Again, include the field only if the value changed.

---

### No-change detection

```python
if not payload:
    st.info("No changes detected.")
```

If no field was added to the payload, there is no reason to send a PATCH request.

This avoids unnecessary backend calls and gives the user clear feedback.

---

### The PATCH request

```python
response = requests.patch(
    f"{API_URL}/tasks/{selected_id}", json=payload
)
```

This sends only the changed fields to the backend.

That matches the intended semantics of PATCH exactly.

---

### Success feedback

```python
st.success(
    f"Task updated successfully (PATCH).  \n"
    f"Fields changed: {', '.join(payload.keys())}"
)
```

This is nice because it does not just say the update worked.
It also lists which fields were sent.

That makes the operation more transparent.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s23"></a>

<details>
<summary>23 — Page 4 — `Delete a task`</summary>

<br/>

```python
elif page == "Delete a task":
    st.header("Delete a Task")
```

This block starts the delete page. 

---

### What this page does

It implements the **Delete** operation in CRUD.

The workflow is:

1. load all tasks
2. let the user choose one
3. display a warning summarizing the task
4. require an explicit confirmation click
5. send the DELETE request

---

### Why delete operations deserve extra care

Deletion is destructive.
Once completed, the resource is gone from the in-memory database.

Even in a simple learning project, it is a good habit to:

* make the action explicit
* show what is about to be deleted
* require confirmation

This page follows that pattern.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s24"></a>

<details>
<summary>24 — Sending a DELETE request and refreshing the UI</summary>

<br/>

The delete logic is:

```python
all_tasks = requests.get(f"{API_URL}/tasks").json()

if not all_tasks:
    st.info("No tasks available.")
else:
    task_options = {
        f"#{t['id']} — {t['title']}": t["id"] for t in all_tasks
    }
    selected_label = st.selectbox("Select a task to delete", list(task_options.keys()))
    selected_id = task_options[selected_label]

    current = requests.get(f"{API_URL}/tasks/{selected_id}").json()

    st.warning(
        f"You are about to delete:  \n"
        f"**#{current['id']} — {current['title']}**  \n"
        f"Priority: {current['priority']} | "
        f"Status: {'Completed' if current['completed'] else 'In progress'}"
    )

    col1, col2 = st.columns([1, 4])
    with col1:
        if st.button("Confirm Delete", type="primary"):
            response = requests.delete(f"{API_URL}/tasks/{selected_id}")
            if response.status_code == 204:
                st.success(f"Task #{selected_id} deleted successfully.")
                st.rerun()
            elif response.status_code == 404:
                st.error(f"Task #{selected_id} not found.")
            else:
                st.error(f"Error {response.status_code}: {response.text}")
    with col2:
        st.write("")
```

This block loads the task, shows a warning, performs the delete, and refreshes the UI. 

---

### Loading and selecting tasks

This is similar to the edit page:

* get all tasks
* build a label-to-ID mapping
* let the user choose one

Then the code fetches the full selected task so the UI can present a deletion summary.

---

### The warning message

```python
st.warning(
    f"You are about to delete:  \n"
    f"**#{current['id']} — {current['title']}**  \n"
    f"Priority: {current['priority']} | "
    f"Status: {'Completed' if current['completed'] else 'In progress'}"
)
```

This is an important UX step.

The user sees exactly:

* which task is targeted
* its priority
* its status

That reduces the chance of deleting the wrong task.

---

### The confirmation button

```python
if st.button("Confirm Delete", type="primary"):
```

The deletion happens only if the button is clicked.

That creates an explicit final action.

---

### The DELETE request

```python
response = requests.delete(f"{API_URL}/tasks/{selected_id}")
```

This sends a DELETE request to the backend.

The backend should return:

* `204 No Content` on success
* `404 Not Found` if the task no longer exists

---

### Handling `204`

```python
if response.status_code == 204:
    st.success(f"Task #{selected_id} deleted successfully.")
    st.rerun()
```

A successful delete returns `204`.

Then the UI:

* shows a success message
* calls `st.rerun()`

---

### What `st.rerun()` does

`st.rerun()` tells Streamlit to run the script again from the top.

This is useful because after deletion:

* the selected task should disappear from the dropdown
* the list of tasks has changed
* the current UI state should refresh

Without a rerun, the page might still display stale selection data.

---

### Handling `404`

```python
elif response.status_code == 404:
    st.error(f"Task #{selected_id} not found.")
```

This handles the case where the task disappeared between selection and deletion.

That can happen in multi-user or multi-session systems.
Even though this example is simple, handling it is still correct.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s25"></a>

<details>
<summary>25 — The full annotated file — everything at once</summary>

<br/>

> This is the complete `frontend.py` file with a comment above every meaningful line.
> Read it from top to bottom as a recap of everything covered in this document. 

```python
# ─────────────────────────────────────────────────────────────────────────────
# IMPORTS — bring in the external tools used by this UI
# ─────────────────────────────────────────────────────────────────────────────

# streamlit → framework used to build the web interface
# as st     → short alias so we can write st.title(), st.button(), etc.
import streamlit as st

# requests → HTTP client used to call the FastAPI backend
import requests


# ─────────────────────────────────────────────────────────────────────────────
# BACKEND BASE URL — where the FastAPI server is expected to run
# ─────────────────────────────────────────────────────────────────────────────

# The frontend will call this base URL for all API requests
# Example:
#   GET    http://127.0.0.1:8000/tasks
#   POST   http://127.0.0.1:8000/tasks
#   DELETE http://127.0.0.1:8000/tasks/3
API_URL = "http://127.0.0.1:8000"


# ─────────────────────────────────────────────────────────────────────────────
# PAGE CONFIGURATION — controls browser tab title, icon, and layout
# ─────────────────────────────────────────────────────────────────────────────

st.set_page_config(
    page_title="Task Manager",  # text shown in browser tab
    page_icon="✓",              # icon shown in browser tab
    layout="wide",              # use wide layout instead of narrow centered layout
)


# ─────────────────────────────────────────────────────────────────────────────
# PAGE HEADER — visible title and short description at the top of the page
# ─────────────────────────────────────────────────────────────────────────────

st.title("Task Manager")
st.caption("Streamlit interface connected to the FastAPI backend")


# ─────────────────────────────────────────────────────────────────────────────
# HELPER FUNCTION — check whether the backend is reachable
# ─────────────────────────────────────────────────────────────────────────────

def server_is_up() -> bool:
    # This function returns True if the backend root endpoint responds with 200
    # Otherwise it returns False

    try:
        # Send a small GET request to the API root
        # timeout=2 avoids waiting too long if the backend is down
        r = requests.get(f"{API_URL}/", timeout=2)

        # Return True only if the backend answered with HTTP 200 OK
        return r.status_code == 200

    except Exception:
        # If anything goes wrong (connection error, timeout, etc.),
        # treat the server as unavailable
        return False


# ─────────────────────────────────────────────────────────────────────────────
# EARLY BACKEND CHECK — do not continue if FastAPI is not running
# ─────────────────────────────────────────────────────────────────────────────

if not server_is_up():
    # Show an error box explaining what is wrong and how to fix it
    st.error(
        "Cannot reach the FastAPI server at http://127.0.0.1:8000  \n"
        "Start it first:  `uvicorn main:app --reload`"
    )

    # Stop execution of the rest of the Streamlit script
    st.stop()


# ─────────────────────────────────────────────────────────────────────────────
# SIDEBAR NAVIGATION — choose which page of the UI to display
# ─────────────────────────────────────────────────────────────────────────────

st.sidebar.title("Navigation")

# The selected option is stored in the variable page
page = st.sidebar.radio(
    "Go to",
    ["View all tasks", "Create a task", "Edit a task", "Delete a task"],
)


# ─────────────────────────────────────────────────────────────────────────────
# PAGE 1 — VIEW ALL TASKS
# ─────────────────────────────────────────────────────────────────────────────

if page == "View all tasks":
    st.header("All Tasks")

    # Create two side-by-side columns for the filters
    col1, col2 = st.columns(2)

    with col1:
        # Dropdown used to filter by completion status
        filter_completed = st.selectbox(
            "Filter by status",
            ["All", "Completed", "Not completed"],
        )

    with col2:
        # Dropdown used to filter by priority
        filter_priority = st.selectbox(
            "Filter by priority",
            ["All", "high", "medium", "low"],
        )

    # Build query parameters dynamically based on the selected filters
    params = {}

    if filter_completed == "Completed":
        params["completed"] = "true"
    elif filter_completed == "Not completed":
        params["completed"] = "false"

    if filter_priority != "All":
        params["priority"] = filter_priority

    # Send GET /tasks with optional query parameters
    response = requests.get(f"{API_URL}/tasks", params=params)

    if response.status_code == 200:
        # Convert JSON response into Python objects
        tasks = response.json()

        if not tasks:
            # No matching tasks
            st.info("No tasks match the selected filters.")
        else:
            # Show how many tasks were returned
            st.write(f"**{len(tasks)} task(s) found**")

            # Loop through all returned tasks
            for task in tasks:
                # Status marker depends on task completion
                status_icon = "✅" if task["completed"] else "⬜"

                # Priority marker depends on priority level
                priority_color = {
                    "high": "🔴",
                    "medium": "🟡",
                    "low": "🟢",
                }.get(task["priority"], "⚪")

                # Use an expander so each task can be opened or collapsed
                with st.expander(
                    f"{status_icon} #{task['id']} — {task['title']}  {priority_color} {task['priority'].upper()}"
                ):
                    # Split task details into two columns for readability
                    col_a, col_b = st.columns(2)

                    with col_a:
                        st.write(f"**Description:** {task['description'] or '—'}")
                        st.write(f"**Priority:** {task['priority']}")

                    with col_b:
                        st.write(f"**Status:** {'Completed' if task['completed'] else 'In progress'}")
                        st.write(f"**Created:** {task['created_at']}")
                        st.write(f"**ID:** {task['id']}")

    else:
        # The backend returned an unexpected error
        st.error(f"Error {response.status_code}: {response.text}")


# ─────────────────────────────────────────────────────────────────────────────
# PAGE 2 — CREATE A TASK
# ─────────────────────────────────────────────────────────────────────────────

elif page == "Create a task":
    st.header("Create a New Task")

    # Group all create inputs inside a form
    # clear_on_submit=True resets the fields after submission
    with st.form("create_form", clear_on_submit=True):
        # Required title field
        title = st.text_input("Title *", placeholder="e.g. Learn FastAPI")

        # Optional description field
        description = st.text_area("Description", placeholder="Optional description...")

        # Priority selector
        priority = st.selectbox("Priority", ["medium", "high", "low"])

        # Checkbox to mark the task completed immediately
        completed = st.checkbox("Mark as completed immediately", value=False)

        # Submit button for the form
        submitted = st.form_submit_button("Create Task", type="primary")

    if submitted:
        # Frontend validation: title must not be empty after stripping spaces
        if not title.strip():
            st.warning("Title is required.")
        else:
            # Build the JSON payload expected by the backend
            payload = {
                "title": title.strip(),
                "description": description.strip(),
                "priority": priority,
                "completed": completed,
            }

            # Send POST /tasks
            response = requests.post(f"{API_URL}/tasks", json=payload)

            if response.status_code == 201:
                # Backend created the task successfully
                created = response.json()

                st.success(
                    f"Task created successfully!  \n"
                    f"ID: **{created['id']}** — {created['title']}"
                )

                # Show the returned JSON task object
                st.json(created)

            elif response.status_code == 422:
                # Validation error returned by FastAPI/Pydantic
                errors = response.json().get("detail", [])

                for err in errors:
                    # Convert ["body", "title"] into "body → title"
                    field = " → ".join(str(x) for x in err.get("loc", []))
                    st.error(f"Validation error in {field}: {err.get('msg')}")

            else:
                # Generic unexpected error
                st.error(f"Error {response.status_code}: {response.text}")


# ─────────────────────────────────────────────────────────────────────────────
# PAGE 3 — EDIT A TASK
# ─────────────────────────────────────────────────────────────────────────────

elif page == "Edit a task":
    st.header("Edit a Task")

    # Load all tasks so we can populate the selector
    all_tasks = requests.get(f"{API_URL}/tasks").json()

    if not all_tasks:
        # No tasks exist yet, so editing is not possible
        st.info("No tasks available. Create one first.")
    else:
        # Build readable labels mapped to real task IDs
        task_options = {
            f"#{t['id']} — {t['title']}": t["id"] for t in all_tasks
        }

        # Let the user choose a task to edit
        selected_label = st.selectbox("Select a task to edit", list(task_options.keys()))
        selected_id = task_options[selected_label]

        # Fetch the full selected task from the backend
        current = requests.get(f"{API_URL}/tasks/{selected_id}").json()

        # Show current values
        st.subheader("Current values")
        col1, col2 = st.columns(2)

        with col1:
            st.write(f"**Title:** {current['title']}")
            st.write(f"**Description:** {current['description'] or '—'}")

        with col2:
            st.write(f"**Priority:** {current['priority']}")
            st.write(f"**Status:** {'Completed' if current['completed'] else 'In progress'}")

        # Visual separator
        st.divider()
        st.subheader("New values (leave blank to keep current)")

        # Let the user choose PATCH or PUT
        mode = st.radio(
            "Update mode",
            ["PATCH — update specific fields only", "PUT — replace everything"],
            horizontal=True,
        )

        # Editing form
        with st.form("edit_form"):
            # In PUT mode, prefill with current value because PUT replaces everything
            # In PATCH mode, leave title empty to mean "do not change unless filled"
            new_title = st.text_input(
                "Title",
                value=current["title"] if "PUT" in mode else "",
                placeholder="Leave blank to keep current" if "PATCH" in mode else "",
            )

            new_description = st.text_area(
                "Description",
                value=current["description"] if "PUT" in mode else "",
            )

            new_priority = st.selectbox(
                "Priority",
                ["medium", "high", "low"],
                index=["medium", "high", "low"].index(current["priority"]),
            )

            new_completed = st.checkbox(
                "Completed",
                value=current["completed"],
            )

            save = st.form_submit_button("Save Changes", type="primary")

        if save:
            if "PUT" in mode:
                # PUT means full replacement, so title is required
                if not new_title.strip():
                    st.warning("Title is required for PUT.")
                else:
                    # Build a complete replacement payload
                    payload = {
                        "title": new_title.strip(),
                        "description": new_description.strip(),
                        "priority": new_priority,
                        "completed": new_completed,
                    }

                    # Send PUT /tasks/{id}
                    response = requests.put(f"{API_URL}/tasks/{selected_id}", json=payload)

                    if response.status_code == 200:
                        st.success("Task replaced successfully (PUT).")
                        st.json(response.json())
                    else:
                        st.error(f"Error {response.status_code}: {response.text}")

            else:
                # PATCH means partial update, so only changed fields should be sent
                payload = {}

                if new_title.strip():
                    payload["title"] = new_title.strip()

                if new_description.strip() != current["description"]:
                    payload["description"] = new_description.strip()

                if new_priority != current["priority"]:
                    payload["priority"] = new_priority

                if new_completed != current["completed"]:
                    payload["completed"] = new_completed

                if not payload:
                    # Nothing changed, so there is no request to send
                    st.info("No changes detected.")
                else:
                    # Send PATCH /tasks/{id} with only the changed fields
                    response = requests.patch(
                        f"{API_URL}/tasks/{selected_id}", json=payload
                    )

                    if response.status_code == 200:
                        st.success(
                            f"Task updated successfully (PATCH).  \n"
                            f"Fields changed: {', '.join(payload.keys())}"
                        )
                        st.json(response.json())
                    else:
                        st.error(f"Error {response.status_code}: {response.text}")


# ─────────────────────────────────────────────────────────────────────────────
# PAGE 4 — DELETE A TASK
# ─────────────────────────────────────────────────────────────────────────────

elif page == "Delete a task":
    st.header("Delete a Task")

    # Load all tasks to populate the delete selector
    all_tasks = requests.get(f"{API_URL}/tasks").json()

    if not all_tasks:
        st.info("No tasks available.")
    else:
        # Build readable labels mapped to IDs
        task_options = {
            f"#{t['id']} — {t['title']}": t["id"] for t in all_tasks
        }

        # Let the user choose the task to delete
        selected_label = st.selectbox("Select a task to delete", list(task_options.keys()))
        selected_id = task_options[selected_label]

        # Fetch the current task for the warning message
        current = requests.get(f"{API_URL}/tasks/{selected_id}").json()

        # Show a warning before deletion
        st.warning(
            f"You are about to delete:  \n"
            f"**#{current['id']} — {current['title']}**  \n"
            f"Priority: {current['priority']} | "
            f"Status: {'Completed' if current['completed'] else 'In progress'}"
        )

        # Use two columns so the delete button does not stretch awkwardly
        col1, col2 = st.columns([1, 4])

        with col1:
            if st.button("Confirm Delete", type="primary"):
                # Send DELETE /tasks/{id}
                response = requests.delete(f"{API_URL}/tasks/{selected_id}")

                if response.status_code == 204:
                    # Successful deletion
                    st.success(f"Task #{selected_id} deleted successfully.")

                    # Rerun the app so the selector refreshes and the deleted task disappears
                    st.rerun()

                elif response.status_code == 404:
                    # Task no longer exists
                    st.error(f"Task #{selected_id} not found.")

                else:
                    # Generic unexpected error
                    st.error(f"Error {response.status_code}: {response.text}")

        with col2:
            # Empty placeholder column for spacing/alignment
            st.write("")
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<p align="center">
  <strong>End of Code Explained — frontend.py</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>
```
