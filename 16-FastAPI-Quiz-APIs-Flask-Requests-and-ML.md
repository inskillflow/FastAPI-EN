<a id="top"></a>

# 📝 Quiz — APIs, FastAPI, Flask, requests & ML

> **How to use this file:** Read each question, choose your answer, then open the `<details>` block below to see the full explanation with examples.

## Table of Contents

| # | Question topic |
|---|---|
| 1 | [What does API stand for?](#q1) |
| 2 | [HTTP method to CREATE a resource](#q2) |
| 3 | [HTTP method to READ data](#q3) |
| 4 | [HTTP method to REPLACE a resource entirely](#q4) |
| 5 | [HTTP method to partially UPDATE a resource](#q5) |
| 6 | [HTTP method to DELETE a resource](#q6) |
| 7 | [Status code: resource not found](#q7) |
| 8 | [Status code: successfully created](#q8) |
| 9 | [Status code: deleted, no content](#q9) |
| 10 | [Status code: authentication required](#q10) |
| 11 | [Difference between 401 and 403](#q11) |
| 12 | [Status code: server-side error category](#q12) |
| 13 | [FastAPI validation failure status code](#q13) |
| 14 | [Number of REST architectural constraints](#q14) |
| 15 | [The Stateless REST constraint](#q15) |
| 16 | [What is a route / endpoint?](#q16) |
| 17 | [Path parameter vs query parameter](#q17) |
| 18 | [Python library for HTTP requests](#q18) |
| 19 | [raise_for_status() method](#q19) |
| 20 | [Sending query params with requests](#q20) |
| 21 | [Sending JSON body with requests](#q21) |
| 22 | [What is a virtual environment?](#q22) |
| 23 | [Activating a venv on Windows](#q23) |
| 24 | [Saving dependencies to a file](#q24) |
| 25 | [The @ decorator symbol](#q25) |
| 26 | [Starting a FastAPI server](#q26) |
| 27 | [FastAPI automatic documentation URL](#q27) |
| 28 | [Flask function to return JSON](#q28) |
| 29 | [Flask function to read JSON body](#q29) |
| 30 | [Project architecture: who displays the prediction?](#q30) |

---

<a id="q1"></a>

### ❓ Question 1 — What does `API` stand for?

- A) Automated Processing Interface
- B) Advanced Protocol Integration
- C) Application Programming Interface
- D) Application Protocol Interface

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: C) Application Programming Interface**

---

#### What is an API?

An **API** (Application Programming Interface) is a contract that defines how two software components can communicate with each other. The word "interface" is the key — an interface hides the internal complexity and exposes only what is needed.

Think of a restaurant menu. You (the client) see a list of dishes (the API). You choose a dish and give your order to the waiter (send a request). The waiter goes to the kitchen (the server) and brings back the food (the response). You never go into the kitchen yourself. You never see how the food is prepared. The menu is the interface.

In the same way, an API exposes what a system can do without revealing how it does it internally.

#### Concrete examples in everyday life

| Situation | Client | API | Server |
|---|---|---|---|
| Weather app | Your phone | OpenWeatherMap API | Weather database |
| Online payment | E-commerce site | Stripe API | Stripe payment server |
| Login with Google | Any website | Google OAuth API | Google servers |
| Your ML project | Streamlit | FastAPI `/predict` | TensorFlow model |

#### Why is it called "Programming Interface"?

Because it is designed to be used by programs, not humans directly. A human uses a graphical interface (buttons, forms). A program uses a programming interface (functions, HTTP endpoints, method calls).

#### Types of APIs

There are several types:

- **REST APIs** — the most common today, use HTTP methods (GET, POST, PUT, DELETE) and JSON
- **SOAP APIs** — older standard, use XML, common in enterprise systems (banking, government)
- **GraphQL APIs** — developed by Facebook, let the client specify exactly what data it needs
- **gRPC** — Google's binary protocol, very fast, used in microservices

This course focuses on **REST APIs** because they are the current standard for web services.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q2"></a>

### ❓ Question 2 — Which HTTP method is used to **create** a new resource?

- A) GET
- B) PUT
- C) CREATE
- D) POST

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) POST**

---

#### Why POST for creation?

**POST** sends data to the server to create a new resource. The key distinction from other methods is that:
- The client does not know the ID of the new resource yet
- The server assigns the ID and returns it in the response
- The response is usually `201 Created` with the new resource in the body

#### Code example with `requests`

```python
import requests

new_user = {
    "name": "Alice",
    "email": "alice@example.com",
    "role": "student"
}

response = requests.post(
    "http://127.0.0.1:8000/users",
    json=new_user
)

print(response.status_code)  # 201 Created
print(response.json())
# {"id": 42, "name": "Alice", "email": "alice@example.com", "role": "student"}
```

Notice: the client did not provide `id: 42`. The server assigned it and returned it.

#### FastAPI endpoint that handles POST

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class UserCreate(BaseModel):
    name: str
    email: str
    role: str = "student"

users = {}
next_id = 1

@app.post("/users", status_code=201)
def create_user(user: UserCreate):
    global next_id
    new_user = {"id": next_id, **user.model_dump()}
    users[next_id] = new_user
    next_id += 1
    return new_user
```

#### Why not PUT or CREATE?

- **PUT** is used to *replace* an existing resource — you must already know the resource's ID (e.g., `PUT /users/42`)
- **CREATE** does not exist as an HTTP method — it is not part of the HTTP specification
- **GET** must never modify server state — it is read-only

#### Summary

| Method | Used when | Client knows ID? |
|---|---|---|
| POST | Creating a new resource | No — server assigns it |
| PUT | Replacing an existing resource | Yes — in the URL |
| PATCH | Updating fields of an existing resource | Yes — in the URL |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q3"></a>

### ❓ Question 3 — Which HTTP method **retrieves data** without side effects?

- A) POST
- B) FETCH
- C) READ
- D) GET

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) GET**

---

#### What is GET?

**GET** is the most common HTTP method. It is used to retrieve data from the server. The critical property of GET is that it must be **safe** and **idempotent**:

- **Safe** — calling GET should never change anything on the server
- **Idempotent** — calling GET 1 time or 100 times gives the same result and has the same effect (no effect at all)

#### Why "no side effects" matters

If you load a web page 5 times (by refreshing), you expect the page to look the same each time. If pressing a refresh button accidentally created 5 orders on an e-commerce site, that would be catastrophic. GET's safety guarantee prevents this.

Browsers rely on this guarantee for features like:
- Caching responses (a GET response can be safely cached)
- Pre-fetching pages in the background
- Showing previously visited pages without a warning

#### Code examples with `requests`

```python
import requests

# Get all users
response = requests.get("http://127.0.0.1:8000/users")
users = response.json()

# Get a specific user by ID
response = requests.get("http://127.0.0.1:8000/users/42")
user = response.json()

# Get with query parameters (filtering)
response = requests.get(
    "http://127.0.0.1:8000/users",
    params={"role": "student", "limit": 10, "page": 2}
)
# URL becomes: /users?role=student&limit=10&page=2
```

#### GET has no body

Unlike POST or PUT, GET requests should not include a body. Any data must be passed through the URL:
- Path parameters: `/users/42` (identifies the resource)
- Query parameters: `/users?role=student` (filters the results)

#### Note on "FETCH" and "READ"

- **FETCH** is a JavaScript function (the browser equivalent of `requests.get()`) — it is not an HTTP method
- **READ** is a concept (the R in CRUD) but not an HTTP method

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q4"></a>

### ❓ Question 4 — Which HTTP method **replaces an entire resource**?

- A) PATCH
- B) POST
- C) UPDATE
- D) PUT

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) PUT**

---

#### PUT replaces everything

**PUT** sends a complete new version of a resource to the server. Whatever you send becomes the new state of the resource. Any field you omit is removed (or reset to its default).

This is the key difference from PATCH — PUT is a full replacement, PATCH is a partial update.

#### Concrete example

Imagine a user resource currently stored on the server:

```json
{
  "id": 42,
  "name": "Alice",
  "email": "alice@example.com",
  "role": "student",
  "grade": 85
}
```

**With PUT — full replacement:**

```python
response = requests.put(
    "http://127.0.0.1:8000/users/42",
    json={
        "name": "Alice Smith",
        "email": "alice.smith@example.com",
        "role": "student",
        "grade": 90
    }
)
```

Result on server:
```json
{"id": 42, "name": "Alice Smith", "email": "alice.smith@example.com", "role": "student", "grade": 90}
```

**What if you forget a field with PUT?**

```python
response = requests.put(
    "http://127.0.0.1:8000/users/42",
    json={
        "name": "Alice Smith"
        # forgot email, role, grade!
    }
)
```

Result on server — those fields are lost:
```json
{"id": 42, "name": "Alice Smith", "email": null, "role": null, "grade": null}
```

This is why PUT requires sending the **complete** resource.

#### FastAPI endpoint for PUT

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class UserFull(BaseModel):
    name: str
    email: str
    role: str
    grade: float

@app.put("/users/{user_id}")
def replace_user(user_id: int, user: UserFull):
    if user_id not in users_db:
        raise HTTPException(status_code=404, detail="User not found")
    # The entire resource is replaced
    users_db[user_id] = {"id": user_id, **user.model_dump()}
    return users_db[user_id]
```

#### PUT vs PATCH — when to use which?

| Scenario | Method |
|---|---|
| Update a user's profile (all fields) | PUT |
| Change only a user's email address | PATCH |
| Reset a configuration to new defaults | PUT |
| Toggle a boolean flag | PATCH |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q5"></a>

### ❓ Question 5 — Which HTTP method **partially updates** only specific fields?

- A) PUT
- B) UPDATE
- C) POST
- D) PATCH

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) PATCH**

---

#### PATCH — surgical updates

**PATCH** sends only the fields you want to change. The server updates those fields and leaves everything else untouched. This is the "surgical" approach — you only touch what you need to change.

#### Concrete example

Current state on the server:
```json
{
  "id": 42,
  "name": "Alice",
  "email": "alice@example.com",
  "role": "student",
  "grade": 85,
  "active": true
}
```

**With PATCH — update only the grade:**

```python
response = requests.patch(
    "http://127.0.0.1:8000/users/42",
    json={"grade": 92}  # only sending what changes
)
```

Result on server — everything else preserved:
```json
{
  "id": 42,
  "name": "Alice",
  "email": "alice@example.com",
  "role": "student",
  "grade": 92,        ← updated
  "active": true
}
```

#### FastAPI endpoint for PATCH

The key is using `Optional` fields so that fields not provided are ignored:

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional

app = FastAPI()

class UserPatch(BaseModel):
    name: Optional[str] = None       # None = not provided, leave unchanged
    email: Optional[str] = None
    role: Optional[str] = None
    grade: Optional[float] = None
    active: Optional[bool] = None

@app.patch("/users/{user_id}")
def update_user(user_id: int, update: UserPatch):
    if user_id not in users_db:
        raise HTTPException(status_code=404, detail="User not found")

    # Only update fields that were actually provided
    for field, value in update.model_dump(exclude_none=True).items():
        users_db[user_id][field] = value

    return users_db[user_id]
```

The `exclude_none=True` is the key — it filters out all fields that were not provided (they have value `None`).

#### Real-world use cases for PATCH

- Toggling a user's `active` status: `{"active": false}`
- Updating only a password: `{"password": "new-hashed-password"}`
- Changing a product's stock: `{"in_stock": false}`
- Updating a single setting in a configuration object

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q6"></a>

### ❓ Question 6 — Which HTTP method **removes** a resource?

- A) REMOVE
- B) GET
- C) POST
- D) DELETE

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) DELETE**

---

#### DELETE removes a resource

**DELETE** tells the server to remove the specified resource. Like PUT and PATCH, it requires the resource ID in the URL path.

```python
import requests

response = requests.delete("http://127.0.0.1:8000/users/42")

print(response.status_code)  # 204 No Content
# Body is empty — nothing to return after deletion
```

#### Expected responses after DELETE

| Status code | Meaning |
|---|---|
| `204 No Content` | Successfully deleted — no body |
| `200 OK` | Successfully deleted — body contains confirmation message |
| `404 Not Found` | The resource to delete does not exist |

#### FastAPI endpoint for DELETE

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

users_db = {1: "Alice", 2: "Bob", 3: "Carol"}

@app.delete("/users/{user_id}", status_code=204)
def delete_user(user_id: int):
    if user_id not in users_db:
        raise HTTPException(status_code=404, detail=f"User {user_id} not found")
    del users_db[user_id]
    # status_code=204 means no body is returned
```

#### Flask endpoint for DELETE

```python
@app.route("/users/<int:user_id>", methods=["DELETE"])
def delete_user(user_id):
    if user_id not in users_db:
        return jsonify({"error": "Not found"}), 404
    del users_db[user_id]
    return "", 204  # empty string = no body
```

#### DELETE is idempotent

Like PUT, DELETE is **idempotent**: deleting the same resource twice has the same effect as deleting it once (the resource is gone). The second DELETE will return 404, but the final state is the same — the resource does not exist.

#### What DELETE does NOT do

DELETE does not delete the entire collection. `DELETE /users/42` deletes user 42. To delete all users, you would need a separate design decision (many APIs do not expose this for safety reasons).

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q7"></a>

### ❓ Question 7 — What HTTP status code means **resource not found**?

- A) 200
- B) 500
- C) 401
- D) 404

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) 404**

---

#### 404 Not Found

**404** is returned when the requested resource does not exist on the server. It is one of the most well-known status codes — you have certainly seen "404 Page Not Found" when browsing the web.

#### When does 404 occur in an API?

```python
# Example: requesting a user that does not exist
response = requests.get("http://127.0.0.1:8000/users/9999")
print(response.status_code)   # 404
print(response.json())        # {"detail": "User 9999 not found"}
```

#### Generating 404 in FastAPI

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()
users_db = {1: {"name": "Alice"}, 2: {"name": "Bob"}}

@app.get("/users/{user_id}")
def get_user(user_id: int):
    if user_id not in users_db:
        raise HTTPException(
            status_code=404,
            detail=f"User with ID {user_id} does not exist"
        )
    return users_db[user_id]
```

#### 404 vs other "not found" situations

| Situation | Correct code | Explanation |
|---|---|---|
| `GET /users/9999` — user doesn't exist | 404 | The specific resource does not exist |
| `GET /nonexistent-route` — route doesn't exist | 404 | The URL path is not defined |
| `DELETE /users/9999` — already deleted | 404 | Cannot delete what doesn't exist |
| `GET /users` — empty list | 200 | The collection exists, it's just empty |

The last row is important: an empty collection returns **200**, not 404. The resource (the users collection) exists, it just contains no items.

#### How to handle 404 on the client side

```python
import requests

response = requests.get("http://127.0.0.1:8000/users/9999")

if response.status_code == 200:
    print("User found:", response.json())
elif response.status_code == 404:
    print("This user does not exist in the database")
elif response.status_code >= 500:
    print("Server error — try again later")
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q8"></a>

### ❓ Question 8 — What HTTP status code means a resource was **successfully created**?

- A) 200
- B) 202
- C) 201
- D) 204

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: C) 201**

---

#### 201 Created

**201 Created** is the correct response after a successful `POST` request that creates a new resource. It signals two things:
1. The request was processed successfully
2. A new resource was created as a result

#### Why not 200?

`200 OK` means "the request succeeded" generically. `201 Created` is more specific — it tells the client that something new was created. This distinction matters for automated tools that parse API responses.

#### The Location header

A well-designed API also includes a `Location` header in a 201 response, pointing to the URL of the newly created resource:

```
HTTP/1.1 201 Created
Location: /users/42
Content-Type: application/json

{"id": 42, "name": "Alice", ...}
```

#### Setting 201 in FastAPI

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class BookCreate(BaseModel):
    title: str
    author: str

books = {}
next_id = 1

@app.post("/books", status_code=201)  # explicitly set 201
def create_book(book: BookCreate):
    global next_id
    new_book = {"id": next_id, **book.model_dump()}
    books[next_id] = new_book
    next_id += 1
    return new_book
```

#### Setting 201 in Flask

```python
@app.route("/books", methods=["POST"])
def create_book():
    data = request.get_json()
    new_book = {"id": next_id, **data}
    return jsonify(new_book), 201  # the second argument is the status code
```

#### The 2xx family at a glance

| Code | Name | Typical use |
|---|---|---|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST (new resource created) |
| 202 | Accepted | Request accepted, processing later (async) |
| 204 | No Content | Successful DELETE (no body to return) |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q9"></a>

### ❓ Question 9 — What status code does a successful **DELETE** typically return?

- A) 200
- B) 201
- C) 404
- D) 204

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) 204**

---

#### 204 No Content

**204 No Content** means the request was processed successfully but there is no body to return. This is the standard response after a DELETE because:
- The resource no longer exists — there is nothing to return
- Sending a body would be unnecessary

#### What 204 looks like

```python
response = requests.delete("http://127.0.0.1:8000/users/42")

print(response.status_code)  # 204
print(len(response.content)) # 0 — empty body
print(response.text)         # "" — empty string
```

#### FastAPI implementation

```python
@app.delete("/users/{user_id}", status_code=204)
def delete_user(user_id: int):
    if user_id not in users_db:
        raise HTTPException(status_code=404, detail="Not found")
    del users_db[user_id]
    # No return statement needed — FastAPI returns empty body with 204
```

#### Flask implementation

```python
@app.route("/users/<int:user_id>", methods=["DELETE"])
def delete_user(user_id):
    if user_id not in users_db:
        return jsonify({"error": "Not found"}), 404
    del users_db[user_id]
    return "", 204  # empty string = no body
```

#### Is 200 also acceptable for DELETE?

Yes — some APIs return `200 OK` with a confirmation message instead of `204`:

```json
HTTP 200
{"message": "User 42 was successfully deleted"}
```

Both `200` and `204` are acceptable for DELETE. The choice is a design decision. `204` is more semantically precise. `200` is useful when you want to confirm details of what was deleted.

#### Other uses of 204

`204` can also appear in other contexts where an action succeeds but there is nothing to return, such as a PATCH request that succeeded silently, or a "mark as read" endpoint.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q10"></a>

### ❓ Question 10 — What status code means **authentication is required**?

- A) 400
- B) 403
- C) 422
- D) 401

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) 401**

---

#### 401 Unauthorized — a misleading name

**401 Unauthorized** is the response when a request reaches a protected endpoint without valid authentication credentials. Despite the name "Unauthorized", it actually means **unauthenticated** — "I don't know who you are."

The HTTP specification requires the server to include a `WWW-Authenticate` header with a 401 response, indicating what authentication scheme is expected.

#### When does 401 occur?

```python
# Trying to access a protected route without a token
response = requests.get("http://127.0.0.1:8000/profile")
print(response.status_code)  # 401
print(response.json())       # {"detail": "Not authenticated"}

# Trying with a wrong/expired token
response = requests.get(
    "http://127.0.0.1:8000/profile",
    headers={"Authorization": "Bearer expired_or_wrong_token"}
)
print(response.status_code)  # 401
print(response.json())       # {"detail": "Invalid or expired token"}
```

#### FastAPI implementation

```python
from fastapi import FastAPI, HTTPException, Header
from typing import Optional

app = FastAPI()

VALID_TOKEN = "secret-token-123"

@app.get("/profile")
def get_profile(authorization: Optional[str] = Header(None)):
    if not authorization:
        raise HTTPException(
            status_code=401,
            detail="Authentication required. Provide a Bearer token."
        )
    token = authorization.replace("Bearer ", "")
    if token != VALID_TOKEN:
        raise HTTPException(
            status_code=401,
            detail="Invalid or expired token"
        )
    return {"user": "Alice", "email": "alice@example.com"}
```

#### Correct usage from the client

```python
# First: login to get a token
login = requests.post(
    "http://127.0.0.1:8000/login",
    json={"username": "alice", "password": "secret"}
)
token = login.json()["access_token"]

# Then: use the token in all protected requests
headers = {"Authorization": f"Bearer {token}"}
profile = requests.get("http://127.0.0.1:8000/profile", headers=headers)
print(profile.json())
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q11"></a>

### ❓ Question 11 — What is the **difference between 401 and 403**?

- A) 401 = resource not found, 403 = server crash
- B) 401 = not authenticated (who are you?), 403 = authenticated but not allowed
- C) 401 = wrong HTTP method, 403 = wrong URL
- D) They mean exactly the same thing

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: B) 401 = not authenticated, 403 = authenticated but not allowed**

