<a id="top"></a>

# Testing Guide — Task Manager API (FastAPI)

> **Beginner-friendly guide** to testing all endpoints of the Task Manager API.
> Step-by-step instructions for **Swagger UI**, **Postman**, **curl**, **VS Code REST Client**, and **Python requests**.
> No prior experience required.

---

## Table of Contents

| # | Section |
|---|---|
| 1 | [Project Overview — What is this API?](#s1) |
| 2 | [Start the Server](#s2) |
| 3 | [Endpoints Map — All 7 Routes at a Glance](#s3) |
| 4 | [Test with Swagger UI — in your Browser](#s4) |
| 5 | [Test with Postman](#s5) |
| 6 | [Test with curl](#s6) |
| 7 | [Test with VS Code REST Client](#s7) |
| 8 | [Test with Python (requests)](#s8) |
| 9 | [HTTP Status Codes Explained](#s9) |
| 10 | [Common Errors and How to Fix Them](#s10) |
| 11 | [Practice Exercises](#s11) |

---

<a id="s1"></a>

<details>
<summary>1 — Project Overview — What is this API?</summary>

<br/>

### What does this API do?

This is a **Task Manager API** — a simple REST API that lets you manage a to-do list.

You can:
- **Read** all tasks or a single task
- **Create** a new task
- **Update** an existing task (fully or partially)
- **Delete** a task

All data is stored **in memory** (a Python dictionary). Every time you restart the server, it resets to the 4 sample tasks.

---

### Project files

```text
mini_fastapi/
│
├── main.py          ← FastAPI server — all endpoints are here
├── frontend.py      ← Streamlit web interface (optional)
├── requirements.txt ← Python packages needed
├── README.md        ← Full documentation
├── test_api.http    ← VS Code REST Client test file
└── curl_examples.sh ← curl commands for testing
```

---

### What is a Task?

Each task has these fields:

| Field | Type | Required? | Default | Description |
|---|---|---|---|---|
| `id` | integer | auto-assigned | — | Unique ID, set by the server |
| `title` | string | **yes** | — | Short name of the task |
| `description` | string | no | `""` | Longer explanation |
| `completed` | boolean | no | `false` | Is the task done? |
| `priority` | string | no | `"medium"` | `"low"`, `"medium"`, or `"high"` |
| `created_at` | string | auto-assigned | — | Date/time the task was created |

---

### Sample tasks (pre-loaded at startup)

The API starts with 4 tasks already in memory:

| ID | Title | Completed | Priority |
|---|---|---|---|
| 1 | Learn FastAPI | false | high |
| 2 | Build a REST API | true | high |
| 3 | Write unit tests | false | medium |
| 4 | Deploy to production | false | low |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s2"></a>

<details>
<summary>2 — Start the Server</summary>

<br/>

> Before testing anything, the FastAPI server must be running. Follow these steps.

---

### Step 1 — Open a terminal in the project folder

```powershell
# Windows — navigate to the project folder
cd "c:\00-projetsGA\2025H-420-911-MA-main -COURSES EN ANGLAIS\inskillflow-docs-portfolio-1\mini_fastapi"
```

```bash
# macOS / Linux
cd /path/to/mini_fastapi
```

---

### Step 2 — Activate the virtual environment

```powershell
# Windows
venv\Scripts\activate
# Your prompt changes to: (venv) PS C:\...>
```

```bash
# macOS / Linux
source venv/bin/activate
# Your prompt changes to: (venv) $
```

> If `venv` does not exist yet, create it first:
> ```powershell
> python -m venv venv
> venv\Scripts\activate
> pip install -r requirements.txt
> ```

---

### Step 3 — Start FastAPI

```bash
# All platforms — run this command
uvicorn main:app --reload
```

You should see output like this:

```text
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process
INFO:     Application startup complete.
```

---

### Step 4 — Verify the server is running

Open your browser and go to:

```text
http://127.0.0.1:8000
```

You should see this JSON response:

```json
{
  "message": "Task Manager API is running",
  "docs": "http://127.0.0.1:8000/docs",
  "total_tasks": 4
}
```

The server is working. You are ready to test.

---

### Important URLs

| URL | What it is |
|---|---|
| `http://127.0.0.1:8000` | Root endpoint — API info |
| `http://127.0.0.1:8000/docs` | **Swagger UI** — interactive test page |
| `http://127.0.0.1:8000/redoc` | ReDoc — read-only documentation |
| `http://127.0.0.1:8000/openapi.json` | Raw JSON specification |

---

### Stop the server

```bash
# In the terminal where uvicorn is running:
Ctrl + C
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>3 — Endpoints Map — All 7 Routes at a Glance</summary>

<br/>

> An **endpoint** is a specific URL + HTTP method combination. Think of it as a door to a specific function of the API.

---

### Complete list of endpoints

| # | Method | URL | What it does | Success code |
|---|---|---|---|---|
| 1 | `GET` | `/` | API info (message, total tasks) | 200 |
| 2 | `GET` | `/tasks` | List all tasks | 200 |
| 3 | `GET` | `/tasks?completed=true` | List only completed tasks | 200 |
| 4 | `GET` | `/tasks?priority=high` | List only high-priority tasks | 200 |
| 5 | `GET` | `/tasks/{id}` | Get one task by its ID | 200 |
| 6 | `POST` | `/tasks` | Create a new task | 201 |
| 7 | `PUT` | `/tasks/{id}` | Replace a task entirely | 200 |
| 8 | `PATCH` | `/tasks/{id}` | Update specific fields only | 200 |
| 9 | `DELETE` | `/tasks/{id}` | Delete a task | 204 |

---

### HTTP Methods — what each one means

| Method | Meaning | Analogy |
|---|---|---|
| `GET` | Read data — never modifies anything | Looking at a menu |
| `POST` | Create new data | Placing an order |
| `PUT` | Replace everything in an existing record | Rewriting your order completely |
| `PATCH` | Update only specific fields | Changing just the drink in your order |
| `DELETE` | Remove a record permanently | Cancelling your order |

---

### JSON body required?

| Method | Needs a body? | Body model |
|---|---|---|
| GET | No | — |
| DELETE | No | — |
| POST | **Yes** | `TaskCreate` |
| PUT | **Yes** | `TaskCreate` |
| PATCH | **Yes** (partial) | `TaskUpdate` |

---

### TaskCreate — fields for POST and PUT

```json
{
  "title": "My task title",
  "description": "Optional details",
  "completed": false,
  "priority": "high"
}
```

Only `title` is required. The other fields have defaults.

---

### TaskUpdate — fields for PATCH

```json
{
  "completed": true
}
```

Every field is optional. Send only what you want to change.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>4 — Test with Swagger UI — in your Browser</summary>

<br/>

> **Swagger UI** is the easiest way to test your API. It is built into FastAPI automatically.
> No installation required — just a browser.

---

### Open Swagger UI

Make sure the server is running, then open:

```text
http://127.0.0.1:8000/docs
```

You will see a page with all your endpoints listed as colored buttons.

---

### Color code of the buttons

| Color | Method | Meaning |
|---|---|---|
| 🟢 Green | GET | Read |
| 🟡 Yellow | POST | Create |
| 🔵 Blue | PUT | Replace |
| 🟣 Purple | PATCH | Partial update |
| 🔴 Red | DELETE | Delete |

---

### Test 1 — GET / (root)

1. Click the green **GET** button next to `/`
2. Click **"Try it out"** (top right of the panel)
3. Click the blue **"Execute"** button
4. Scroll down — you will see:
   - **Request URL:** `http://127.0.0.1:8000/`
   - **Response code:** `200`
   - **Response body:**
     ```json
     {
       "message": "Task Manager API is running",
       "docs": "http://127.0.0.1:8000/docs",
       "total_tasks": 4
     }
     ```

---

### Test 2 — GET /tasks (list all tasks)

1. Click **GET** `/tasks`
2. Click **"Try it out"**
3. Leave the `completed` and `priority` fields empty
4. Click **"Execute"**
5. You get a list of 4 tasks in JSON format

---

### Test 3 — GET /tasks with a filter

1. Click **GET** `/tasks`
2. Click **"Try it out"**
3. In the `completed` field, type: `false`
4. Click **"Execute"**
5. You get only the uncompleted tasks (IDs 1, 3, 4)

Try also:
- `priority` = `high` → returns tasks 1 and 2
- `completed` = `true` → returns only task 2

---

### Test 4 — GET /tasks/{task_id} (one task)

1. Click **GET** `/tasks/{task_id}`
2. Click **"Try it out"**
3. In the `task_id` field, type: `1`
4. Click **"Execute"**
5. You get only task number 1

Now try `task_id` = `99`:
- You get a **404 error** with the message: `"Task with ID 99 not found"`

---

### Test 5 — POST /tasks (create a task)

1. Click **POST** `/tasks`
2. Click **"Try it out"**
3. In the **Request body** text area, replace the example with:
   ```json
   {
     "title": "Study for the exam",
     "description": "Review chapters 3 to 7",
     "completed": false,
     "priority": "high"
   }
   ```
4. Click **"Execute"**
5. You get a **201 Created** response with your new task, which has `"id": 5`

---

### Test 6 — PUT /tasks/{task_id} (replace a task)

1. Click **PUT** `/tasks/{task_id}`
2. Click **"Try it out"**
3. `task_id` = `1`
4. Body:
   ```json
   {
     "title": "Master FastAPI",
     "description": "Read docs AND build projects",
     "completed": false,
     "priority": "high"
   }
   ```
5. Click **"Execute"**
6. Task 1 is completely replaced with the new data
7. Note: `created_at` is preserved (it never changes)

---

### Test 7 — PATCH /tasks/{task_id} (partial update)

1. Click **PATCH** `/tasks/{task_id}`
2. Click **"Try it out"**
3. `task_id` = `3`
4. Body (only what you want to change):
   ```json
   {
     "completed": true
   }
   ```
5. Click **"Execute"**
6. Task 3 is now marked as completed — all other fields are unchanged

---

### Test 8 — DELETE /tasks/{task_id}

1. Click **DELETE** `/tasks/{task_id}`
2. Click **"Try it out"**
3. `task_id` = `4`
4. Click **"Execute"**
5. You get a **204 No Content** response — the task is deleted

Verify: go back to **GET /tasks** and run it again — task 4 is gone.

---

### Tips for Swagger UI

> - After each test, click **"Execute"** again without changing anything to see the current state.
> - The **"Schema"** tab (next to "Example Value") shows you the exact field types expected.
> - If you get **422**, your JSON body has a typo or wrong type — check the field names carefully.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s5"></a>

<details>
<summary>5 — Test with Postman</summary>

<br/>

> **Postman** is a desktop app for testing APIs. Download it from:
> ```text
> https://www.postman.com/downloads/
> ```

---

### Setup — create a collection

1. Open Postman
2. Click **"New"** → **"Collection"**
3. Name it: `Task Manager API`
4. This keeps all your requests organized in one place

---

### Test 1 — GET / (root)

```text
Method: GET
URL:    http://127.0.0.1:8000/
```

Steps:
1. Click **"New"** → **"HTTP Request"**
2. Select method: **GET**
3. Enter URL: `http://127.0.0.1:8000/`
4. Click **Send**
5. Bottom panel shows: **200 OK** and the JSON response

---

### Test 2 — GET /tasks (list all tasks)

```text
Method: GET
URL:    http://127.0.0.1:8000/tasks
```

---

### Test 3 — GET /tasks with query parameters

```text
Method: GET
URL:    http://127.0.0.1:8000/tasks
```

Steps:
1. Click the **"Params"** tab
2. Add a row:
   ```
   KEY         VALUE
   completed   false
   ```
3. Postman automatically builds the URL: `http://127.0.0.1:8000/tasks?completed=false`
4. Click **Send**

Add a second filter:
```
KEY         VALUE
completed   false
priority    high
```
→ URL becomes: `/tasks?completed=false&priority=high`

---

### Test 4 — GET /tasks/{task_id}

```text
Method: GET
URL:    http://127.0.0.1:8000/tasks/1
```

Try with ID 99 to see the 404 error:
```text
URL:    http://127.0.0.1:8000/tasks/99
```

---

### Test 5 — POST /tasks (create a task)

```text
Method: POST
URL:    http://127.0.0.1:8000/tasks
```

Steps:
1. Method: **POST**
2. URL: `http://127.0.0.1:8000/tasks`
3. Click the **"Body"** tab
4. Select **"raw"**
5. Change the dropdown from **"Text"** to **"JSON"**
6. Enter:
   ```json
   {
     "title": "Study for the exam",
     "description": "Review chapters 3 to 7",
     "completed": false,
     "priority": "high"
   }
   ```
7. Click **Send**
8. Expected response: **201 Created**

---

### Test 6 — POST with only the required field

```json
{
  "title": "Quick note"
}
```

This works too — `description`, `completed`, and `priority` use their default values.

---

### Test 7 — PUT /tasks/{task_id} (replace entirely)

```text
Method: PUT
URL:    http://127.0.0.1:8000/tasks/1
Body (JSON):
{
  "title": "Master FastAPI",
  "description": "Read docs AND build projects",
  "completed": false,
  "priority": "high"
}
```

Expected: **200 OK** — task 1 is fully replaced.

---

### Test 8 — PATCH /tasks/{task_id} (partial update)

```text
Method: PATCH
URL:    http://127.0.0.1:8000/tasks/3
Body (JSON):
{
  "completed": true
}
```

Expected: **200 OK** — only `completed` changed; other fields are the same.

Try changing only the priority:
```json
{
  "priority": "low"
}
```

---

### Test 9 — DELETE /tasks/{task_id}

```text
Method: DELETE
URL:    http://127.0.0.1:8000/tasks/4
```

Steps:
1. Method: **DELETE**
2. URL: `http://127.0.0.1:8000/tasks/4`
3. No body needed
4. Click **Send**
5. Expected: **204 No Content** — the response body is empty

---

### Postman status code indicators

| Status shown in Postman | Meaning |
|---|---|
| **200 OK** | Success — GET, PUT, PATCH |
| **201 Created** | Success — POST |
| **204 No Content** | Success — DELETE (no body returned) |
| **404 Not Found** | The task ID does not exist |
| **422 Unprocessable Entity** | Your JSON body has a validation error |

---

### Save requests to your collection

After testing, click the **"Save"** button to save each request to your `Task Manager API` collection. This way you can run them again without retyping.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>6 — Test with curl</summary>

<br/>

> **curl** is a command-line tool for making HTTP requests. It is available on all platforms.

---

### Check if curl is available

```bash
curl --version
```

If not installed:
- **Windows:** curl is included in Windows 10/11 — open PowerShell and try
- **macOS:** already installed
- **Linux:** `sudo apt install curl`

---

### Test 1 — GET / (root)

```bash
curl http://127.0.0.1:8000/
```

Expected output:
```json
{"message":"Task Manager API is running","docs":"http://127.0.0.1:8000/docs","total_tasks":4}
```

---

### Test 2 — GET /tasks (list all tasks)

```bash
curl http://127.0.0.1:8000/tasks
```

---

### Test 3 — GET /tasks with filters

```bash
# Filter by completed=false
curl "http://127.0.0.1:8000/tasks?completed=false"

# Filter by priority=high
curl "http://127.0.0.1:8000/tasks?priority=high"

# Combine both filters
curl "http://127.0.0.1:8000/tasks?completed=false&priority=high"
```

> Always put the URL in quotes when it contains `&` — otherwise the shell may interpret it incorrectly.

---

### Test 4 — GET /tasks/{task_id}

```bash
# Get task with ID 1
curl http://127.0.0.1:8000/tasks/1

# Get task with ID 99 — triggers a 404 error
curl http://127.0.0.1:8000/tasks/99
```

---

### Test 5 — POST /tasks (create a task)

```bash
curl -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d "{\"title\": \"Study for the exam\", \"description\": \"Review chapters 3 to 7\", \"completed\": false, \"priority\": \"high\"}"
```

**Windows PowerShell version:**
```powershell
Invoke-RestMethod -Method Post `
  -Uri "http://127.0.0.1:8000/tasks" `
  -ContentType "application/json" `
  -Body '{"title": "Study for the exam", "description": "Review chapters 3 to 7", "completed": false, "priority": "high"}'
```

---

### Test 6 — POST with only the required field

```bash
curl -X POST http://127.0.0.1:8000/tasks \
     -H "Content-Type: application/json" \
     -d "{\"title\": \"Quick note\"}"
```

---

### Test 7 — PUT /tasks/{task_id} (replace entirely)

```bash
curl -X PUT http://127.0.0.1:8000/tasks/1 \
     -H "Content-Type: application/json" \
     -d "{\"title\": \"Master FastAPI\", \"description\": \"Read docs AND build projects\", \"completed\": false, \"priority\": \"high\"}"
```

**Windows PowerShell:**
```powershell
Invoke-RestMethod -Method Put `
  -Uri "http://127.0.0.1:8000/tasks/1" `
  -ContentType "application/json" `
  -Body '{"title": "Master FastAPI", "description": "Read docs AND build projects", "completed": false, "priority": "high"}'
```

---

### Test 8 — PATCH /tasks/{task_id} (partial update)

```bash
# Mark task 3 as completed
curl -X PATCH http://127.0.0.1:8000/tasks/3 \
     -H "Content-Type: application/json" \
     -d "{\"completed\": true}"
```

**Windows PowerShell:**
```powershell
Invoke-RestMethod -Method Patch `
  -Uri "http://127.0.0.1:8000/tasks/3" `
  -ContentType "application/json" `
  -Body '{"completed": true}'
```

---

### Test 9 — DELETE /tasks/{task_id}

```bash
# Delete task with ID 4
curl -X DELETE http://127.0.0.1:8000/tasks/4

# Show the HTTP status code (should be 204)
curl -X DELETE http://127.0.0.1:8000/tasks/4 -w "\nHTTP Status: %{http_code}\n"
```

**Windows PowerShell:**
```powershell
Invoke-RestMethod -Method Delete -Uri "http://127.0.0.1:8000/tasks/4"
```

---

### curl flags explained

| Flag | Meaning | Example |
|---|---|---|
| `-X` | HTTP method | `-X POST` |
| `-H` | Add a header | `-H "Content-Type: application/json"` |
| `-d` | Request body data | `-d '{"title": "test"}'` |
| `-w` | Print info after response | `-w "%{http_code}"` |
| `-v` | Verbose — show headers | `-v` |
| `-s` | Silent — hide progress | `-s` |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s7"></a>

<details>
<summary>7 — Test with VS Code REST Client</summary>

<br/>

> **REST Client** is a VS Code extension that lets you write and run HTTP requests directly from a `.http` file.

---

### Install the extension

1. Open VS Code
2. Press `Ctrl+Shift+X` (Extensions panel)
3. Search: `REST Client`
4. Install the extension by **Huachao Mao**

---

### Open the existing test file

The project already includes a ready-to-use file:

```text
mini_fastapi/test_api.http
```

Open it in VS Code. You will see requests like:

```http
@baseUrl = http://127.0.0.1:8000

### Get all tasks
GET {{baseUrl}}/tasks
```

Click **"Send Request"** above each block to run it.

---

### Write your own .http file

Create a new file called `my_tests.http` in the project folder:

```http
@baseUrl = http://127.0.0.1:8000

### 1 - Root endpoint
GET {{baseUrl}}/

###

### 2 - Get all tasks
GET {{baseUrl}}/tasks

###

### 3 - Get only completed tasks
GET {{baseUrl}}/tasks?completed=true

###

### 4 - Get only high-priority tasks
GET {{baseUrl}}/tasks?priority=high

###

### 5 - Get task with ID 1
GET {{baseUrl}}/tasks/1

###

### 6 - Get task that does not exist (404)
GET {{baseUrl}}/tasks/99

###

### 7 - Create a new task
POST {{baseUrl}}/tasks
Content-Type: application/json

{
  "title": "Study for the exam",
  "description": "Review chapters 3 to 7",
  "completed": false,
  "priority": "high"
}

###

### 8 - Create a task with only the title
POST {{baseUrl}}/tasks
Content-Type: application/json

{
  "title": "Quick note"
}

###

### 9 - Replace task 1 entirely (PUT)
PUT {{baseUrl}}/tasks/1
Content-Type: application/json

{
  "title": "Master FastAPI",
  "description": "Read docs AND build projects",
  "completed": false,
  "priority": "high"
}

###

### 10 - Mark task 3 as completed (PATCH)
PATCH {{baseUrl}}/tasks/3
Content-Type: application/json

{
  "completed": true
}

###

### 11 - Change only the priority of task 2 (PATCH)
PATCH {{baseUrl}}/tasks/2
Content-Type: application/json

{
  "priority": "low"
}

###

### 12 - Delete task 4
DELETE {{baseUrl}}/tasks/4

###

### 13 - Try to delete a task that does not exist (404)
DELETE {{baseUrl}}/tasks/99

###
```

---

### How to run a request

1. Click **"Send Request"** (appears above each `###` block when you hover)
2. A panel opens on the right showing the response
3. You see: status code, headers, and body

---

### REST Client tips

> - Each block is separated by `###`
> - The `@baseUrl` variable avoids retyping the URL every time
> - Leave a blank line between the headers and the JSON body
> - The `Content-Type: application/json` header is required for POST, PUT, PATCH

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s8"></a>

<details>
<summary>8 — Test with Python (requests)</summary>

<br/>

> The `requests` library lets you make HTTP calls from Python code. It is already in `requirements.txt`.

---

### Install (if needed)

```bash
pip install requests
```

---

### Create a test script

Create a file called `test_manual.py` in the project folder:

```python
import requests

BASE_URL = "http://127.0.0.1:8000"


# ------------------------------------------------------------------ GET /
print("\n=== GET / ===")
response = requests.get(f"{BASE_URL}/")
print(f"Status: {response.status_code}")
print(response.json())


# ----------------------------------------------------------- GET /tasks
print("\n=== GET /tasks ===")
response = requests.get(f"{BASE_URL}/tasks")
print(f"Status: {response.status_code}")
tasks = response.json()
print(f"Number of tasks: {len(tasks)}")
for task in tasks:
    print(f"  [{task['id']}] {task['title']} — priority: {task['priority']}")


# ---------------------------------------- GET /tasks with filter
print("\n=== GET /tasks?completed=false ===")
response = requests.get(f"{BASE_URL}/tasks", params={"completed": "false"})
print(f"Status: {response.status_code}")
tasks = response.json()
print(f"Uncompleted tasks: {len(tasks)}")


# ----------------------------------------- GET /tasks/{task_id}
print("\n=== GET /tasks/1 ===")
response = requests.get(f"{BASE_URL}/tasks/1")
print(f"Status: {response.status_code}")
print(response.json())


# ------------------------------------ GET /tasks/{id} — 404 case
print("\n=== GET /tasks/99 (not found) ===")
response = requests.get(f"{BASE_URL}/tasks/99")
print(f"Status: {response.status_code}")
print(response.json())


# --------------------------------------------------------- POST /tasks
print("\n=== POST /tasks ===")
new_task = {
    "title": "Study for the exam",
    "description": "Review chapters 3 to 7",
    "completed": False,
    "priority": "high"
}
response = requests.post(f"{BASE_URL}/tasks", json=new_task)
print(f"Status: {response.status_code}")
created = response.json()
print(f"New task created with ID: {created['id']}")
print(created)


# ------------------------------------------ PUT /tasks/{task_id}
print("\n=== PUT /tasks/1 ===")
replacement = {
    "title": "Master FastAPI",
    "description": "Read docs AND build projects",
    "completed": False,
    "priority": "high"
}
response = requests.put(f"{BASE_URL}/tasks/1", json=replacement)
print(f"Status: {response.status_code}")
print(response.json())


# ---------------------------------------- PATCH /tasks/{task_id}
print("\n=== PATCH /tasks/3 — mark as completed ===")
partial_update = {"completed": True}
response = requests.patch(f"{BASE_URL}/tasks/3", json=partial_update)
print(f"Status: {response.status_code}")
print(response.json())


# --------------------------------------- DELETE /tasks/{task_id}
print("\n=== DELETE /tasks/4 ===")
response = requests.delete(f"{BASE_URL}/tasks/4")
print(f"Status: {response.status_code}")
# 204 No Content — no body returned

# Confirm it is gone
response = requests.get(f"{BASE_URL}/tasks/4")
print(f"Try to get deleted task — Status: {response.status_code}")
```

---

### Run the script

```bash
# Make sure the FastAPI server is already running in another terminal
python test_manual.py
```

---

### Understanding the requests library

| `requests` method | HTTP method | Example |
|---|---|---|
| `requests.get(url)` | GET | Read data |
| `requests.post(url, json=body)` | POST | Create |
| `requests.put(url, json=body)` | PUT | Replace |
| `requests.patch(url, json=body)` | PATCH | Partial update |
| `requests.delete(url)` | DELETE | Delete |

---

### Reading the response

```python
response = requests.get("http://127.0.0.1:8000/tasks/1")

# HTTP status code (200, 201, 204, 404, 422...)
print(response.status_code)

# Response body as a Python dictionary
print(response.json())

# Response body as raw text
print(response.text)

# Check if the request was successful (status 200–299)
if response.ok:
    print("Success!")
else:
    print("Something went wrong.")
```

---

### Sending query parameters

```python
# These two lines are equivalent:

# Option A — build the URL manually
requests.get("http://127.0.0.1:8000/tasks?completed=false&priority=high")

# Option B — use the params dict (recommended)
requests.get("http://127.0.0.1:8000/tasks", params={"completed": "false", "priority": "high"})
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>9 — HTTP Status Codes Explained</summary>

<br/>

> Every HTTP response includes a **status code** — a 3-digit number that tells you what happened.

---

### Status codes used by this API

| Code | Name | When you see it | Meaning |
|---|---|---|---|
| **200** | OK | GET, PUT, PATCH | Request succeeded — response body contains the data |
| **201** | Created | POST | Task was created — response body contains the new task |
| **204** | No Content | DELETE | Task was deleted — response body is empty |
| **404** | Not Found | GET, PUT, PATCH, DELETE | The task ID does not exist in the database |
| **422** | Unprocessable Entity | POST, PUT, PATCH | Validation error — your JSON body is invalid |

---

### The 3 families of status codes

| Range | Family | Meaning |
|---|---|---|
| 2xx | **Success** | Everything worked |
| 4xx | **Client error** | You sent something wrong |
| 5xx | **Server error** | The server crashed (not your fault) |

---

### 200 OK — example

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

### 201 Created — example

Returned after `POST /tasks`:

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

---

### 204 No Content

Returned after `DELETE /tasks/{id}`.
The response body is **completely empty**. This is normal and expected.

---

### 404 Not Found — example

Returned when the task ID does not exist:

```json
{
  "detail": "Task with ID 99 not found"
}
```

---

### 422 Unprocessable Entity — example

Returned when the JSON body fails validation:

```json
{
  "detail": [
    {
      "type": "missing",
      "loc": ["body", "title"],
      "msg": "Field required",
      "input": {}
    }
  ]
}
```

This tells you exactly which field is missing or invalid.

---

### Quick decision guide

```text
Got 200 or 201? → 
Got 204?        → Success — DELETE worked, no body is normal
Got 404?        → Check the task ID — it does not exist
Got 422?        → Fix your JSON — wrong field name, missing field, or wrong type
Got 500?        → Server crashed — check the terminal where uvicorn is running
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>10 — Common Errors and How to Fix Them</summary>

<br/>

---

### Error — Server is not running

**Symptom:** Any request gives `Connection refused` or `Unable to connect`

```text
requests.exceptions.ConnectionError: HTTPConnectionPool(host='127.0.0.1', port=8000)
```

**Fix:** Start the FastAPI server first:
```bash
uvicorn main:app --reload
```

---

### Error — 404 Not Found

**Symptom:**
```json
{"detail": "Task with ID 99 not found"}
```

**Cause:** The task ID you used does not exist.

**Fix:**
1. Run `GET /tasks` to see which IDs currently exist
2. Use a valid ID in your request
3. Remember: the data resets every time you restart the server

---

### Error — 422 Unprocessable Entity

**Symptom:**
```json
{
  "detail": [
    {"type": "missing", "loc": ["body", "title"], "msg": "Field required"}
  ]
}
```

**Common causes and fixes:**

| Cause | Wrong | Correct |
|---|---|---|
| Missing required field | `{"description": "test"}` | `{"title": "test"}` |
| Wrong field name | `{"name": "test"}` | `{"title": "test"}` |
| Wrong type for `completed` | `{"completed": "yes"}` | `{"completed": true}` |
| Missing `Content-Type` header | No header | `Content-Type: application/json` |
| Sending plain text instead of JSON | `title=test` | `{"title": "test"}` |

---

### Error — Port already in use

**Symptom:**
```text
ERROR: [Errno 10048] Only one usage of each socket address
```

**Fix (Windows):**
```powershell
# Find what is using port 8000
netstat -ano | findstr :8000

# Kill the process (replace 12345 with the PID you found)
taskkill /PID 12345 /F

# Or use a different port
uvicorn main:app --reload --port 8001
```

**Fix (macOS/Linux):**
```bash
# Kill everything on port 8000
lsof -ti:8000 | xargs kill -9

# Or use a different port
uvicorn main:app --reload --port 8001
```

---

### Error — Module not found

**Symptom:**
```text
ModuleNotFoundError: No module named 'fastapi'
```

**Fix:**
```powershell
# Windows — activate venv first
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

---

### Error — venv\Scripts\activate is disabled (Windows)

**Symptom:**
```text
venv\Scripts\activate.ps1 cannot be loaded because running scripts is disabled
```

**Fix (run once in PowerShell as administrator):**
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

### Error — 405 Method Not Allowed

**Symptom:**
```json
{"detail": "Method Not Allowed"}
```

**Cause:** You used the wrong HTTP method for that route.

**Fix:** Check the endpoint table in Section 3. For example:
- `DELETE /tasks` does not exist — you must use `DELETE /tasks/{id}`
- `POST /tasks/1` does not exist — POST goes to `/tasks` (no ID)

---

### Error — Empty response body on DELETE

**This is NOT an error.** A `204 No Content` response is always empty. This is the correct behavior for a successful DELETE.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>11 — Practice Exercises</summary>

<br/>

> Work through these exercises in order. Each one builds on the previous.
> Use **any tool** you prefer: Swagger UI, Postman, curl, or Python.

---

### Exercise 1 — Explore the existing data

1. Start the server: `uvicorn main:app --reload`
2. Send a `GET /tasks` request
3. How many tasks are there?
4. Which tasks are **not** completed?
5. Which tasks have priority `"high"`?

**Expected answers:** 4 tasks total, 3 uncompleted, 2 with high priority.

---

### Exercise 2 — Retrieve one specific task

1. Get task with ID `2`
2. What is its title?
3. Is it completed?
4. Now try ID `10` — what happens?

**Expected:** Task 2 is "Build a REST API", completed = true. ID 10 returns a 404.

---

### Exercise 3 — Use query parameters

1. Get all tasks where `completed = true`
2. Get all tasks where `priority = low`
3. Get all tasks where `completed = false` AND `priority = medium`

**Expected:** 1 task completed, 1 task low priority, 1 task uncompleted + medium.

---

### Exercise 4 — Create a new task

Send a `POST /tasks` with this body:
```json
{
  "title": "Read about REST APIs",
  "description": "Understand GET, POST, PUT, PATCH, DELETE",
  "completed": false,
  "priority": "medium"
}
```

1. What ID was assigned to your new task?
2. Run `GET /tasks` — how many tasks are there now?

**Expected:** New task gets ID `5`, total becomes 5 tasks.

---

### Exercise 5 — Create a minimal task

Send a `POST /tasks` with only the required field:
```json
{
  "title": "Minimal task"
}
```

1. What are the default values for `completed`, `priority`, and `description`?

**Expected:** `completed = false`, `priority = "medium"`, `description = ""`.

---

### Exercise 6 — Replace a task entirely (PUT)

1. Send a `PUT /tasks/3` with this body:
   ```json
   {
     "title": "Write integration tests",
     "description": "Use pytest with TestClient",
     "completed": true,
     "priority": "high"
   }
   ```
2. Verify with `GET /tasks/3` — did all fields change?
3. Did `created_at` change? (It should NOT change)

---

### Exercise 7 — Partial update (PATCH)

1. Send a `PATCH /tasks/1` with only:
   ```json
   {
     "completed": true
   }
   ```
2. Verify with `GET /tasks/1` — is `completed` now `true`?
3. Did `title`, `description`, and `priority` stay the same?

---

### Exercise 8 — Change the priority

1. Send a `PATCH /tasks/2` with:
   ```json
   {
     "priority": "low"
   }
   ```
2. Verify with `GET /tasks/2`

---

### Exercise 9 — Delete a task

1. Delete task with ID `4`
2. Verify it is gone with `GET /tasks/4` — what status code do you get?
3. Run `GET /tasks` — how many tasks remain?

**Expected:** 404 after deletion, 4 tasks remain (if you created 2 in exercises 4 and 5).

---

### Exercise 10 — Error handling

Intentionally send bad requests and observe the error messages:

1. `GET /tasks/0` — ID 0 does not exist
2. `POST /tasks` with an empty body `{}`
3. `POST /tasks` with `{"completed": "yes"}` (wrong type — should be boolean)
4. `DELETE /tasks/99` — ID that does not exist

For each request, note:
- The HTTP status code
- The `detail` field in the error response

---

### Challenge — Full workflow in Python

Write a Python script (`challenge.py`) that does all of the following **in sequence**:

1. Check that the API is running with `GET /`
2. List all tasks
3. Create a new task with title `"My challenge task"` and `priority = "high"`
4. Save the new task's `id` in a variable
5. Mark it as completed using `PATCH`
6. Verify it is completed using `GET /tasks/{id}`
7. Delete it using `DELETE`
8. Verify it is gone using `GET /tasks/{id}` — confirm you get a 404
9. Print `"Challenge complete!"` at the end

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<p align="center">
  <strong>End of Testing Guide — Task Manager API</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>
