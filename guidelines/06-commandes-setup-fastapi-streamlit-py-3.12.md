> [!TIP]
> **Initialisation du projet**
>
> ```bat
> cd C:\Users\%USERNAME%\Downloads
> mkdir application1
> cd application1
> git clone https://github.com/inskillflow/demo_api_1_simple_fastapi_app.git .
> code .
> ```

---

## OPEN A NEW TERMINAL (TERMINAL 1)

```bat
py -3.12 --version
py -3.12 -m venv myapp1
.\myapp1\Scripts\activate
python --version
pip install -r requirements.txt
pip list
```

---

## OPEN A NEW TERMINAL (TERMINAL 2)

```bat
cd C:\Users\%USERNAME%\Downloads\application1
.\myapp1\Scripts\activate
```

---

> [!IMPORTANT]
> **From now on:**
>
> - **Terminal 1** = backend terminal
> - **Terminal 2** = frontend terminal

---

## GO TO TERMINAL 1 (BACKEND TERMINAL)

```bat
uvicorn main:app --reload
```

> [!WARNING]
> **Do not run:**
>
> ```bat
> python main.py
> ```

---

## GO TO TERMINAL 2 (FRONTEND TERMINAL)

```bat
streamlit run frontend.py
```

### OR

```bat
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