---

#### The key distinction: identity vs permission

| Code | Name | Meaning | Analogy |
|---|---|---|---|
| 401 | Unauthorized | "I don't know who you are" | No ID card at all |
| 403 | Forbidden | "I know who you are, but you can't do this" | Has an ID card, but access denied |

#### 401 — I don't know who you are

The server cannot identify the caller. Either no token was provided, or the token is invalid/expired.

```text
Client: GET /admin/dashboard
Server: 401 — No authentication credentials provided.
         Please login first and include a Bearer token.

Client: GET /admin/dashboard
        Authorization: Bearer this-token-is-expired
Server: 401 — Token is expired. Please login again.
```

#### 403 — I know who you are, but NO

The server has successfully verified the caller's identity, but the caller does not have the permission to perform this action.

```text
Client: GET /admin/dashboard
        Authorization: Bearer valid-token-for-alice
Server: 403 — Alice is authenticated, but Alice is a "student" role,
         not an "admin" role. Access to the admin dashboard is denied.
```

#### Code example

```python
from fastapi import FastAPI, HTTPException, Header
from typing import Optional

app = FastAPI()

USERS = {
    "alice-token": {"name": "Alice", "role": "student"},
    "bob-token":   {"name": "Bob",   "role": "admin"}
}

@app.get("/admin/dashboard")
def admin_dashboard(authorization: Optional[str] = Header(None)):
    # Check authentication (401)
    if not authorization or authorization not in USERS:
        raise HTTPException(status_code=401, detail="Authentication required")

    user = USERS[authorization]

    # Check authorization (403)
    if user["role"] != "admin":
        raise HTTPException(
            status_code=403,
            detail=f"Access denied. Required role: admin. Your role: {user['role']}"
        )

    return {"message": f"Welcome to the admin dashboard, {user['name']}!"}
```

