<a id="top"></a>

# Lab — Build Your Own Contact Manager API (FastAPI)

> **Your assignment:** build a CRUD REST API from scratch using FastAPI.
> This lab is intentionally very close to the Task Manager API you already tested — the structure is identical.
> Only the **data model** and **domain** change.
> Step-by-step instructions. Test your own work as you go.

---

## Table of Contents

| # | Section |
|---|---|
| 1 | [The Assignment — What You Are Building](#s1) |
| 2 | [Project Setup — Folder, venv, Dependencies](#s2) |
| 3 | [Step 1 — Create `main.py` and Run the Server](#s3) |
| 4 | [Step 2 — Define the Data Model (Pydantic)](#s4) |
| 5 | [Step 3 — Create the In-Memory Database](#s5) |
| 6 | [Step 4 — Build the GET Endpoints](#s6) |
| 7 | [Step 5 — Build the POST Endpoint](#s7) |
| 8 | [Step 6 — Build the PUT Endpoint](#s8) |
| 9 | [Step 7 — Build the PATCH Endpoint](#s9) |
| 10 | [Step 8 — Build the DELETE Endpoint](#s10) |
| 11 | [Step 9 — Test Everything with Swagger UI](#s11) |
| 12 | [Step 10 — Write a `.http` Test File](#s12) |
| 13 | [Validation Checklist](#s13) |
| 14 | [Bonus Challenges](#s14) |

---

<a id="s1"></a>

<details>
<summary>1 — The Assignment — What You Are Building</summary>

<br/>

### Your task

> **Build a Contact Manager REST API** using FastAPI.
> The API must allow a user to manage a list of contacts: create them, read them, update them, and delete them.

This is the same architecture as the Task Manager you tested, but instead of tasks you are managing **contacts**.

---

### What is a Contact?

Each contact in your API must have these fields:

| Field | Type | Required? | Default | Description |
|---|---|---|---|---|
| `id` | integer | auto-assigned by server | — | Unique identifier |
| `name` | string | **YES** | — | Full name of the contact |
| `email` | string | no | `""` | Email address |
| `phone` | string | no | `""` | Phone number |
| `favorite` | boolean | no | `false` | Is this a favorite contact? |
| `category` | string | no | `"personal"` | `"personal"`, `"work"`, or `"school"` |
| `created_at` | string | auto-assigned by server | — | Date and time of creation |

---

### Endpoints you must build

| Method | Route | What it does |
|---|---|---|
| GET | `/` | Returns a welcome message and the total number of contacts |
| GET | `/contacts` | Returns all contacts |
| GET | `/contacts?favorite=true` | Returns only favorite contacts |
| GET | `/contacts?category=work` | Returns contacts filtered by category |
| GET | `/contacts/{id}` | Returns one contact by ID — 404 if not found |
| POST | `/contacts` | Creates a new contact — returns 201 |
| PUT | `/contacts/{id}` | Replaces a contact entirely — 404 if not found |
| PATCH | `/contacts/{id}` | Updates specific fields only — 404 if not found |
| DELETE | `/contacts/{id}` | Deletes a contact — returns 204 — 404 if not found |

---

### Sample data to pre-load

Start your API with these 4 contacts already in memory:

| ID | Name | Email | Favorite | Category |
|---|---|---|---|---|
| 1 | Alice Martin | alice@example.com | true | work |
| 2 | Bob Smith | bob@example.com | false | school |
| 3 | Carol Johnson | carol@example.com | true | personal |
| 4 | David Lee | david@example.com | false | work |

---

### What is NOT different from the Task Manager

- Same tools: FastAPI, Uvicorn, Pydantic v2
- Same file structure: `main.py`
- Same architecture: in-memory `dict`, auto-increment `next_id`
- Same error handling: `HTTPException` with 404
- Same response codes: 200, 201, 204, 404, 422

---

### What IS different

| Task Manager | Contact Manager (your lab) |
|---|---|
| Resource: `Task` | Resource: `Contact` |
| Fields: title, description, completed, priority | Fields: name, email, phone, favorite, category |
| Route: `/tasks` | Route: `/contacts` |
| Filter: `?completed=` and `?priority=` | Filter: `?favorite=` and `?category=` |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s2"></a>

<details>
<summary>2 — Project Setup — Folder, venv, Dependencies</summary>

<br/>

### Step A — Create your project folder

```powershell
# Windows
mkdir contact_manager_api
cd contact_manager_api
```

```bash
# macOS / Linux
mkdir contact_manager_api
cd contact_manager_api
```

---

### Step B — Create and activate the virtual environment

```powershell
# Windows
python -m venv venv
venv\Scripts\activate
```

```bash
# macOS / Linux
python3 -m venv venv
source venv/bin/activate
```

Your prompt should now show `(venv)`.

---

### Step C — Install the dependencies

```bash
pip install fastapi uvicorn pydantic
```

---

### Step D — Create a `requirements.txt`

```bash
pip freeze > requirements.txt
```

---

### Step E — Verify your folder structure

After these steps, your folder should look like this:

```text
contact_manager_api/
│
├── venv/                ← virtual environment (created automatically)
├── requirements.txt     ← saved dependencies
└── main.py              ← you will create this in the next step
```

---

### Step F — Verify FastAPI is installed

```bash
python -c "import fastapi; print(fastapi.__version__)"
```

You should see a version number like `0.115.0`. If you see an error, run `pip install fastapi` again.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>3 — Step 1 — Create main.py and Run the Server</summary>

<br/>

### Create the file

Create a file called `main.py` in your project folder.

Start with the absolute minimum — just the app with a root endpoint:

```python
from fastapi import FastAPI

app = FastAPI(
    title="Contact Manager API",
    description="CRUD API for managing contacts",
    version="1.0.0",
)


@app.get("/")
def root():
    return {
        "message": "Contact Manager API is running",
        "docs": "http://127.0.0.1:8000/docs",
        "total_contacts": 0,
    }
```

---

### Start the server

```bash
uvicorn main:app --reload
```

You should see:

```text
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Application startup complete.
```

---

### Test checkpoint #1

Open your browser and go to `http://127.0.0.1:8000`

Expected response:
```json
{
  "message": "Contact Manager API is running",
  "docs": "http://127.0.0.1:8000/docs",
  "total_contacts": 0
}
```

Also open: `http://127.0.0.1:8000/docs`

You should see the Swagger UI with one green GET button.

> If this works — move to Step 2.
> If not — check that `(venv)` is active and uvicorn is installed.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>4 — Step 2 — Define the Data Model (Pydantic)</summary>

<br/>

### What is a Pydantic model?

A Pydantic model is a Python class that describes the **shape** of your data.
FastAPI uses it to:
- **Validate** incoming JSON bodies (POST, PUT, PATCH)
- **Serialize** outgoing responses (what the API returns)

---

### Add the imports at the top of `main.py`

Replace the top of your file with:

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional, List
from datetime import datetime
```

---

### Add the three Pydantic models

Add this block **before** your `app = FastAPI(...)` line:

```python
# ---------------------------------------------------------------
# Pydantic models
# ---------------------------------------------------------------

class ContactCreate(BaseModel):
    """Fields required or optional to CREATE a contact (POST and PUT)."""
    name: str
    email: str = ""
    phone: str = ""
    favorite: bool = False
    category: str = "personal"   # "personal" | "work" | "school"


class ContactUpdate(BaseModel):
    """All fields are optional — used for PATCH (partial update)."""
    name: Optional[str] = None
    email: Optional[str] = None
    phone: Optional[str] = None
    favorite: Optional[bool] = None
    category: Optional[str] = None


class Contact(BaseModel):
    """Full contact object — what the API returns."""
    id: int
    name: str
    email: str
    phone: str
    favorite: bool
    category: str
    created_at: str
```

---

### Understanding the three models

| Model | Used for | Fields |
|---|---|---|
| `ContactCreate` | POST body, PUT body | name (required) + email, phone, favorite, category (all optional with defaults) |
| `ContactUpdate` | PATCH body | All fields optional — send only what you want to change |
| `Contact` | API response | All fields including `id` and `created_at` (set by the server) |

---

### Test checkpoint #2

Your file should import and start without errors:

```bash
# Stop and restart the server (Ctrl+C then run again):
uvicorn main:app --reload
```

No error in the terminal = the models are valid.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s5"></a>

<details>
<summary>5 — Step 3 — Create the In-Memory Database</summary>

<br/>

### Add the in-memory database

Add this block **after** your Pydantic models and **before** your routes:

```python
# ---------------------------------------------------------------
# Helper function — get current timestamp
# ---------------------------------------------------------------

def _now() -> str:
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")


# ---------------------------------------------------------------
# In-memory database — pre-loaded with 4 sample contacts
# ---------------------------------------------------------------

contacts_db: dict[int, dict] = {
    1: {
        "id": 1,
        "name": "Alice Martin",
        "email": "alice@example.com",
        "phone": "514-000-0001",
        "favorite": True,
        "category": "work",
        "created_at": "2026-03-01 08:00:00",
    },
    2: {
        "id": 2,
        "name": "Bob Smith",
        "email": "bob@example.com",
        "phone": "514-000-0002",
        "favorite": False,
        "category": "school",
        "created_at": "2026-03-01 09:00:00",
    },
    3: {
        "id": 3,
        "name": "Carol Johnson",
        "email": "carol@example.com",
        "phone": "514-000-0003",
        "favorite": True,
        "category": "personal",
        "created_at": "2026-03-01 10:00:00",
    },
    4: {
        "id": 4,
        "name": "David Lee",
        "email": "david@example.com",
        "phone": "514-000-0004",
        "favorite": False,
        "category": "work",
        "created_at": "2026-03-01 11:00:00",
    },
}

next_id = 5   # auto-increment counter
```

---

### Update the root endpoint to use real data

Now update your `root()` function to return the actual count:

```python
@app.get("/")
def root():
    return {
        "message": "Contact Manager API is running",
        "docs": "http://127.0.0.1:8000/docs",
        "total_contacts": len(contacts_db),
    }
```

---

### Test checkpoint #3

Restart the server and open `http://127.0.0.1:8000`

Expected:
```json
{
  "message": "Contact Manager API is running",
  "docs": "http://127.0.0.1:8000/docs",
  "total_contacts": 4
}
```

`total_contacts` must be `4` now — not `0`.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>6 — Step 4 — Build the GET Endpoints</summary>

<br/>

### GET /contacts — list all contacts

Add this route after the `root()` function:

```python
@app.get("/contacts", response_model=List[Contact], summary="Get all contacts")
def get_contacts(favorite: Optional[bool] = None, category: Optional[str] = None):
    """
    Returns all contacts.
    - Filter by favorite: GET /contacts?favorite=true
    - Filter by category: GET /contacts?category=work
    - Combine:            GET /contacts?favorite=true&category=work
    """
    contacts = list(contacts_db.values())

    if favorite is not None:
        contacts = [c for c in contacts if c["favorite"] == favorite]

    if category is not None:
        contacts = [c for c in contacts if c["category"] == category]

    return contacts
```

---

### Test checkpoint #4a

Open Swagger UI at `http://127.0.0.1:8000/docs`

1. Click **GET /contacts** → Try it out → Execute
2. You should see a list of 4 contacts

3. Now add `favorite = true` → Execute
4. You should see only Alice and Carol (both have `favorite: true`)

5. Now add `category = work` → Execute
6. You should see Alice and David

---

### GET /contacts/{contact_id} — get one contact

Add this route after the previous one:

```python
@app.get("/contacts/{contact_id}", response_model=Contact, summary="Get one contact by ID")
def get_contact(contact_id: int):
    """Returns one contact. Returns 404 if the contact does not exist."""
    if contact_id not in contacts_db:
        raise HTTPException(
            status_code=404,
            detail=f"Contact with ID {contact_id} not found",
        )
    return contacts_db[contact_id]
```

---

### Test checkpoint #4b

In Swagger UI:

1. **GET /contacts/{contact_id}** → Try it out → `contact_id = 1` → Execute
2. Expected: Alice Martin's full details

3. Try `contact_id = 99`
4. Expected: `404` error with message `"Contact with ID 99 not found"`

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s7"></a>

<details>
<summary>7 — Step 5 — Build the POST Endpoint</summary>

<br/>

### POST /contacts — create a new contact

Add this route:

```python
@app.post("/contacts", response_model=Contact, status_code=201, summary="Create a new contact")
def create_contact(contact: ContactCreate):
    """
    Creates a new contact and returns it with its assigned ID.
    Required field: name
    Optional fields: email, phone, favorite, category
    """
    global next_id
    new_contact = {
        "id": next_id,
        **contact.model_dump(),
        "created_at": _now(),
    }
    contacts_db[next_id] = new_contact
    next_id += 1
    return new_contact
```

---

### Understanding this code

```python
global next_id           # allows modifying the variable outside the function
**contact.model_dump()  # unpacks all fields from the Pydantic model into the dict
_now()                   # generates the current timestamp
```

---

### Test checkpoint #5

In Swagger UI → **POST /contacts** → Try it out

**Test A — Full contact:**
```json
{
  "name": "Emma Wilson",
  "email": "emma@example.com",
  "phone": "514-000-0005",
  "favorite": true,
  "category": "school"
}
```
Expected: **201 Created**, new contact with `"id": 5`

**Test B — Only the required field:**
```json
{
  "name": "Frank Garcia"
}
```
Expected: **201 Created**, `id: 6`, with default values:
```json
{
  "email": "",
  "phone": "",
  "favorite": false,
  "category": "personal"
}
```

**Test C — Missing the required field (intentional error):**
```json
{
  "email": "no-name@example.com"
}
```
Expected: **422 Unprocessable Entity**

**After these tests:** run **GET /contacts** — how many contacts are there now?

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s8"></a>

<details>
<summary>8 — Step 6 — Build the PUT Endpoint</summary>

<br/>

### PUT /contacts/{contact_id} — replace a contact entirely

Add this route:

```python
@app.put("/contacts/{contact_id}", response_model=Contact, summary="Replace a contact entirely (PUT)")
def replace_contact(contact_id: int, contact: ContactCreate):
    """
    Replaces the entire contact with the provided data.
    All fields are overwritten. name is required.
    Returns 404 if the contact does not exist.
    created_at is always preserved.
    """
    if contact_id not in contacts_db:
        raise HTTPException(
            status_code=404,
            detail=f"Contact with ID {contact_id} not found",
        )
    updated_contact = {
        "id": contact_id,
        **contact.model_dump(),
        "created_at": contacts_db[contact_id]["created_at"],
    }
    contacts_db[contact_id] = updated_contact
    return updated_contact
```

---

### Key point — PUT vs PATCH

| PUT | PATCH |
|---|---|
| Replaces **everything** | Updates **only what you send** |
| `name` is required | Everything is optional |
| Fields you omit revert to defaults | Fields you omit stay unchanged |

---

### Test checkpoint #6

In Swagger UI → **PUT /contacts/{contact_id}** → Try it out

**Test A — Replace contact 1:**
- `contact_id = 1`
- Body:
```json
{
  "name": "Alice Martin-Tremblay",
  "email": "alice.new@company.com",
  "phone": "514-999-0001",
  "favorite": true,
  "category": "work"
}
```
Expected: **200 OK** — contact 1 is fully replaced

**Test B — Replace contact 2 with minimal data:**
- `contact_id = 2`
- Body:
```json
{
  "name": "Bob Smith"
}
```
Expected: **200 OK** — `email`, `phone`, `phone` reset to defaults (`""`, `""`, `false`, `"personal"`)

**Test C — Non-existent contact:**
- `contact_id = 99`
Expected: **404 Not Found**

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>9 — Step 7 — Build the PATCH Endpoint</summary>

<br/>

### PATCH /contacts/{contact_id} — partial update

Add this route:

```python
@app.patch("/contacts/{contact_id}", response_model=Contact, summary="Partially update a contact (PATCH)")
def update_contact(contact_id: int, update: ContactUpdate):
    """
    Updates only the fields that are provided.
    Fields not included in the request body are left unchanged.
    Returns 404 if the contact does not exist.

    Example — mark as favorite:
    { "favorite": true }
    """
    if contact_id not in contacts_db:
        raise HTTPException(
            status_code=404,
            detail=f"Contact with ID {contact_id} not found",
        )
    for field, value in update.model_dump(exclude_none=True).items():
        contacts_db[contact_id][field] = value
    return contacts_db[contact_id]
```

---

### Understanding `exclude_none=True`

```python
update.model_dump(exclude_none=True)
```

This line converts only the fields that were **actually provided** in the request.

Example: if you send `{"favorite": true}`, the dict becomes `{"favorite": True}`.
The fields `name`, `email`, `phone`, `category` are not in the dict, so they are not overwritten.

---

### Test checkpoint #7

In Swagger UI → **PATCH /contacts/{contact_id}** → Try it out

**Test A — Mark contact 2 as a favorite:**
- `contact_id = 2`
- Body: `{"favorite": true}`
- Expected: 200, contact 2 now has `"favorite": true`, everything else unchanged

**Test B — Change category of contact 4:**
- `contact_id = 4`
- Body: `{"category": "personal"}`
- Expected: 200, only `category` changed

**Test C — Update two fields at once:**
- `contact_id = 3`
- Body:
```json
{
  "phone": "514-111-2222",
  "favorite": false
}
```
- Expected: 200, only `phone` and `favorite` changed

**Test D — Non-existent contact:**
- `contact_id = 99`, body: `{"favorite": true}`
- Expected: 404

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>10 — Step 8 — Build the DELETE Endpoint</summary>

<br/>

### DELETE /contacts/{contact_id} — delete a contact

Add this route:

```python
@app.delete("/contacts/{contact_id}", status_code=204, summary="Delete a contact")
def delete_contact(contact_id: int):
    """
    Deletes a contact permanently.
    Returns 204 No Content on success.
    Returns 404 if the contact does not exist.
    """
    if contact_id not in contacts_db:
        raise HTTPException(
            status_code=404,
            detail=f"Contact with ID {contact_id} not found",
        )
    del contacts_db[contact_id]
```

---

### Key point — 204 No Content

The DELETE endpoint returns **no body** — this is intentional and correct.
`status_code=204` tells FastAPI to return an empty response.

---

### Test checkpoint #8

In Swagger UI → **DELETE /contacts/{contact_id}** → Try it out

**Test A — Delete contact 4:**
- `contact_id = 4`
- Expected: **204 No Content** (response body is empty — this is correct)

**Test B — Verify the deletion:**
- Run **GET /contacts** — David Lee should be gone
- Run **GET /contacts/4** — Expected: **404 Not Found**

**Test C — Try to delete again (contact 4 is already gone):**
- `contact_id = 4`
- Expected: **404 Not Found** with message `"Contact with ID 4 not found"`

**Test D — Delete a non-existent contact:**
- `contact_id = 99`
- Expected: **404 Not Found**

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>11 — Step 9 — Test Everything with Swagger UI</summary>

<br/>

### Full test sequence — run these in order

Your server must be running: `uvicorn main:app --reload`

Open: `http://127.0.0.1:8000/docs`

---

#### Round 1 — Read the existing data

| # | Action | Expected result |
|---|---|---|
| 1 | GET `/` | `total_contacts: 4` |
| 2 | GET `/contacts` | List of 4 contacts |
| 3 | GET `/contacts?favorite=true` | Alice and Carol only |
| 4 | GET `/contacts?category=work` | Alice and David only |
| 5 | GET `/contacts?favorite=false&category=work` | David only |
| 6 | GET `/contacts/1` | Alice Martin's full details |
| 7 | GET `/contacts/99` | **404** — Contact with ID 99 not found |

---

#### Round 2 — Create contacts

| # | Action | Expected result |
|---|---|---|
| 8 | POST `/contacts` with full body | **201** — new contact with `id: 5` |
| 9 | POST `/contacts` with only `name` | **201** — new contact with `id: 6`, default values |
| 10 | POST `/contacts` with no `name` | **422** — validation error |
| 11 | GET `/contacts` | Now shows 6 contacts |

---

#### Round 3 — Update contacts

| # | Action | Expected result |
|---|---|---|
| 12 | PUT `/contacts/1` with full body | **200** — contact 1 fully replaced |
| 13 | PUT `/contacts/99` | **404** — not found |
| 14 | PATCH `/contacts/2` with `{"favorite": true}` | **200** — only favorite changed |
| 15 | PATCH `/contacts/3` with `{"category": "work"}` | **200** — only category changed |
| 16 | PATCH `/contacts/99` | **404** — not found |

---

#### Round 4 — Delete contacts

| # | Action | Expected result |
|---|---|---|
| 17 | DELETE `/contacts/4` | **204** — empty body |
| 18 | GET `/contacts/4` | **404** — contact is gone |
| 19 | DELETE `/contacts/4` again | **404** — already deleted |
| 20 | GET `/contacts` | Shows 5 contacts (6 minus the deleted one) |

---

### All 20 tests passed? Your API is complete.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s12"></a>

<details>
<summary>12 — Step 10 — Write a .http Test File</summary>

<br/>

### Install the REST Client extension (if not done yet)

1. VS Code → Extensions (`Ctrl+Shift+X`)
2. Search: `REST Client` by Huachao Mao → Install

---

### Create `test_contacts.http`

Create this file in your project folder:

```http
@baseUrl = http://127.0.0.1:8000

### Root endpoint
GET {{baseUrl}}/

###

### Get all contacts
GET {{baseUrl}}/contacts

###

### Get only favorite contacts
GET {{baseUrl}}/contacts?favorite=true

###

### Get contacts by category
GET {{baseUrl}}/contacts?category=work

###

### Combine filters
GET {{baseUrl}}/contacts?favorite=false&category=work

###

### Get contact with ID 1
GET {{baseUrl}}/contacts/1

###

### Get contact with ID 99 — expect 404
GET {{baseUrl}}/contacts/99

###

### Create a full contact
POST {{baseUrl}}/contacts
Content-Type: application/json

{
  "name": "Emma Wilson",
  "email": "emma@example.com",
  "phone": "514-000-0005",
  "favorite": true,
  "category": "school"
}

###

### Create a contact with only the name
POST {{baseUrl}}/contacts
Content-Type: application/json

{
  "name": "Frank Garcia"
}

###

### Create a contact without name — expect 422
POST {{baseUrl}}/contacts
Content-Type: application/json

{
  "email": "no-name@example.com"
}

###

### Replace contact 1 entirely (PUT)
PUT {{baseUrl}}/contacts/1
Content-Type: application/json

{
  "name": "Alice Martin-Tremblay",
  "email": "alice.new@company.com",
  "phone": "514-999-0001",
  "favorite": true,
  "category": "work"
}

###

### Replace non-existent contact — expect 404
PUT {{baseUrl}}/contacts/99
Content-Type: application/json

{
  "name": "Nobody"
}

###

### Mark contact 2 as favorite (PATCH)
PATCH {{baseUrl}}/contacts/2
Content-Type: application/json

{
  "favorite": true
}

###

### Change category of contact 3 (PATCH)
PATCH {{baseUrl}}/contacts/3
Content-Type: application/json

{
  "category": "work"
}

###

### Update two fields at once (PATCH)
PATCH {{baseUrl}}/contacts/3
Content-Type: application/json

{
  "phone": "514-111-2222",
  "email": "carol.updated@example.com"
}

###

### PATCH non-existent contact — expect 404
PATCH {{baseUrl}}/contacts/99
Content-Type: application/json

{
  "favorite": true
}

###

### Delete contact 4
DELETE {{baseUrl}}/contacts/4

###

### Try to get deleted contact — expect 404
GET {{baseUrl}}/contacts/4

###

### Try to delete again — expect 404
DELETE {{baseUrl}}/contacts/4

###

### Verify final state
GET {{baseUrl}}/contacts

###
```

---

### How to use this file

1. Open `test_contacts.http` in VS Code
2. Click **"Send Request"** above each block
3. A panel opens on the right with the response
4. Verify the status code and body match what is expected

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s13"></a>

<details>
<summary>13 — Validation Checklist</summary>

<br/>

> Use this checklist to verify your API is fully working before submitting.

---

### File structure

- [ ] `main.py` exists in the project folder
- [ ] `requirements.txt` exists
- [ ] `venv/` folder exists
- [ ] `test_contacts.http` exists

---

### Server

- [ ] `uvicorn main:app --reload` starts without errors
- [ ] `http://127.0.0.1:8000` returns JSON with `total_contacts: 4`
- [ ] `http://127.0.0.1:8000/docs` shows the Swagger UI with all 9 endpoints

---

### Pydantic models

- [ ] `ContactCreate` has `name` (required), and 4 optional fields with defaults
- [ ] `ContactUpdate` has all 5 fields as `Optional` with default `None`
- [ ] `Contact` has all 7 fields including `id` and `created_at`

---

### In-memory database

- [ ] `contacts_db` is a `dict` pre-loaded with 4 contacts (IDs 1 to 4)
- [ ] `next_id` starts at `5`

---

### GET endpoints

- [ ] `GET /` returns `total_contacts` equal to the current number of contacts
- [ ] `GET /contacts` returns all 4 contacts
- [ ] `GET /contacts?favorite=true` returns only Alice and Carol
- [ ] `GET /contacts?category=work` returns only Alice and David
- [ ] `GET /contacts/1` returns Alice's details
- [ ] `GET /contacts/99` returns **404** with a descriptive error message

---

### POST endpoint

- [ ] `POST /contacts` with a full body returns **201** with the new contact including `id: 5`
- [ ] `POST /contacts` with only `name` returns **201** with default values
- [ ] `POST /contacts` without `name` returns **422**

---

### PUT endpoint

- [ ] `PUT /contacts/1` with a full body replaces the contact completely
- [ ] `PUT /contacts/1` keeps `created_at` unchanged
- [ ] `PUT /contacts/99` returns **404**

---

### PATCH endpoint

- [ ] `PATCH /contacts/2` with `{"favorite": true}` changes only `favorite`
- [ ] All other fields of contact 2 stay exactly the same
- [ ] `PATCH /contacts/99` returns **404**

---

### DELETE endpoint

- [ ] `DELETE /contacts/4` returns **204** with an empty body
- [ ] `GET /contacts/4` after deletion returns **404**
- [ ] `DELETE /contacts/4` a second time returns **404**

---

### Test file

- [ ] `test_contacts.http` has at least one block for every endpoint
- [ ] All blocks with `###` separators are correct
- [ ] `Content-Type: application/json` is present for POST, PUT, and PATCH blocks

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s14"></a>

<details>
<summary>14 — Bonus Challenges</summary>

<br/>

> These are optional challenges for students who finish early.
> Each one adds a real feature to your API.

---

### Bonus 1 — Add a search endpoint

Add a new route that lets you search contacts by name:

```text
GET /contacts/search?name=alice
```

It should return all contacts where `name` contains the search string (case-insensitive).

**Hint:**
```python
@app.get("/contacts/search", ...)
def search_contacts(name: str):
    ...
    results = [c for c in contacts_db.values() if name.lower() in c["name"].lower()]
    ...
```

Test it:
- `GET /contacts/search?name=alice` → returns Alice
- `GET /contacts/search?name=son` → returns Carol Johnson and Bob Smith

---

### Bonus 2 — Validate the category field

Right now, you can set `category` to any string, even `"pizza"`.
Add validation to allow only `"personal"`, `"work"`, or `"school"`.

**Option A — Manual validation in the route:**
```python
VALID_CATEGORIES = {"personal", "work", "school"}

if contact.category not in VALID_CATEGORIES:
    raise HTTPException(
        status_code=400,
        detail=f"Invalid category '{contact.category}'. Allowed: personal, work, school"
    )
```

**Option B — Use a Python `Enum` in the Pydantic model (advanced):**
```python
from enum import Enum

class CategoryEnum(str, Enum):
    personal = "personal"
    work = "work"
    school = "school"

class ContactCreate(BaseModel):
    name: str
    category: CategoryEnum = CategoryEnum.personal
    ...
```

Test it:
- `POST /contacts` with `"category": "pizza"` → expect **400** or **422**
- `POST /contacts` with `"category": "work"` → expect **201**

---

### Bonus 3 — Add a summary endpoint

Add a route that returns statistics about the contacts:

```text
GET /contacts/summary
```

Expected response:
```json
{
  "total": 4,
  "favorites": 2,
  "by_category": {
    "personal": 1,
    "work": 2,
    "school": 1
  }
}
```

**Hint:** use a loop and a `dict` to count by category.

---

### Bonus 4 — Write a Python test script

Create a file called `test_script.py` that:

1. Calls `GET /` and prints the total number of contacts
2. Creates a new contact with `name = "Test Contact"`
3. Saves the new contact's `id`
4. Calls `PATCH` to mark it as a favorite
5. Verifies with `GET /contacts/{id}` that `favorite` is now `true`
6. Deletes the contact
7. Verifies with `GET /contacts/{id}` that the response is **404**
8. Prints `"All tests passed!"` if everything worked

---

### Bonus 5 — Add a Streamlit frontend

Look at `frontend.py` from the Task Manager project.
Adapt it to work with your Contact Manager API.

Changes needed:
- Replace all references to `tasks` with `contacts`
- Update the field names (`title` → `name`, `completed` → `favorite`, etc.)
- Update the URL from `/tasks` to `/contacts`
- Change the base URL to `http://127.0.0.1:8000`

Run both servers at the same time:
```bash
# Terminal 1
uvicorn main:app --reload

# Terminal 2
streamlit run frontend.py
```

Open `http://localhost:8501` to see your Contact Manager UI.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

## Complete `main.py` — Reference Solution

<details>
<summary>Click to reveal — only look after you have tried building it yourself</summary>

<br/>

```python
"""
Contact Manager API — FastAPI Lab
----------------------------------
Endpoints:
  GET    /                    — API info
  GET    /contacts            — list all (filter: ?favorite= ?category=)
  GET    /contacts/{id}       — get one contact
  POST   /contacts            — create a new contact
  PUT    /contacts/{id}       — replace entirely
  PATCH  /contacts/{id}       — partial update
  DELETE /contacts/{id}       — delete

Run:
  uvicorn main:app --reload

Docs:
  http://127.0.0.1:8000/docs
"""

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional, List
from datetime import datetime

app = FastAPI(
    title="Contact Manager API",
    description="CRUD API for managing contacts",
    version="1.0.0",
)


# ---------------------------------------------------------------
# Pydantic models
# ---------------------------------------------------------------

class ContactCreate(BaseModel):
    name: str
    email: str = ""
    phone: str = ""
    favorite: bool = False
    category: str = "personal"


class ContactUpdate(BaseModel):
    name: Optional[str] = None
    email: Optional[str] = None
    phone: Optional[str] = None
    favorite: Optional[bool] = None
    category: Optional[str] = None


class Contact(BaseModel):
    id: int
    name: str
    email: str
    phone: str
    favorite: bool
    category: str
    created_at: str


# ---------------------------------------------------------------
# In-memory database
# ---------------------------------------------------------------

def _now() -> str:
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S")


contacts_db: dict[int, dict] = {
    1: {"id": 1, "name": "Alice Martin",   "email": "alice@example.com",  "phone": "514-000-0001", "favorite": True,  "category": "work",     "created_at": "2026-03-01 08:00:00"},
    2: {"id": 2, "name": "Bob Smith",       "email": "bob@example.com",    "phone": "514-000-0002", "favorite": False, "category": "school",   "created_at": "2026-03-01 09:00:00"},
    3: {"id": 3, "name": "Carol Johnson",   "email": "carol@example.com",  "phone": "514-000-0003", "favorite": True,  "category": "personal", "created_at": "2026-03-01 10:00:00"},
    4: {"id": 4, "name": "David Lee",       "email": "david@example.com",  "phone": "514-000-0004", "favorite": False, "category": "work",     "created_at": "2026-03-01 11:00:00"},
}

next_id = 5


# ---------------------------------------------------------------
# Routes
# ---------------------------------------------------------------

@app.get("/", summary="Root — API info")
def root():
    return {
        "message": "Contact Manager API is running",
        "docs": "http://127.0.0.1:8000/docs",
        "total_contacts": len(contacts_db),
    }


@app.get("/contacts", response_model=List[Contact], summary="Get all contacts")
def get_contacts(favorite: Optional[bool] = None, category: Optional[str] = None):
    contacts = list(contacts_db.values())
    if favorite is not None:
        contacts = [c for c in contacts if c["favorite"] == favorite]
    if category is not None:
        contacts = [c for c in contacts if c["category"] == category]
    return contacts


@app.get("/contacts/{contact_id}", response_model=Contact, summary="Get one contact by ID")
def get_contact(contact_id: int):
    if contact_id not in contacts_db:
        raise HTTPException(status_code=404, detail=f"Contact with ID {contact_id} not found")
    return contacts_db[contact_id]


@app.post("/contacts", response_model=Contact, status_code=201, summary="Create a new contact")
def create_contact(contact: ContactCreate):
    global next_id
    new_contact = {"id": next_id, **contact.model_dump(), "created_at": _now()}
    contacts_db[next_id] = new_contact
    next_id += 1
    return new_contact


@app.put("/contacts/{contact_id}", response_model=Contact, summary="Replace a contact entirely")
def replace_contact(contact_id: int, contact: ContactCreate):
    if contact_id not in contacts_db:
        raise HTTPException(status_code=404, detail=f"Contact with ID {contact_id} not found")
    updated = {"id": contact_id, **contact.model_dump(), "created_at": contacts_db[contact_id]["created_at"]}
    contacts_db[contact_id] = updated
    return updated


@app.patch("/contacts/{contact_id}", response_model=Contact, summary="Partially update a contact")
def update_contact(contact_id: int, update: ContactUpdate):
    if contact_id not in contacts_db:
        raise HTTPException(status_code=404, detail=f"Contact with ID {contact_id} not found")
    for field, value in update.model_dump(exclude_none=True).items():
        contacts_db[contact_id][field] = value
    return contacts_db[contact_id]


@app.delete("/contacts/{contact_id}", status_code=204, summary="Delete a contact")
def delete_contact(contact_id: int):
    if contact_id not in contacts_db:
        raise HTTPException(status_code=404, detail=f"Contact with ID {contact_id} not found")
    del contacts_db[contact_id]
```

</details>

---

<p align="center">
  <strong>End of Lab — Contact Manager API</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>
