> [!TIP]
> **Installation de Python et initialisation du projet**
>
> ```bash
> sudo apt update
> sudo apt install -y python3-full git
> cd ~/Downloads
> mkdir application1
> cd application1
> git clone https://github.com/inskillflow/demo_api_1_simple_fastapi_app.git .
> code .
> ```

> [!CAUTION]
> **On Ubuntu 24.04, `python3` is the default system Python.**
>
> ```bash
> python3 --version
> ```

> [!CAUTION]
> **If you want the command `python`, install:**
>
> ```bash
> sudo apt install -y python-is-python3
> python --version
> ```

---

## OPEN A NEW TERMINAL (TERMINAL 1)

```bash
cd ~/Downloads/application1
python3 --version
python3 -m venv myapp1
source myapp1/bin/activate
python --version
pip install -r requirements.txt
pip list
```

> [!CAUTION]
> **If `python3 -m venv myapp1` does not work, reinstall the full Python package:**
>
> ```bash
> <=============== sudo apt update
> sudo apt install -y python3-full
> ```

> [!CAUTION]
> **If `python3` does not work on your machine, try with `python`:**
>
> ```bash
> python --version
> python -m venv myapp1
> ```

---

## OPEN A NEW TERMINAL (TERMINAL 2)

```bash
cd ~/Downloads/application1
source myapp1/bin/activate
```

---

> [!IMPORTANT]
> **From now on:**
>
> - **Terminal 1** = backend terminal
> - **Terminal 2** = frontend terminal

---

## GO TO TERMINAL 1 (BACKEND TERMINAL)

```bash
uvicorn main:app --reload
```

> [!WARNING]
> **Do not run:**
>
> ```bash
> python main.py
> ```

---

## GO TO TERMINAL 2 (FRONTEND TERMINAL)

```bash
streamlit run frontend.py
```

### OR

```bash
python -m streamlit run frontend.py
```

---

> [!NOTE]
> **Next steps**
>
> - Analyse `backend.py`
> - Analyse `frontend.py`
> - Do Evaluation 1: creating the API
> - Do Evaluation 2: creating the UI for your API