Test calls:
```python
# No token → 401
requests.get("http://127.0.0.1:8000/admin/dashboard")

# Alice (student) → 403
requests.get("http://...", headers={"authorization": "alice-token"})

# Bob (admin) → 200
requests.get("http://...", headers={"authorization": "bob-token"})
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q12"></a>

### ❓ Question 12 — Which category of status codes represents **server-side errors**?

- A) 2xx
- B) 3xx
- C) 4xx
- D) 5xx

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) 5xx**

---

#### The five categories explained

| Range | Category | Who is at fault? | Example |
|---|---|---|---|
| 1xx | Informational | Neither | 100 Continue |
| 2xx | Success | Nobody | 200 OK, 201 Created |
| 3xx | Redirection | Neither | 301 Moved Permanently |
| 4xx | Client error | **The client** | 404 Not Found, 422 Validation Error |
| 5xx | Server error | **The server** | 500 Internal Server Error |

#### The critical distinction: 4xx vs 5xx

- A **4xx** error means the client sent a bad request. Fix the request and try again.
- A **5xx** error means the server has a problem. The client did nothing wrong. Retrying might work later.

#### Most common 5xx codes

```
500 Internal Server Error
    → An unhandled exception occurred in the server code
    → Example: division by zero, null reference, unhandled exception

502 Bad Gateway
    → The server received an invalid response from an upstream service
    → Example: your FastAPI server called another API that crashed

503 Service Unavailable
    → The server is temporarily down or overloaded
    → Example: server restarting, database connection pool full

504 Gateway Timeout
    → The upstream service took too long to respond
```

#### What causes a 500 in FastAPI?

```python
@app.get("/buggy")
def buggy_endpoint():
    numbers = [1, 2, 3]
    return numbers[999]  # IndexError! → FastAPI returns 500
