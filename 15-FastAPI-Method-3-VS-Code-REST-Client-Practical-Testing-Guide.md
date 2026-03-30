<a id="top"></a>

# VS Code REST Client — Step-by-Step Testing Guide
## Task Manager API — `main.py`

> **Tool used: REST Client extension for VS Code only.**
> No browser, no Postman, no terminal commands — just VS Code.
> This guide tells you exactly what to install, what to type, where to click, and what you should see.
> Follow every step in order. Do not skip anything.

---

## Table of Contents

| # | What you will do |
|---|---|
| 0 | [Install REST Client and start the server](#s0) |
| 1 | [Create the test file — `my_tests.http`](#s1) |
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
| 20 | [Your complete `.http` file — all tests in one place](#s20) |

---

<a id="s0"></a>

<details>
<summary>0 — Install REST Client and start the server</summary>

<br/>

### A — Install the REST Client extension

1. Open **VS Code**
2. Press **`Ctrl + Shift + X`** — the Extensions panel opens on the left
3. In the search bar at the top of the Extensions panel, type:
   ```
   REST Client
   ```
4. Look for the extension named **"REST Client"** by **Huachao Mao**
   - It has a small icon that looks like a lightning bolt or a globe
   - It has millions of downloads
5. Click the blue **"Install"** button
6. Wait a few seconds — the extension installs automatically

---

### B — Verify it is installed

After installation, the button changes to **"Installed"** or shows a gear icon.

You can also check: press `Ctrl + Shift + P`, type `REST Client`, and you should see commands like **"Rest Client: Send Request"** in the list.

---

### C — Start the FastAPI server

Before sending any request, the server must be running.

Press **`Ctrl + `` `** (backtick — the key to the left of `1` on the keyboard).

A terminal panel opens at the bottom of VS Code.

Type this and press **Enter**:

```powershell
cd "c:\00-projetsGA\2025H-420-911-MA-main -COURSES EN ANGLAIS\inskillflow-docs-portfolio-1\mini_fastapi"
```

Then activate the virtual environment:

```powershell
venv\Scripts\activate
```

Your prompt changes to `(venv) PS C:\...>`.

> If you see an error about "running scripts is disabled":
> ```powershell
> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
> ```
> Then try `venv\Scripts\activate` again.

Then start the server:

```bash
uvicorn main:app --reload
```

You should see:

```text
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Application startup complete.
```

> ⚠️ Keep this terminal open. If you close it, the server stops and all tests will fail.
> To open a second terminal alongside it: click the **`+`** button inside the terminal panel.

---

### D — Verify the server is running

Open a **second terminal** (click `+` in the terminal panel) and type:

```powershell
curl http://127.0.0.1:8000/
```

You should see:

```json
{"message":"Task Manager API is running","docs":"http://127.0.0.1:8000/docs","total_tasks":4}
```

Server is running. Move to the next step.

---

> ✅ Server is running and returns `total_tasks: 4`?
> Perfect — move to step 1.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s1"></a>

<details>
<summary>1 — Create the test file — `my_tests.http`</summary>

<br/>

### What is a `.http` file?

A `.http` file is a plain text file where you write HTTP requests.
The REST Client extension reads this file and lets you run each request with a single click.

Think of it as a notebook for your API tests — you write all your requests here and keep them for later.

---

### Step 1 — Navigate to the project folder in VS Code

On the left sidebar of VS Code, click the **Explorer** icon (the icon that looks like two stacked pages).

You should see the folder `mini_fastapi` with its files.

---

### Step 2 — Create a new file

Right-click on the **`mini_fastapi` folder** in the Explorer.

A context menu appears. Click **"New File"**.

A text field appears. Type the file name:

```
my_tests.http
```

Press **Enter**.

The file is created and opens automatically in the editor.

---

### Step 3 — Understand the file structure

A `.http` file is made of **blocks**. Each block is one request.

Rules:
- Each block starts with the **HTTP method and URL** on one line
- Blocks are **separated by `###`** (three hash symbols)
- A blank line is required between the **headers** and the **body** (for POST, PUT, PATCH)
- You can add **comments** with `#` at the start of a line

Example of what a block looks like:

```http
### This is a comment describing the test
GET http://127.0.0.1:8000/tasks
```

---

### Step 4 — Add the base URL variable

At the very top of the file, type this line:

```http
@baseUrl = http://127.0.0.1:8000
```

This creates a **variable** called `baseUrl`. Instead of typing the full URL every time, you can write `{{baseUrl}}`.

For example:
- Full URL: `http://127.0.0.1:8000/tasks`
- With variable: `{{baseUrl}}/tasks`

Both do the same thing. The variable saves you time and avoids typos.

---

### Step 5 — Your file now looks like this

```http
@baseUrl = http://127.0.0.1:8000
```

That is all for now. You will add one test block at a time in the steps below.

---

> ✅ The file `my_tests.http` is created and open in VS Code?
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

### Step 1 — Add this block to your file

Click at the end of the last line in your `my_tests.http` file.

Press **Enter** twice to add blank lines, then type:

```http
### TEST 1 — Root endpoint — is the server running?
GET {{baseUrl}}/
```

Your file now looks like this:

```http
@baseUrl = http://127.0.0.1:8000

### TEST 1 — Root endpoint — is the server running?
GET {{baseUrl}}/
```

---

### Step 2 — Find the "Send Request" link

Look at the line that says `GET {{baseUrl}}/`.

**Hover your mouse over that line** (or look just above it).

You will see a small text appear above the line:

```
Send Request
```

It looks like a clickable link in grey or blue text.

---

### Step 3 — Click "Send Request"

**Click on "Send Request".**

VS Code splits the editor into two panels:
- **Left panel:** your `.http` file
- **Right panel:** the response from the server

---

### Step 4 — Read the response in the right panel

The right panel shows:

**At the top — the status line:**
```
HTTP/1.1 200 OK
```

**Then the headers** (content-type, etc.) — you can ignore these for now.

**Then the response body:**
```json
{
  "message": "Task Manager API is running",
  "docs": "http://127.0.0.1:8000/docs",
  "total_tasks": 4
}
```

---

### What this means

| What you see | What it means |
|---|---|
| `HTTP/1.1 200 OK` | The request succeeded — 200 = success |
| `"message"` | The server is alive and responding |
| `"total_tasks": 4` | There are currently 4 tasks in memory |

---

### Step 5 — Look at the color of the status

The REST Client extension colors the status line:
- **Green** = 2xx = success
- **Orange/Red** = 4xx = client error
- **Red** = 5xx = server crash

`200 OK` should appear in **green**.

---

> ✅ You see `HTTP/1.1 200 OK` and `"total_tasks": 4` in the right panel?
> Perfect — move to Test 2.

> ❌ You see `Error: connect ECONNREFUSED` or similar? The server is not running. Go back to step 0.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>TEST 2 — GET `/tasks` — List all tasks</summary>

<br/>

### What this test does

You ask the server: "Give me all tasks."
You get a list of all 4 tasks currently in memory.

---

### Step 1 — Add this block to your file

Click at the end of the last line in `my_tests.http`.

Press **Enter** twice, then type:

```http
###

### TEST 2 — List all tasks
GET {{baseUrl}}/tasks
```

> The `###` on a line by itself acts as a separator between blocks.
> Always put a `###` between two tests.

---

### Your file now looks like this

```http
@baseUrl = http://127.0.0.1:8000

### TEST 1 — Root endpoint — is the server running?
GET {{baseUrl}}/

###

### TEST 2 — List all tasks
GET {{baseUrl}}/tasks
```

---

### Step 2 — Click "Send Request" above the TEST 2 block

Hover over the line `GET {{baseUrl}}/tasks`.

Click **"Send Request"** that appears above it.

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 200 OK`

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

- The response starts with `[` and ends with `]` → this is a **list** (array)
- Each task is between `{` and `}` → each is a **JSON object**
- There are **4 tasks** in total
- Only task 2 has `"completed": true`

---

> ✅ You see a list of 4 tasks with `HTTP/1.1 200 OK`?
> Perfect — move to Test 3.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>TEST 3 — GET `/tasks?completed=true` — Only completed tasks</summary>

<br/>

### What this test does

You add a **filter** to the URL.
You only want tasks where `completed` is `true`.
The `?completed=true` part of the URL is called a **query parameter**.

---

### Step 1 — Add this block to your file

```http
###

### TEST 3 — Filter: only completed tasks
GET {{baseUrl}}/tasks?completed=true
```

The filter is written **directly in the URL** — you just add `?completed=true` at the end.

---

### Step 2 — Click "Send Request" above TEST 3

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 200 OK`

**Body:** only **1 task**:

```json
[
  {
    "id": 2,
    "title": "Build a REST API",
    "completed": true,
    "priority": "high",
    ...
  }
]
```

Only task 2 came back — it is the only one with `"completed": true`.

---

### Step 4 — Add a second version: completed=false

Add another block right after:

```http
###

### TEST 3b — Filter: only NOT completed tasks
GET {{baseUrl}}/tasks?completed=false
```

Click **"Send Request"** above TEST 3b.

You should get **3 tasks** — IDs 1, 3, and 4.

---

### What to notice

- `?` in the URL starts the query string
- `completed=true` is the filter
- The server reads this and returns only matching tasks

---

> ✅ `completed=true` → 1 task, `completed=false` → 3 tasks?
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

### Step 1 — Add this block to your file

```http
###

### TEST 4 — Filter: only high priority tasks
GET {{baseUrl}}/tasks?priority=high
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 200 OK`

**Body:** 2 tasks — IDs 1 and 2 (both have `"priority": "high"`).

---

### Step 4 — Try the other priority values

Add these blocks and run each one:

```http
###

### TEST 4b — Filter: medium priority
GET {{baseUrl}}/tasks?priority=medium

###

### TEST 4c — Filter: low priority
GET {{baseUrl}}/tasks?priority=low
```

| Block | Expected result |
|---|---|
| `priority=high` | 2 tasks — IDs 1 and 2 |
| `priority=medium` | 1 task — ID 3 "Write unit tests" |
| `priority=low` | 1 task — ID 4 "Deploy to production" |

---

> ✅ Correct number of tasks for each priority?
> Perfect — move to Test 5.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>TEST 5 — GET `/tasks` with two filters at once</summary>

<br/>

### What this test does

You combine **two filters** in the same URL.
You want tasks that are NOT completed AND have high priority.

---

### Step 1 — Add this block to your file

```http
###

### TEST 5 — Two filters: completed=false AND priority=high
GET {{baseUrl}}/tasks?completed=false&priority=high
```

Notice the **`&`** between the two filters. It means "AND".

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 200 OK`

**Body:** only **1 task** — task 1 "Learn FastAPI":

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

| Task | completed=false? | priority=high? | Included? |
|---|---|---|---|
| 1 "Learn FastAPI" | ✅ | ✅ | **YES** |
| 2 "Build a REST API" | ❌ (it's true) | ✅ | No |
| 3 "Write unit tests" | ✅ | ❌ (medium) | No |
| 4 "Deploy to production" | ✅ | ❌ (low) | No |

Both conditions must be true at the same time → only task 1 passes.

---

> ✅ You see only task 1 with both filters?
> Perfect — move to Test 6.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s7"></a>

<details>
<summary>TEST 6 — GET `/tasks/1` — Get task number 1</summary>

<br/>

### What this test does

You ask for one specific task by its **ID number**.
You get exactly one task — not a list.

---

### Step 1 — Add this block to your file

```http
###

### TEST 6 — Get one task by ID (task 1)
GET {{baseUrl}}/tasks/1
```

The `1` at the end of the URL is the task ID. It is part of the URL path.

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 200 OK`

**Body:**

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

Notice: the response starts with `{` — it is a **single object**, not a list (`[`).

---

### Step 4 — Add blocks for other IDs

```http
###

### TEST 6b — Get task 2
GET {{baseUrl}}/tasks/2

###

### TEST 6c — Get task 3
GET {{baseUrl}}/tasks/3

###

### TEST 6d — Get task 4
GET {{baseUrl}}/tasks/4
```

Run each one. Expected titles:

| Block | Expected |
|---|---|
| `/tasks/1` | "Learn FastAPI" |
| `/tasks/2` | "Build a REST API" |
| `/tasks/3` | "Write unit tests" |
| `/tasks/4` | "Deploy to production" |

---

> ✅ You get the correct task for each ID?
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
You want to see what the server returns when the record is not found.

---

### Step 1 — Add this block to your file

```http
###

### TEST 7 — Get a task that does not exist (expect 404)
GET {{baseUrl}}/tasks/99
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 404 Not Found`

The status line is shown in **orange or red** — this is the REST Client's way of showing that the request did not succeed.

**Body:**

```json
{
  "detail": "Task with ID 99 not found"
}
```

---

### What 404 means

> **404 = Not Found**
> The server looked for task 99 — it simply does not exist.
> This is NOT a crash or a bug. The API was designed to return 404 for missing records.

---

### Step 4 — Try a few more

```http
###

### TEST 7b — ID 0 (also does not exist)
GET {{baseUrl}}/tasks/0

###

### TEST 7c — ID 1000
GET {{baseUrl}}/tasks/1000
```

All of them return `404 Not Found` with the matching message.

---

> ✅ You see `HTTP/1.1 404 Not Found` with "Task with ID 99 not found"?
> Perfect — move to Test 8.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>TEST 8 — POST `/tasks` — Create a complete task</summary>

<br/>

### What this test does

You are going to **create a new task**.
For the first time, you send data **in the body** of the request.

---

### How a POST block works in a `.http` file

A POST block has **three parts**, in this exact order:

```
Line 1: ### Comment
Line 2: POST url
Line 3: Content-Type: application/json      ← header (tells the server: I am sending JSON)
Line 4: (blank line)                         ← REQUIRED blank line
Line 5: {                                    ← JSON body starts here
Line 6:   "field": "value"
Line 7: }
```

> ⚠️ The **blank line between the header and the body is mandatory.**
> If you forget it, REST Client will not send the body correctly.

---

### Step 1 — Add this block to your file

```http
###

### TEST 8 — Create a complete task (POST)
POST {{baseUrl}}/tasks
Content-Type: application/json

{
  "title": "Study for the exam",
  "description": "Review chapters 3 to 7",
  "completed": false,
  "priority": "high"
}
```

Copy this **exactly**. Make sure there is a blank line between `Content-Type: application/json` and the `{`.

---

### Step 2 — Click "Send Request" above TEST 8

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 201 Created`

> **201 = Created** — your task was successfully added!

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

The server automatically added:
- `"id": 5` — the next available ID
- `"created_at"` — the current date and time

You did NOT send these two fields. The server created them for you.

---

### Step 4 — Check the status color

`HTTP/1.1 201 Created` is shown in **green** — it is a success status.

---

> ✅ You see `HTTP/1.1 201 Created` with `"id": 5` and a `created_at` value?
> Perfect — move to Test 9.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>TEST 9 — POST `/tasks` — Create a minimal task (title only)</summary>

<br/>

### What this test does

Only `title` is required. You are going to send only the title.
The server fills in all other fields with their **default values**.

---

### Step 1 — Add this block to your file

```http
###

### TEST 9 — Create a task with title only (POST — minimal)
POST {{baseUrl}}/tasks
Content-Type: application/json

{
  "title": "Quick reminder"
}
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 201 Created`

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

The server applied **default values** for the fields you did not send:

| Field | You sent | Server applied |
|---|---|---|
| `title` | "Quick reminder" | "Quick reminder" |
| `description` | *(not sent)* | `""` — empty string |
| `completed` | *(not sent)* | `false` |
| `priority` | *(not sent)* | `"medium"` |

---

> ✅ You see `201 Created` with `"id": 6` and the correct default values?
> Perfect — move to Test 10.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>TEST 10 — POST `/tasks` — Intentional error (missing title)</summary>

<br/>

### What this test does

You send a broken request **on purpose** to see what happens.
`title` is required — the server must refuse the request.

---

### Step 1 — Add this block to your file

```http
###

### TEST 10 — Intentional error: missing title (expect 422)
POST {{baseUrl}}/tasks
Content-Type: application/json

{}
```

An empty `{}` — no fields at all.

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 422 Unprocessable Entity`

The status is shown in **orange or red**.

> **422 = Unprocessable Entity**
> The server received the request but the data is **invalid**.
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

Read the error message carefully:
- `"loc": ["body", "title"]` → the problem is the field `title` in the body
- `"msg": "Field required"` → you must send `title`

---

### Step 4 — Add a second broken test: wrong type

```http
###

### TEST 10b — Intentional error: wrong type for completed (expect 422)
POST {{baseUrl}}/tasks
Content-Type: application/json

{
  "title": "Test task",
  "completed": "yes"
}
```

`completed` must be `true` or `false` — not the string `"yes"`.

Click **"Send Request"**. You get another **422** — this time about `completed`.

---

> ✅ Both broken requests return `HTTP/1.1 422 Unprocessable Entity`?
> Perfect — move to Test 11.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s12"></a>

<details>
<summary>TEST 11 — GET `/tasks` again — Confirm the new tasks are saved</summary>

<br/>

### What this test does

After creating 2 tasks (Tests 8 and 9), you re-run the GET to verify they are saved.

---

### Step 1 — Add this block to your file

```http
###

### TEST 11 — List all tasks again — should now show 6 tasks
GET {{baseUrl}}/tasks
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 200 OK`

You should now see **6 tasks** — the original 4 plus the 2 you just created:

| ID | Title |
|---|---|
| 1 | Learn FastAPI |
| 2 | Build a REST API |
| 3 | Write unit tests |
| 4 | Deploy to production |
| 5 | Study for the exam ← **you created this in Test 8** |
| 6 | Quick reminder ← **you created this in Test 9** |

---

> ✅ You see 6 tasks total?
> Perfect — move to Test 12.

> ❌ You still see 4? The server was restarted and the data reset. Go back to Tests 8 and 9.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s13"></a>

<details>
<summary>TEST 12 — PUT `/tasks/1` — Replace task 1 entirely</summary>

<br/>

### What this test does

**PUT** replaces a task **completely** — every field is overwritten.
Think of it as deleting the old task and creating a brand new one in its place (keeping the same ID).

---

### Step 1 — Add this block to your file

```http
###

### TEST 12 — Replace task 1 entirely (PUT)
PUT {{baseUrl}}/tasks/1
Content-Type: application/json

{
  "title": "Master FastAPI completely",
  "description": "Build 3 real projects from scratch",
  "completed": false,
  "priority": "high"
}
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 200 OK`

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

What stayed the same:
- `"id": 1` — the ID never changes
- `"created_at": "2026-03-19 08:00:00"` — the original creation date is preserved

What changed:
- **Everything else** was replaced with your new data

---

### Step 4 — Understand PUT vs PATCH

| | PUT | PATCH |
|---|---|---|
| What it does | Replaces **everything** | Updates **only what you send** |
| `title` required? | Yes | No |
| Fields you don't send | Reset to default values | Stay unchanged |
| When to use | Full replacement | Small targeted change |

---

### Step 5 — Add a verification block

```http
###

### TEST 12 verification — Read task 1 to confirm it changed
GET {{baseUrl}}/tasks/1
```

Click **"Send Request"** above this block. You should see `"title": "Master FastAPI completely"`.

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

**PATCH** updates **only the fields you send**.
All other fields stay exactly as they were.
You are going to change only `completed` for task 3.

---

### Step 1 — First, read task 3 to see its current state

Add and run this block:

```http
###

### TEST 13 — Read task 3 BEFORE the PATCH
GET {{baseUrl}}/tasks/3
```

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

`completed` is `false`. You are changing it to `true`.

---

### Step 2 — Add the PATCH block

```http
###

### TEST 13 — Mark task 3 as completed (PATCH — one field only)
PATCH {{baseUrl}}/tasks/3
Content-Type: application/json

{
  "completed": true
}
```

Only one field in the body. That is intentional — PATCH sends only what changes.

---

### Step 3 — Click "Send Request" above the PATCH block

---

### Step 4 — Read the response

**Status:** `HTTP/1.1 200 OK`

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

Compare field by field:

| Field | Before | After | Changed? |
|---|---|---|---|
| `id` | 3 | 3 | No |
| `title` | "Write unit tests" | "Write unit tests" | **No** |
| `description` | "Use pytest..." | "Use pytest..." | **No** |
| `completed` | `false` | `true` | **✅ YES** |
| `priority` | "medium" | "medium" | **No** |
| `created_at` | "2026-03-19..." | "2026-03-19..." | **No** |

Only `completed` changed. Everything else is identical.

---

> ✅ Task 3 now has `"completed": true` and all other fields unchanged?
> Perfect — move to Test 14.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s15"></a>

<details>
<summary>TEST 14 — PATCH `/tasks/2` — Change only the priority</summary>

<br/>

### What this test does

Another PATCH — this time you change only the `priority`.
This confirms PATCH really does only touch what you send.

---

### Step 1 — Add this block

```http
###

### TEST 14 — Change only the priority of task 2 (PATCH)
PATCH {{baseUrl}}/tasks/2
Content-Type: application/json

{
  "priority": "low"
}
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 200 OK`

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

### Step 4 — Try changing two fields at once

PATCH can change multiple fields in one request:

```http
###

### TEST 14b — Change two fields at once (PATCH)
PATCH {{baseUrl}}/tasks/1
Content-Type: application/json

{
  "completed": true,
  "priority": "medium"
}
```

Both `completed` and `priority` change for task 1. `title` and `description` stay the same.

---

> ✅ Task 2 `priority` is now `"low"` with status `200 OK`?
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

### How a DELETE block works

A DELETE block is very simple — **no body needed**:

```http
### Comment
DELETE url
```

No `Content-Type` header. No body. Just the method and the URL.

---

### Step 1 — Add this block to your file

```http
###

### TEST 15 — Delete task 4 (DELETE)
DELETE {{baseUrl}}/tasks/4
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 204 No Content`

> **204 = No Content**
> The task was deleted successfully.
> The server has nothing to return — the body is empty.
> **This is correct. An empty body on a DELETE = success.**

The response body area in VS Code will show nothing — completely blank. This is expected.

---

### Step 4 — What does `204 No Content` look like in REST Client?

The right panel shows only:

```
HTTP/1.1 204 No Content
```

Followed by the headers. The body section is empty.

The status `204` is shown in **green** — it is a success code.

---

### Remember the success codes

| Code | Name | When |
|---|---|---|
| 200 | OK | GET, PUT, PATCH |
| 201 | Created | POST |
| **204** | **No Content** | **DELETE** |

All three are green = all three mean success.

---

> ✅ You see `HTTP/1.1 204 No Content` with an empty body?
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
The server should say it no longer exists.

---

### Step 1 — Add this block

```http
###

### TEST 16 — Try to read deleted task 4 (expect 404)
GET {{baseUrl}}/tasks/4
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 404 Not Found`

**Body:**

```json
{
  "detail": "Task with ID 4 not found"
}
```

Task 4 is gone. The server confirms it does not exist anymore.

---

### Step 4 — Also check the full list

Add this and run it:

```http
###

### TEST 16 verification — Full list after deletion
GET {{baseUrl}}/tasks
```

You should now see **5 tasks** — IDs 1, 2, 3, 5, 6. Task 4 is absent.

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

You try to delete task 4 **again** — even though it was already deleted.
The server should return 404 — there is nothing to delete.

---

### Step 1 — Add this block

```http
###

### TEST 17 — Delete task 4 again — already gone (expect 404)
DELETE {{baseUrl}}/tasks/4
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — Read the response

**Status:** `HTTP/1.1 404 Not Found`

**Body:**

```json
{
  "detail": "Task with ID 4 not found"
}
```

The server cannot delete something that does not exist.
It returns 404 gracefully — no crash, no unexpected behavior.

---

> ✅ You see `404 Not Found` when deleting an already-deleted task?
> Perfect — move to the Final Check.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s19"></a>

<details>
<summary>Final Check — GET `/tasks` — See the final state</summary>

<br/>

### Step 1 — Add this block

```http
###

### FINAL CHECK — Full list — what is left after all tests
GET {{baseUrl}}/tasks
```

---

### Step 2 — Click "Send Request"

---

### Step 3 — What you should see

**5 tasks:**

| ID | Title | What changed |
|---|---|---|
| 1 | Master FastAPI completely | Title replaced in Test 12 |
| 2 | Build a REST API | Priority changed to "low" in Test 14 |
| 3 | Write unit tests | Marked as completed in Test 13 |
| 5 | Study for the exam | Created in Test 8 |
| 6 | Quick reminder | Created in Test 9 |

Task 4 is gone — deleted in Test 15.

---

### Summary of all tests

| Test | Method | URL | Expected Status |
|---|---|---|---|
| 1 | GET | `/` | ✅ 200 |
| 2 | GET | `/tasks` | ✅ 200 |
| 3 | GET | `/tasks?completed=true` | ✅ 200 |
| 4 | GET | `/tasks?priority=high` | ✅ 200 |
| 5 | GET | `/tasks?completed=false&priority=high` | ✅ 200 |
| 6 | GET | `/tasks/1` | ✅ 200 |
| 7 | GET | `/tasks/99` | ✅ 404 |
| 8 | POST | `/tasks` (full body) | ✅ 201 |
| 9 | POST | `/tasks` (title only) | ✅ 201 |
| 10 | POST | `/tasks` (empty body) | ✅ 422 |
| 11 | GET | `/tasks` (verify 6) | ✅ 200 |
| 12 | PUT | `/tasks/1` | ✅ 200 |
| 13 | PATCH | `/tasks/3` | ✅ 200 |
| 14 | PATCH | `/tasks/2` | ✅ 200 |
| 15 | DELETE | `/tasks/4` | ✅ 204 |
| 16 | GET | `/tasks/4` (confirm) | ✅ 404 |
| 17 | DELETE | `/tasks/4` (again) | ✅ 404 |

---

### How to reset the data

The data lives only in memory. To go back to the original 4 tasks:

1. Click on the terminal where uvicorn is running
2. Press `Ctrl + C` to stop it
3. Run `uvicorn main:app --reload` again
4. All tasks reset to the original 4 — you can run all tests again from the start

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s20"></a>

<details>
<summary>Your complete `.http` file — all tests in one place</summary>

<br/>

> Copy everything below and paste it into your `my_tests.http` file to have all tests ready at once.
> Each block has a **"Send Request"** link — click the one you want to run.

```http
@baseUrl = http://127.0.0.1:8000

### TEST 1 — Root endpoint — is the server running?
GET {{baseUrl}}/

###

### TEST 2 — List all tasks
GET {{baseUrl}}/tasks

###

### TEST 3 — Filter: only completed tasks
GET {{baseUrl}}/tasks?completed=true

###

### TEST 3b — Filter: only NOT completed tasks
GET {{baseUrl}}/tasks?completed=false

###

### TEST 4 — Filter: only high priority
GET {{baseUrl}}/tasks?priority=high

###

### TEST 4b — Filter: medium priority
GET {{baseUrl}}/tasks?priority=medium

###

### TEST 4c — Filter: low priority
GET {{baseUrl}}/tasks?priority=low

###

### TEST 5 — Two filters: completed=false AND priority=high
GET {{baseUrl}}/tasks?completed=false&priority=high

###

### TEST 6 — Get task 1 by ID
GET {{baseUrl}}/tasks/1

###

### TEST 6b — Get task 2
GET {{baseUrl}}/tasks/2

###

### TEST 6c — Get task 3
GET {{baseUrl}}/tasks/3

###

### TEST 6d — Get task 4
GET {{baseUrl}}/tasks/4

###

### TEST 7 — Task does not exist (expect 404)
GET {{baseUrl}}/tasks/99

###

### TEST 8 — Create a complete task (POST)
POST {{baseUrl}}/tasks
Content-Type: application/json

{
  "title": "Study for the exam",
  "description": "Review chapters 3 to 7",
  "completed": false,
  "priority": "high"
}

###

### TEST 9 — Create a task with title only (POST — minimal)
POST {{baseUrl}}/tasks
Content-Type: application/json

{
  "title": "Quick reminder"
}

###

### TEST 10 — Intentional error: missing title (expect 422)
POST {{baseUrl}}/tasks
Content-Type: application/json

{}

###

### TEST 10b — Intentional error: wrong type for completed (expect 422)
POST {{baseUrl}}/tasks
Content-Type: application/json

{
  "title": "Test task",
  "completed": "yes"
}

###

### TEST 11 — List all tasks again (should show 6 tasks now)
GET {{baseUrl}}/tasks

###

### TEST 12 — Replace task 1 entirely (PUT)
PUT {{baseUrl}}/tasks/1
Content-Type: application/json

{
  "title": "Master FastAPI completely",
  "description": "Build 3 real projects from scratch",
  "completed": false,
  "priority": "high"
}

###

### TEST 12 verification — Read task 1 after PUT
GET {{baseUrl}}/tasks/1

###

### TEST 13 — Read task 3 BEFORE the PATCH
GET {{baseUrl}}/tasks/3

###

### TEST 13 — Mark task 3 as completed (PATCH — one field only)
PATCH {{baseUrl}}/tasks/3
Content-Type: application/json

{
  "completed": true
}

###

### TEST 14 — Change only the priority of task 2 (PATCH)
PATCH {{baseUrl}}/tasks/2
Content-Type: application/json

{
  "priority": "low"
}

###

### TEST 14b — Change two fields at once (PATCH)
PATCH {{baseUrl}}/tasks/1
Content-Type: application/json

{
  "completed": true,
  "priority": "medium"
}

###

### TEST 15 — Delete task 4 (DELETE)
DELETE {{baseUrl}}/tasks/4

###

### TEST 16 — Try to read deleted task 4 (expect 404)
GET {{baseUrl}}/tasks/4

###

### TEST 16 verification — Full list after deletion (should show 5 tasks)
GET {{baseUrl}}/tasks

###

### TEST 17 — Delete task 4 again — already gone (expect 404)
DELETE {{baseUrl}}/tasks/4

###

### FINAL CHECK — Full list — final state
GET {{baseUrl}}/tasks

###
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<p align="center">
  <strong>End of VS Code REST Client Step-by-Step Guide</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>
