<a id="top"></a>

# Quiz — Labs pratiques : FastAPI, Streamlit & TensorFlow

> **Consigne :** Lis chaque question et choisis la meilleure réponse parmi les options A, B, C ou D. Ce quiz couvre les labs pratiques des fichiers 01 à 04 de ce module.

## Table of Contents

| #  | Sujet de la question                                                        |
| -- | --------------------------------------------------------------------------- |
| 1  | [Rôle d'Uvicorn dans un projet FastAPI](#q1)                                |
| 2  | [Option `--reload` au démarrage](#q2)                                       |
| 3  | [Créer une application FastAPI](#q3)                                        |
| 4  | [Décorateur `@app.get()`](#q4)                                              |
| 5  | [Décorateur `@app.post()`](#q5)                                             |
| 6  | [Lever une erreur HTTP avec `HTTPException`](#q6)                           |
| 7  | [Rôle de `BaseModel` (Pydantic)](#q7)                                       |
| 8  | [URL de la documentation automatique FastAPI](#q8)                          |
| 9  | [Type hints dans les paramètres FastAPI](#q9)                               |
| 10 | [Paramètres de requête (query params) dans FastAPI](#q10)                   |
| 11 | [Paramètres de chemin (path params) dans FastAPI](#q11)                     |
| 12 | [Commande pour démarrer un serveur FastAPI](#q12)                           |
| 13 | [Rôle de `st.selectbox()` dans Streamlit](#q13)                             |
| 14 | [Rôle de `st.number_input()` dans Streamlit](#q14)                         |
| 15 | [Rôle de `st.button()` dans Streamlit](#q15)                                |
| 16 | [Différence entre `st.success()` et `st.error()`](#q16)                    |
| 17 | [Envoyer des query params avec `requests.get()`](#q17)                      |
| 18 | [Envoyer un corps JSON avec `requests.post()`](#q18)                        |
| 19 | [Lire la réponse JSON d'une requête `requests`](#q19)                       |
| 20 | [Vérifier le code de statut HTTP dans `requests`](#q20)                     |
| 21 | [Commande pour démarrer une application Streamlit](#q21)                    |
| 22 | [Ordre d'exécution dans le projet TensorFlow + FastAPI + Streamlit](#q22)   |
| 23 | [Rôle de `model.save('model.h5')`](#q23)                                    |
| 24 | [Rôle de `model.predict(features)`](#q24)                                   |
| 25 | [Activation `sigmoid` en sortie d'un réseau de neurones](#q25)              |
| 26 | [Activation `relu` dans les couches cachées](#q26)                          |
| 27 | [Rôle de `np.array(input).reshape(1, -1)`](#q27)                            |
| 28 | [Quel service tourne sur le port 8000 ?](#q28)                              |
| 29 | [Quel service tourne sur le port 8501 ?](#q29)                              |
| 30 | [Que se passe-t-il si le backend n'est pas démarré avant le frontend ?](#q30) |

---

<a id="q1"></a>

### ❓ Question 1 — Quel est le rôle d'Uvicorn dans un projet FastAPI ?

- A) Uvicorn est un framework web qui remplace FastAPI
- B) Uvicorn est un serveur ASGI qui exécute l'application FastAPI
- C) Uvicorn est un gestionnaire de paquets Python
- D) Uvicorn est une librairie pour envoyer des requêtes HTTP

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q2"></a>

### ❓ Question 2 — À quoi sert l'option `--reload` dans la commande `uvicorn main:app --reload` ?

- A) Elle recharge automatiquement la page dans le navigateur
- B) Elle réinstalle les dépendances à chaque démarrage
- C) Elle redémarre automatiquement le serveur à chaque modification du code
- D) Elle active le mode HTTPS

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q3"></a>

### ❓ Question 3 — Quelle ligne de code crée correctement une application FastAPI ?

- A) `app = FastAPI.create()`
- B) `app = new FastAPI()`
- C) `app = FastAPI()`
- D) `app = fastapi.App()`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q4"></a>

### ❓ Question 4 — Que fait le décorateur `@app.get("/add")` ?

- A) Il importe la fonction `/add` depuis un autre module
- B) Il enregistre la fonction comme gestionnaire des requêtes HTTP GET vers `/add`
- C) Il exécute immédiatement la fonction `add` au démarrage du serveur
- D) Il redirige les requêtes GET vers une autre URL

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q5"></a>

### ❓ Question 5 — Quelle méthode HTTP utilise-t-on pour envoyer des données JSON à `/predict` dans le projet TensorFlow ?

- A) GET
- B) DELETE
- C) PUT
- D) POST

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q6"></a>

### ❓ Question 6 — Dans le lab calculatrice, que se passe-t-il si l'utilisateur tente de diviser par zéro ?

- A) FastAPI retourne `{"result": null}`
- B) FastAPI plante et le serveur s'arrête
- C) FastAPI lève une `HTTPException` avec le code 400
- D) FastAPI retourne `{"result": 0}`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q7"></a>

### ❓ Question 7 — Quel est le rôle de `BaseModel` de Pydantic dans le backend TensorFlow ?

- A) Il définit la structure et le type attendu des données reçues dans la requête
- B) Il entraîne le modèle de machine learning
- C) Il connecte FastAPI à la base de données
- D) Il génère automatiquement les endpoints REST

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q8"></a>

### ❓ Question 8 — À quelle URL est accessible la documentation Swagger UI d'une application FastAPI tournant en local ?

- A) `http://127.0.0.1:8000/swagger`
- B) `http://127.0.0.1:8000/api`
- C) `http://127.0.0.1:8000/docs`
- D) `http://127.0.0.1:8000/help`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q9"></a>