```

#### How to handle 5xx on the client side

```python
import requests
import time

def reliable_get(url, retries=3):
    for attempt in range(retries):
        try:
            response = requests.get(url, timeout=5)
            if response.status_code < 500:
                return response  # 2xx, 3xx, 4xx — no retry needed
            print(f"Server error {response.status_code}, retrying... ({attempt+1}/{retries})")
            time.sleep(2 ** attempt)  # exponential backoff: 1s, 2s, 4s
        except requests.exceptions.ConnectionError:
            print(f"Connection failed, retrying... ({attempt+1}/{retries})")
            time.sleep(2 ** attempt)
    return None
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q13"></a>

### ❓ Question 13 — What status code does **FastAPI** return when Pydantic validation fails?

- A) 400
- B) 401
- C) 404
- D) 422

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) 422**

---

#### 422 Unprocessable Entity

**422** means the request was syntactically valid (proper JSON, correct Content-Type) but semantically invalid — the data does not meet the schema requirements. FastAPI uses this code automatically whenever a Pydantic model rejects the incoming data.

#### What triggers a 422 in FastAPI?

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class StudentCreate(BaseModel):
    name: str           # required
    grade: float        # required, must be a number
    active: bool = True # optional, default True
```

Sending this **valid** request → 201:
```json
{"name": "Alice", "grade": 88.5}
```

Sending this → **422** (missing required field):
```json
{"name": "Alice"}
```

Sending this → **422** (wrong type):
```json
{"name": "Alice", "grade": "not-a-number"}
```

#### What does the 422 response look like?

```json
{
  "detail": [
    {
      "type": "missing",
      "loc": ["body", "grade"],
      "msg": "Field required",
      "input": {"name": "Alice"},
      "url": "https://errors.pydantic.dev/2.0/v/missing"
    }
  ]
}
```

The `loc` field tells you exactly where the problem is: in the `body`, field `grade`.

#### Why 422 and not 400?

- **400 Bad Request** means the request itself is malformed (invalid JSON, wrong Content-Type)
- **422 Unprocessable Entity** means the request structure is fine but the content violates business rules or schema constraints

FastAPI follows this distinction precisely:
- Send non-JSON body → **400**
- Send valid JSON that fails Pydantic → **422**

#### How to handle 422 on the client side

```python
response = requests.post(
    "http://127.0.0.1:8000/students",
    json={"name": "Alice"}  # missing grade
)

if response.status_code == 422:
    errors = response.json()["detail"]
    for error in errors:
        field = " → ".join(str(x) for x in error["loc"])
        print(f"Validation error in {field}: {error['msg']}")
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q14"></a>

### ❓ Question 14 — How many **architectural constraints** define REST?

- A) 3
- B) 4
- C) 5
- D) 6

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) 6**

---

#### The 6 REST constraints (Roy Fielding, 2000)

REST was defined by Roy Fielding in his doctoral dissertation in 2000. An API is only truly RESTful if it respects all 6 constraints:

| # | Constraint | Core idea |
|---|---|---|
| 1 | Client-Server | UI and data storage are separated |
| 2 | Stateless | No session state stored on server |
| 3 | Cacheable | Responses declare if they can be cached |
| 4 | Uniform Interface | Consistent URL/method patterns for all resources |
| 5 | Layered System | Client doesn't know about intermediaries |
| 6 | Code on Demand | *(optional)* Server can send executable code |

#### 1. Client-Server

The client (frontend) and server (backend) are completely independent. The frontend only knows the API contract. The backend only knows how to respond to requests. This allows them to evolve independently — you can rebuild the frontend in React without changing the backend, and vice versa.

#### 2. Stateless

Each request contains all information needed to process it. The server does not remember previous requests. This is why JWT tokens are sent with every request.

```text
❌ Stateful (bad):  Server remembers: "User Alice is logged in from session X"
✅ Stateless (good): Every request includes: Authorization: Bearer alice-token
```

#### 3. Cacheable

GET responses can be cached by the client or intermediary proxies. This reduces server load. The `Cache-Control` and `ETag` headers control caching. POST/PUT/DELETE responses are typically not cached.

#### 4. Uniform Interface

All resources follow the same pattern:
- Identified by URLs (`/users/42`)
- Manipulated via standard HTTP methods (GET, POST, PUT, DELETE)
- Self-descriptive messages (Content-Type header)
- Responses in standard format (JSON, XML)

#### 5. Layered System

The client doesn't know if it's talking to the actual server or to a load balancer, CDN, or security gateway in between. Each layer only sees the layer immediately next to it.

#### 6. Code on Demand (optional)

The only optional constraint. Servers can send executable code to the client (like JavaScript). This is how web pages work, but it is rarely seen in modern REST APIs.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q15"></a>

### ❓ Question 15 — Which REST constraint says the **server stores no session between requests**?

- A) Cacheable
- B) Uniform Interface
- C) Layered System
- D) Stateless

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) Stateless**

---

#### What "Stateless" means in practice

**Stateless** means that every HTTP request is completely independent. The server has no memory of previous requests. Each request must carry all the information needed to process it.

#### The session problem — what Stateless solves

In old web applications, the server kept a "session" for each logged-in user:

```text
Old approach (stateful):
  1. User logs in → Server creates Session ID: "abc123"
  2. User visits /dashboard → Server checks: "abc123" = Alice, OK
  3. User visits /orders → Server checks: "abc123" = Alice, OK

Problem: if you have 3 servers behind a load balancer,
         requests must always go to the same server!
         → "Session affinity" or shared session storage needed
```

REST solves this with tokens:

```text
REST approach (stateless):
  1. User logs in → Server returns JWT token
  2. User visits /dashboard
     Authorization: Bearer eyJhbGci... (token contains Alice's identity)
     → Any server can verify the token independently
  3. User visits /orders
     Authorization: Bearer eyJhbGci...
     → Same — any server can handle it
```

#### Practical implications

**Every request must include authentication:**

```python
# ❌ Wrong — assuming the server "remembers" login from previous request
requests.post("http://api.example.com/login", json={"user": "alice"})
requests.get("http://api.example.com/profile")  # won't work! server forgot

# ✅ Correct — token sent with every request
login = requests.post("http://api.example.com/login", json={"user": "alice", "pass": "x"})
token = login.json()["token"]

headers = {"Authorization": f"Bearer {token}"}
requests.get("http://api.example.com/profile", headers=headers)
requests.get("http://api.example.com/orders", headers=headers)
```

#### Benefits of Stateless

- **Scalability** — any server in a cluster can handle any request
- **Reliability** — if a server crashes, requests can be retried on another server
- **Simplicity** — no shared session storage needed
- **Testability** — each request can be tested in isolation

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q16"></a>

### ❓ Question 16 — What is a **route** (or endpoint) in a web API?

- A) The IP address of the server
- B) A specific URL + HTTP method combination that maps to a function
- C) The name of the Python file containing the server code
- D) The JSON response body returned by the server

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: B) A specific URL + HTTP method combination that maps to a function**

---

#### What is a route?

A **route** (also called an **endpoint**) is a combination of:
1. An **HTTP method** (GET, POST, PUT, DELETE, PATCH)
2. A **URL path** (`/students`, `/students/42`, `/predict`)

Together they map to a **function** that handles that specific type of request.

#### The routing table concept

When a web framework starts, it builds a routing table — a lookup table that maps each method + path combination to a function:

```text
Routing table:
  GET    /students        → list_students()
  POST   /students        → create_student()
  GET    /students/{id}   → get_student()
  PUT    /students/{id}   → replace_student()
  PATCH  /students/{id}   → update_student()
  DELETE /students/{id}   → delete_student()
```

When a request arrives, the framework:
1. Reads the method (GET) and path (`/students/42`)
2. Finds the matching route (`GET /students/{id}` → `get_student`)
3. Extracts path parameters (`id = 42`)
4. Calls the function with those parameters
5. Returns the response

#### Routes in FastAPI

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/students")           # Route 1: GET /students
def list_students():
    return [{"id": 1, "name": "Alice"}]

@app.post("/students")          # Route 2: POST /students
def create_student():
    return {"id": 2, "name": "Bob"}

