<a id="top"></a>

# Part 2 — Build a Streamlit Frontend for Your API

> **This is the direct continuation of Part 1 — Final Evaluation (Build a Complete REST API).**
> You must work on the **exact same subject** you chose or were assigned in Part 1.
> Do NOT start a new project. Do NOT switch subjects.
> Your `main.py` from Part 1 is the backend. This assignment adds a `frontend.py` on top of it.

---

## ⚠️ Important — Same Subject, Same Project

| Part 1 subject | Your resource | Your API routes | Your frontend pages |
|---|---|---|---|
| Subject 1 — Book Library | `Book` | `/books` | books pages |
| Subject 2 — Student Grade Manager | `Student` | `/students` | students pages |
| Subject 3 — Movie Watchlist | `Movie` | `/movies` | movies pages |
| Subject 4 — Bug Tracker | `Bug` | `/bugs` | bugs pages |
| Subject 5 — Recipe Manager | `Recipe` | `/recipes` | recipes pages |
| Subject 6 — Workout Log | `Workout` | `/workouts` | workouts pages |

**If you built a Book Library API in Part 1, your Streamlit app manages books.**
**If you built a Bug Tracker in Part 1, your Streamlit app manages bugs.**
And so on. The frontend must match the backend you already built.

---

## Before You Start — Verify Your Part 1 Backend Works

Before writing a single line of Streamlit code, confirm that your `main.py` is still working:

```bash
# In your project folder, activate venv and start the server
uvicorn main:app --reload
```

Then open `http://127.0.0.1:8000/docs` — you must see all 7 endpoints working.

If your backend has bugs, fix them first. A broken API = a broken frontend.

---

## Table of Contents

