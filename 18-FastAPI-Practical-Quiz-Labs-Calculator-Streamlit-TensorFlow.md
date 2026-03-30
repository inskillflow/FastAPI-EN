<a id="top"></a>

# Quiz — Practical Labs: FastAPI, Streamlit & TensorFlow

> **Instructions:** Read each question and choose the best answer from options A, B, C or D. This quiz covers the practical labs from files 01 to 04 of this module.

## Table of Contents

| #  | Question topic                                                                        |
| -- | ------------------------------------------------------------------------------------- |
| 1  | [Role of Uvicorn in a FastAPI project](#q1)                                           |
| 2  | [The `--reload` option at startup](#q2)                                               |
| 3  | [Creating a FastAPI application](#q3)                                                 |
| 4  | [The `@app.get()` decorator](#q4)                                                     |
| 5  | [The `@app.post()` decorator](#q5)                                                    |
| 6  | [Raising an HTTP error with `HTTPException`](#q6)                                     |
| 7  | [Role of `BaseModel` (Pydantic)](#q7)                                                 |
| 8  | [FastAPI automatic documentation URL](#q8)                                            |
| 9  | [Type hints in FastAPI parameters](#q9)                                               |
| 10 | [Query parameters in FastAPI](#q10)                                                   |
| 11 | [Path parameters in FastAPI](#q11)                                                    |
| 12 | [Command to start a FastAPI server](#q12)                                             |
| 13 | [Role of `st.selectbox()` in Streamlit](#q13)                                         |
| 14 | [Role of `st.number_input()` in Streamlit](#q14)                                     |
| 15 | [Role of `st.button()` in Streamlit](#q15)                                            |
| 16 | [Difference between `st.success()` and `st.error()`](#q16)                           |
| 17 | [Sending query params with `requests.get()`](#q17)                                    |
| 18 | [Sending a JSON body with `requests.post()`](#q18)                                    |
| 19 | [Reading the JSON response from a `requests` call](#q19)                              |
| 20 | [Checking the HTTP status code in `requests`](#q20)                                   |
| 21 | [Command to start a Streamlit application](#q21)                                      |
| 22 | [Execution order in the TensorFlow + FastAPI + Streamlit project](#q22)               |
| 23 | [Role of `model.save('model.h5')`](#q23)                                              |
| 24 | [Role of `model.predict(features)`](#q24)                                             |
| 25 | [`sigmoid` activation in neural network output](#q25)                                 |
| 26 | [`relu` activation in hidden layers](#q26)                                            |
| 27 | [Role of `np.array(input).reshape(1, -1)`](#q27)                                      |
| 28 | [Which service runs on port 8000?](#q28)                                              |
| 29 | [Which service runs on port 8501?](#q29)                                              |
| 30 | [What happens if the backend is not started before the frontend?](#q30)               |

---

<a id="q1"></a>

### ❓ Question 1 — What is the role of Uvicorn in a FastAPI project?

- A) Uvicorn is a web framework that replaces FastAPI
- B) Uvicorn is an ASGI server that runs the FastAPI application
- C) Uvicorn is a Python package manager
- D) Uvicorn is a library for sending HTTP requests

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q2"></a>

### ❓ Question 2 — What is the purpose of the `--reload` option in `uvicorn main:app --reload`?

- A) It automatically reloads the page in the browser
- B) It reinstalls dependencies at each startup
- C) It automatically restarts the server on every code change
- D) It enables HTTPS mode

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q3"></a>

### ❓ Question 3 — Which line of code correctly creates a FastAPI application?

- A) `app = FastAPI.create()`
- B) `app = new FastAPI()`
- C) `app = FastAPI()`
- D) `app = fastapi.App()`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q4"></a>

### ❓ Question 4 — What does the decorator `@app.get("/add")` do?

- A) It imports the `/add` function from another module
- B) It registers the function as a handler for HTTP GET requests to `/add`
- C) It immediately executes the `add` function when the server starts
- D) It redirects GET requests to another URL

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q5"></a>

### ❓ Question 5 — Which HTTP method is used to send JSON data to `/predict` in the TensorFlow project?

- A) GET
- B) DELETE
- C) PUT
- D) POST

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q6"></a>

### ❓ Question 6 — In the calculator lab, what happens if the user tries to divide by zero?

- A) FastAPI returns `{"result": null}`
- B) FastAPI crashes and the server stops
- C) FastAPI raises an `HTTPException` with code 400
- D) FastAPI returns `{"result": 0}`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q7"></a>

### ❓ Question 7 — What is the role of Pydantic's `BaseModel` in the TensorFlow backend?

- A) It defines the expected structure and type of data received in the request
- B) It trains the machine learning model
- C) It connects FastAPI to the database
- D) It automatically generates REST endpoints

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q8"></a>

### ❓ Question 8 — At what URL is the Swagger UI documentation of a locally running FastAPI application accessible?

- A) `http://127.0.0.1:8000/swagger`
- B) `http://127.0.0.1:8000/api`
- C) `http://127.0.0.1:8000/docs`
- D) `http://127.0.0.1:8000/help`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q9"></a>

### ❓ Question 9 — In `def add(a: float, b: float)`, what do the `: float` annotations do?

- A) They serve only for documentation; FastAPI ignores them at runtime
- B) FastAPI uses them to automatically validate and convert received parameters
- C) They prevent integers from being passed as parameters
- D) They define default values for `a` and `b`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q10"></a>

### ❓ Question 10 — In the URL `http://127.0.0.1:8000/add?a=10&b=5`, what are `a` and `b` called?

- A) Path parameters
- B) Body parameters
- C) Query parameters
- D) Header parameters

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q11"></a>

### ❓ Question 11 — In the URL `http://127.0.0.1:8000/items/42`, what is `42` called?