@app.get("/students/{student_id}")   # Route 3: GET /students/{id}
def get_student(student_id: int):
    return {"id": student_id, "name": "Alice"}
```

Note: `GET /students` and `POST /students` are **different routes** even though they share the same URL. The method is part of the route identity.

#### Routes in Flask

```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/students", methods=["GET"])   # Route 1
def list_students():
    return jsonify([{"id": 1, "name": "Alice"}])

@app.route("/students", methods=["POST"])  # Route 2
def create_student():
    return jsonify({"id": 2}), 201

@app.route("/students/<int:student_id>", methods=["GET"])  # Route 3
def get_student(student_id):
    return jsonify({"id": student_id})
```

#### The analogy

Think of routes like phone extensions in an office:
- The office number is the server address (`127.0.0.1:8000`)
- The extension is the path (`/students`)
- The type of call is the method (GET = inquiry, POST = new order)
- The person who picks up is the handler function

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q17"></a>

### ❓ Question 17 — What is the difference between a **path parameter** and a **query parameter**?

- A) Path params are in the body, query params are in the header
- B) Path params identify a specific resource (in the URL), query params filter/sort results (after `?`)
- C) Path params are only used in POST, query params in GET
- D) There is no difference — they are two names for the same thing

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: B) Path params identify a specific resource; query params filter/sort (after `?`)**

---

#### Path parameters — "which specific resource?"

A **path parameter** is embedded directly in the URL path. It identifies a **specific resource** unambiguously.

```text
GET /students/42
              ↑
              Path parameter: student_id = 42
              Identifies EXACTLY student #42

GET /books/978-3-16-148410-0
           ↑
           Path parameter: isbn
```

In FastAPI, path parameters are declared with curly braces:
```python
@app.get("/students/{student_id}")
def get_student(student_id: int):  # FastAPI extracts and converts to int
    return students_db[student_id]
```

In Flask, they use angle brackets:
```python
@app.route("/students/<int:student_id>", methods=["GET"])
def get_student(student_id):
    return jsonify(students_db[student_id])
```

#### Query parameters — "how do you want the data?"

**Query parameters** come after `?` in the URL, separated by `&`. They are used for optional modifiers: filtering, sorting, pagination, searching.

```text
GET /students?active=true&grade_min=80&limit=10&page=2
              ↑
              Query parameters — optional, modify the results
```

In FastAPI, query parameters are function parameters with defaults:
```python
@app.get("/students")
def list_students(
    active: Optional[bool] = None,
    grade_min: Optional[float] = None,
    limit: int = 20,
    page: int = 1
):
    # active, grade_min, limit, page are all query params
    ...
```

In requests (client side):
```python
response = requests.get(
    "http://127.0.0.1:8000/students",
    params={
        "active": True,
        "grade_min": 80,
        "limit": 10,
        "page": 2
    }
)
# URL becomes: /students?active=True&grade_min=80&limit=10&page=2
```

#### Side-by-side comparison

| Aspect | Path parameter | Query parameter |
|---|---|---|
| Location | Inside the path | After `?` in the URL |
| Purpose | Identify a specific resource | Filter, sort, paginate |
| Required? | Always required | Usually optional |
| Example | `/students/42` | `/students?active=true` |
| FastAPI syntax | `{student_id}` in path | Function param with default |
| Flask syntax | `<int:student_id>` in path | `request.args.get()` |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q18"></a>

### ❓ Question 18 — What is the standard **Python library for making HTTP requests**?

- A) http
- B) urllib
- C) httpx
- D) requests

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) requests**

---

#### Why `requests` and not the built-in `urllib`?

Python has a built-in `urllib.request` module, but it is verbose and low-level. The `requests` library wraps it with a clean, human-friendly API.

Compare making the same POST request:

**With `urllib` (verbose, complex):**
```python
import urllib.request
import json

data = json.dumps({"name": "Alice"}).encode("utf-8")
req = urllib.request.Request(
    "http://127.0.0.1:8000/users",
    data=data,
    headers={"Content-Type": "application/json"},
    method="POST"
)
with urllib.request.urlopen(req) as response:
    result = json.loads(response.read().decode("utf-8"))
```

**With `requests` (clean, readable):**
```python
import requests
result = requests.post("http://127.0.0.1:8000/users", json={"name": "Alice"}).json()
```

#### Installation

```bash
pip install requests
```

#### Key features of `requests`

```python
import requests

# GET with params
r = requests.get("http://api.example.com/users", params={"role": "admin"})

# POST with JSON body (sets Content-Type: application/json automatically)
r = requests.post("http://api.example.com/users", json={"name": "Alice"})

# With authentication headers
r = requests.get("http://api.example.com/me",
    headers={"Authorization": "Bearer my-token"})

# With timeout (avoid hanging forever)
r = requests.get("http://api.example.com/data", timeout=5)

# Access the response
print(r.status_code)      # 200
print(r.json())           # parsed JSON body as Python dict
print(r.text)             # body as string
print(r.headers)          # response headers
r.raise_for_status()      # raises exception if 4xx or 5xx
```

#### Note on `httpx`

`httpx` is a modern alternative to `requests` that supports async. For most synchronous use cases, `requests` is simpler. FastAPI's `TestClient` is actually based on `httpx`.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q19"></a>

### ❓ Question 19 — What does `response.raise_for_status()` do?

- A) Prints the status code to the console
- B) Returns the status code as an integer
- C) Raises an `HTTPError` exception if the status is 4xx or 5xx
- D) Retries the request if it failed

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: C) Raises an `HTTPError` exception if the status is 4xx or 5xx**

---

#### The problem it solves

By default, `requests` does not raise an exception for error status codes. A 404 or 500 response is returned as a normal `Response` object. You must check `status_code` manually, or call `raise_for_status()`.

```python
# ❌ Dangerous — silently ignores errors
response = requests.get("http://api.example.com/users/9999")
data = response.json()  # might be {"detail": "Not found"} — no exception raised!

# ✅ Safe — raises exception for any 4xx or 5xx
response = requests.get("http://api.example.com/users/9999")
response.raise_for_status()  # raises HTTPError if status is 4xx or 5xx
data = response.json()  # only reached if status was 2xx
```

#### Full error handling pattern

```python
import requests
from requests.exceptions import HTTPError, ConnectionError, Timeout

def get_user(user_id: int):
    try:
        response = requests.get(
            f"http://127.0.0.1:8000/users/{user_id}",
            timeout=5
        )
        response.raise_for_status()  # raises for 4xx/5xx
        return response.json()

    except HTTPError as e:
        status = e.response.status_code
        if status == 404:
            print(f"User {user_id} not found")
        elif status == 401:
            print("Authentication required")
        elif status == 403:
            print("Access denied")
        elif status == 422:
            print("Validation error:", e.response.json())
        elif status >= 500:
            print(f"Server error ({status}) — try again later")
        return None

    except ConnectionError:
        print("Could not connect to the server — is it running?")
        return None

    except Timeout:
        print("Request timed out after 5 seconds")
        return None
```

#### What HTTPError contains

```python
try:
    response = requests.get("http://127.0.0.1:8000/users/999")
    response.raise_for_status()
except requests.exceptions.HTTPError as e:
    print(e)                        # "404 Client Error: Not Found for url: ..."
    print(e.response.status_code)   # 404
    print(e.response.json())        # {"detail": "User 999 not found"}
    print(e.response.url)           # "http://127.0.0.1:8000/users/999"
```

#### When to use it

Use `raise_for_status()` almost always in production code. It is a safety net that ensures your program does not silently process error responses as if they were successful.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q20"></a>

### ❓ Question 20 — How do you pass **query parameters** in `requests.get()`?

- A) `requests.get(url, body={"key": "value"})`
- B) `requests.get(url, query={"key": "value"})`
- C) `requests.get(url, params={"key": "value"})`
- D) `requests.get(url, args={"key": "value"})`

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: C) `requests.get(url, params={"key": "value"})`**

---

#### The `params` argument

The `params` keyword argument accepts a dictionary of query parameters. The `requests` library automatically encodes them and appends them to the URL after `?`.

```python
import requests

