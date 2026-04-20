> [!TIP]
> **Initialisation du projet**
>
> ```powershell
> cd C:\Users\$env:USERNAME\Downloads
> mkdir application1
> cd application1
> git clone https://github.com/inskillflow/demo_api_1_simple_fastapi_app.git .
> code .
> ```

---

## OPEN A NEW TERMINAL (TERMINAL 1)

```powershell
py -3.12 --version
py -3.12 -m venv myapp1
.\myapp1\Scripts\Activate.ps1
python --version
pip install -r requirements.txt
pip list
```

> [!CAUTION]
> **If PowerShell blocks the activation script, run:**
>
> ```powershell
>  Set-ExecutionPolicy -Scope Process Bypass  
> .\myapp1\Scripts\Activate.ps1 
> ```

---

## OPEN A NEW TERMINAL (TERMINAL 2)

```powershell
cd C:\Users\$env:USERNAME\Downloads\application1
.\myapp1\Scripts\Activate.ps1
```

---

> [!IMPORTANT]
> **From now on:**
>
> - **Terminal 1** = backend terminal
> - **Terminal 2** = frontend terminal

---

## GO TO TERMINAL 1 (BACKEND TERMINAL)

```powershell
uvicorn main:app --reload
```

> [!WARNING]
> **Do not run:**
>
> ```powershell
> python main.py
> ```

---

## GO TO TERMINAL 2 (FRONTEND TERMINAL)

```powershell
streamlit run frontend.py
```

### OR

```powershell
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