- A) A query parameter
- B) A path parameter
- C) A body parameter
- D) A header parameter

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q12"></a>

### ❓ Question 12 — Which command correctly starts a FastAPI server whose application is in `backend.py`?

- A) `python backend.py`
- B) `fastapi run backend`
- C) `uvicorn backend:app --reload`
- D) `streamlit run backend.py`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q13"></a>

### ❓ Question 13 — What does `st.selectbox("Choose an operation", ["Addition", "Subtraction", ...])` do in Streamlit?

- A) Displays a free text field
- B) Creates a dropdown list with the provided options
- C) Creates radio buttons for each option
- D) Displays an alert message with the options

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q14"></a>

### ❓ Question 14 — What does `st.number_input("Enter the first number", format="%f")` return in the Streamlit frontend?

- A) A string entered by the user
- B) A floating point number entered by the user
- C) An HTML `<input>` object
- D) A list of numeric values

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q15"></a>

### ❓ Question 15 — What does the block `if st.button("Calculate"):` do in the Streamlit frontend?

- A) It creates a button and executes the block code on every page reload
- B) It creates a button and executes the block code only when the user clicks it
- C) It automatically submits the form without waiting for a click
- D) It disables the button after the first click

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q16"></a>

### ❓ Question 16 — What is the difference between `st.success(message)` and `st.error(message)`?

- A) `st.success()` displays in red, `st.error()` in green
- B) `st.success()` displays in green (successful operation), `st.error()` displays in red (error)
- C) Both display the same thing, only the position differs
- D) `st.success()` is for logs, `st.error()` is for the interface

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q17"></a>

### ❓ Question 17 — How do you correctly send parameters `a=10` and `b=5` with `requests.get()`?

- A) `requests.get(f"{API_URL}/add?a=10&b=5")`
- B) `requests.get(f"{API_URL}/add", params={"a": a, "b": b})`
- C) `requests.get(f"{API_URL}/add", json={"a": a, "b": b})`
- D) Both A and B are correct

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q18"></a>

### ❓ Question 18 — How do you send a JSON body `{"features": [...]}` with `requests.post()`?

- A) `requests.post(url, params={"features": features})`
- B) `requests.post(url, data={"features": features})`
- C) `requests.post(url, json={"features": features})`
- D) `requests.post(url, body={"features": features})`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q19"></a>

### ❓ Question 19 — How do you extract the `result` value from a FastAPI response with `requests`?

- A) `response.text["result"]`
- B) `response.content.result`
- C) `response.json().get("result")`
- D) `response.data["result"]`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q20"></a>

### ❓ Question 20 — In the Streamlit frontend, why do we check `response.status_code == 200`?

- A) To verify that the server is running
- B) To ensure the request succeeded before displaying the result
- C) To prevent Streamlit from blocking
- D) To limit the number of requests per second

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q21"></a>

### ❓ Question 21 — Which command starts a Streamlit application contained in `frontend.py`?

- A) `python frontend.py`
- B) `uvicorn frontend:app`
- C) `streamlit run frontend.py`
- D) `streamlit start frontend.py`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q22"></a>

### ❓ Question 22 — In the TensorFlow + FastAPI + Streamlit project, what is the correct execution order?

- A) `frontend.py` → `backend.py` → `model.py`
- B) `backend.py` → `model.py` → `frontend.py`
- C) `model.py` → `backend.py` → `frontend.py`
- D) The order does not matter

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q23"></a>

### ❓ Question 23 — What does `model.save('model.h5')` do in `model.py`?

- A) It sends the model to a remote server
- B) It saves the trained model to disk in HDF5 format
- C) It compiles the model before training
- D) It exports the model in JSON format

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q24"></a>

### ❓ Question 24 — What does `model.predict(features)` return in the FastAPI backend?

- A) A list of text labels (e.g. `"positive"`, `"negative"`)
- B) A numpy array containing the predicted probability
- C) A JSON dictionary ready to be returned to the client
- D) The name of the predicted class only

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q25"></a>

### ❓ Question 25 — Why do we use `sigmoid` activation in the output layer of a binary classification model?

- A) To speed up training
- B) To produce a value between 0 and 1 interpretable as a probability
- C) To handle negative values in the features
- D) Because it is required with TensorFlow

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q26"></a>

### ❓ Question 26 — Why do we use `relu` activation in the hidden layers of the neural network?

- A) It normalizes the input data
- B) It introduces non-linearity and avoids the vanishing gradient problem
- C) It limits values between -1 and 1
- D) It is required in Sequential models

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q27"></a>

### ❓ Question 27 — Why do we call `np.array(input.features).reshape(1, -1)` in the backend before `model.predict()`?

- A) To sort the features in ascending order
- B) To convert the list to a 2D numpy array with 1 row (a single sample)
- C) To normalize values between 0 and 1
- D) To verify that the list contains exactly 8 values

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q28"></a>

### ❓ Question 28 — In FastAPI + Streamlit projects, which service listens on port `8000` by default?

- A) Streamlit
- B) The web browser
- C) FastAPI (via Uvicorn)
- D) TensorFlow Serving

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q29"></a>

### ❓ Question 29 — In FastAPI + Streamlit projects, which service listens on port `8501` by default?

- A) FastAPI
- B) Flask
- C) Jupyter Notebook
- D) Streamlit

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q30"></a>

### ❓ Question 30 — If the FastAPI backend is not started and the user clicks "Calculate" in Streamlit, what happens?

- A) Streamlit automatically starts the backend
- B) Streamlit displays `{"result": null}` without an error
- C) `requests.get()` raises a connection refused exception
- D) FastAPI automatically returns a 500 code

<p align="right"><a href="#top">↑ Back to top</a></p>
