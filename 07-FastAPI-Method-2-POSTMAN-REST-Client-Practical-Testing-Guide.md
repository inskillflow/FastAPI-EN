<a id="top"></a>

# Postman — Step-by-Step Testing Guide
## Task Manager API — `main.py`

> **Tool used: Postman only** — no browser, no curl, no code.
> This guide tells you exactly where to click, what to type, and what you should see.
> Follow every step in order. Do not skip anything.

---

## Table of Contents

| # | What you will test |
|---|---|
| 0 | [Install Postman and start the server](#s0) |
| 1 | [Create a Collection — keep everything organized](#s1) |
| 2 | [TEST 1 — GET `/` — Is the server alive?](#s2) |
| 3 | [TEST 2 — GET `/tasks` — List all tasks](#s3) |
| 4 | [TEST 3 — GET `/tasks?completed=true` — Only completed tasks](#s4) |
| 5 | [TEST 4 — GET `/tasks?priority=high` — Only high priority](#s5) |
| 6 | [TEST 5 — GET `/tasks` with two filters at once](#s6) |
| 7 | [TEST 6 — GET `/tasks/1` — Get task number 1](#s7) |
| 8 | [TEST 7 — GET `/tasks/99` — Task that does not exist](#s8) |
| 9 | [TEST 8 — POST `/tasks` — Create a complete task](#s9) |
| 10 | [TEST 9 — POST `/tasks` — Create a minimal task (title only)](#s10) |
| 11 | [TEST 10 — POST `/tasks` — Intentional error (missing title)](#s11) |
| 12 | [TEST 11 — GET `/tasks` again — Confirm the new tasks are saved](#s12) |
| 13 | [TEST 12 — PUT `/tasks/1` — Replace task 1 entirely](#s13) |
| 14 | [TEST 13 — PATCH `/tasks/3` — Mark task 3 as completed](#s14) |
| 15 | [TEST 14 — PATCH `/tasks/2` — Change only the priority](#s15) |
| 16 | [TEST 15 — DELETE `/tasks/4` — Delete task 4](#s16) |
| 17 | [TEST 16 — GET `/tasks/4` — Confirm the deletion](#s17) |
| 18 | [TEST 17 — DELETE `/tasks/4` again — Already deleted](#s18) |
| 19 | [Final Check — GET `/tasks` — See the final state](#s19) |

---

<a id="s0"></a>

<details>
<summary>0 — Install Postman and start the server</summary>

<br/>

### A — Download and install Postman

If Postman is not installed on your computer:

1. Open your browser
2. Go to:
   ```
   https://www.postman.com/downloads/
   ```
3. Click the button **"Download the App"** (Windows version)
4. Run the installer
5. Open Postman — it will ask you to create a free account or sign in
6. You can click **"Skip and go to the app"** at the bottom if you do not want to create an account

---

### B — What Postman looks like when it opens

When Postman opens, you will see:

- A **left sidebar** with your collections and history
- A **main area** in the center where you build your requests
- A **top bar** with a `+` button to open new tabs

---

### C — Start the FastAPI server

Before doing anything in Postman, the server must be running.

Open VS Code. Press **`Ctrl + `` `** (backtick key — next to the 1 key on the keyboard).

A terminal opens at the bottom. Type this and press **Enter**:

```powershell
cd "c:\00-projetsGA\2025H-420-911-MA-main -COURSES EN ANGLAIS\inskillflow-docs-portfolio-1\mini_fastapi"
```

Then activate the virtual environment:

```powershell
venv\Scripts\activate
```

Your prompt changes to `(venv) PS C:\...>`.

Then start the server:

```bash
uvicorn main:app --reload
```

You should see:

```text
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Application startup complete.
```

> ⚠️ Keep this terminal open. The server stops if you close it.

---

### D — Verify the server works in the browser

Before using Postman, open your browser and go to:

```
http://127.0.0.1:8000
```

You should see:

```json
{"message": "Task Manager API is running", "docs": "http://127.0.0.1:8000/docs", "total_tasks": 4}
```

Server is running. Now go to Postman.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s1"></a>

<details>
<summary>1 — Create a Collection — keep everything organized</summary>

<br/>

> A **Collection** in Postman is like a folder. It keeps all your API requests grouped together.
> You create it once and save all your requests inside it.

---

### Step 1 — Find the Collections panel

On the **left sidebar**, look for the word **"Collections"**.

Click on it.

---

### Step 2 — Create a new Collection

Look for a **`+`** button or a **"New Collection"** button.

Click it.

---

### Step 3 — Name the Collection

A text field appears at the top. The default name is something like "New Collection".

**Click on the name and change it to:**

```
Task Manager API
```

Press **Enter** to confirm.

---

### Step 4 — Your collection is ready

You will now see **"Task Manager API"** in the left sidebar.

Every request you create will be saved here.

---

> ✅ You see "Task Manager API" in the left sidebar?
> Perfect — move to Test 1.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s2"></a>

<details>
<summary>TEST 1 — GET `/` — Is the server alive?</summary>

<br/>

### What this test does

You are asking the server: "Are you running?"
The server replies with a welcome message and the number of tasks.

---

### Step 1 — Open a new request tab

At the top of Postman, click the **`+`** button (or press `Ctrl+T`).

A new tab opens with an empty request.

---

### Step 2 — Set the HTTP method to GET

On the left side of the URL bar, you see a dropdown that says **"GET"**.

It is already set to GET — do nothing. ✅

---

### Step 3 — Enter the URL

Click in the URL bar (it says **"Enter URL or paste text"**).

Type:

```
http://127.0.0.1:8000/
```

---

### Step 4 — Click "Send"

Click the blue **"Send"** button on the right side of the URL bar.

---

### Step 5 — Read the response

At the bottom of the screen, the **response panel** appears.

Look at:

**1 — The status code** (top right of the response panel):

```
200 OK
```

**2 — The response body** (the "Body" tab at the bottom):

```json
{
    "message": "Task Manager API is running",
    "docs": "http://127.0.0.1:8000/docs",
    "total_tasks": 4
}
```

---

### What this means

| Field | Value | Explanation |
|---|---|---|
| `message` | "Task Manager API is running" | Server is alive |
| `total_tasks` | 4 | There are 4 tasks in memory |
| Status | 200 OK | Request succeeded |

---

### Step 6 — Save this request

Click the **"Save"** button (top right area, or `Ctrl+S`).

A dialog opens. Fill it in:
- **Request name:** `GET /` or `Root`
- **Collection:** click on `Task Manager API`

Click **"Save"**.

---

> ✅ You see `200 OK` and `total_tasks: 4`?
> Perfect — move to Test 2.

> ❌ You see `Could not send request` or `Error`? The server is not running. Go back to step 0.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>TEST 2 — GET `/tasks` — List all tasks</summary>

<br/>

### What this test does

You are asking the server: "Give me all the tasks."
The server replies with a list of all 4 tasks.

---

### Step 1 — Open a new request tab

Click the **`+`** button at the top to open a new tab.

---

### Step 2 — Method is GET

The method dropdown already shows **GET** — leave it as is. ✅

---

### Step 3 — Enter the URL

```
http://127.0.0.1:8000/tasks
```

---

### Step 4 — Click "Send"

---

### Step 5 — Read the response

**Status:** `200 OK`

**Body:**

```json
[
    {
        "id": 1,
        "title": "Learn FastAPI",
        "description": "Read the official FastAPI documentation",
        "completed": false,
        "priority": "high",
        "created_at": "2026-03-19 08:00:00"
    },
    {
        "id": 2,
        "title": "Build a REST API",
        "description": "Create a CRUD API with GET, POST, PUT, DELETE",
        "completed": true,
        "priority": "high",
        "created_at": "2026-03-19 09:00:00"
    },
    {
        "id": 3,
        "title": "Write unit tests",
        "description": "Use pytest and FastAPI TestClient",
        "completed": false,
        "priority": "medium",
        "created_at": "2026-03-19 10:00:00"
    },
    {
        "id": 4,
        "title": "Deploy to production",
        "description": "Use Docker and a cloud provider",
        "completed": false,
        "priority": "low",
        "created_at": "2026-03-19 11:00:00"
    }
]
```

---

### What to notice

- The response starts with `[` and ends with `]` → this is a **list** (array) of 4 tasks
- Each task is inside `{` and `}` → each one is a **JSON object**
- Only task 2 has `"completed": true`
- Tasks 1 and 2 have `"priority": "high"`

---

### Step 6 — Save this request

`Ctrl+S` → Name: `GET /tasks — all` → Collection: `Task Manager API` → Save.

---

> ✅ You see a list of 4 tasks with status `200 OK`?
> Perfect — move to Test 3.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>TEST 3 — GET `/tasks?completed=true` — Only completed tasks</summary>

<br/>

### What this test does

You add a **filter** to the request: you only want tasks that are completed.
This is done with a **query parameter** — an extra option added to the URL.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Enter the base URL

```
http://127.0.0.1:8000/tasks
```

---

### Step 3 — Add the query parameter using the Params tab

Instead of typing the filter in the URL manually, use the **Params tab** — it is cleaner.

Below the URL bar, click on the **"Params"** tab.

You will see a table with columns **KEY** and **VALUE**.

Click in the first empty row under **KEY** and type:

```
completed
```

Then click in the **VALUE** column of the same row and type:

```
true
```

---

### Step 4 — Look at the URL bar

After filling in the Params, the URL bar automatically updates to:

```
http://127.0.0.1:8000/tasks?completed=true
```

Postman built the URL for you. The `?completed=true` part is called a **query string**.

---

### Step 5 — Click "Send"

---

### Step 6 — Read the response

**Status:** `200 OK`

**Body:** only **1 task** this time:

```json
[
    {
        "id": 2,
        "title": "Build a REST API",
        "description": "Create a CRUD API with GET, POST, PUT, DELETE",
        "completed": true,
        "priority": "high",
        "created_at": "2026-03-19 09:00:00"
    }
]
```

Only task 2 came back because it is the only one with `"completed": true`.

---

### Step 7 — Try `completed=false`

In the Params table, change the VALUE from `true` to:

```
false
```

Click **Send** again.

This time you should get **3 tasks** — IDs 1, 3, and 4 (all not completed).

---

### Step 8 — Save this request

`Ctrl+S` → Name: `GET /tasks — completed filter` → Save.

---

> ✅ `completed=true` gives 1 task, `completed=false` gives 3 tasks?
> Perfect — move to Test 4.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s5"></a>

<details>
<summary>TEST 4 — GET `/tasks?priority=high` — Only high priority</summary>

<br/>

### What this test does

You filter tasks by their priority level.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Enter the base URL

```
http://127.0.0.1:8000/tasks
```

---

### Step 3 — Add the query parameter

Click the **"Params"** tab.

| KEY | VALUE |
|---|---|
| priority | high |

---

### Step 4 — Click "Send"

---

### Step 5 — Read the response

**Status:** `200 OK`

**Body:** 2 tasks — IDs 1 and 2:

```json
[
    {
        "id": 1,
        "title": "Learn FastAPI",
        "priority": "high",
        ...
    },
    {
        "id": 2,
        "title": "Build a REST API",
        "priority": "high",
        ...
    }
]
```

---

### Step 6 — Try the other priority values

Change the VALUE in the Params tab, click **Send** each time:

| priority value | Expected result |
|---|---|
| `high` | 2 tasks — IDs 1 and 2 |
| `medium` | 1 task — ID 3 "Write unit tests" |
| `low` | 1 task — ID 4 "Deploy to production" |

---

### Step 7 — Save

`Ctrl+S` → Name: `GET /tasks — priority filter` → Save.

---

> ✅ `priority=high` gives 2 tasks, `medium` gives 1, `low` gives 1?
> Perfect — move to Test 5.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>TEST 5 — GET `/tasks` with two filters at once</summary>

<br/>

### What this test does

You combine **two query parameters** at the same time.
You want tasks that are: NOT completed AND have high priority.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Enter the base URL

```
http://127.0.0.1:8000/tasks
```

---

### Step 3 — Add TWO rows in the Params tab

Click the **"Params"** tab.

Fill in **two rows**:

| KEY | VALUE |
|---|---|
| completed | false |
| priority | high |

---

### Step 4 — Look at the URL bar

The URL bar should now show:

```
http://127.0.0.1:8000/tasks?completed=false&priority=high
```

Notice the **`&`** between the two filters — it means "AND both of these conditions must be true".

---

### Step 5 — Click "Send"

---

### Step 6 — Read the response

**Status:** `200 OK`

**Body:** only **1 task** — task 1:

```json
[
    {
        "id": 1,
        "title": "Learn FastAPI",
        "completed": false,
        "priority": "high",
        ...
    }
]
```

Why only task 1?

| Task | completed=false? | priority=high? | Result |
|---|---|---|---|
| Task 1 "Learn FastAPI" | ✅ false | ✅ high | **included** |
| Task 2 "Build a REST API" | ❌ true | ✅ high | excluded |
| Task 3 "Write unit tests" | ✅ false | ❌ medium | excluded |
| Task 4 "Deploy to production" | ✅ false | ❌ low | excluded |

---

### Step 7 — Save

`Ctrl+S` → Name: `GET /tasks — two filters` → Save.

---

> ✅ You see only task 1 with both filters active?
> Perfect — move to Test 6.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s7"></a>

<details>
<summary>TEST 6 — GET `/tasks/1` — Get task number 1</summary>

<br/>

### What this test does

You ask for one specific task by its **ID**.
You do not get a list — you get exactly one task.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Method is GET — leave it as is

---

### Step 3 — Enter the URL with the ID in it

```
http://127.0.0.1:8000/tasks/1
```

The `1` at the end is the task ID. It is part of the URL — not a query parameter.

---

### Step 4 — Make sure the Params tab is empty

Click on the **"Params"** tab and verify there are no rows filled in.
If there are, uncheck them or delete them.

---

### Step 5 — Click "Send"

---

### Step 6 — Read the response

**Status:** `200 OK`

**Body:** one single task (NOT a list — notice there are no `[` `]` brackets):

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

### What to notice

- The response starts with `{` — it is a **single object**, not a list
- If it were a list, it would start with `[`

---

### Step 7 — Try other IDs

Change the URL to test other tasks — click **Send** each time:

| URL | Expected title |
|---|---|
| `http://127.0.0.1:8000/tasks/2` | "Build a REST API" |
| `http://127.0.0.1:8000/tasks/3` | "Write unit tests" |
| `http://127.0.0.1:8000/tasks/4` | "Deploy to production" |

---

### Step 8 — Save

`Ctrl+S` → Name: `GET /tasks/{id}` → Save.

---

> ✅ You get the correct task for each ID with status `200 OK`?
> Perfect — move to Test 7.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s8"></a>

<details>
<summary>TEST 7 — GET `/tasks/99` — Task that does not exist</summary>

<br/>

### What this test does

You ask for a task with ID 99 — which does not exist.
You want to see what the server does when the record is not found.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Method: GET

---

### Step 3 — Enter this URL

```
http://127.0.0.1:8000/tasks/99
```

---

### Step 4 — Click "Send"

---

### Step 5 — Read the response

**Status:** `404 Not Found` (shown in red or orange in Postman)

**Body:**

```json
{
    "detail": "Task with ID 99 not found"
}
```

---

### What 404 means

> **404 = Not Found**
> The server looked for task 99 — it does not exist in the database.
> This is NOT a crash. The server handled it correctly and told you what happened.

---

### Step 6 — Look at the status indicator in Postman

At the top right of the response panel, you see:

```
404 Not Found
```

It is displayed in **red/orange** — Postman uses colors to help you:
- **Green** = 2xx = success
- **Orange/Red** = 4xx = client error (you asked for something wrong)
- **Red** = 5xx = server error (the server crashed)

---

### Step 7 — Try a few more non-existent IDs

| URL | Expected |
|---|---|
| `http://127.0.0.1:8000/tasks/0` | 404 — "Task with ID 0 not found" |
| `http://127.0.0.1:8000/tasks/100` | 404 — "Task with ID 100 not found" |

---

### Step 8 — Save

`Ctrl+S` → Name: `GET /tasks/{id} — 404` → Save.

---

> ✅ You see `404 Not Found` with the message "Task with ID 99 not found"?
> Perfect — move to Test 8.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>TEST 8 — POST `/tasks` — Create a complete task</summary>

<br/>

### What this test does

You are going to **create a new task** by sending data to the server.
This is the first test where you send data in the **body** of the request.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Change the method to POST

Click the **method dropdown** (currently says **"GET"**) on the left of the URL bar.

A list appears:
```
GET
POST
PUT
PATCH
DELETE
HEAD
OPTIONS
...
```

Click **"POST"**.

The dropdown now shows **POST**.

---

### Step 3 — Enter the URL

```
http://127.0.0.1:8000/tasks
```

No ID at the end — you are creating a new task, not targeting an existing one.

---

### Step 4 — Go to the Body tab

Below the URL bar, you see several tabs:
```
Params  |  Authorization  |  Headers  |  Body  |  Pre-request Script  |  Tests
```

Click on **"Body"**.

---

### Step 5 — Select "raw" and change to JSON

Inside the Body tab, you see these options:
```
○ none  ○ form-data  ○ x-www-form-urlencoded  ● raw  ○ binary  ○ GraphQL
```

Click **"raw"**.

A dropdown appears on the right of these options — it says **"Text"**.

Click on that dropdown and change it to **"JSON"**.

> ⚠️ This step is critical. If you forget to set it to JSON, the server will not understand your data and will return a 422 error.

---

### Step 6 — Type the JSON body

A large text area appears below. It is empty.

Click inside it and type **exactly** this:

```json
{
    "title": "Study for the exam",
    "description": "Review chapters 3 to 7",
    "completed": false,
    "priority": "high"
}
```

> ⚠️ Rules for JSON:
> - Use **double quotes** `"` — NOT single quotes `'`
> - `false` and `true` are all **lowercase**
> - Do NOT add a comma after the last field
> - The body must start with `{` and end with `}`

---

### Step 7 — Click "Send"

---

### Step 8 — Read the response

**Status:** `201 Created` (shown in green)

> **201 = Created** — your task was successfully created!

**Body:**

```json
{
    "id": 5,
    "title": "Study for the exam",
    "description": "Review chapters 3 to 7",
    "completed": false,
    "priority": "high",
    "created_at": "2026-03-30 14:22:05"
}
```

Notice what the server **added automatically**:
- `"id": 5` — the server assigned the next available ID
- `"created_at": "..."` — the server stamped the current date and time

You did NOT send these fields — the server created them for you.

---

### Step 9 — Save

`Ctrl+S` → Name: `POST /tasks — full` → Save.

---

> ✅ You see `201 Created` with `"id": 5` and a `created_at` timestamp?
> Perfect — move to Test 9.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>TEST 9 — POST `/tasks` — Create a minimal task (title only)</summary>

<br/>

### What this test does

Only `title` is required. All other fields have **default values**.
You are going to create a task with only the title — no other fields.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Set method to POST

Click the method dropdown → select **POST**.

---

### Step 3 — Enter the URL

```
http://127.0.0.1:8000/tasks
```

---

### Step 4 — Body tab → raw → JSON

Click **Body** → click **raw** → change dropdown to **JSON**.

---

### Step 5 — Type only the title

In the text area, type:

```json
{
    "title": "Quick reminder"
}
```

Only one field. That is intentional.

---

### Step 6 — Click "Send"

---

### Step 7 — Read the response

**Status:** `201 Created`

**Body:**

```json
{
    "id": 6,
    "title": "Quick reminder",
    "description": "",
    "completed": false,
    "priority": "medium",
    "created_at": "2026-03-30 14:23:11"
}
```

The server filled in the **default values** for the missing fields:

| Field | You sent | Server used |
|---|---|---|
| `title` | "Quick reminder" | "Quick reminder" |
| `description` | *(not sent)* | `""` (empty — the default) |
| `completed` | *(not sent)* | `false` (the default) |
| `priority` | *(not sent)* | `"medium"` (the default) |

---

### Step 8 — Save

`Ctrl+S` → Name: `POST /tasks — title only` → Save.

---

> ✅ You see `201 Created` with `"id": 6` and default values for the missing fields?
> Perfect — move to Test 10.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>TEST 10 — POST `/tasks` — Intentional error (missing title)</summary>

<br/>

### What this test does

You are going to send a **broken request on purpose** to see what happens when required data is missing.
`title` is required — the server must refuse the request.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Method: POST

---

### Step 3 — URL

```
http://127.0.0.1:8000/tasks
```

---

### Step 4 — Body → raw → JSON

---

### Step 5 — Send an empty body

In the text area, type:

```json
{}
```

Just two curly braces — nothing inside.

---

### Step 6 — Click "Send"

---

### Step 7 — Read the response

**Status:** `422 Unprocessable Entity` (shown in red/orange)

> **422 = Unprocessable Entity**
> The server received your request, but the data inside is **invalid**.
> `title` is missing — and it is required.

**Body:**

```json
{
    "detail": [
        {
            "type": "missing",
            "loc": [
                "body",
                "title"
            ],
            "msg": "Field required",
            "input": {}
        }
    ]
}
```

Read the error:
- `"loc": ["body", "title"]` → the problem is in the **body**, field **`title`**
- `"msg": "Field required"` → you must provide `title`

---

### Step 8 — Try another type of error

Change the body to this:

```json
{
    "title": "Test task",
    "completed": "yes"
}
```

`completed` must be `true` or `false` — not a string like `"yes"`.

Click **Send**.

You get another **422** — this time the error points to `completed` with the wrong type.

---

### Step 9 — Save

`Ctrl+S` → Name: `POST /tasks — 422 error` → Save.

---

> ✅ You see `422 Unprocessable Entity` for both broken requests?
> Perfect — move to Test 11.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s12"></a>

<details>
<summary>TEST 11 — GET `/tasks` again — Confirm the new tasks are saved</summary>

<br/>

### What this test does

After creating 2 tasks (Tests 8 and 9), you go back to read the full list.
You want to confirm your new tasks are actually saved in memory.

---

### Step 1 — Go back to your saved request

In the **left sidebar**, look for your collection **"Task Manager API"**.

Click on the request named **`GET /tasks — all`** that you saved earlier.

It opens in a new tab.

---

### Step 2 — Make sure Params is empty

Click the **Params** tab and verify there are no active rows (or uncheck them all).

---

### Step 3 — Click "Send"

---

### Step 4 — Read the response

**Status:** `200 OK`

You should see **6 tasks** now — the original 4 plus the 2 you created:

| ID | Title |
|---|---|
| 1 | Learn FastAPI |
| 2 | Build a REST API |
| 3 | Write unit tests |
| 4 | Deploy to production |
| 5 | Study for the exam ← **you created this in Test 8** |
| 6 | Quick reminder ← **you created this in Test 9** |

---

> ✅ You see 6 tasks total including your 2 new ones?
> Perfect — move to Test 12.

> ❌ You still see only 4? The server was restarted — the data reset. Go back to Tests 8 and 9 to recreate them.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s13"></a>

<details>
<summary>TEST 12 — PUT `/tasks/1` — Replace task 1 entirely</summary>

<br/>

### What this test does

**PUT** replaces a task **completely** — every single field is overwritten.
Think of it as: "Throw away the old task and put this new one in its place."

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Change the method to PUT

Click the method dropdown → select **PUT**.

---

### Step 3 — Enter the URL with the task ID

```
http://127.0.0.1:8000/tasks/1
```

The `1` at the end = you are replacing task 1 "Learn FastAPI".

---

### Step 4 — Body → raw → JSON

Click **Body** → click **raw** → change dropdown to **JSON**.

---

### Step 5 — Type the replacement data

```json
{
    "title": "Master FastAPI completely",
    "description": "Build 3 real projects from scratch",
    "completed": false,
    "priority": "high"
}
```

---

### Step 6 — Click "Send"

---

### Step 7 — Read the response

**Status:** `200 OK`

**Body:**

```json
{
    "id": 1,
    "title": "Master FastAPI completely",
    "description": "Build 3 real projects from scratch",
    "completed": false,
    "priority": "high",
    "created_at": "2026-03-19 08:00:00"
}
```

Notice:
- `"id": 1` stayed — the ID never changes
- `"created_at"` stayed — the original creation date is preserved
- **Everything else was completely replaced** with your new data

---

### Step 8 — Verify: read task 1 again

Open your saved request `GET /tasks/{id}`, change the URL to:

```
http://127.0.0.1:8000/tasks/1
```

Click **Send**. You should see the new title: "Master FastAPI completely".

---

### Understand PUT vs PATCH

| | PUT | PATCH |
|---|---|---|
| What it does | Replaces **everything** | Updates **only what you send** |
| `title` required? | Yes | No |
| Unspecified fields | Reset to defaults | Stay unchanged |
| When to use | Full replacement | Small changes |

---

### Step 9 — Save

`Ctrl+S` → Name: `PUT /tasks/{id}` → Save.

---

> ✅ Task 1 title is now "Master FastAPI completely" with status `200 OK`?
> Perfect — move to Test 13.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s14"></a>

<details>
<summary>TEST 13 — PATCH `/tasks/3` — Mark task 3 as completed</summary>

<br/>

### What this test does

**PATCH** updates **only the specific fields you send**.
Everything else stays exactly as it was.
You are going to change only `completed` — nothing else.

---

### Step 1 — First, read task 3 to see what it looks like now

Open a new tab. Method: **GET**. URL: `http://127.0.0.1:8000/tasks/3`. Click **Send**.

You should see:
```json
{
    "id": 3,
    "title": "Write unit tests",
    "description": "Use pytest and FastAPI TestClient",
    "completed": false,
    "priority": "medium",
    "created_at": "2026-03-19 10:00:00"
}
```

`completed` is currently `false`. You are going to change it to `true`.

---

### Step 2 — Open a new request tab

Click `+`.

---

### Step 3 — Change the method to PATCH

Click the method dropdown → select **PATCH**.

---

### Step 4 — Enter the URL

```
http://127.0.0.1:8000/tasks/3
```

---

### Step 5 — Body → raw → JSON

Click **Body** → click **raw** → change dropdown to **JSON**.

---

### Step 6 — Type only the field you want to change

```json
{
    "completed": true
}
```

**Only one field.** That is the whole point of PATCH.

---

### Step 7 — Click "Send"

---

### Step 8 — Read the response

**Status:** `200 OK`

**Body:**

```json
{
    "id": 3,
    "title": "Write unit tests",
    "description": "Use pytest and FastAPI TestClient",
    "completed": true,
    "priority": "medium",
    "created_at": "2026-03-19 10:00:00"
}
```

Compare before and after:

| Field | Before | After |
|---|---|---|
| `title` | "Write unit tests" | "Write unit tests" ← **unchanged** |
| `description` | "Use pytest..." | "Use pytest..." ← **unchanged** |
| `completed` | `false` | `true` ← **✅ changed** |
| `priority` | "medium" | "medium" ← **unchanged** |
| `created_at` | "2026-03-19 10:00:00" | "2026-03-19 10:00:00" ← **unchanged** |

Only `completed` changed. Everything else is identical.

---

### Step 9 — Save

`Ctrl+S` → Name: `PATCH /tasks/{id} — completed` → Save.

---

> ✅ Task 3 now has `"completed": true` and everything else is unchanged?
> Perfect — move to Test 14.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s15"></a>

<details>
<summary>TEST 14 — PATCH `/tasks/2` — Change only the priority</summary>

<br/>

### What this test does

Another PATCH test — this time you change only the `priority`.
This confirms that PATCH really does leave everything else untouched no matter which field you change.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Method: PATCH

---

### Step 3 — URL

```
http://127.0.0.1:8000/tasks/2
```

---

### Step 4 — Body → raw → JSON

---

### Step 5 — Type only the priority

```json
{
    "priority": "low"
}
```

---

### Step 6 — Click "Send"

---

### Step 7 — Read the response

**Status:** `200 OK`

**Body:**

```json
{
    "id": 2,
    "title": "Build a REST API",
    "description": "Create a CRUD API with GET, POST, PUT, DELETE",
    "completed": true,
    "priority": "low",
    "created_at": "2026-03-19 09:00:00"
}
```

`priority` changed from `"high"` to `"low"`. Everything else: untouched.

---

### Step 8 — Try changing two fields at once with PATCH

PATCH also works with multiple fields — you don't have to send just one.

Change the URL to `http://127.0.0.1:8000/tasks/1` and send:

```json
{
    "completed": true,
    "priority": "medium"
}
```

Both `completed` AND `priority` will change for task 1. `title` and `description` stay the same.

---

### Step 9 — Save

`Ctrl+S` → Name: `PATCH /tasks/{id} — priority` → Save.

---

> ✅ Task 2 priority is now `"low"` with status `200 OK`?
> Perfect — move to Test 15.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s16"></a>

<details>
<summary>TEST 15 — DELETE `/tasks/4` — Delete task 4</summary>

<br/>

### What this test does

You are going to **permanently delete** a task.
Once deleted, the task is gone from memory and cannot be recovered (unless you restart the server).

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Change the method to DELETE

Click the method dropdown → select **DELETE**.

---

### Step 3 — Enter the URL

```
http://127.0.0.1:8000/tasks/4
```

You are deleting task 4 ("Deploy to production").

---

### Step 4 — Body tab — leave it empty

Click on the **Body** tab.

Make sure **"none"** is selected:

```
● none  ○ form-data  ○ x-www-form-urlencoded  ○ raw  ...
```

DELETE requests do NOT need a body. Leave it empty.

---

### Step 5 — Click "Send"

---

### Step 6 — Read the response

**Status:** `204 No Content` (shown in green)

> **204 = No Content**
> The task was deleted successfully.
> There is nothing to return — the body is empty. **This is correct and expected.**

Look at the **Body area** at the bottom — it says something like:

```
This request does not have a response body
```

or the body area is completely blank.

**An empty body on a DELETE = success.**

---

### Step 7 — Look at the status indicator

In Postman, the status `204 No Content` is shown in **green** — green always means success.

| Status | Color | Meaning |
|---|---|---|
| 200 OK | Green | Success with data |
| 201 Created | Green | Success — resource created |
| **204 No Content** | **Green** | **Success — nothing to return (DELETE worked)** |
| 404 Not Found | Orange/Red | Resource not found |
| 422 | Orange/Red | Validation error |

---

### Step 8 — Save

`Ctrl+S` → Name: `DELETE /tasks/{id}` → Save.

---

> ✅ You see `204 No Content` with an empty body?
> Perfect — move to Test 16.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s17"></a>

<details>
<summary>TEST 16 — GET `/tasks/4` — Confirm the deletion</summary>

<br/>

### What this test does

After deleting task 4, you try to read it.
The server should tell you it no longer exists.

---

### Step 1 — Open a new request tab

Click `+`.

---

### Step 2 — Method: GET

---

### Step 3 — URL: the deleted task

```
http://127.0.0.1:8000/tasks/4
```

---

### Step 4 — Click "Send"

---

### Step 5 — Read the response

**Status:** `404 Not Found` (red/orange)

**Body:**

```json
{
    "detail": "Task with ID 4 not found"
}
```

Task 4 is gone. The server confirms it does not exist anymore.

---

### Step 6 — Check the full list to confirm

Open your saved request `GET /tasks — all` from the left sidebar.

Click **Send**.

You should see **5 tasks** — IDs 1, 2, 3, 5, 6.
Task 4 is no longer in the list.

---

> ✅ You get `404 Not Found` for task 4 and see 5 tasks in the full list?
> Perfect — move to Test 17.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s18"></a>

<details>
<summary>TEST 17 — DELETE `/tasks/4` again — Already deleted</summary>

<br/>

### What this test does

You try to delete task 4 **again** — even though it was already deleted in Test 15.
The server should return a 404 because there is nothing to delete.

---

### Step 1 — Open your saved DELETE request

In the left sidebar, click on `DELETE /tasks/{id}` that you saved.

The URL is already `http://127.0.0.1:8000/tasks/4`.

---

### Step 2 — Click "Send"

---

### Step 3 — Read the response

**Status:** `404 Not Found`

**Body:**

```json
{
    "detail": "Task with ID 4 not found"
}
```

The server handled this gracefully:
- It did not crash
- It did not delete something random
- It simply said: "I cannot delete something that does not exist"

---

> ✅ You see `404 Not Found` when trying to delete an already-deleted task?
> Perfect — move to the Final Check.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s19"></a>

<details>
<summary>Final Check — GET `/tasks` — See the final state</summary>

<br/>

### What this test does

One last GET to see all the tasks in their final state after everything you changed.

---

### Step 1 — Open your saved `GET /tasks — all` request

Click it in the left sidebar.

---

### Step 2 — Make sure Params is empty

Click the **Params** tab — uncheck or remove all rows.

---

### Step 3 — Click "Send"

---

### Step 4 — What you should see

**5 tasks** — task 4 was deleted:

| ID | Title | Changes you made |
|---|---|---|
| 1 | Master FastAPI completely | Title replaced in Test 12 |
| 2 | Build a REST API | Priority changed to "low" in Test 14 |
| 3 | Write unit tests | Marked as completed in Test 13 |
| 5 | Study for the exam | Created in Test 8 |
| 6 | Quick reminder | Created in Test 9 |

Task 4 is gone — deleted in Test 15.

---

### Summary — all tests completed

| Test | HTTP Method | URL | Status |
|---|---|---|---|
| Test 1 | GET | `/` | ✅ 200 |
| Test 2 | GET | `/tasks` | ✅ 200 |
| Test 3 | GET | `/tasks?completed=true` | ✅ 200 |
| Test 4 | GET | `/tasks?priority=high` | ✅ 200 |
| Test 5 | GET | `/tasks?completed=false&priority=high` | ✅ 200 |
| Test 6 | GET | `/tasks/1` | ✅ 200 |
| Test 7 | GET | `/tasks/99` | ✅ 404 |
| Test 8 | POST | `/tasks` (full body) | ✅ 201 |
| Test 9 | POST | `/tasks` (title only) | ✅ 201 |
| Test 10 | POST | `/tasks` (broken body) | ✅ 422 |
| Test 11 | GET | `/tasks` (verify 6 tasks) | ✅ 200 |
| Test 12 | PUT | `/tasks/1` | ✅ 200 |
| Test 13 | PATCH | `/tasks/3` (completed) | ✅ 200 |
| Test 14 | PATCH | `/tasks/2` (priority) | ✅ 200 |
| Test 15 | DELETE | `/tasks/4` | ✅ 204 |
| Test 16 | GET | `/tasks/4` (confirm deleted) | ✅ 404 |
| Test 17 | DELETE | `/tasks/4` (already gone) | ✅ 404 |
| Final | GET | `/tasks` (final state) | ✅ 200 |

---

### Your saved requests in Postman

In the left sidebar, your collection **"Task Manager API"** now contains all these saved requests. You can click any of them at any time to run them again.

---

### How to reset the data

The data lives only in memory. To get back the original 4 tasks:

1. Go to the VS Code terminal where uvicorn is running
2. Press `Ctrl + C` to stop the server
3. Run `uvicorn main:app --reload` again
4. All data resets to the original 4 tasks

---

### Postman quick reference — what you learned

| To do this | Use this method | Needs a body? |
|---|---|---|
| Read all records | GET | No |
| Read one record | GET + ID in URL | No |
| Filter results | GET + Params tab | No |
| Create a record | POST | Yes (JSON) |
| Replace a record | PUT + ID in URL | Yes (JSON — all fields) |
| Update specific fields | PATCH + ID in URL | Yes (JSON — only changed fields) |
| Delete a record | DELETE + ID in URL | No |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<p align="center">
  <strong>End of Postman Step-by-Step Guide</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>
