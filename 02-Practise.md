# Calculator Project with Streamlit and FastAPI

This project consists of creating a simple web calculator using Streamlit for the frontend and FastAPI for the backend. The frontend allows the user to choose an operation (addition, subtraction, multiplication, division) and enter two numbers. The backend performs the requested calculation and returns the result to the frontend.

## Prerequisites

* Python 3.7 or newer
* pip (Python package manager)

## Installation

1. Clone this repository to your local machine:

```bash
git clone https://your-git-repo.git
cd your-repo
```

2. Create a virtual environment and activate it:

```bash
python -m venv myenv
# On Windows
myenv\Scripts\activate
# On macOS and Linux
source myenv/bin/activate
```

3. Install the required dependencies:

```bash
pip install streamlit requests fastapi uvicorn
```

## Starting the Backend

1. Create a file `backend.py` with the following code:

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

@app.get("/add")
def add(a: float, b: float):
    return {"result": a + b}

@app.get("/subtract")
def subtract(a: float, b: float):
    return {"result": a - b}

@app.get("/multiply")
def multiply(a: float, b: float):
    return {"result": a * b}

@app.get("/divide")
def divide(a: float, b: float):
    if b == 0:
        raise HTTPException(status_code=400, detail="Division by zero")
    return {"result": a / b}
```

2. Start the FastAPI server:

```bash
uvicorn backend:app --reload
```

## Starting the Frontend

1. Create a file `frontend.py` with the following code:

```python
import streamlit as st
import requests

# FastAPI API URL
API_URL = "http://127.0.0.1:8000"

st.title("Calculator with Streamlit and FastAPI")

operation = st.selectbox("Choose an operation", ["Addition", "Subtraction", "Multiplication", "Division"])
a = st.number_input("Enter the first number", format="%f")
b = st.number_input("Enter the second number", format="%f")

if st.button("Calculate"):
    if operation == "Addition":
        response = requests.get(f"{API_URL}/add", params={"a": a, "b": b})
    elif operation == "Subtraction":
        response = requests.get(f"{API_URL}/subtract", params={"a": a, "b": b})
    elif operation == "Multiplication":
        response = requests.get(f"{API_URL}/multiply", params={"a": a, "b": b})
    elif operation == "Division":
        response = requests.get(f"{API_URL}/divide", params={"a": a, "b": b})
    
    if response.status_code == 200:
        result = response.json().get("result")
        st.success(f"The result of {operation.lower()} is: {result}")
    else:
        st.error(f"Error: {response.json().get('detail')}")
```

2. Run the Streamlit application:

```bash
streamlit run frontend.py
```

## Usage

1. Access the Streamlit web interface by opening a browser and going to the address shown by Streamlit after launching the application (default: `http://localhost:8501`).
2. Select an operation, enter the numbers, and click on "Calculate" to see the result.

## Project Structure

```text
.
├── backend.py
├── frontend.py
├── README.md
└── myenv/
```

## Notes

* Make sure the FastAPI backend is running before launching the Streamlit frontend.
* If you encounter connection problems, check that the API URL in `frontend.py` is correct.

# Command Summary:

```python
# Clone the repository
git clone https://github.com/hrhouma/fastapi-calculator.git
cd fastapi-calculator

# Create and activate a virtual environment
python -m venv myenv
# On Windows
myenv\Scripts\activate
# On macOS and Linux
source myenv/bin/activate

# Install dependencies
pip install streamlit requests fastapi uvicorn

# Start the FastAPI server
uvicorn backend:app --reload

# Start the Streamlit application
streamlit run frontend.py
```
