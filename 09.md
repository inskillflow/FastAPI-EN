<a id="top"></a>

# curl — Step-by-Step Testing Guide (Linux)
## Task Manager API — `main.py`

> **Tool used: curl in the Linux terminal only.**
> No browser, no Postman, no VS Code extension.
> Just your terminal and curl.
> This guide tells you exactly what to type, what to press, and what you should see.
> Follow every step in order. Do not skip anything.

---

## Table of Contents

| # | What you will test |
|---|---|
| 0 | [Install curl and start the server](#s0) |
| 1 | [TEST 1 — GET `/` — Is the server alive?](#s1) |
| 2 | [TEST 2 — GET `/tasks` — List all tasks](#s2) |
| 3 | [TEST 3 — GET `/tasks?completed=true` — Only completed tasks](#s3) |
| 4 | [TEST 4 — GET `/tasks?priority=high` — Only high priority](#s4) |
| 5 | [TEST 5 — GET `/tasks` with two filters at once](#s5) |
| 6 | [TEST 6 — GET `/tasks/1` — Get task number 1](#s6) |
| 7 | [TEST 7 — GET `/tasks/99` — Task that does not exist](#s7) |
| 8 | [TEST 8 — POST `/tasks` — Create a complete task](#s8) |
| 9 | [TEST 9 — POST `/tasks` — Create a minimal task (title only)](#s9) |
| 10 | [TEST 10 — POST `/tasks` — Intentional error (missing title)](#s10) |
| 11 | [TEST 11 — GET `/tasks` again — Confirm the new tasks are saved](#s11) |
| 12 | [TEST 12 — PUT `/tasks/1` — Replace task 1 entirely](#s12) |
| 13 | [TEST 13 — PATCH `/tasks/3` — Mark task 3 as completed](#s13) |
| 14 | [TEST 14 — PATCH `/tasks/2` — Change only the priority](#s14) |
| 15 | [TEST 15 — DELETE `/tasks/4` — Delete task 4](#s15) |
| 16 | [TEST 16 — GET `/tasks/4` — Confirm the deletion](#s16) |
| 17 | [TEST 17 — DELETE `/tasks/4` again — Already deleted](#s17) |
| 18 | [Final Check — GET `/tasks` — See the final state](#s18) |
| 19 | [curl flags explained — quick reference](#s19) |

---

<a id="s0"></a>

<details>
<summary>0 — Install curl and start the server</summary>

<br/>

### A — Check if curl is already installed

Open a terminal. Type this and press **Enter**:

```bash
curl --version
```

If curl is installed, you will see something like:

```text
curl 7.81.0 (x86_64-pc-linux-gnu) libcurl/7.81.0 OpenSSL/3.0.2
...
```

If you see `command not found`, install it:

```bash
sudo apt update
sudo apt install -y curl
```

Then verify again:

```bash
curl --version
```

---

### B — Navigate to the project folder

```bash
cd /path/to/mini_fastapi
```

Replace `/path/to/mini_fastapi` with the actual path on your system. Example:

```bash
cd ~/projects/mini_fastapi
```

---

### C — Activate the virtual environment

```bash
source venv/bin/activate
```

Your prompt changes to:

```text
(venv) yourname@ubuntu:~/projects/mini_fastapi$
```

If `venv` does not exist yet:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

### D — Start the FastAPI server

```bash
uvicorn main:app --reload
```

You should see:

```text
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Application startup complete.
```

> ⚠️ Keep this terminal open. The server stops if you close it.
> Open a **second terminal** for all your curl commands — press `Ctrl + Alt + T` or right-click the desktop.

---

### E — Open a second terminal for curl

All the `curl` commands in this guide must be typed in a **different terminal** than the one running the server.

In the second terminal, activate the venv too (optional for curl — but good habit):

```bash
cd ~/projects/mini_fastapi
source venv/bin/activate
```

---

> ✅ Server is running in terminal 1. Terminal 2 is open for curl commands.
> Move to Test 1.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s1"></a>

<details>
<summary>TEST 1 — GET `/` — Is the server alive?</summary>

<br/>

### What this test does

You ask the server: "Are you running?"
The server replies with a welcome message and the number of tasks.

---

### The command

In your **second terminal**, type this and press **Enter**:

```bash
curl http://127.0.0.1:8000/
```

---

### What you should see

```json
{"message":"Task Manager API is running","docs":"http://127.0.0.1:8000/docs","total_tasks":4}
```

---

### Make it easier to read — pretty print

The response above is all on one line — it is hard to read.
Add the `-s` flag and pipe it through `python3 -m json.tool` to format it nicely:

```bash
curl -s http://127.0.0.1:8000/ | python3 -m json.tool
```

Now you see:

```json
{
    "message": "Task Manager API is running",
    "docs": "http://127.0.0.1:8000/docs",
    "total_tasks": 4
}
```

> 💡 You can add `| python3 -m json.tool` at the end of **any** curl command in this guide to make the output readable.

---

### What this means

| Field | Value | Meaning |
|---|---|---|
| `message` | "Task Manager API is running" | Server is alive |
| `total_tasks` | 4 | 4 tasks are currently in memory |

---

### Also check the HTTP status code

By default curl does not show the status code. Add `-w "\n\nHTTP Status: %{http_code}\n"` to see it:

```bash
curl -s http://127.0.0.1:8000/ -w "\n\nHTTP Status: %{http_code}\n" | python3 -m json.tool
```

You will see `HTTP Status: 200` at the bottom.

---

> ✅ You see `"total_tasks": 4`?
> Perfect — move to Test 2.

> ❌ You see `curl: (7) Failed to connect`? The server is not running. Go back to step 0.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s2"></a>

<details>
<summary>TEST 2 — GET `/tasks` — List all tasks</summary>

<br/>

### What this test does

You ask the server: "Give me all the tasks."
The server replies with a list of all 4 tasks.

---

### The command

```bash
curl -s http://127.0.0.1:8000/tasks | python3 -m json.tool
```

---

### What you should see

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

- The output starts with `[` and ends with `]` → this is a **list** (array) of 4 tasks
- Each task is between `{` and `}` → each one is a **JSON object**
- Only task 2 has `"completed": true`
- Tasks 1 and 2 have `"priority": "high"`

---

> ✅ You see 4 tasks?
> Perfect — move to Test 3.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>TEST 3 — GET `/tasks?completed=true` — Only completed tasks</summary>

<br/>

### What this test does

You add a **filter** to the URL.
You only want tasks where `completed` is `true`.
The `?completed=true` part of the URL is called a **query parameter**.

---

### The command

```bash
curl -s "http://127.0.0.1:8000/tasks?completed=true" | python3 -m json.tool
```

> ⚠️ Always put the URL in **double quotes** when it contains `?` or `&`.
> Without quotes, the shell may misinterpret the `?` or `&` character.

---

### What you should see

Only **1 task** — task 2:

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

---

### Now try the opposite: completed=false

```bash
curl -s "http://127.0.0.1:8000/tasks?completed=false" | python3 -m json.tool
```

You should get **3 tasks** — IDs 1, 3, and 4.

---

### What to notice

- `?` in the URL starts the filter section
- `completed=true` tells the server: "only give me tasks where `completed` equals `true`"
- The server filters the list and returns only the matching ones

---

> ✅ `completed=true` → 1 task, `completed=false` → 3 tasks?
> Perfect — move to Test 4.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>TEST 4 — GET `/tasks?priority=high` — Only high priority</summary>

<br/>

### What this test does

You filter tasks by their priority level.

---

### The commands — try all three values

```bash
# High priority only
curl -s "http://127.0.0.1:8000/tasks?priority=high" | python3 -m json.tool
```

Expected: **2 tasks** — IDs 1 and 2.

```bash
# Medium priority only
curl -s "http://127.0.0.1:8000/tasks?priority=medium" | python3 -m json.tool
```

Expected: **1 task** — ID 3 "Write unit tests".

```bash
# Low priority only
curl -s "http://127.0.0.1:8000/tasks?priority=low" | python3 -m json.tool
```

Expected: **1 task** — ID 4 "Deploy to production".

---

### Run each command one at a time

Type the first command, press **Enter**, read the result.
Then type the second command, press **Enter**, read the result.
Then the third.

---

> ✅ `high` → 2 tasks, `medium` → 1 task, `low` → 1 task?
> Perfect — move to Test 5.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s5"></a>

<details>
<summary>TEST 5 — GET `/tasks` with two filters at once</summary>

<br/>

### What this test does

You combine **two filters** in the same URL using `&`.
You want tasks that are NOT completed AND have high priority.

---

### The command

```bash
curl -s "http://127.0.0.1:8000/tasks?completed=false&priority=high" | python3 -m json.tool
```

---

### What you should see

Only **1 task** — task 1:

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

---

### Why only task 1?

| Task | completed=false? | priority=high? | Result |
|---|---|---|---|
| 1 "Learn FastAPI" | ✅ false | ✅ high | **included** |
| 2 "Build a REST API" | ❌ true | ✅ high | excluded |
| 3 "Write unit tests" | ✅ false | ❌ medium | excluded |
| 4 "Deploy to production" | ✅ false | ❌ low | excluded |

Both conditions must be true at the same time → only task 1 passes.

---

### The `&` in the URL

`&` separates multiple query parameters. It means "AND".

```text
/tasks?completed=false&priority=high
        ^                ^
        filter 1         filter 2
```

> ⚠️ Always wrap URLs with `&` in **double quotes** in bash, otherwise the shell will run `priority=high` as a background command.

---

> ✅ You see only task 1 with both filters?
> Perfect — move to Test 6.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>TEST 6 — GET `/tasks/1` — Get task number 1</summary>

<br/>

### What this test does

You ask for one specific task by its **ID number**.
You get exactly one task — not a list.

---

### The command

```bash
curl -s http://127.0.0.1:8000/tasks/1 | python3 -m json.tool
```

---

### What you should see

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

Notice: the response starts with `{` — it is a **single object**, not a list.
A list would start with `[`.

---

### Try other IDs — run each command separately

```bash
# Task 2
curl -s http://127.0.0.1:8000/tasks/2 | python3 -m json.tool

# Task 3
curl -s http://127.0.0.1:8000/tasks/3 | python3 -m json.tool

# Task 4
curl -s http://127.0.0.1:8000/tasks/4 | python3 -m json.tool
```

Expected titles:

| Command | Expected title |
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

<a id="s7"></a>

<details>
<summary>TEST 7 — GET `/tasks/99` — Task that does not exist</summary>

<br/>

### What this test does

You ask for a task with ID 99 — which does not exist.
You want to see what the server returns when a record is missing.

---

### The command

```bash
curl -s http://127.0.0.1:8000/tasks/99 | python3 -m json.tool
```

---

### What you should see

```json
{
    "detail": "Task with ID 99 not found"
}
```

---

### See the HTTP status code too

```bash
curl -s -o /dev/null -w "HTTP Status: %{http_code}\n" http://127.0.0.1:8000/tasks/99
```

You will see:

```text
HTTP Status: 404
```

> **404 = Not Found**
> The server looked for task 99 — it does not exist.
> This is NOT a crash. The API is programmed to return 404 for missing records.

---

### Explanation of the flags used

```bash
curl -s -o /dev/null -w "HTTP Status: %{http_code}\n" URL
```

| Flag | Meaning |
|---|---|
| `-s` | Silent — hides the progress bar |
| `-o /dev/null` | Throws away the body (we only want the status code here) |
| `-w "..."` | Prints custom text after the request — `%{http_code}` is replaced by the actual code |

---

### Try a few more non-existent IDs

```bash
curl -s http://127.0.0.1:8000/tasks/0 | python3 -m json.tool
curl -s http://127.0.0.1:8000/tasks/100 | python3 -m json.tool
```

Both return 404 with the matching message.

---

> ✅ You see `"detail": "Task with ID 99 not found"` and HTTP status 404?
> Perfect — move to Test 8.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s8"></a>

<details>
<summary>TEST 8 — POST `/tasks` — Create a complete task</summary>

<br/>

### What this test does

You are going to **create a new task** by sending data to the server.
This is the first test where you send data in the **body** of the request.

---

### How a POST command works with curl

A POST request needs three extra things compared to a GET:

| What | curl flag | Example |
|---|---|---|
| Change the method to POST | `-X POST` | `-X POST` |
| Tell the server you are sending JSON | `-H "Content-Type: application/json"` | `-H "Content-Type: application/json"` |
| Send the JSON body | `-d '...'` | `-d '{"title": "My task"}'` |

---

### The command

```bash
curl -s -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d '{"title": "Study for the exam", "description": "Review chapters 3 to 7", "completed": false, "priority": "high"}' \
     | python3 -m json.tool
```

> The `\` at the end of a line means "the command continues on the next line".
> You can also type the whole command on one line without the `\`.

---

### What you should see

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

### See the HTTP status code

```bash
curl -s -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d '{"title": "Study for the exam", "description": "Review chapters 3 to 7", "completed": false, "priority": "high"}' \
     -w "\n\nHTTP Status: %{http_code}\n" \
     | python3 -m json.tool
```

You should see `HTTP Status: 201` at the bottom.

> **201 = Created** — your task was successfully created.

---

> ✅ You see `HTTP Status: 201` and `"id": 5` in the response?
> Perfect — move to Test 9.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>TEST 9 — POST `/tasks` — Create a minimal task (title only)</summary>

<br/>

### What this test does

Only `title` is required. You send only the title.
The server fills in all other fields with their **default values**.

---

### The command

```bash
curl -s -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d '{"title": "Quick reminder"}' \
     | python3 -m json.tool
```

---

### What you should see

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

> ✅ You see `"id": 6` and the correct default values?
> Perfect — move to Test 10.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>TEST 10 — POST `/tasks` — Intentional error (missing title)</summary>

<br/>

### What this test does

You send a broken request **on purpose**.
`title` is required — the server must refuse the request with an error.

---

### Test A — Empty body

```bash
curl -s -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d '{}' \
     | python3 -m json.tool
```

### What you should see

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

The error message tells you exactly what is wrong:
- `"loc": ["body", "title"]` → the problem is the field `title` in the body
- `"msg": "Field required"` → you must provide `title`

---

### See the status code

```bash
curl -s -o /dev/null -w "HTTP Status: %{http_code}\n" \
     -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d '{}'
```

Output:

```text
HTTP Status: 422
```

> **422 = Unprocessable Entity**
> The server received your request but the data is **invalid**.

---

### Test B — Wrong type for a field

```bash
curl -s -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d '{"title": "Test task", "completed": "yes"}' \
     | python3 -m json.tool
```

`completed` must be `true` or `false` — not the string `"yes"`.

You get another **422** — this time about `completed` having the wrong type.

---

> ✅ Both broken commands return HTTP status 422?
> Perfect — move to Test 11.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>TEST 11 — GET `/tasks` again — Confirm the new tasks are saved</summary>

<br/>

### What this test does

After creating 2 tasks (Tests 8 and 9), you re-run the GET to verify they are in the list.

---

### The command

```bash
curl -s http://127.0.0.1:8000/tasks | python3 -m json.tool
```

---

### What you should see

**6 tasks** — the original 4 plus the 2 you just created:

| ID | Title |
|---|---|
| 1 | Learn FastAPI |
| 2 | Build a REST API |
| 3 | Write unit tests |
| 4 | Deploy to production |
| 5 | Study for the exam ← **created in Test 8** |
| 6 | Quick reminder ← **created in Test 9** |

---

> ✅ You see 6 tasks total including your 2 new ones?
> Perfect — move to Test 12.

> ❌ You still see 4? The server was restarted and the data reset. Go back to Tests 8 and 9.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s12"></a>

<details>
<summary>TEST 12 — PUT `/tasks/1` — Replace task 1 entirely</summary>

<br/>

### What this test does

**PUT** replaces a task **completely** — every single field is overwritten with the new data.
Think of it as: "delete the old task and put this new one in its place."

---

### The command

```bash
curl -s -X PUT http://127.0.0.1:8000/tasks/1 \
     -H "Content-Type: application/json" \
     -d '{"title": "Master FastAPI completely", "description": "Build 3 real projects from scratch", "completed": false, "priority": "high"}' \
     | python3 -m json.tool
```

---

### What you should see

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
- `"title"`, `"description"`, `"completed"`, `"priority"` — all completely replaced

---

### Verify: read task 1 again

```bash
curl -s http://127.0.0.1:8000/tasks/1 | python3 -m json.tool
```

You should see `"title": "Master FastAPI completely"`.

---

### PUT vs PATCH — key difference

| | PUT | PATCH |
|---|---|---|
| What it does | Replaces **everything** | Updates **only what you send** |
| Fields not sent | Reset to default values | Stay unchanged |
| `title` required? | Yes | No |

---

> ✅ Task 1 title is now "Master FastAPI completely"?
> Perfect — move to Test 13.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s13"></a>

<details>
<summary>TEST 13 — PATCH `/tasks/3` — Mark task 3 as completed</summary>

<br/>

### What this test does

**PATCH** updates **only the fields you send**.
All other fields stay exactly as they were.
You are going to change only `completed` for task 3.

---

### Step 1 — Read task 3 first to see its current state

```bash
curl -s http://127.0.0.1:8000/tasks/3 | python3 -m json.tool
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

`completed` is `false`. You are going to change it to `true`.

---

### Step 2 — Send the PATCH

```bash
curl -s -X PATCH http://127.0.0.1:8000/tasks/3 \
     -H "Content-Type: application/json" \
     -d '{"completed": true}' \
     | python3 -m json.tool
```

Only one field in the body — `"completed": true`. That is intentional.

---

### What you should see

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
| `title` | "Write unit tests" | "Write unit tests" | **No** |
| `description` | "Use pytest..." | "Use pytest..." | **No** |
| `completed` | `false` | `true` | **✅ YES** |
| `priority` | "medium" | "medium" | **No** |
| `created_at` | "2026-03-19..." | "2026-03-19..." | **No** |

Only `completed` changed. Everything else is identical.

---

> ✅ Task 3 has `"completed": true` and all other fields unchanged?
> Perfect — move to Test 14.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s14"></a>

<details>
<summary>TEST 14 — PATCH `/tasks/2` — Change only the priority</summary>

<br/>

### What this test does

Another PATCH — you change only the `priority` field of task 2.
Everything else stays the same.

---

### The command

```bash
curl -s -X PATCH http://127.0.0.1:8000/tasks/2 \
     -H "Content-Type: application/json" \
     -d '{"priority": "low"}' \
     | python3 -m json.tool
```

---

### What you should see

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

### Try changing two fields at once

PATCH also works with multiple fields:

```bash
curl -s -X PATCH http://127.0.0.1:8000/tasks/1 \
     -H "Content-Type: application/json" \
     -d '{"completed": true, "priority": "medium"}' \
     | python3 -m json.tool
```

Both `completed` and `priority` change for task 1. `title` and `description` stay the same.

---

> ✅ Task 2 `priority` is now `"low"` and everything else is unchanged?
> Perfect — move to Test 15.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s15"></a>

<details>
<summary>TEST 15 — DELETE `/tasks/4` — Delete task 4</summary>

<br/>

### What this test does

You are going to **permanently delete** a task.
Once deleted, the task is gone from memory.

---

### The command

```bash
curl -s -X DELETE http://127.0.0.1:8000/tasks/4
```

> For DELETE, you do not need `-H` or `-d` — no body is required.

---

### What you should see

**Nothing.** The terminal shows no output. This is correct.

A successful DELETE returns **204 No Content** — an empty body.
curl shows nothing when the body is empty.

---

### Confirm the status code

```bash
curl -s -o /dev/null -w "HTTP Status: %{http_code}\n" -X DELETE http://127.0.0.1:8000/tasks/4
```

Wait — task 4 was just deleted. Run this **before** step 1 if you want to capture the 204:

Actually — let's redo the test with the status code in one shot.

**Restart the server first to reset the data (optional)**, or just accept that task 4 is already deleted and the status will be 404.

If task 4 still exists, this command shows both the body and the status:

```bash
curl -s -X DELETE http://127.0.0.1:8000/tasks/4 \
     -w "\nHTTP Status: %{http_code}\n"
```

Expected output:

```text

HTTP Status: 204
```

The line before `HTTP Status: 204` is empty — that is the empty body. **204 = success.**

---

### Remember the success codes

| Code | Name | When you see it |
|---|---|---|
| 200 | OK | GET, PUT, PATCH |
| 201 | Created | POST |
| **204** | **No Content** | **DELETE — empty body is correct** |

---

> ✅ You see `HTTP Status: 204` with an empty body?
> Perfect — move to Test 16.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s16"></a>

<details>
<summary>TEST 16 — GET `/tasks/4` — Confirm the deletion</summary>

<br/>

### What this test does

After deleting task 4, you try to read it.
The server should say it no longer exists.

---

### The command

```bash
curl -s http://127.0.0.1:8000/tasks/4 | python3 -m json.tool
```

---

### What you should see

```json
{
    "detail": "Task with ID 4 not found"
}
```

Task 4 is gone. The server confirms it does not exist anymore.

---

### Confirm the status code is 404

```bash
curl -s -o /dev/null -w "HTTP Status: %{http_code}\n" http://127.0.0.1:8000/tasks/4
```

Output:

```text
HTTP Status: 404
```

---

### Also confirm the full list no longer has task 4

```bash
curl -s http://127.0.0.1:8000/tasks | python3 -m json.tool
```

You should see **5 tasks** — IDs 1, 2, 3, 5, 6. Task 4 is absent.

---

> ✅ You get `404 Not Found` for task 4 and see 5 tasks in the full list?
> Perfect — move to Test 17.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s17"></a>

<details>
<summary>TEST 17 — DELETE `/tasks/4` again — Already deleted</summary>

<br/>

### What this test does

You try to delete task 4 **again** — even though it was already deleted.
The server should return 404.

---

### The command

```bash
curl -s -X DELETE http://127.0.0.1:8000/tasks/4 | python3 -m json.tool
```

---

### What you should see

```json
{
    "detail": "Task with ID 4 not found"
}
```

Confirm the status code:

```bash
curl -s -o /dev/null -w "HTTP Status: %{http_code}\n" -X DELETE http://127.0.0.1:8000/tasks/4
```

Output:

```text
HTTP Status: 404
```

The server cannot delete something that does not exist.
It returns 404 gracefully — no crash, no unexpected behavior.

---

> ✅ You see `404 Not Found` when trying to delete an already-deleted task?
> Perfect — move to the Final Check.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s18"></a>

<details>
<summary>Final Check — GET `/tasks` — See the final state</summary>

<br/>

### The command

```bash
curl -s http://127.0.0.1:8000/tasks | python3 -m json.tool
```

---

### What you should see — 5 tasks

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

| Test | Command | Expected status |
|---|---|---|
| 1 | `curl http://127.0.0.1:8000/` | ✅ 200 |
| 2 | `curl http://127.0.0.1:8000/tasks` | ✅ 200 |
| 3 | `curl "...tasks?completed=true"` | ✅ 200 |
| 4 | `curl "...tasks?priority=high"` | ✅ 200 |
| 5 | `curl "...tasks?completed=false&priority=high"` | ✅ 200 |
| 6 | `curl http://127.0.0.1:8000/tasks/1` | ✅ 200 |
| 7 | `curl http://127.0.0.1:8000/tasks/99` | ✅ 404 |
| 8 | `curl -X POST ... -d '{full body}'` | ✅ 201 |
| 9 | `curl -X POST ... -d '{"title": "..."}'` | ✅ 201 |
| 10 | `curl -X POST ... -d '{}'` | ✅ 422 |
| 11 | `curl http://127.0.0.1:8000/tasks` | ✅ 200 (6 tasks) |
| 12 | `curl -X PUT ... -d '{full body}'` | ✅ 200 |
| 13 | `curl -X PATCH ... -d '{"completed": true}'` | ✅ 200 |
| 14 | `curl -X PATCH ... -d '{"priority": "low"}'` | ✅ 200 |
| 15 | `curl -X DELETE .../tasks/4` | ✅ 204 |
| 16 | `curl .../tasks/4` (after delete) | ✅ 404 |
| 17 | `curl -X DELETE .../tasks/4` (again) | ✅ 404 |

---

### How to reset the data

The data lives only in memory. To get back the original 4 tasks:

1. Go to the terminal where uvicorn is running
2. Press `Ctrl + C` to stop the server
3. Run `uvicorn main:app --reload` again
4. All data resets — you can run all tests again from scratch

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s19"></a>

<details>
<summary>curl flags explained — quick reference</summary>

<br/>

### Flags used in this guide

| Flag | Full name | What it does | Example |
|---|---|---|---|
| `-s` | `--silent` | Hides the progress bar and error messages | `curl -s URL` |
| `-X` | `--request` | Sets the HTTP method | `-X POST`, `-X PUT`, `-X DELETE` |
| `-H` | `--header` | Adds a header to the request | `-H "Content-Type: application/json"` |
| `-d` | `--data` | Sends data in the request body | `-d '{"title": "test"}'` |
| `-o` | `--output` | Saves the response to a file or `/dev/null` | `-o /dev/null` |
| `-w` | `--write-out` | Prints custom text after the request | `-w "Status: %{http_code}\n"` |

---

### Common curl patterns used in this guide

**Read all (GET — no body):**
```bash
curl -s http://127.0.0.1:8000/tasks | python3 -m json.tool
```

**Read with a filter (GET — query parameter):**
```bash
curl -s "http://127.0.0.1:8000/tasks?completed=true" | python3 -m json.tool
```

**Read one by ID (GET — ID in URL):**
```bash
curl -s http://127.0.0.1:8000/tasks/1 | python3 -m json.tool
```

**Create (POST — body required):**
```bash
curl -s -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d '{"title": "My task"}' \
     | python3 -m json.tool
```

**Replace entirely (PUT — body required):**
```bash
curl -s -X PUT http://127.0.0.1:8000/tasks/1 \
     -H "Content-Type: application/json" \
     -d '{"title": "New title", "description": "...", "completed": false, "priority": "high"}' \
     | python3 -m json.tool
```

**Partial update (PATCH — body required, only changed fields):**
```bash
curl -s -X PATCH http://127.0.0.1:8000/tasks/1 \
     -H "Content-Type: application/json" \
     -d '{"completed": true}' \
     | python3 -m json.tool
```

**Delete (DELETE — no body):**
```bash
curl -s -X DELETE http://127.0.0.1:8000/tasks/1
```

**Check the HTTP status code only:**
```bash
curl -s -o /dev/null -w "HTTP Status: %{http_code}\n" http://127.0.0.1:8000/tasks/1
```

**Show body AND status code:**
```bash
curl -s http://127.0.0.1:8000/tasks/1 \
     -w "\n\nHTTP Status: %{http_code}\n" \
     | python3 -m json.tool
```

---

### HTTP methods — summary

| Method | curl flag | Needs `-H` and `-d`? | When to use |
|---|---|---|---|
| GET | *(default, no flag needed)* | No | Read data |
| POST | `-X POST` | Yes | Create a new record |
| PUT | `-X PUT` | Yes | Replace a record entirely |
| PATCH | `-X PATCH` | Yes | Update specific fields only |
| DELETE | `-X DELETE` | No | Delete a record |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<p align="center">
  <strong>End of curl Step-by-Step Guide — Linux</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>