# Simple filter
response = requests.get(
    "http://127.0.0.1:8000/students",
    params={"active": True}
)
# Actual URL sent: http://127.0.0.1:8000/students?active=True

# Multiple params
response = requests.get(
    "http://127.0.0.1:8000/students",
    params={
        "active": True,
        "grade_min": 80,
        "limit": 10,
        "page": 2,
        "sort": "name"
    }
)
# URL: /students?active=True&grade_min=80&limit=10&page=2&sort=name

# Verify the final URL
print(response.url)
```

#### Why use `params=` instead of building the URL manually?

```python
# ❌ Manual — error-prone, not encoded properly
url = f"http://127.0.0.1:8000/students?name=John Smith&active=true"
# Problem: space in "John Smith" is not URL-encoded!

# ✅ Using params= — encoding is handled automatically
response = requests.get(
    "http://127.0.0.1:8000/students",
    params={"name": "John Smith", "active": True}
)
# URL: /students?name=John+Smith&active=True  ← properly encoded
```

#### Receiving `params` in FastAPI

```python
@app.get("/students")
def list_students(
    active: Optional[bool] = None,
    grade_min: Optional[float] = None,
    limit: int = 20,
    page: int = 1,
    sort: str = "id"
):
    students = list(students_db.values())

    if active is not None:
        students = [s for s in students if s["active"] == active]

    if grade_min is not None:
        students = [s for s in students if s["grade"] >= grade_min]

    # Sort
    students.sort(key=lambda s: s[sort])

    # Paginate
    start = (page - 1) * limit
    return students[start:start + limit]
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q21"></a>

### ❓ Question 21 — How do you send **JSON data in the body** with `requests.post()`?

- A) `requests.post(url, data=payload)`
- B) `requests.post(url, body=payload)`
- C) `requests.post(url, json=payload)`
- D) `requests.post(url, payload=payload)`

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: C) `requests.post(url, json=payload)`**

---

#### The `json=` argument

Using `json=payload` does three things automatically:
1. Serializes the Python dict to a JSON string
2. Sets the `Content-Type: application/json` header
3. Encodes the string as UTF-8

```python
import requests

payload = {
    "name": "Alice",
    "grade": 88.5,
    "active": True
}

response = requests.post(
    "http://127.0.0.1:8000/students",
    json=payload
)

print(response.status_code)  # 201
print(response.json())       # {"id": 4, "name": "Alice", "grade": 88.5, "active": true}
```

#### What `data=` does instead

Using `data=payload` sends the data as a form-encoded body (`application/x-www-form-urlencoded`), which is what HTML forms use. This is NOT what most APIs expect.

```python
# ❌ Using data= with a dict
response = requests.post(url, data={"name": "Alice", "grade": 88.5})
# Header: Content-Type: application/x-www-form-urlencoded
# Body: name=Alice&grade=88.5  ← form format, NOT JSON

# ✅ Using json=
response = requests.post(url, json={"name": "Alice", "grade": 88.5})
# Header: Content-Type: application/json
# Body: {"name": "Alice", "grade": 88.5}  ← JSON format
```

#### What if you need to control JSON serialization manually?

```python
import requests
import json

payload = {"name": "Alice", "grade": 88.5}

response = requests.post(
    "http://127.0.0.1:8000/students",
    data=json.dumps(payload),           # manual serialization
    headers={"Content-Type": "application/json"}  # manual header
)
# This is equivalent to using json=payload
```

#### PUT and PATCH use the same `json=` argument

```python
# PUT — replace entire resource
requests.put("http://127.0.0.1:8000/students/1",
    json={"name": "Alice", "grade": 95.0, "active": True})

# PATCH — update specific fields only
requests.patch("http://127.0.0.1:8000/students/1",
    json={"grade": 95.0})
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q22"></a>

### ❓ Question 22 — What is a **virtual environment** in Python?

- A) A cloud server where Python code runs
- B) A Docker container
- C) An isolated Python installation with its own packages, separate from other projects
- D) A VS Code extension for Python

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: C) An isolated Python installation with its own packages, separate from other projects**

---

#### The problem without virtual environments

Imagine you have two projects:
- **Project A** needs `requests==2.28.0` and `fastapi==0.95.0`
- **Project B** needs `requests==2.31.0` and `flask==3.0.0`

If you install everything globally with `pip install`, only one version of `requests` can exist at a time. This creates conflicts.

```text
Global Python (without venv):
  pip install requests==2.28  → requests 2.28 installed
  pip install requests==2.31  → requests 2.31 installed, 2.28 REMOVED
  ❌ Project A now broken
```

#### The solution: virtual environments

```text
Project A:                    Project B:
  venv_a/                       venv_b/
  ├── Python 3.11               ├── Python 3.11
  ├── requests==2.28.0          ├── requests==2.31.0
  └── fastapi==0.95.0           └── flask==3.0.0

✅ Completely isolated — they never interfere
```

#### Creating and using a virtual environment

**Windows:**
```bash
# Create
python -m venv venv

# Activate — prompt changes to (venv)
venv\Scripts\activate

# Install packages (only inside this venv)
pip install fastapi uvicorn requests flask

# See what is installed
pip list

# Save dependencies
pip freeze > requirements.txt

# Deactivate when done
deactivate
```

**macOS / Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
pip install fastapi uvicorn requests flask
pip freeze > requirements.txt
deactivate
```

#### VS Code integration

1. Press `Ctrl+Shift+P`
2. Type `Python: Select Interpreter`
3. Choose the Python inside your `venv` folder
4. New terminals will automatically activate the venv

#### What is `requirements.txt`?

```bash
pip freeze > requirements.txt
```

This creates a file like:
```text
annotated-types==0.7.0
anyio==4.4.0
fastapi==0.115.0
pydantic==2.7.4
requests==2.31.0
uvicorn==0.30.1
```

Anyone can recreate the exact same environment:
```bash
python -m venv venv
venv\Scripts\activate   # Windows
pip install -r requirements.txt
```

This guarantees the project runs identically on every machine.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q23"></a>

### ❓ Question 23 — How do you **activate** a virtual environment on **Windows**?

- A) `source venv/activate`
- B) `activate venv`
- C) `python venv activate`
- D) `venv\Scripts\activate`

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) `venv\Scripts\activate`**

---

#### Platform differences

| Platform | Activation command |
|---|---|
| Windows (PowerShell) | `venv\Scripts\activate` |
| Windows (cmd.exe) | `venv\Scripts\activate.bat` |
| macOS | `source venv/bin/activate` |
| Linux | `source venv/bin/activate` |

#### Why different commands?

On **macOS/Linux**, shell scripts are run with `source` (or `.`). The script is at `venv/bin/activate`.

On **Windows**, the activation script is a `.ps1` (PowerShell) or `.bat` (cmd) file, located in `venv\Scripts\`. Note the **backslash** on Windows vs **forward slash** on macOS/Linux.

#### How to tell if it is activated

After activation, your terminal prompt changes:

```text
Before: PS C:\my-project>
After:  (venv) PS C:\my-project>
```

The `(venv)` prefix tells you the virtual environment is active.

#### Common error on Windows

If you get `running scripts is disabled`, you need to allow PowerShell to run scripts:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Then run `venv\Scripts\activate` again.

#### Complete workflow reminder

```bash
# 1 — Create (only once per project)
python -m venv venv

# 2 — Activate (every time you open a terminal for this project)
venv\Scripts\activate          # Windows
source venv/bin/activate       # macOS/Linux

# 3 — Install dependencies
pip install -r requirements.txt

# 4 — Work on the project
python fastapi_demo.py
uvicorn main:app --reload

# 5 — Deactivate when done
deactivate
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q24"></a>

### ❓ Question 24 — What command **saves all installed packages** with their versions to a file?

- A) `pip list > requirements.txt`
- B) `pip save requirements.txt`
- C) `pip freeze > requirements.txt`
- D) `pip export requirements.txt`

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: C) `pip freeze > requirements.txt`**

---

#### What `pip freeze` does

`pip freeze` prints all currently installed packages with their exact version numbers in a format suitable for a `requirements.txt` file:

```bash
pip freeze
```

