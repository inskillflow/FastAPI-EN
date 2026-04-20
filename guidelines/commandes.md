## :::Tips

```bat
cd C:\Users\%USERNAME%\Downloads
mkdir application1
cd application1
git clone https://github.com/inskillflow/demo_api_1_simple_fastapi_app.git .
code .
```

---

## OPEN A NEW TERMINAL (TERMINAL 1)

```bat
python3 --version
python3 -m venv myapp1
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

## FROM NOW

* **Terminal 1** = backend terminal
* **Terminal 2** = frontend terminal

---

## Go to terminal 1 (backend terminal)

```bat
uvicorn main:app --reload
```

> Do **not** run:

```bat
python main.py
```

---

## Go to terminal 2 (frontend terminal)

```bat
streamlit run frontend.py
```

### or

```bat
python -m streamlit run frontend.py
```

---

## NEXT

* Analyse `backend.py`
* Analyse `frontend.py`
* Do Evaluation 1: creating the API 
* Do Evaluation 2: creating the UI for your API

