<a id="top"></a>

# Commands Reference — FastAPI, Streamlit, Flask, TensorFlow & Python

> **Complete command reference** for setting up and running Python AI projects across **Windows**, **macOS**, and **Linux Ubuntu 22.04**.
> All commands are copy-paste ready.

---

## Table of Contents

| # | Section |
|---|---|
| 1 | [System Setup — Install Python](#s1) |
| 2 | [Project Folder Setup](#s2) |
| 3 | [Virtual Environment — Create & Activate](#s3) |
| 4 | [Package Management — pip](#s4) |
| 5 | [Install Project Dependencies](#s5) |
| 6 | [Run FastAPI](#s6) |
| 7 | [Run Streamlit](#s7) |
| 8 | [Run Flask](#s8) |
| 9 | [TensorFlow — Train & Save a Model](#s9) |
| 10 | [Git — Version Control](#s10) |
| 11 | [Useful Python Commands](#s11) |
| 12 | [Postman — Quick Reference](#s12) |
| 13 | [Troubleshooting — Common Errors](#s13) |
| 14 | [Full Setup Cheatsheet](#s14) |

---

<a id="s1"></a>

<details>
<summary>1 — System Setup — Install Python</summary>

<br/>

### Windows

Python is not installed by default on Windows. Download the official installer:

```text
https://www.python.org/downloads/
```

> **Important:** During installation, check the box **"Add Python to PATH"** before clicking Install.

Verify the installation:

```powershell
python --version
pip --version
```

If `python` does not work, try `py`:

```powershell
py --version
```

---

### macOS

macOS comes with a system Python (usually 2.x — do not use it).
Install Python 3 via the official installer or Homebrew:

**Option A — Official installer:**
```text
https://www.python.org/downloads/
```

**Option B — Homebrew (recommended for developers):**
```bash
# Install Homebrew first (if not already installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Python
brew install python

# Verify
python3 --version
pip3 --version
```

> On macOS, always use `python3` and `pip3` (not `python` / `pip` which may point to Python 2).

---

### Linux — Ubuntu 22.04

Ubuntu 22.04 comes with Python 3.10. Check the version and install missing tools:

```bash
# Check current Python version
python3 --version

# Update package list
sudo apt update

# Install Python 3, pip, and venv support
sudo apt install -y python3 python3-pip python3-venv

# Optional: install a specific Python version (e.g. 3.11)
sudo apt install -y python3.11 python3.11-venv python3.11-pip

# Verify
python3 --version
pip3 --version
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s2"></a>

<details>
<summary>2 — Project Folder Setup</summary>

<br/>

### Windows

```powershell
# Create the project folder
mkdir my_ai_project

# Navigate into it
cd my_ai_project

# Verify your current location
pwd
```

Or with a full path:

```powershell
mkdir C:\00-projects\my_ai_project
cd C:\00-projects\my_ai_project
```

---

### macOS

```bash
# Create the project folder
mkdir my_ai_project

# Navigate into it
cd my_ai_project

# Verify your current location
pwd
# /Users/yourname/my_ai_project
```

---

### Linux — Ubuntu 22.04

```bash
# Create the project folder
mkdir my_ai_project

# Navigate into it
cd my_ai_project

# Verify your current location
pwd
# /home/yourname/my_ai_project

# Optional: create the folder and navigate in one command
mkdir -p ~/projects/my_ai_project && cd ~/projects/my_ai_project
```

---

### Recommended project structure

Once inside the folder, create the files manually or let your code generate them:

```text
my_ai_project/
│
├── venv/                    ← virtual environment (never commit this to Git)
├── model.py                 ← TensorFlow: train and save the model
├── model.h5                 ← saved trained model
├── backend.py               ← FastAPI server
├── frontend.py              ← Streamlit UI
├── requirements.txt         ← list of dependencies
└── README.md                ← project documentation
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s3"></a>

<details>
<summary>3 — Virtual Environment — Create & Activate</summary>

<br/>

> A virtual environment isolates your project's packages from other projects and from the system Python. **Always create one per project.**

---

### Windows

```powershell
# Create the virtual environment (inside your project folder)
python -m venv venv

# Activate it — your prompt will change to (venv)
venv\Scripts\activate

# Verify: Python now points to venv
where python
# C:\...\my_ai_project\venv\Scripts\python.exe  ← correct

# Deactivate when done
deactivate
```

> If you get an error `running scripts is disabled`, run this once in PowerShell:
> ```powershell
> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
> ```

---

### macOS

```bash
# Create the virtual environment
python3 -m venv venv

# Activate it — your prompt will change to (venv)
source venv/bin/activate

# Verify: Python now points to venv
which python
# /Users/yourname/my_ai_project/venv/bin/python  ← correct

# Deactivate when done
deactivate
```

---

### Linux — Ubuntu 22.04

```bash
# Create the virtual environment
python3 -m venv venv

# Activate it — your prompt will change to (venv)
source venv/bin/activate

# Verify
which python
# /home/yourname/my_ai_project/venv/bin/python  ← correct

# Deactivate when done
deactivate
```

---

### VS Code — Select the virtual environment

1. Open VS Code in the project folder: `code .`
2. Press `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (macOS)
3. Type: `Python: Select Interpreter`
4. Choose the Python inside `venv` (shows the path with `venv`)
5. Every new terminal in VS Code will automatically activate the venv

---

### Summary table

| Action | Windows | macOS / Linux |
|---|---|---|
| Create venv | `python -m venv venv` | `python3 -m venv venv` |
| Activate | `venv\Scripts\activate` | `source venv/bin/activate` |
| Deactivate | `deactivate` | `deactivate` |
| Verify active | `where python` | `which python` |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s4"></a>

<details>
<summary>4 — Package Management — pip</summary>

<br/>

> All `pip` commands below assume the virtual environment is **activated**. You will see `(venv)` in your prompt.

---

### Core pip commands — all platforms

```bash
# Upgrade pip itself (always do this first)
python -m pip install --upgrade pip

# Install a single package
pip install requests

# Install a specific version
pip install fastapi==0.115.0

# Install a package and all its dependencies
pip install fastapi uvicorn pydantic

# Install from a requirements.txt file
pip install -r requirements.txt

# Uninstall a package
pip uninstall requests

# List all installed packages
pip list

# Show details about a specific package
pip show fastapi

# Check for outdated packages
pip list --outdated

# Upgrade a specific package
pip install --upgrade fastapi

# Save all installed packages with exact versions
pip freeze > requirements.txt

# Search for a package (deprecated in pip 21+, use https://pypi.org instead)
# pip search fastapi
```

---

### Understanding requirements.txt

The `requirements.txt` file lets anyone recreate your exact environment:

```bash
# Your requirements.txt might look like this:
# fastapi==0.115.0
# uvicorn==0.30.1
# streamlit==1.35.0
# tensorflow==2.16.1
# pydantic==2.7.4
# numpy==1.26.4
# pandas==2.2.2
# scikit-learn==1.5.0

# Install from it:
pip install -r requirements.txt
```

---

### Windows — specific notes

```powershell
# If you have both Python 2 and Python 3:
py -3 -m pip install fastapi        # forces Python 3 pip

# If pip is not found:
python -m pip install fastapi       # use module syntax instead
```

---

### macOS — specific notes

```bash
# Use pip3 if pip points to Python 2
pip3 install fastapi

# Or use the explicit module syntax
python3 -m pip install fastapi
```

---

### Linux — Ubuntu 22.04 — specific notes

```bash
# If pip is not installed
sudo apt install python3-pip

# Always use python3 and pip3 outside a venv
pip3 install fastapi

# Inside a venv, just pip works fine
pip install fastapi
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s5"></a>

<details>
<summary>5 — Install Project Dependencies</summary>

<br/>

> Make sure your virtual environment is **activated** before running any of these.

---

### Full install — Windows

```powershell
# 1. Create and activate the virtual environment
python -m venv venv
venv\Scripts\activate

# 2. Upgrade pip
python -m pip install --upgrade pip

# 3. Install all dependencies for the AI project
pip install fastapi uvicorn streamlit tensorflow pandas numpy scikit-learn python-multipart requests

# 4. Save the dependencies
pip freeze > requirements.txt

# 5. Verify what was installed
pip list
```

---

### Full install — macOS

```bash
# 1. Create and activate the virtual environment
python3 -m venv venv
source venv/bin/activate

# 2. Upgrade pip
python -m pip install --upgrade pip

# 3. Install all dependencies
pip install fastapi uvicorn streamlit tensorflow pandas numpy scikit-learn python-multipart requests

# 4. Save the dependencies
pip freeze > requirements.txt

# 5. Verify
pip list
```

---

### Full install — Linux Ubuntu 22.04

```bash
# 0. Prepare the system (run once)
sudo apt update
sudo apt install -y python3 python3-pip python3-venv

# 1. Create project folder and navigate into it
mkdir my_ai_project && cd my_ai_project

# 2. Create and activate the virtual environment
python3 -m venv venv
source venv/bin/activate

# 3. Upgrade pip
python -m pip install --upgrade pip

# 4. Install all dependencies
pip install fastapi uvicorn streamlit tensorflow pandas numpy scikit-learn python-multipart requests

# 5. Save the dependencies
pip freeze > requirements.txt

# 6. Verify
pip list
```

---

### What each package does

| Package | Purpose |
|---|---|
| `fastapi` | Build REST APIs quickly with automatic validation and docs |
| `uvicorn` | ASGI server that runs FastAPI applications |
| `streamlit` | Build interactive web UIs for data/ML apps in pure Python |
| `tensorflow` | Deep learning framework — train and use neural networks |
| `pandas` | Data manipulation — DataFrames, CSV, filtering |
| `numpy` | Numerical computation — arrays, matrices, math |
| `scikit-learn` | Traditional ML — preprocessing, metrics, train/test split |
| `python-multipart` | Required by FastAPI to handle file uploads and form data |
| `requests` | Make HTTP calls to APIs from Python |
| `pydantic` | Data validation (installed automatically with FastAPI) |

---

### Flask dependencies (if using Flask instead of FastAPI)

```bash
pip install flask flask-restful flask-cors

# For a more complete Flask project
pip install flask python-dotenv flask-sqlalchemy
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s6"></a>

<details>
<summary>6 — Run FastAPI</summary>

<br/>

### Basic startup — all platforms

```bash
# Standard development command (auto-reload on file save)
uvicorn backend:app --reload

# Explanation:
# uvicorn  = the ASGI server
# backend  = the Python file name (backend.py, without .py)
# :app     = the FastAPI() instance variable inside that file
# --reload = auto-restart when you save changes
```

---

### Different startup options

```bash
# Default port is 8000 — change it if needed
uvicorn backend:app --reload --port 8080

# Allow access from other machines on your network
uvicorn backend:app --reload --host 0.0.0.0 --port 8000

# Specify a different log level
uvicorn backend:app --reload --log-level debug

# Production mode — no reload, multiple workers
uvicorn backend:app --workers 4 --host 0.0.0.0 --port 8000
```

---

### If your file is named differently

```bash
# File: main.py, variable: api
uvicorn main:api --reload

# File: server.py, variable: application
uvicorn server:application --reload

# File: app.py, variable: app
uvicorn app:app --reload
```

---

### Where to access FastAPI after starting

Once the server is running, open in your browser:

| URL | What you see |
|---|---|
| `http://127.0.0.1:8000` | Root endpoint (if defined) |
| `http://127.0.0.1:8000/docs` | Swagger UI — interactive documentation |
| `http://127.0.0.1:8000/redoc` | ReDoc — clean read-only docs |
| `http://127.0.0.1:8000/openapi.json` | Raw OpenAPI JSON schema |

---

### Minimal FastAPI file (backend.py)

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def root():
    return {"message": "FastAPI is running"}

@app.get("/health")
def health():
    return {"status": "ok"}
```

Start with: `uvicorn backend:app --reload`

---

### Stop the server

```bash
# In the terminal where FastAPI is running:
Ctrl + C
```

---

### Windows — specific notes

```powershell
# If uvicorn is not found after pip install:
python -m uvicorn backend:app --reload

# Run in a specific terminal window without blocking VS Code
# Open a new terminal: Ctrl + `  (backtick)
# Then run uvicorn in that terminal
```

---

### Linux Ubuntu 22.04 — run in background

```bash
# Run FastAPI in the background (keeps running after closing terminal)
nohup uvicorn backend:app --host 0.0.0.0 --port 8000 &

# See the process ID
echo $!

# Stop it later
kill <PID>

# Or find and kill it
pkill -f uvicorn
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s7"></a>

<details>
<summary>7 — Run Streamlit</summary>

<br/>

### Basic startup — all platforms

```bash
# Run the Streamlit app
streamlit run frontend.py

# Streamlit automatically opens a browser at http://localhost:8501
```

---

### Different startup options

```bash
# Change the port (default is 8501)
streamlit run frontend.py --server.port 8502

# Disable the browser from opening automatically
streamlit run frontend.py --server.headless true

# Allow access from other machines
streamlit run frontend.py --server.address 0.0.0.0

# Use a specific theme
streamlit run frontend.py --theme.base dark
```

---

### Where to access Streamlit after starting

| URL | Description |
|---|---|
| `http://localhost:8501` | Your Streamlit app |
| `http://localhost:8501/_stcore/health` | Health check |

---

### Minimal Streamlit file (frontend.py)

```python
import streamlit as st
import requests

st.set_page_config(page_title="AI Prediction App", layout="centered")
st.title("AI Prediction App")

st.write("Enter 8 feature values below:")
features = [st.number_input(f"Feature {i+1}", value=0.0) for i in range(8)]

if st.button("Predict"):
    with st.spinner("Calling the model..."):
        response = requests.post(
            "http://127.0.0.1:8000/predict",
            json={"features": features}
        )
    if response.status_code == 200:
        prediction = response.json()["prediction"]
        st.success(f"Prediction: {prediction:.4f}")
    else:
        st.error(f"Error: {response.status_code}")
```

---

### Useful Streamlit CLI commands

```bash
# Check Streamlit version
streamlit version

# See all configuration options
streamlit config show

# Clear Streamlit cache
streamlit cache clear

# Get help
streamlit --help
```

---

### Run FastAPI and Streamlit at the same time

You need **two terminals** open at the same time:

**Terminal 1 — FastAPI (backend):**
```bash
source venv/bin/activate      # or venv\Scripts\activate on Windows
uvicorn backend:app --reload
```

**Terminal 2 — Streamlit (frontend):**
```bash
source venv/bin/activate      # or venv\Scripts\activate on Windows
streamlit run frontend.py
```

> In VS Code: use `Ctrl + `` (backtick) to open a second terminal, or click the `+` button in the terminal panel.

---

### Stop Streamlit

```bash
# In the terminal where Streamlit is running:
Ctrl + C
```

---

### Linux Ubuntu 22.04 — run Streamlit in background

```bash
# Run in background
nohup streamlit run frontend.py --server.headless true &

# Stop it
pkill -f streamlit
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s8"></a>

<details>
<summary>8 — Run Flask</summary>

<br/>

### Method 1 — Run the Python file directly (all platforms)

```bash
# If your Flask file sets app.run() at the bottom
python app.py
```

Minimal Flask file with `app.run()`:

```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/")
def root():
    return jsonify({"message": "Flask is running"})

if __name__ == "__main__":
    app.run(debug=True, port=5000)
```

```bash
python app.py
# Running on http://127.0.0.1:5000
```

---

### Method 2 — Flask CLI (all platforms)

```bash
# Tell Flask which file is your app
# Windows
set FLASK_APP=app.py
set FLASK_ENV=development
flask run

# macOS / Linux
export FLASK_APP=app.py
export FLASK_ENV=development
flask run
```

Or in one line:

```bash
# macOS / Linux
FLASK_APP=app.py FLASK_ENV=development flask run

# Windows PowerShell
$env:FLASK_APP = "app.py"; $env:FLASK_ENV = "development"; flask run
```

---

### Flask startup options

```bash
# Change port (default is 5000)
flask run --port 5001

# Allow external access
flask run --host 0.0.0.0 --port 5000

# With debug mode (auto-reload on save)
flask run --debug
```

---

### Where to access Flask after starting

| URL | What you see |
|---|---|
| `http://127.0.0.1:5000` | Root endpoint |
| `http://127.0.0.1:5000/students` | Example route |

> Note: Flask does **not** have automatic documentation like FastAPI's `/docs`. Use Postman or write your own docs.

---

### Stop Flask

```bash
Ctrl + C
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s9"></a>

<details>
<summary>9 — TensorFlow — Train & Save a Model</summary>

<br/>

### Install TensorFlow

```bash
# Standard install (includes Keras)
pip install tensorflow

# For Apple Silicon (M1/M2/M3 Macs):
pip install tensorflow-macos tensorflow-metal

# Verify the installation
python -c "import tensorflow as tf; print(tf.__version__)"
```

---

### Train and save a model (model.py)

```python
import numpy as np
import tensorflow as tf

# 1 — Generate dummy data
np.random.seed(42)
X = np.random.rand(1000, 8)
y = (X[:, 0] + X[:, 3] > 1.0).astype(float)

# 2 — Build the model
model = tf.keras.Sequential([
    tf.keras.layers.Dense(64, activation="relu", input_shape=(8,)),
    tf.keras.layers.Dense(32, activation="relu"),
    tf.keras.layers.Dense(1,  activation="sigmoid")
])

# 3 — Compile
model.compile(
    optimizer="adam",
    loss="binary_crossentropy",
    metrics=["accuracy"]
)

# 4 — Train
model.fit(X, y, epochs=20, batch_size=32, validation_split=0.2)

# 5 — Save
model.save("model.h5")   # legacy .h5 format
# model.save("model")    # SavedModel format (folder)
# model.save("model.keras")  # new Keras format

print("Model saved successfully.")
```

Run:
```bash
python model.py
```

---

### Load and use the model (inside FastAPI backend.py)

```python
import tensorflow as tf
import numpy as np
from fastapi import FastAPI
from pydantic import BaseModel
from typing import List

app = FastAPI()

# Load once at startup
model = tf.keras.models.load_model("model.h5")

class InputData(BaseModel):
    features: List[float]

@app.post("/predict")
def predict(data: InputData):
    arr = np.array(data.features).reshape(1, -1)
    prediction = float(model.predict(arr)[0][0])
    return {"prediction": prediction}
```

---

### Useful TensorFlow commands

```bash
# Check GPU availability
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"

# Check TensorFlow and Keras versions
python -c "import tensorflow as tf; print(tf.__version__, tf.keras.__version__)"

# Disable GPU (force CPU)
CUDA_VISIBLE_DEVICES=-1 python model.py   # Linux/macOS
$env:CUDA_VISIBLE_DEVICES="-1"            # Windows PowerShell
python model.py
```

---

### Model formats — comparison

| Format | Command | Use case |
|---|---|---|
| `.h5` | `model.save("model.h5")` | Legacy, widely supported |
| `SavedModel` | `model.save("model_folder")` | TensorFlow production serving |
| `.keras` | `model.save("model.keras")` | New Keras 3 format |
| TFLite | `tf.lite.TFLiteConverter...` | Mobile and edge devices |
| ONNX | `tf2onnx` library | Cross-framework deployment |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s10"></a>

<details>
<summary>10 — Git — Version Control</summary>

<br/>

### Install Git

```bash
# Windows — download from:
# https://git-scm.com/download/win

# macOS
brew install git

# Linux Ubuntu 22.04
sudo apt install -y git
```

Verify:
```bash
git --version
```

---

### Initial Git configuration (one time per machine)

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Verify
git config --list
```

---

### Start a new Git repository

```bash
# Inside your project folder
git init

# Create a .gitignore to exclude venv and other unwanted files
echo "venv/" >> .gitignore
echo "__pycache__/" >> .gitignore
echo "*.pyc" >> .gitignore
echo "model.h5" >> .gitignore
echo ".env" >> .gitignore
echo ".DS_Store" >> .gitignore
```

---

### Daily Git workflow

```bash
# Check the status of your files
git status

# Stage a specific file
git add backend.py

# Stage all changed files
git add .

# Commit with a message
git commit -m "Add FastAPI predict endpoint"

# See commit history
git log --oneline
```

---

### Work with GitHub

```bash
# Clone an existing repository
git clone https://github.com/username/my_ai_project.git
cd my_ai_project

# Link your local repo to a remote GitHub repo
git remote add origin https://github.com/username/my_ai_project.git

# Push your commits to GitHub
git push -u origin main

# Pull the latest changes from GitHub
git pull

# Check remote URL
git remote -v
```

---

### Branches

```bash
# Create and switch to a new branch
git checkout -b feature/add-login

# List all branches
git branch

# Switch to an existing branch
git checkout main

# Merge a branch into main
git checkout main
git merge feature/add-login

# Delete a branch after merging
git branch -d feature/add-login
```

---

### .gitignore recommended for Python AI projects

```text
# Virtual environment
venv/
env/
.venv/

# Python compiled files
__pycache__/
*.pyc
*.pyo
*.pyd

# Trained model files (optional — large files)
*.h5
*.keras
model/

# Environment variables
.env
.env.local

# VS Code settings (optional)
.vscode/

# macOS files
.DS_Store

# Jupyter notebooks checkpoints
.ipynb_checkpoints/
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s11"></a>

<details>
<summary>11 — Useful Python Commands</summary>

<br/>

### Check Python environment

```bash
# Python version
python --version          # Windows (venv active)
python3 --version         # macOS/Linux

# Python location
where python              # Windows
which python              # macOS/Linux

# List all Python versions available
py --list                 # Windows (Python Launcher)

# Run a Python script
python script.py

# Run a Python module
python -m module_name

# Open Python interactive shell
python
>>> print("hello")
>>> exit()
```

---

### Virtual environment inspection

```bash
# Verify venv is active (should show venv path)
where python              # Windows
which python              # macOS/Linux

# List installed packages
pip list

# Show package details
pip show fastapi

# Find outdated packages
pip list --outdated

# Show dependencies of a package
pip show --verbose fastapi
```

---

### Running scripts

```bash
# Run a Python script
python backend.py
python model.py
python test_api.py

# Pass arguments to a script
python script.py --host 0.0.0.0 --port 8000

# Run a module (preferred for packages)
python -m uvicorn backend:app --reload
python -m pytest tests/
python -m pip install fastapi
```

---

### Jupyter Notebook (optional)

```bash
# Install
pip install notebook jupyterlab

# Start classic Notebook
jupyter notebook

# Start JupyterLab (modern interface)
jupyter lab

# Start on a specific port
jupyter lab --port 8888

# Start without opening browser
jupyter lab --no-browser
```

---

### Testing with pytest

```bash
# Install
pip install pytest

# Run all tests in the project
pytest

# Run a specific test file
pytest test_backend.py

# Verbose output
pytest -v

# Run tests matching a keyword
pytest -k "test_predict"

# FastAPI testing with TestClient
pip install httpx  # required for FastAPI TestClient
```

---

### Environment variables

```bash
# Windows — set temporary
set API_KEY=mysecretkey
set DATABASE_URL=postgresql://...

# Windows — set permanent (PowerShell)
[System.Environment]::SetEnvironmentVariable("API_KEY", "mysecretkey", "User")

# macOS / Linux — set temporary
export API_KEY=mysecretkey
export DATABASE_URL=postgresql://...

# macOS / Linux — set permanent (add to ~/.bashrc or ~/.zshrc)
echo 'export API_KEY=mysecretkey' >> ~/.bashrc
source ~/.bashrc
```

Using a `.env` file (recommended):

```bash
# Install python-dotenv
pip install python-dotenv

# .env file
API_KEY=mysecretkey
DATABASE_URL=postgresql://localhost/mydb
DEBUG=True
```

```python
# In your Python file
from dotenv import load_dotenv
import os

load_dotenv()   # reads .env file

api_key = os.getenv("API_KEY")
debug = os.getenv("DEBUG", "False") == "True"
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s12"></a>

<details>
<summary>12 — Postman — Quick Reference</summary>

<br/>

### Install Postman

```text
Download: https://www.postman.com/downloads/
Available for: Windows, macOS, Linux
```

---

### Testing a GET request

```text
1. Open Postman
2. Click "New" → "HTTP Request"
3. Select method: GET
4. Enter URL: http://127.0.0.1:8000/students
5. Click "Send"
6. See the response in the bottom panel
```

---

### Testing a POST request with JSON body

```text
1. Select method: POST
2. Enter URL: http://127.0.0.1:8000/students
3. Click "Body" tab
4. Select "raw" → change dropdown to "JSON"
5. Enter:
   {
     "name": "Alice",
     "grade": 88.5,
     "active": true
   }
6. Click "Send"
7. Expected status: 201 Created
```

---

### Testing with authentication (Bearer Token)

```text
1. Click "Authorization" tab
2. Select type: "Bearer Token"
3. Enter your token in the field
4. Click "Send"
   → Postman adds "Authorization: Bearer <token>" header automatically
```

---

### Testing a DELETE request

```text
1. Select method: DELETE
2. Enter URL: http://127.0.0.1:8000/students/42
3. Click "Send"
4. Expected status: 204 No Content
```

---

### Testing a PATCH request

```text
1. Select method: PATCH
2. Enter URL: http://127.0.0.1:8000/students/42
3. Body → raw → JSON:
   {
     "grade": 95.0
   }
4. Click "Send"
5. Expected status: 200 OK
```

---

### Using query parameters in Postman

```text
1. Select method: GET
2. Enter base URL: http://127.0.0.1:8000/students
3. Click "Params" tab
4. Add rows:
   KEY       VALUE
   active    true
   limit     10
   page      2
5. Postman builds the URL: /students?active=true&limit=10&page=2
6. Click "Send"
```

---

### Postman vs curl — same request, different tools

**Postman (visual):**
```text
POST http://127.0.0.1:8000/predict
Body (JSON): {"features": [0.5, 0.2, 0.8, 0.1, 0.9, 0.3, 0.7, 0.4]}
```

**curl (command line — all platforms):**
```bash
curl -X POST http://127.0.0.1:8000/predict \
     -H "Content-Type: application/json" \
     -d '{"features": [0.5, 0.2, 0.8, 0.1, 0.9, 0.3, 0.7, 0.4]}'
```

**Python requests (code):**
```python
import requests
response = requests.post(
    "http://127.0.0.1:8000/predict",
    json={"features": [0.5, 0.2, 0.8, 0.1, 0.9, 0.3, 0.7, 0.4]}
)
print(response.json())
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s13"></a>

<details>
<summary>13 — Troubleshooting — Common Errors</summary>

<br/>

### `python` is not recognized (Windows)

```text
Error: 'python' is not recognized as an internal or external command
```

**Fix:**
```powershell
# Option 1: use 'py' instead
py --version

# Option 2: reinstall Python and check "Add Python to PATH"
# https://www.python.org/downloads/

# Option 3: add manually to PATH
# Settings → System → Advanced → Environment Variables
# Add: C:\Users\<you>\AppData\Local\Programs\Python\Python311\
```

---

### `pip` is not recognized

```bash
# Use the module syntax instead
python -m pip install fastapi

# Or on Linux
python3 -m pip install fastapi
```

---

### `running scripts is disabled` (Windows PowerShell)

```text
Error: venv\Scripts\activate.ps1 cannot be loaded because running scripts is disabled
```

**Fix (one-time, in PowerShell as administrator):**
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

### `Address already in use` — port conflict

```text
Error: [Errno 98] Address already in use
       error: [WinError 10048] Only one usage of each socket address...
```

**Fix:**
```bash
# Use a different port
uvicorn backend:app --reload --port 8001
streamlit run frontend.py --server.port 8502

# Or kill the process using the port
# Windows
netstat -ano | findstr :8000
taskkill /PID <PID> /F

# macOS / Linux
lsof -ti:8000 | xargs kill -9
```

---

### Module not found

```text
ModuleNotFoundError: No module named 'fastapi'
```

**Fix:**
```bash
# Make sure your virtual environment is activated
# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

# Then install the missing package
pip install fastapi

# Verify
pip list | grep fastapi     # macOS/Linux
pip list | findstr fastapi  # Windows
```

---

### TensorFlow import error

```text
ImportError: Could not find cuda drivers on your machine
```

**Fix (if you don't have a GPU):**
```python
# At the top of your Python file
import os
os.environ["CUDA_VISIBLE_DEVICES"] = "-1"  # forces CPU mode
import tensorflow as tf
```

---

### FastAPI returns 422 Unprocessable Entity

```text
{"detail": [{"type": "missing", "loc": ["body", "features"], "msg": "Field required"}]}
```

**Fix:** The JSON body you sent is missing a required field or has the wrong type.

```python
# Check what your Pydantic model expects
class InputData(BaseModel):
    features: List[float]  # ← must send {"features": [0.1, 0.2, ...]}

# Wrong:  {"data": [0.1, 0.2]}
# Correct: {"features": [0.1, 0.2]}
```

---

### Streamlit cannot connect to FastAPI

```text
requests.exceptions.ConnectionError: HTTPConnectionPool(host='127.0.0.1', port=8000)
```

**Fix:** FastAPI server is not running. Start it first:
```bash
# In a separate terminal
uvicorn backend:app --reload
```

---

### `model.h5` file not found

```text
OSError: Unable to open file (unable to open file: name = 'model.h5', errno = 2)
```

**Fix:** Train and save the model first:
```bash
python model.py
# This generates model.h5 in the current folder
```

Then start FastAPI from the **same folder**:
```bash
cd my_ai_project
uvicorn backend:app --reload
```

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="s14"></a>

<details>
<summary>14 — Full Setup Cheatsheet</summary>

<br/>

### Windows — complete setup from scratch

```powershell
# 1. Create project folder and navigate into it
mkdir my_ai_project
cd my_ai_project

# 2. Create the virtual environment
python -m venv venv

# 3. Activate the virtual environment
venv\Scripts\activate
# Prompt changes to: (venv) PS C:\...\my_ai_project>

# 4. Upgrade pip
python -m pip install --upgrade pip

# 5. Install all dependencies
pip install fastapi uvicorn streamlit tensorflow pandas numpy scikit-learn python-multipart requests

# 6. Save dependencies
pip freeze > requirements.txt

# 7. Train the model (only once)
python model.py

# 8. Start FastAPI (in this terminal)
uvicorn backend:app --reload
# API running at: http://127.0.0.1:8000
# Docs at:        http://127.0.0.1:8000/docs

# 9. Open a second terminal (Ctrl+`) and activate venv again
venv\Scripts\activate

# 10. Start Streamlit
streamlit run frontend.py
# UI at: http://localhost:8501
```

---

### macOS — complete setup from scratch

```bash
# 1. Create project folder and navigate into it
mkdir my_ai_project
cd my_ai_project

# 2. Create the virtual environment
python3 -m venv venv

# 3. Activate the virtual environment
source venv/bin/activate
# Prompt changes to: (venv) yourname@mac my_ai_project %

# 4. Upgrade pip
python -m pip install --upgrade pip

# 5. Install all dependencies
pip install fastapi uvicorn streamlit tensorflow pandas numpy scikit-learn python-multipart requests

# 6. Save dependencies
pip freeze > requirements.txt

# 7. Train the model (only once)
python model.py

# 8. Start FastAPI (in this terminal)
uvicorn backend:app --reload

# 9. Open a second terminal and activate venv
source venv/bin/activate

# 10. Start Streamlit
streamlit run frontend.py
```

---

### Linux — Ubuntu 22.04 — complete setup from scratch

```bash
# 0. System preparation (only once per machine)
sudo apt update
sudo apt install -y python3 python3-pip python3-venv git curl

# 1. Create project folder and navigate into it
mkdir my_ai_project
cd my_ai_project

# 2. Create the virtual environment
python3 -m venv venv

# 3. Activate the virtual environment
source venv/bin/activate
# Prompt changes to: (venv) yourname@ubuntu:~/my_ai_project$

# 4. Upgrade pip
python -m pip install --upgrade pip

# 5. Install all dependencies
pip install fastapi uvicorn streamlit tensorflow pandas numpy scikit-learn python-multipart requests

# 6. Save dependencies
pip freeze > requirements.txt

# 7. Train the model (only once)
python model.py

# 8. Start FastAPI (in this terminal)
uvicorn backend:app --reload

# 9. Open a second terminal and activate venv
source venv/bin/activate

# 10. Start Streamlit
streamlit run frontend.py
```

---

### Quick reference — all platforms side by side

| Action | Windows | macOS | Linux Ubuntu |
|---|---|---|---|
| Check Python | `python --version` | `python3 --version` | `python3 --version` |
| Create venv | `python -m venv venv` | `python3 -m venv venv` | `python3 -m venv venv` |
| Activate venv | `venv\Scripts\activate` | `source venv/bin/activate` | `source venv/bin/activate` |
| Deactivate venv | `deactivate` | `deactivate` | `deactivate` |
| Install package | `pip install X` | `pip install X` | `pip install X` |
| Save deps | `pip freeze > requirements.txt` | `pip freeze > requirements.txt` | `pip freeze > requirements.txt` |
| Install deps | `pip install -r requirements.txt` | `pip install -r requirements.txt` | `pip install -r requirements.txt` |
| Start FastAPI | `uvicorn backend:app --reload` | `uvicorn backend:app --reload` | `uvicorn backend:app --reload` |
| Start Streamlit | `streamlit run frontend.py` | `streamlit run frontend.py` | `streamlit run frontend.py` |
| Start Flask | `python app.py` | `python app.py` | `python app.py` |
| Train model | `python model.py` | `python model.py` | `python model.py` |
| Kill port 8000 | `netstat -ano \| findstr :8000` then `taskkill /PID X /F` | `lsof -ti:8000 \| xargs kill` | `fuser -k 8000/tcp` |
| Open docs | Browser: `http://127.0.0.1:8000/docs` | same | same |

</details>

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<p align="center">
  <strong>End of Commands Reference</strong><br/>
  <a href="#top">↑ Back to the top</a>
</p>