### ❓ Question 9 — Dans `def add(a: float, b: float)`, à quoi servent les annotations `: float` ?

- A) Elles servent uniquement à la documentation, FastAPI les ignore à l'exécution
- B) FastAPI les utilise pour valider et convertir automatiquement les paramètres reçus
- C) Elles empêchent les entiers d'être passés comme paramètres
- D) Elles définissent des valeurs par défaut pour `a` et `b`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q10"></a>

### ❓ Question 10 — Dans l'URL `http://127.0.0.1:8000/add?a=10&b=5`, comment s'appellent `a` et `b` ?

- A) Des paramètres de chemin (path parameters)
- B) Des paramètres de corps (body parameters)
- C) Des paramètres de requête (query parameters)
- D) Des paramètres d'en-tête (header parameters)

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q11"></a>

### ❓ Question 11 — Dans l'URL `http://127.0.0.1:8000/items/42`, comment s'appelle `42` ?

- A) Un query parameter
- B) Un path parameter
- C) Un body parameter
- D) Un header parameter

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q12"></a>

### ❓ Question 12 — Quelle commande démarre correctement un serveur FastAPI dont l'application se trouve dans `backend.py` ?

- A) `python backend.py`
- B) `fastapi run backend`
- C) `uvicorn backend:app --reload`
- D) `streamlit run backend.py`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q13"></a>

### ❓ Question 13 — Que fait `st.selectbox("Choose an operation", ["Addition", "Subtraction", ...])` dans Streamlit ?

- A) Affiche un champ de texte libre
- B) Crée une liste déroulante avec les options fournies
- C) Crée des boutons radio pour chaque option
- D) Affiche un message d'alerte avec les options

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q14"></a>

### ❓ Question 14 — Que renvoie `st.number_input("Enter the first number", format="%f")` dans le frontend Streamlit ?

- A) Une chaîne de caractères saisie par l'utilisateur
- B) Un nombre flottant saisi par l'utilisateur
- C) Un objet HTML `<input>`
- D) Une liste de valeurs numériques

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q15"></a>

### ❓ Question 15 — Que fait le bloc `if st.button("Calculate"):` dans le frontend Streamlit ?

- A) Il crée un bouton et exécute le code du bloc à chaque rechargement de page
- B) Il crée un bouton et exécute le code du bloc uniquement quand l'utilisateur clique dessus
- C) Il soumet automatiquement le formulaire sans attendre de clic
- D) Il désactive le bouton après le premier clic

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q16"></a>

### ❓ Question 16 — Quelle est la différence entre `st.success(message)` et `st.error(message)` ?

- A) `st.success()` affiche en rouge, `st.error()` en vert
- B) `st.success()` affiche en vert (opération réussie), `st.error()` affiche en rouge (erreur)
- C) Les deux affichent la même chose, seule la position diffère
- D) `st.success()` est pour les logs, `st.error()` est pour l'interface

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q17"></a>

### ❓ Question 17 — Comment envoyer correctement les paramètres `a=10` et `b=5` avec `requests.get()` ?

- A) `requests.get(f"{API_URL}/add?a=10&b=5")`
- B) `requests.get(f"{API_URL}/add", params={"a": a, "b": b})`
- C) `requests.get(f"{API_URL}/add", json={"a": a, "b": b})`
- D) Les deux A et B sont corrects

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q18"></a>

### ❓ Question 18 — Comment envoyer un corps JSON `{"features": [...]}` avec `requests.post()` ?