| # | Section |
|---|---|
| 1 | [What You Are Building](#s1) |
| 2 | [How Streamlit Works — The Basics](#s2) |
| 3 | [How Streamlit Talks to Your API — The `requests` Library](#s3) |
| 4 | [Required Pages and Features](#s4) |
| 5 | [Streamlit Components You Must Use](#s5) |
| 6 | [Grading Rubric](#s6) |
| 7 | [Deliverables](#s7) |
| 8 | [How to Run Both Servers](#s8) |
| 9 | [Streamlit Reference Card](#s9) |
| 10 | [Per-Subject Adaptation Guide](#s10) |
| 11 | [Bonus Challenges](#s11) |

---

<a id="s1"></a>

<details>
<summary>1 — What You Are Building</summary>

<br/>

### The assignment in one sentence

> Build a **Streamlit web interface** (`frontend.py`) that lets a user interact with **the FastAPI backend you built in Part 1** through a browser — without typing a single curl command or opening Swagger.

---

### This is Part 2 — you continue YOUR Part 1 project

You already built a FastAPI CRUD API in Part 1. You chose a subject:

- Subject 1 → Book Library → your API is at `/books`
- Subject 2 → Student Grade Manager → your API is at `/students`
- Subject 3 → Movie Watchlist → your API is at `/movies`
- Subject 4 → Bug Tracker → your API is at `/bugs`
- Subject 5 → Recipe Manager → your API is at `/recipes`
- Subject 6 → Workout Log → your API is at `/workouts`

Your `frontend.py` **must connect to the API you built**.
The resource names, field names, routes, and filters in your frontend must exactly match your Part 1 `main.py`.

> See **Section 10 — Per-Subject Adaptation Guide** for the exact values to use for your subject.

---

### The big picture

```
USER
  │
  │  clicks buttons, fills forms, reads results
  ▼
STREAMLIT (frontend.py — port 8501)
  │
  │  sends HTTP requests using the `requests` library
  │  GET, POST, PUT, PATCH, DELETE → http://127.0.0.1:8000
  ▼
YOUR FASTAPI API (main.py — port 8000)
  │
  │  reads/writes the in-memory dictionary
  ▼
JSON response
  │
  ▼
STREAMLIT displays the result to the user
```

---

### What the user experience looks like

Your app must have a **sidebar with navigation** that lets the user switch between pages.

At minimum, 4 pages are required:

| Page | What the user can do |
|---|---|
| **List / View all** | See all records, apply filters, browse results |
| **Create** | Fill in a form and add a new record |
| **Edit** | Select a record and update it (PATCH or PUT) |
| **Delete** | Select a record and delete it with a confirmation |

---

### Technology stack

| Tool | Role | Install command |
|---|---|---|
| `streamlit` | Builds the web UI — runs in the browser | `pip install streamlit` |
| `requests` | Makes HTTP calls to the FastAPI API | `pip install requests` |

Both are already in your `requirements.txt` (add them if missing).

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s2"></a>

<details>
<summary>2 — How Streamlit Works — The Basics</summary>

<br/>

### What is Streamlit?

Streamlit is a Python library that turns a `.py` script into a **web application** that runs in your browser.

You do not write HTML, CSS, or JavaScript.
You write Python — Streamlit handles everything else.

---

### How Streamlit re-runs your script

Every time a user:
- Clicks a button
- Selects a value in a dropdown
- Types in a text field
- Submits a form

Streamlit **re-runs the entire `frontend.py` script from top to bottom**.

This means: every widget you write produces a value that you can use immediately in the same script.

---

### The basic structure of a Streamlit app

```python
import streamlit as st

# 1. Page configuration (must be the FIRST streamlit call)
st.set_page_config(page_title="My App", layout="wide")

# 2. Title
st.title("My Application")

# 3. Sidebar navigation
page = st.sidebar.radio("Go to", ["Page 1", "Page 2"])

# 4. Conditional content based on selected page
if page == "Page 1":
    st.header("Page 1")
    st.write("Welcome to page 1")

elif page == "Page 2":
    st.header("Page 2")
    st.write("Welcome to page 2")
```

---

### Key Streamlit concepts

| Concept | What it means |
|---|---|
| **Widget** | An interactive element: button, text input, dropdown, checkbox |
| **Re-run** | Every user interaction re-runs the whole script |
| **State** | By default, values reset on re-run — use `st.session_state` to persist them |
| **Layout** | You can arrange widgets in columns, tabs, expanders |

---

### The API base URL constant

At the top of your `frontend.py`, define the API URL as a constant:

```python
API_URL = "http://127.0.0.1:8000"
```

Use this variable everywhere — never hardcode the URL in multiple places.

---

### Always check if the server is running

At the start of your app (after the title), add a health check:

```python
def server_is_up() -> bool:
    try:
        r = requests.get(f"{API_URL}/", timeout=2)
        return r.status_code == 200
    except Exception:
        return False

if not server_is_up():
    st.error("Cannot reach the API server. Start it with: uvicorn main:app --reload")
    st.stop()
```

`st.stop()` stops the script from running further — no point showing the UI if the API is down.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>3 — How Streamlit Talks to Your API — The `requests` Library</summary>

<br/>

### The `requests` library

`requests` is a Python library for making HTTP calls.
You already used it in previous exercises — here you use it inside Streamlit.

---

### The 5 HTTP calls you need

#### GET — fetch data (no body)

```python
# Get all records
response = requests.get(f"{API_URL}/items")

# Get all with filters
response = requests.get(f"{API_URL}/items", params={"completed": "true"})

# Get one by ID
response = requests.get(f"{API_URL}/items/3")
```

#### POST — create a new record

```python
payload = {
    "name": "Alice",
    "email": "alice@example.com"
}
response = requests.post(f"{API_URL}/items", json=payload)
```

#### PUT — replace entirely

```python
payload = {
    "name": "Alice Updated",
    "email": "alice.new@example.com",
    "active": True
}
response = requests.put(f"{API_URL}/items/3", json=payload)
```

#### PATCH — update specific fields

```python
payload = {"active": False}  # only what changes
response = requests.patch(f"{API_URL}/items/3", json=payload)
```

#### DELETE — remove a record

```python
response = requests.delete(f"{API_URL}/items/3")
```

---

### Reading the response

```python
# Status code — 200, 201, 204, 404, 422...
response.status_code

# Response body as a Python dictionary or list
response.json()

# Check if the request succeeded (status 200-299)
response.ok   # True or False
```

---

### Pattern: call the API and show the result

```python
response = requests.get(f"{API_URL}/items")

if response.status_code == 200:
    items = response.json()
    st.success(f"{len(items)} items found")
    for item in items:
        st.write(item)
elif response.status_code == 404:
    st.warning("Not found.")
else:
    st.error(f"Error {response.status_code}: {response.text}")
```

---

### Pattern: submit a form and handle the response

```python
with st.form("create_form", clear_on_submit=True):
    name = st.text_input("Name *")
    submitted = st.form_submit_button("Create", type="primary")

if submitted:
    if not name.strip():
        st.warning("Name is required.")
    else:
        response = requests.post(f"{API_URL}/items", json={"name": name})
        if response.status_code == 201:
            st.success("Created!")
            st.json(response.json())
        else:
            st.error(f"Error: {response.text}")
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>4 — Required Pages and Features</summary>

<br/>

> Adapt the page names and field names to YOUR subject (books, students, movies, bugs, recipes, workouts).
> The structure and requirements below apply to all subjects.

---

### Page 1 — View all records

**This page must:**

- [ ] Call `GET /your-resource` when the page loads
- [ ] Display all records returned by the API
- [ ] Show at least **2 dropdown filters** that match your API's query parameters
- [ ] Build the `params` dict based on selected filters and pass it to `requests.get()`
- [ ] Display a count: "**X records found**"
- [ ] Display each record in an **expandable card** (`st.expander`) showing all fields
- [ ] Show a message like "No records match the selected filters." when the list is empty
- [ ] Show an icon or color indicator for the key boolean field (e.g. ✅ / ⬜ for completed, ⭐ for favorite)

---

### Page 2 — Create a new record

**This page must:**

- [ ] Display a **form** using `st.form()`
- [ ] Include an input for every field of your `XxxCreate` model
- [ ] Use the correct widget for each field type:
  - String fields → `st.text_input()` or `st.text_area()`
  - Boolean fields → `st.checkbox()`
  - Fields with fixed options → `st.selectbox()` (e.g. priority, genre, status)
  - Number fields → `st.number_input()`
- [ ] Validate the required field(s) on the client side before calling the API
- [ ] Call `POST /your-resource` when the form is submitted
- [ ] Show a **success message** with the created record's ID if the response is 201
- [ ] Display the full created record using `st.json()`
- [ ] Show an **error message** if the response is 422 or other error

---

### Page 3 — Edit a record

**This page must:**

- [ ] Load all records with `GET /your-resource` to populate a dropdown selector
- [ ] Let the user select which record to edit using `st.selectbox()`
- [ ] Show the **current values** of the selected record before editing
- [ ] Offer a choice between **PATCH** (partial update) and **PUT** (full replacement) using `st.radio()`
- [ ] Pre-fill form fields with current values when PUT is selected
- [ ] Leave form fields empty when PATCH is selected (send only changed fields)
- [ ] Call `PATCH` or `PUT /your-resource/{id}` on form submission
- [ ] Show the updated record after a successful save
- [ ] Handle the case where no changes were made (for PATCH)

---

### Page 4 — Delete a record

**This page must:**

- [ ] Load all records to populate a dropdown selector
- [ ] Show a **warning message** with the record details before deletion
- [ ] Require the user to click a **"Confirm Delete"** button to actually delete
- [ ] Call `DELETE /your-resource/{id}` when confirmed
- [ ] Show a **success message** after deletion
- [ ] Handle the 404 case (record already deleted)
- [ ] Refresh the page automatically after deletion using `st.rerun()`

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s5"></a>

<details>
<summary>5 — Streamlit Components You Must Use</summary>

<br/>

> Your `frontend.py` must use **at least one** of each component listed below.

---

### Layout components

| Component | How to use it | When to use it |
|---|---|---|
| `st.sidebar.radio()` | `page = st.sidebar.radio("Go to", ["Page 1", "Page 2"])` | Main navigation |
| `st.columns(n)` | `col1, col2 = st.columns(2)` | Side-by-side layout |
| `st.expander()` | `with st.expander("Details"):` | Collapsible card for each record |
| `st.divider()` | `st.divider()` | Horizontal line between sections |
| `st.tabs()` | `tab1, tab2 = st.tabs(["A", "B"])` | Optional — for sub-sections |

---

### Display components

| Component | How to use it | When to use it |
|---|---|---|
| `st.title()` | `st.title("My App")` | Page title (once at top) |
| `st.header()` | `st.header("All Books")` | Section title |
| `st.subheader()` | `st.subheader("Current values")` | Sub-section title |
| `st.write()` | `st.write("Hello")` | General text or dict display |
| `st.json()` | `st.json(response.json())` | Show raw JSON nicely formatted |
| `st.caption()` | `st.caption("Connected to FastAPI")` | Small grey text |
| `st.metric()` | `st.metric("Total", 42)` | Big number highlight |

---

### Feedback components

| Component | When to use it |
|---|---|
| `st.success("Done!")` | After a successful API call |
| `st.error("Something went wrong")` | After a failed API call |
| `st.warning("Are you sure?")` | Before a destructive action (delete) |
| `st.info("No records found.")` | When a list comes back empty |
| `st.spinner("Loading...")` | While waiting for a slow API response |

---

### Input widgets

| Widget | How to use it | For |
|---|---|---|
| `st.text_input("Label")` | Short text | `name`, `title`, `email` |
| `st.text_area("Label")` | Long text | `description` |
| `st.number_input("Label", min_value=0)` | Number | `grade`, `rating`, `duration_minutes` |
| `st.selectbox("Label", options)` | Dropdown | `priority`, `genre`, `status`, `category` |
| `st.checkbox("Label", value=False)` | Toggle | `completed`, `read`, `favorite`, `watched` |
| `st.radio("Label", options)` | Radio buttons | PATCH vs PUT choice |

---

### Form components

```python
# Always wrap inputs in a form to prevent re-runs on every keystroke
with st.form("my_form", clear_on_submit=True):
    name = st.text_input("Name")
    submitted = st.form_submit_button("Submit", type="primary")

# Handle submission OUTSIDE the form block
if submitted:
    # call the API here
```

> `clear_on_submit=True` resets all form fields after the user clicks submit.
> The form **only triggers a re-run when the submit button is clicked** — not on every keystroke.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>6 — Grading Rubric</summary>

<br/>

| Criteria | Points |
|---|---|
| **Page 1 — View all:** loads and displays records correctly | 15 |
| **Page 1 — Filters:** at least 2 working dropdown filters | 10 |
| **Page 1 — Cards:** records displayed in `st.expander()` with all fields | 10 |
| **Page 2 — Create form:** correct widgets for each field type | 10 |
| **Page 2 — POST call:** correct response handling (201 / 422 / error) | 10 |
| **Page 3 — Edit:** loads current values, PATCH and PUT both work | 15 |
| **Page 4 — Delete:** confirmation warning + 204 handling + `st.rerun()` | 10 |
| **Health check:** `server_is_up()` function + `st.stop()` if down | 5 |
| **Code quality:** `API_URL` constant, no hardcoded URLs, readable code | 5 |
| **No crashes:** app runs without errors with 4+ records in the API | 5 |
| **Bonus features** (see section 10) | +15 |
| **Total** | **95 + 15 bonus** |

---

### Common mistakes that lose points

| Mistake | Points lost |
|---|---|
| Hardcoding `http://127.0.0.1:8000` in multiple places (not using `API_URL`) | -3 |
| No validation before POST — calling the API even when required field is empty | -5 |
| Not handling the 404 case in the delete page | -3 |
| App crashes if the API is not running (no health check) | -5 |
| Forms that re-run on every keystroke (not using `st.form()`) | -3 |
| Showing raw Python dicts with `print()` instead of `st.write()` or `st.json()` | -2 |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s7"></a>

<details>
<summary>7 — Deliverables</summary>

<br/>

You must submit **the same project folder as Part 1**, now with `frontend.py` added inside it.

> Do NOT create a new folder. Do NOT rename `main.py`. Just add `frontend.py` to your existing project.

```text
your_project_folder/          ← same folder you submitted for Part 1
├── main.py                   ← UNCHANGED from Part 1 (fix bugs if needed, do not rewrite)
├── frontend.py               ← NEW — this is what you build in Part 2
├── requirements.txt          ← updated to include streamlit and requests
└── test_requests.http        ← UNCHANGED from Part 1
```

---

### How your frontend must match your backend

| In `main.py` (Part 1) | Must match in `frontend.py` (Part 2) |
|---|---|
| Route: `GET /books` | `requests.get(f"{API_URL}/books")` |
| Field: `title` | `st.text_input("Title *")` |
| Field: `read` (boolean) | `st.checkbox("Read")` |
| Filter: `?genre=fiction` | `params["genre"] = filter_genre` |
| Status code: `201` | `if response.status_code == 201:` |

If your Part 1 API uses `/students` with a field called `grade`, then your Streamlit form must have a `grade` input — not a `score` input, not a `mark` input. **Exact same names.**

---

### `requirements.txt` must include at minimum

```text
fastapi
uvicorn
pydantic
streamlit
requests
```

---

### Your `frontend.py` file must

- [ ] Start with a docstring explaining what the file does
- [ ] Have the `API_URL` constant at the top
- [ ] Have the `server_is_up()` health check function
- [ ] Have 4 pages navigable from the sidebar
- [ ] Be runnable with `streamlit run frontend.py` without any errors
- [ ] Work correctly when both servers are running at the same time

---

### Naming convention

Name your pages to match your subject:

| Subject | Suggested page names |
|---|---|
| Book Library | "View all books", "Add a book", "Edit a book", "Delete a book" |
| Student Grades | "View all students", "Add a student", "Edit a student", "Delete a student" |
| Movie Watchlist | "View all movies", "Add a movie", "Edit a movie", "Delete a movie" |
| Bug Tracker | "View all bugs", "Report a bug", "Update a bug", "Close a bug" |
| Recipe Manager | "View all recipes", "Add a recipe", "Edit a recipe", "Delete a recipe" |
| Workout Log | "View all workouts", "Log a workout", "Edit a workout", "Delete a workout" |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s8"></a>

<details>
<summary>8 — How to Run Both Servers</summary>

<br/>

> You need **two terminals open at the same time** — one for FastAPI, one for Streamlit.

---

### Terminal 1 — Start the FastAPI backend

```bash
# Navigate to your project folder
cd your_project_folder

# Activate the virtual environment
source venv/bin/activate        # macOS / Linux
venv\Scripts\activate           # Windows

# Start the FastAPI server
uvicorn main:app --reload
```

You should see:
```text
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Application startup complete.
```

---

### Terminal 2 — Start the Streamlit frontend

Open a **second terminal** (do not close the first one).

```bash
# Navigate to your project folder
cd your_project_folder

# Activate the virtual environment (same venv)
source venv/bin/activate        # macOS / Linux
venv\Scripts\activate           # Windows

# Start the Streamlit app
streamlit run frontend.py
```

You should see:
```text
  You can now view your Streamlit app in your browser.
  Local URL: http://localhost:8501
  Network URL: http://192.168.x.x:8501
```

A browser tab opens automatically.

---

### What is running where

| Server | URL | What you see |
|---|---|---|
| FastAPI backend | `http://127.0.0.1:8000` | JSON responses |
| FastAPI docs | `http://127.0.0.1:8000/docs` | Swagger UI |
| Streamlit frontend | `http://localhost:8501` | Your web interface |

---

### In VS Code — open two terminals side by side

1. Press **`Ctrl + `` `** to open a terminal
2. Run `uvicorn main:app --reload`
3. Click the **`+`** button in the terminal panel to open a second terminal
4. Run `streamlit run frontend.py`
5. Both are visible at the bottom of VS Code simultaneously

---

### Stop both servers

- Terminal 1: press `Ctrl + C` to stop FastAPI
- Terminal 2: press `Ctrl + C` to stop Streamlit

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>9 — Streamlit Reference Card</summary>

<br/>

> Quick copy-paste reference for the most useful Streamlit patterns.

---

### Sidebar navigation

```python
st.sidebar.title("Navigation")
page = st.sidebar.radio(
    "Go to",
    ["View all", "Create", "Edit", "Delete"]
)
```

---

### Columns layout

```python
col1, col2 = st.columns(2)
with col1:
    st.write("Left side")
with col2:
    st.write("Right side")

# Unequal widths
col1, col2, col3 = st.columns([1, 2, 1])
```

---

### Expandable card for a record

```python
for item in items:
    with st.expander(f"#{item['id']} — {item['name']}"):
        st.write(f"**Field 1:** {item['field1']}")
        st.write(f"**Field 2:** {item['field2']}")
```

---

### Dropdown filter with "All" option

```python
filter_status = st.selectbox(
    "Filter by status",
    ["All", "option1", "option2", "option3"]
)

params = {}
if filter_status != "All":
    params["status"] = filter_status

response = requests.get(f"{API_URL}/items", params=params)
```

---

### Create form

```python
with st.form("create_form", clear_on_submit=True):
    name = st.text_input("Name *", placeholder="Required field")
    description = st.text_area("Description", placeholder="Optional")
    category = st.selectbox("Category", ["option1", "option2"])
    active = st.checkbox("Active", value=True)
    number = st.number_input("Rating", min_value=0, max_value=10, value=0)
    submitted = st.form_submit_button("Create", type="primary")

if submitted:
    if not name.strip():
        st.warning("Name is required.")
    else:
        payload = {
            "name": name.strip(),
            "description": description.strip(),
            "category": category,
            "active": active,
            "number": number,
        }
        response = requests.post(f"{API_URL}/items", json=payload)
        if response.status_code == 201:
            st.success(f"Created! ID: {response.json()['id']}")
            st.json(response.json())
        else:
            st.error(f"Error {response.status_code}: {response.text}")
```

---

### Populate dropdown from API data

```python
all_items = requests.get(f"{API_URL}/items").json()

if not all_items:
    st.info("No records found.")
else:
    options = {f"#{item['id']} — {item['name']}": item["id"] for item in all_items}
    selected_label = st.selectbox("Select a record", list(options.keys()))
    selected_id = options[selected_label]
    current = requests.get(f"{API_URL}/items/{selected_id}").json()
```

---

### PATCH — only send changed fields

```python
payload = {}
if new_name.strip() and new_name.strip() != current["name"]:
    payload["name"] = new_name.strip()
if new_active != current["active"]:
    payload["active"] = new_active

if not payload:
    st.info("No changes detected.")
else:
    response = requests.patch(f"{API_URL}/items/{selected_id}", json=payload)
    if response.status_code == 200:
        st.success(f"Updated fields: {', '.join(payload.keys())}")
        st.json(response.json())
```

---

### Delete with confirmation

```python
current = requests.get(f"{API_URL}/items/{selected_id}").json()

st.warning(f"You are about to delete: **#{current['id']} — {current['name']}**")

if st.button("Confirm Delete", type="primary"):
    response = requests.delete(f"{API_URL}/items/{selected_id}")
    if response.status_code == 204:
        st.success(f"Record #{selected_id} deleted.")
        st.rerun()
    elif response.status_code == 404:
        st.error("Record not found — already deleted?")
    else:
        st.error(f"Error {response.status_code}: {response.text}")
```

---

### Loading spinner

```python
with st.spinner("Loading data from API..."):
    response = requests.get(f"{API_URL}/items")
    items = response.json()
```

---

### Metrics row

```python
total = len(items)
active = sum(1 for item in items if item["active"])
inactive = total - active

col1, col2, col3 = st.columns(3)
col1.metric("Total records", total)
col2.metric("Active", active)
col3.metric("Inactive", inactive)
```

---

### Show formatted JSON

```python
# Show a Python dict as nicely formatted JSON
st.json(some_dict)

# Show raw text (less pretty)
st.text(some_string)

# Show a DataFrame-like table
import pandas as pd
st.dataframe(pd.DataFrame(items))
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>10 — Per-Subject Adaptation Guide — What to change for YOUR subject</summary>

<br/>

> This section tells you **exactly** what words to change in your `frontend.py` depending on which subject you did in Part 1.
> Everything else (structure, patterns, logic) stays the same.

---

### Subject 1 — Book Library

| Element | Value to use |
|---|---|
| `API_URL` resource path | `/books` |
| Page title | `"Book Library"` |
| Page names | `"View all books"`, `"Add a book"`, `"Edit a book"`, `"Delete a book"` |
| Required field for POST | `title` |
| Second required field | `author` |
| Boolean field (checkbox) | `read` (Has the book been read?) |
| Filter 1 dropdown | `"Filter by status"` → `["All", "Read", "Not read"]` → `?read=true/false` |
| Filter 2 dropdown | `"Filter by genre"` → `["All", "fiction", "non-fiction", "science", "history", "general"]` |
| Selectbox fields | `genre`, `rating` (0–5 as number_input) |
| Card icon | ✅ if `read=true`, 📖 if `read=false` |
| Key display fields | `title`, `author`, `genre`, `read`, `rating`, `created_at` |

---

### Subject 2 — Student Grade Manager

| Element | Value to use |
|---|---|
| `API_URL` resource path | `/students` |
| Page title | `"Student Grade Manager"` |
| Page names | `"View all students"`, `"Add a student"`, `"Edit a student"`, `"Delete a student"` |
| Required field for POST | `name` |
| Second required field | `course` |
| Boolean field (checkbox) | `passed` (Did the student pass?) |
| Filter 1 dropdown | `"Filter by status"` → `["All", "Passed", "Failed"]` → `?passed=true/false` |
| Filter 2 dropdown | `"Filter by course"` → list of course names OR free text input |
| Number input | `grade` (0.0 to 100.0, step 0.5) |
| Selectbox fields | `semester` → `["fall", "winter"]` |
| Card icon | ✅ if `passed=true`, ❌ if `passed=false` |
| Key display fields | `name`, `course`, `grade`, `passed`, `semester`, `created_at` |

---

### Subject 3 — Movie Watchlist

| Element | Value to use |
|---|---|
| `API_URL` resource path | `/movies` |
| Page title | `"Movie Watchlist"` |
| Page names | `"View all movies"`, `"Add a movie"`, `"Edit a movie"`, `"Delete a movie"` |
| Required field for POST | `title` |
| Optional text field | `director` |
| Boolean field (checkbox) | `watched` (Has the movie been watched?) |
| Filter 1 dropdown | `"Filter by status"` → `["All", "Watched", "Not watched"]` → `?watched=true/false` |
| Filter 2 dropdown | `"Filter by genre"` → `["All", "action", "drama", "comedy", "horror", "sci-fi", "other"]` |
| Number input | `rating` (0 to 10, step 1) |
| Card icon | 🎬 if `watched=true`, 👁️ if `watched=false` |
| Key display fields | `title`, `director`, `genre`, `watched`, `rating`, `created_at` |

---

### Subject 4 — Bug Tracker

| Element | Value to use |
|---|---|
| `API_URL` resource path | `/bugs` |
| Page title | `"Bug Tracker"` |
| Page names | `"View all bugs"`, `"Report a bug"`, `"Update a bug"`, `"Close a bug"` |
| Required field for POST | `title` |
| Optional text area | `description` |
| Boolean field (checkbox) | `resolved` (Is the bug fixed?) |
| Filter 1 dropdown | `"Filter by status"` → `["All", "open", "in_progress", "closed"]` → `?status=...` |
| Filter 2 dropdown | `"Filter by severity"` → `["All", "low", "medium", "high", "critical"]` → `?severity=...` |
| Selectbox fields | `status` → `["open", "in_progress", "closed"]`, `severity` → `["low", "medium", "high", "critical"]` |
| Card icon | 🔴 if `severity=critical`, 🟠 if `high`, 🟡 if `medium`, 🟢 if `low` |
| Key display fields | `title`, `status`, `severity`, `resolved`, `description`, `created_at` |

---

### Subject 5 — Recipe Manager

| Element | Value to use |
|---|---|
| `API_URL` resource path | `/recipes` |
| Page title | `"Recipe Manager"` |
| Page names | `"View all recipes"`, `"Add a recipe"`, `"Edit a recipe"`, `"Delete a recipe"` |
| Required field for POST | `name` |
| Boolean field (checkbox) | `favorite` (Is this a favorite?) |
| Filter 1 dropdown | `"Filter by favorites"` → `["All", "Favorites only", "Non-favorites"]` → `?favorite=true/false` |
| Filter 2 dropdown | `"Filter by category"` → `["All", "breakfast", "lunch", "dinner", "dessert", "other"]` |
| Selectbox fields | `category`, `difficulty` → `["easy", "medium", "hard"]` |
| Number input | `time_minutes` (min=1, step=5) |
| Card icon | ⭐ if `favorite=true`, 🍽️ if `favorite=false` |
| Key display fields | `name`, `category`, `difficulty`, `time_minutes`, `favorite`, `created_at` |

---

### Subject 6 — Workout Log

| Element | Value to use |
|---|---|
| `API_URL` resource path | `/workouts` |
| Page title | `"Workout Log"` |
| Page names | `"View all workouts"`, `"Log a workout"`, `"Edit a workout"`, `"Delete a workout"` |
| Required field for POST | `exercise` |
| Boolean field (checkbox) | `completed` (Was the session completed?) |
| Filter 1 dropdown | `"Filter by status"` → `["All", "Completed", "Not completed"]` → `?completed=true/false` |
| Filter 2 dropdown | `"Filter by intensity"` → `["All", "light", "moderate", "intense"]` → `?intensity=...` |
| Selectbox fields | `muscle_group` → `["legs", "chest", "back", "arms", "core", "full body"]`, `intensity` → `["light", "moderate", "intense"]` |
| Number input | `duration_minutes` (min=1, step=5) |
| Card icon | ✅ if `completed=true`, 🏋️ if `completed=false` |
| Key display fields | `exercise`, `muscle_group`, `duration_minutes`, `intensity`, `completed`, `created_at` |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>11 — Bonus Challenges</summary>

<br/>

> These are optional. Each completed bonus adds points on top of your base grade.

---

### Bonus A — Stats dashboard (+5 points)

Add a **5th page** called `"Dashboard"` or `"Stats"` that displays:

- Total number of records (use `st.metric()`)
- Count of records for each key field value (e.g. completed vs not completed)
- A simple summary table using `st.dataframe()` or `st.table()`
- At least one visual metric using `st.metric()` with a delta value

Example for Bug Tracker:
```
Total bugs: 12     Open: 5     In Progress: 3     Closed: 4
Critical bugs: 2
```

Example for Book Library:
```
Total books: 10     Read: 7     Unread: 3
Average rating (read books): 4.2
```

---

### Bonus B — Search by name (+3 points)

On the **View all** page, add a text input that lets the user filter records by name (or title) client-side:

```python
search = st.text_input("Search by name", placeholder="Type to filter...")

if search:
    items = [item for item in items if search.lower() in item["name"].lower()]
```

This filter runs in Streamlit — no new API endpoint needed.

---

### Bonus C — Quick action buttons (+4 points)

On the **View all** page, inside each `st.expander()`, add **action buttons** directly:

- A **"Mark as done"** / **"Mark as active"** toggle button that calls PATCH immediately
- A **"Delete"** button with a confirmation step
- After the action, call `st.rerun()` to refresh the list automatically

This means the user never has to navigate to the Edit or Delete page for simple actions.

---

### Bonus D — Pandas table view (+3 points)

On the **View all** page, add a toggle that lets the user switch between:

- **Card view** — `st.expander()` per record (current requirement)
- **Table view** — `st.dataframe()` using a Pandas DataFrame

```python
view_mode = st.radio("View as", ["Cards", "Table"], horizontal=True)

if view_mode == "Table":
    import pandas as pd
    df = pd.DataFrame(items)
    st.dataframe(df, use_container_width=True)
else:
    for item in items:
        with st.expander(...):
            ...
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

## Final Checklist Before Submitting

```text
PART 1 CHECK — is your backend still working?
  ✓ This is the same main.py from Part 1 — do NOT create a new one
  ✓ All 7 endpoints still work correctly
  ✓ uvicorn main:app --reload starts without errors
  ✓ http://127.0.0.1:8000/docs shows all your endpoints

SUBJECT CONSISTENCY — does your frontend match your backend?
  ✓ You are using the same subject as Part 1 (books / students / movies / bugs / recipes / workouts)
  ✓ The API_URL resource path matches your Part 1 routes (/books, /students, /movies...)
  ✓ Field names in forms match your Part 1 Pydantic model fields exactly
  ✓ Filter dropdowns match the query parameters your Part 1 API accepts

FRONTEND (frontend.py)
  ✓ streamlit run frontend.py starts without errors
  ✓ server_is_up() function is present and called at startup
  ✓ API_URL = "http://127.0.0.1:8000" constant is defined at the top
  ✓ Sidebar navigation with 4 pages using the correct names for your subject
  ✓ Page 1 — displays all records with 2 working filters and expandable cards
  ✓ Page 2 — create form with correct widgets for your subject's fields
  ✓ Page 2 — POST call succeeds, 201 shows success, 422 shows error details
  ✓ Page 3 — edit page with PATCH and PUT, current values shown before editing
  ✓ Page 4 — delete page with warning showing the record details before deletion
  ✓ Page 4 — st.rerun() called after successful deletion
  ✓ No hardcoded URLs anywhere — API_URL variable used throughout
  ✓ No crashes when the API has 0, 4, or 10+ records

FILES
  ✓ main.py         ← unchanged from Part 1 (or bugs fixed)
  ✓ frontend.py     ← new file added in Part 2
  ✓ requirements.txt (includes fastapi, uvicorn, pydantic, streamlit, requests)
  ✓ test_requests.http ← from Part 1
```

---

<p align="center">
  <strong>Part 2 — Streamlit Frontend — End of Evaluation</strong><br/>
  This evaluation continues directly from the <strong>Part 1 — Build a Complete REST API</strong> assignment.<br/>
  <a href="#top">↑ Back to the top</a>
</p>