Output:
```text
annotated-types==0.7.0
anyio==4.4.0
fastapi==0.115.0
httptools==0.6.1
pydantic==2.7.4
requests==2.31.0
starlette==0.41.2
uvicorn==0.30.1
```

The `>` redirects this output to a file:
```bash
pip freeze > requirements.txt
```

#### Why exact versions matter

Without exact versions, `pip install -r requirements.txt` might install different versions later when new releases come out. This can break your project.

```text
# Bad — no versions
requirements.txt:
  fastapi
  requests

# Good — exact versions
requirements.txt:
  fastapi==0.115.0
  requests==2.31.0
```

#### Difference between `pip list` and `pip freeze`

```bash
pip list    # shows packages in a human-readable table
# Package    Version
# -------    -------
# fastapi    0.115.0
# requests   2.31.0

pip freeze  # shows packages in requirements.txt format
# fastapi==0.115.0
# requests==2.31.0
```

`pip list` is for reading. `pip freeze` is for saving to a file.

#### Restoring from requirements.txt

```bash
# Someone else clones your project:
git clone https://github.com/you/my-project.git
cd my-project

# They create and activate a venv:
python -m venv venv
venv\Scripts\activate

# They install exactly the same packages:
pip install -r requirements.txt
```

This guarantees that the project runs identically on their machine.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q25"></a>

### ❓ Question 25 — What does the `@` symbol represent in `@app.get("/students")`?

- A) It sends a notification email
- B) It is a Python decorator that registers the function as a route handler
- C) It marks the function as asynchronous
- D) It imports the function from another module

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: B) It is a Python decorator that registers the function as a route handler**

---

#### What is a decorator?

A **decorator** is a Python feature that wraps a function to add behavior to it without modifying the function's own code. The `@` syntax is "syntactic sugar" — a cleaner way to write a function wrapping pattern.

#### The equivalence — with and without `@`

```python
# WITH decorator syntax (clean)
@app.get("/students")
def list_students():
    return [{"id": 1, "name": "Alice"}]

# WITHOUT decorator syntax (exactly equivalent)
def list_students():
    return [{"id": 1, "name": "Alice"}]

list_students = app.get("/students")(list_students)
```

Both are identical. `@app.get("/students")` above a function is a shortcut for calling `app.get("/students")` and passing the function to it.

#### What happens when FastAPI reads `@app.get("/students")`?

1. Python evaluates `app.get("/students")` — this returns a **decorator function**
2. Python passes `list_students` to that decorator function
3. FastAPI registers the route: "when a GET request arrives at `/students`, call `list_students`"
4. The original function is returned unchanged

```text
@app.get("/students")     →  FastAPI routing table:
def list_students(): ...     GET /students → list_students()
                             POST /students → (not defined)
                             ...
```

#### Decorators are not specific to FastAPI

Decorators are a core Python feature. You may have seen them in other contexts:

```python
# Python built-ins
class MyClass:
    @classmethod         # decorator — makes this a class method
    def from_string(cls, s): ...

    @staticmethod        # decorator — no self or cls argument
    def helper(): ...

    @property            # decorator — getter accessed as attribute
    def full_name(self): ...

# Testing
import pytest
@pytest.mark.parametrize(...)   # decorator — runs test with multiple inputs
def test_something(...): ...

# Timing
import time
def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time()-start:.3f}s")
        return result
    return wrapper

@timer   # wraps add_numbers with timing logic
def add_numbers(a, b):
    return a + b
```

#### All FastAPI decorators

```python
@app.get("/path")      # GET — read data
@app.post("/path")     # POST — create
@app.put("/path")      # PUT — replace
@app.patch("/path")    # PATCH — partial update
@app.delete("/path")   # DELETE — remove
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q26"></a>

### ❓ Question 26 — What command **starts** a FastAPI server?

- A) `python app.py`
- B) `flask run`
- C) `python manage.py runserver`
- D) `uvicorn main:app --reload`

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) `uvicorn main:app --reload`**

---

#### Breaking down the command

```bash
uvicorn    main    :    app    --reload
   ↑         ↑              ↑       ↑
  ASGI    filename     FastAPI   auto-restart
 server  (main.py)    instance   on change
```

- `uvicorn` — the ASGI server that runs FastAPI apps
- `main` — the Python filename without `.py`
- `:app` — the variable name of the `FastAPI()` instance in that file
- `--reload` — restart the server automatically when you save a file change

#### What is ASGI?

ASGI (Asynchronous Server Gateway Interface) is the successor to WSGI. It supports asynchronous operations, which is what makes FastAPI fast.

- **WSGI** (Flask, Django traditional) — handles one request at a time per process
- **ASGI** (FastAPI, Django Channels) — handles many requests concurrently with async

#### Your `main.py` file

```python
from fastapi import FastAPI

app = FastAPI()  # ← this is what ":app" refers to

@app.get("/")
def root():
    return {"message": "Hello"}
```

Command: `uvicorn main:app --reload`

If your file is named `server.py` and the variable is `my_app`:
```python
my_app = FastAPI()
```
Command: `uvicorn server:my_app --reload`

#### Other useful options

```bash
# Change the port (default is 8000)
uvicorn main:app --reload --port 8080

# Make it accessible from other machines on the network
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# Production mode (no --reload, multiple workers)
uvicorn main:app --workers 4 --host 0.0.0.0 --port 8000
```

#### Comparison with Flask and Django

| Framework | Start command | Server |
|---|---|---|
| FastAPI | `uvicorn main:app --reload` | Uvicorn (ASGI) |
| Flask | `python app.py` or `flask run` | Werkzeug (WSGI) |
| Django | `python manage.py runserver` | Django dev server (WSGI) |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q27"></a>

### ❓ Question 27 — What URL shows **FastAPI's automatic Swagger documentation**?

- A) `/swagger`
- B) `/api`
- C) `/docs`
- D) `/help`

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: C) `/docs`**

---

#### FastAPI's automatic documentation

One of FastAPI's most powerful features is that it automatically generates interactive documentation from your code. No extra configuration needed — as soon as you write an endpoint, it appears in the docs.

FastAPI provides **two documentation interfaces**:

| URL | Tool | Description |
|---|---|---|
| `http://127.0.0.1:8000/docs` | **Swagger UI** | Interactive — test endpoints directly in browser |
| `http://127.0.0.1:8000/redoc` | **ReDoc** | Read-only — cleaner, better for reading |
| `http://127.0.0.1:8000/openapi.json` | **OpenAPI spec** | Raw JSON schema — used by tools to generate clients |

#### What Swagger UI shows

For each endpoint, Swagger UI displays:
- The HTTP method and URL path
- A description (from the docstring or description parameter)
- All path parameters, query parameters
- The request body schema (from Pydantic)
- All possible response codes and their schemas
- A "Try it out" button to test the endpoint live

#### Using "Try it out" in Swagger

1. Open `http://127.0.0.1:8000/docs` in your browser
2. Click on an endpoint (e.g., `POST /students`)
3. Click **"Try it out"**
4. Edit the example JSON body
5. Click **"Execute"**
6. See the real response from your running server

This is the fastest way to test your API without writing any client code.

#### How FastAPI generates the docs

The documentation is generated from:
- The **decorator** (`@app.post("/students")`) → method and path
- The **Pydantic model** (`StudentCreate`) → request body schema
- The **type hints** → parameter types and validation rules
- The **docstring** → endpoint description
- The **status_code** parameter → expected response codes

```python
@app.post("/students", status_code=201,
    summary="Create a new student",
    description="Creates a student and returns the created object with its assigned ID.")
def create_student(student: StudentCreate) -> StudentResponse:
    """
    Create a student with:
    - **name**: full name of the student
    - **grade**: grade between 0 and 100
    """
    ...
```

All of this appears in the Swagger UI automatically.

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q28"></a>

### ❓ Question 28 — What Flask function **converts a Python dict to a proper JSON HTTP response**?

- A) `json.dumps()`
- B) `response.json()`
- C) `to_json()`
- D) `jsonify()`

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) `jsonify()`**

---

#### What `jsonify()` does — three things at once