- A) `requests.post(url, params={"features": features})`
- B) `requests.post(url, data={"features": features})`
- C) `requests.post(url, json={"features": features})`
- D) `requests.post(url, body={"features": features})`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q19"></a>

### ❓ Question 19 — Comment extraire la valeur `result` d'une réponse FastAPI avec `requests` ?

- A) `response.text["result"]`
- B) `response.content.result`
- C) `response.json().get("result")`
- D) `response.data["result"]`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q20"></a>

### ❓ Question 20 — Dans le frontend Streamlit, pourquoi vérifie-t-on `response.status_code == 200` ?

- A) Pour vérifier que le serveur est bien démarré
- B) Pour s'assurer que la requête a réussi avant d'afficher le résultat
- C) Pour éviter que Streamlit se bloque
- D) Pour limiter le nombre de requêtes par seconde

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q21"></a>

### ❓ Question 21 — Quelle commande démarre une application Streamlit contenue dans `frontend.py` ?

- A) `python frontend.py`
- B) `uvicorn frontend:app`
- C) `streamlit run frontend.py`
- D) `streamlit start frontend.py`

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q22"></a>

### ❓ Question 22 — Dans le projet TensorFlow + FastAPI + Streamlit, quel est l'ordre correct d'exécution ?

- A) `frontend.py` → `backend.py` → `model.py`
- B) `backend.py` → `model.py` → `frontend.py`
- C) `model.py` → `backend.py` → `frontend.py`
- D) L'ordre n'a pas d'importance

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q23"></a>

### ❓ Question 23 — Que fait `model.save('model.h5')` dans `model.py` ?

- A) Il envoie le modèle vers un serveur distant
- B) Il sauvegarde le modèle entraîné sur disque au format HDF5
- C) Il compile le modèle avant l'entraînement
- D) Il exporte le modèle en format JSON

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q24"></a>

### ❓ Question 24 — Que retourne `model.predict(features)` dans le backend FastAPI ?

- A) Une liste de labels textuels (ex. `"positive"`, `"negative"`)
- B) Un tableau numpy contenant la probabilité prédite
- C) Un dictionnaire JSON prêt à être retourné au client
- D) Le nom de la classe prédite uniquement

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q25"></a>

### ❓ Question 25 — Pourquoi utilise-t-on l'activation `sigmoid` dans la couche de sortie du modèle de classification binaire ?

- A) Pour accélérer l'entraînement
- B) Pour produire une valeur entre 0 et 1 interprétable comme une probabilité
- C) Pour gérer les valeurs négatives dans les features
- D) Parce que c'est obligatoire avec TensorFlow

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q26"></a>

### ❓ Question 26 — Pourquoi utilise-t-on l'activation `relu` dans les couches cachées du réseau de neurones ?

- A) Elle normalise les données d'entrée
- B) Elle introduit de la non-linéarité et évite le problème du gradient qui disparaît
- C) Elle limite les valeurs entre -1 et 1
- D) Elle est obligatoire dans les modèles Sequential

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q27"></a>

### ❓ Question 27 — Pourquoi appelle-t-on `np.array(input.features).reshape(1, -1)` dans le backend avant `model.predict()` ?

- A) Pour trier les features par ordre croissant
- B) Pour convertir la liste en tableau numpy 2D avec 1 ligne (un seul échantillon)
- C) Pour normaliser les valeurs entre 0 et 1
- D) Pour vérifier que la liste contient exactement 8 valeurs

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q28"></a>

### ❓ Question 28 — Dans les projets FastAPI + Streamlit, quel service écoute sur le port `8000` par défaut ?

- A) Streamlit
- B) Le navigateur web
- C) FastAPI (via Uvicorn)
- D) TensorFlow Serving

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q29"></a>

### ❓ Question 29 — Dans les projets FastAPI + Streamlit, quel service écoute sur le port `8501` par défaut ?

- A) FastAPI
- B) Flask
- C) Jupyter Notebook
- D) Streamlit

<p align="right"><a href="#top">↑ Back to top</a></p>

---

<a id="q30"></a>

### ❓ Question 30 — Si le backend FastAPI n'est pas démarré et que l'utilisateur clique sur "Calculate" dans Streamlit, que se passe-t-il ?

- A) Streamlit démarre automatiquement le backend
- B) Streamlit affiche `{"result": null}` sans erreur
- C) `requests.get()` lève une exception de connexion refusée
- D) FastAPI retourne un code 500 automatiquement

<p align="right"><a href="#top">↑ Back to top</a></p>