`jsonify(data)` does all of this automatically:
1. Converts the Python dict/list to a valid **JSON string** (double quotes, proper format)
2. Sets the `Content-Type: application/json` header
3. Creates a proper Flask `Response` object

```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/student")
def get_student():
    data = {"id": 1, "name": "Alice", "grade": 88.5, "active": True}
    return jsonify(data)
    # Response body:  {"id": 1, "name": "Alice", "grade": 88.5, "active": true}
    # Content-Type:   application/json
    # Status code:    200 (default)
```

#### Why NOT `str(data)` or `json.dumps()`

```python
# ❌ str(data) — Python string representation, NOT valid JSON
return str({"name": "Alice"})
# Body: "{'name': 'Alice'}"  ← single quotes = INVALID JSON
# Content-Type: text/html  ← wrong header

# ❌ json.dumps() alone — not a Flask response
return json.dumps({"name": "Alice"})
# Body: '{"name": "Alice"}'  ← valid JSON string
# Content-Type: text/html  ← WRONG header! client may not parse it

# ✅ jsonify() — correct in every way
return jsonify({"name": "Alice"})
# Body: '{"name": "Alice"}'  ← valid JSON string
# Content-Type: application/json  ← correct header
```

#### Setting the status code with `jsonify()`

```python
# Default: 200 OK
return jsonify(data)

# Custom status code
return jsonify(data), 201           # Created
return jsonify({"error": "..."}), 404  # Not Found
return jsonify({"error": "..."}), 400  # Bad Request
return jsonify({"error": "..."}), 422  # Unprocessable
```

#### `jsonify()` can take lists too

```python
@app.route("/students")
def list_students():
    students = [
        {"id": 1, "name": "Alice"},
        {"id": 2, "name": "Bob"}
    ]
    return jsonify(students), 200
```

#### FastAPI vs Flask — returning JSON

In **FastAPI**, you simply return a Python dict and FastAPI converts it to JSON automatically:
```python
@app.get("/student")
def get_student():
    return {"id": 1, "name": "Alice"}  # FastAPI handles JSON serialization
```

In **Flask**, you must explicitly call `jsonify()`:
```python
@app.route("/student")
def get_student():
    return jsonify({"id": 1, "name": "Alice"})  # must call jsonify()
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q29"></a>

### ❓ Question 29 — How do you **read the JSON body** of an incoming POST request in Flask?

- A) `flask.body`
- B) `request.json_data()`
- C) `request.body`
- D) `request.get_json()`

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) `request.get_json()`**

---

#### What `request.get_json()` does

When a client sends a POST request with a JSON body, Flask does not parse it automatically. You must call `request.get_json()` to read the body and convert it from a JSON string into a Python dictionary.

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/students", methods=["POST"])
def create_student():
    data = request.get_json()
    # data is now a Python dict:
    # {"name": "Alice", "grade": 88.5, "active": True}

    print(type(data))   # <class 'dict'>
    print(data["name"]) # "Alice"

    return jsonify({"id": 4, **data}), 201
```

#### When does `request.get_json()` return `None`?

It returns `None` in three cases:

1. **The body is empty** — nothing was sent
2. **The `Content-Type` header is not `application/json`** — Flask does not know it's JSON
3. **The body is not valid JSON** — malformed JSON

```python
@app.route("/students", methods=["POST"])
def create_student():
    data = request.get_json()

    if data is None:
        return jsonify({
            "error": "Request body must be valid JSON with Content-Type: application/json"
        }), 400

    # Manual validation (Flask does not validate automatically — unlike FastAPI)
    required_fields = ["name", "grade"]
    missing = [f for f in required_fields if f not in data]
    if missing:
        return jsonify({"error": f"Missing required fields: {missing}"}), 400

    if not isinstance(data.get("grade"), (int, float)):
        return jsonify({"error": "grade must be a number"}), 400

    # Process the data
    new_student = {"id": len(students_db) + 1, **data}
    return jsonify(new_student), 201
```

#### Force parsing even without Content-Type

Use `force=True` to parse regardless of the Content-Type header:

```python
data = request.get_json(force=True)
```

Use `silent=True` to return `None` instead of raising an exception on parse errors:

```python
data = request.get_json(silent=True)
```

#### Comparison: Flask vs FastAPI

| Task | Flask | FastAPI |
|---|---|---|
| Read JSON body | `request.get_json()` | Pydantic model parameter |
| Validate fields | Manual `if` checks | Automatic via Pydantic |
| Type checking | Manual `isinstance()` | Automatic |
| Return 400/422 | Manual `return jsonify(...), 400` | Automatic |

```python
# FastAPI — automatic, clean
class StudentCreate(BaseModel):
    name: str
    grade: float

@app.post("/students", status_code=201)
def create_student(student: StudentCreate):  # validation is automatic
    return {"id": 1, **student.model_dump()}
```

```python
# Flask — manual, more verbose
@app.route("/students", methods=["POST"])
def create_student():
    data = request.get_json()         # must read manually
    if not data: return ..., 400      # must check manually
    if "name" not in data: return ..., 400  # must validate manually
    return jsonify({...}), 201
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q30"></a>

### ❓ Question 30 — In the TensorFlow + FastAPI + Streamlit project, which component **displays the prediction** to the user?

- A) TensorFlow
- B) FastAPI
- C) Uvicorn
- D) Streamlit

<details>
<summary>✅ Answer & Explanation</summary>

<br/>

**Correct answer: D) Streamlit**

---

#### The three-layer architecture

The project is built with a clear **three-layer architecture**, where each component has one responsibility and does not mix with the others:

```text
Layer 1 — Training (offline, runs once):
  model.py + TensorFlow
  → Generates data, trains the neural network, saves model.h5

Layer 2 — Serving (backend, always running):
  backend.py + FastAPI + Uvicorn
  → Loads model.h5, exposes /predict endpoint, returns JSON

Layer 3 — Interaction (frontend, user-facing):
  frontend.py + Streamlit
  → Displays input fields, sends POST to FastAPI, shows prediction
```

#### The full flow of one prediction

```text
1. User opens http://localhost:8501 (Streamlit)
2. User types 8 numeric values into input fields
3. User clicks "Predict"
4. Streamlit sends: POST http://127.0.0.1:8000/predict
                    Body: {"features": [0.5, 0.8, 0.1, ...]}
5. FastAPI receives, reshapes array, calls model.predict()
6. TensorFlow model returns 0.87
7. FastAPI responds: {"prediction": 0.87}
8. Streamlit receives the response
9. Streamlit displays: "The prediction is: 0.87"
```

#### Why Streamlit and not the others?

| Component | Responsibility | Does it interact with user? |
|---|---|---|
| TensorFlow | Mathematical computation — forward pass, weights | No — it only processes arrays |
| FastAPI | API layer — receives HTTP requests, returns JSON | No — it doesn't have a UI |
| Uvicorn | Web server that runs FastAPI | No — infrastructure only |
| Streamlit | User interface — displays fields and results | **Yes — only this one** |

#### What the Streamlit code looks like

```python
import streamlit as st
import requests

API_URL = "http://127.0.0.1:8000"

st.title("Prediction with TensorFlow and FastAPI")

# Streamlit displays input fields
features = [st.number_input(f"Feature {i+1}", format="%f") for i in range(8)]

# When user clicks the button
if st.button("Predict"):
    response = requests.post(
        f"{API_URL}/predict",
        json={"features": features}
    )
    if response.status_code == 200:
        prediction = response.json()["prediction"]
        # Streamlit displays the result ← this is the answer
        st.success(f"The prediction is: {prediction:.4f}")
        if prediction >= 0.5:
            st.info("Classification: Class 1")
        else:
            st.info("Classification: Class 0")
    else:
        st.error("Prediction error — is the backend running?")
```

#### The analogy

Think of it as a restaurant:
- **TensorFlow** = the chef (prepares the dish)
- **FastAPI** = the kitchen window (passes dishes to the waiter)
- **Streamlit** = the waiter (talks to the customer and delivers the result)

The customer (user) only sees and interacts with the waiter (Streamlit).

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<p align="center">
  <strong>🎉 End of Quiz — 30 Questions Completed</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>
