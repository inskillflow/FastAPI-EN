# Vue d’ensemble des fichiers de commandes et de guides

## Objectif

Ce dossier contient plusieurs fichiers Markdown qui servent soit à :

- exécuter les commandes selon le terminal ou le système utilisé ;
- expliquer les différences entre plusieurs commandes Python ;
- comparer Ubuntu 22.04 et Ubuntu 24.04 pour Python et `venv`.

---

## Tableau récapitulatif

| Fichier | Plateforme / contexte | Ce que fait le fichier | Commande Python principale | Public visé |
|---|---|---|---|---|
| `05-commandes-cmd-setup-fastapi-streamlit-python-3.12.md` | Windows CMD | Montre comment installer et lancer le projet FastAPI + Streamlit avec `python` | `python` | Utilisateurs Windows avec CMD |
| `06-commandes-cmd-setup-fastapi-streamlit-py-3.12.md` | Windows CMD | Montre comment installer et lancer le projet FastAPI + Streamlit avec le lanceur Python `py -3.12` | `py -3.12` | Utilisateurs Windows avec plusieurs versions de Python |
| `07-commandes-powershell-setup-fastapi-streamlit-python-3.12.md` | Windows PowerShell | Montre comment installer et lancer le projet avec `python` dans PowerShell, avec activation via `Activate.ps1` | `python` | Utilisateurs Windows qui travaillent dans PowerShell |
| `08-commandes-powershell-setup-fastapi-streamlit-py-3.12.md` | Windows PowerShell | Montre comment installer et lancer le projet avec `py -3.12` dans PowerShell, avec activation via `Activate.ps1` | `py -3.12` | Utilisateurs Windows avec PowerShell et plusieurs versions de Python |
| `09-commandes-linux-ubuntu24-setup-fastapi-streamlit-python3.md` | Linux Ubuntu 24.04 | Montre comment installer Python, créer le venv et lancer FastAPI + Streamlit sous Ubuntu | `python3` | Utilisateurs Linux / Ubuntu |
| `10-guide-py-vs-python-vs-python3-ubuntu24-windows.md` | Guide théorique | Explique la différence entre `py`, `python` et `python3` selon Windows et Ubuntu | `py`, `python`, `python3` | Toute personne confuse entre les différentes commandes |
| `11-comparaison-ubuntu22-ubuntu24-python-venv.md` | Comparaison système | Compare Ubuntu 22.04 et Ubuntu 24.04 pour Python, `venv`, `python3-full`, `python3.10-venv`, `python3.12-venv` | `python3` | Utilisateurs Linux voulant comprendre les différences entre versions Ubuntu |

---

## Détail par fichier

### `05-commandes-cmd-setup-fastapi-streamlit-python-3.12.md`

Ce fichier contient les commandes à exécuter dans **Windows CMD** avec la commande :

```bat
python
```

Il sert à :

- créer le dossier du projet ;
- cloner le dépôt GitHub ;
- créer un environnement virtuel ;
- activer l’environnement virtuel ;
- installer les dépendances ;
- lancer le backend FastAPI ;
- lancer le frontend Streamlit.

Ce fichier est utile quand `python` fonctionne déjà correctement dans l’invite de commandes Windows.

---

### `06-commandes-cmd-setup-fastapi-streamlit-py-3.12.md`

Ce fichier contient les mêmes étapes que le fichier précédent, mais avec :

```bat
py -3.12
```

Il sert à :

- forcer l’utilisation de Python 3.12 ;
- éviter les ambiguïtés si plusieurs versions de Python sont installées ;
- utiliser le **Python Launcher** propre à Windows.

Ce fichier est particulièrement utile sur Windows quand la commande `python` pointe vers une autre version.

---

### `07-commandes-powershell-setup-fastapi-streamlit-python-3.12.md`

Ce fichier est destiné à **Windows PowerShell** avec la commande :

```powershell
python
```

Il montre :

- les commandes d’installation ;
- la création du venv ;
- l’activation avec :

```powershell
.\myapp1\Scripts\Activate.ps1
```

- l’installation des dépendances ;
- le lancement de FastAPI et Streamlit.

Il inclut aussi le cas où PowerShell bloque l’activation du script, avec `Set-ExecutionPolicy`.

---

### `08-commandes-powershell-setup-fastapi-streamlit-py-3.12.md`

Ce fichier est la version PowerShell utilisant :

```powershell
py -3.12
```

Il sert à :

- utiliser PowerShell ;
- activer le venv avec `Activate.ps1` ;
- forcer explicitement Python 3.12 ;
- garder une logique stable sur une machine Windows avec plusieurs versions de Python.

C’est la combinaison la plus explicite pour Windows : **PowerShell + py -3.12**.

---

### `09-commandes-linux-ubuntu24-setup-fastapi-streamlit-python3.md`

Ce fichier est destiné à **Ubuntu 24.04**.

Il montre :

- l’installation de Python avec :

```bash
sudo apt install -y python3-full git
```

- la création de l’environnement virtuel avec :

```bash
python3 -m venv myapp1
```

- l’activation avec :

```bash
source myapp1/bin/activate
```

- le lancement du backend et du frontend.

Ce fichier explique aussi que sous Ubuntu, on utilise généralement :

```bash
python3
```

et non `py`.

---

### `10-guide-py-vs-python-vs-python3-ubuntu24-windows.md`

Ce fichier n’est pas seulement une suite de commandes.

C’est un **guide explicatif**.

Il sert à comprendre :

- pourquoi Windows utilise souvent `py` ;
- pourquoi Ubuntu utilise généralement `python3` ;
- quand `python` fonctionne ou non ;
- à quoi sert `venv` ;
- pourquoi on doit adapter les commandes selon le système.

Ce fichier est utile pour éviter de recopier des commandes Windows dans Linux, ou l’inverse.

---

### `11-comparaison-ubuntu22-ubuntu24-python-venv.md`

Ce fichier compare **Ubuntu 22.04** et **Ubuntu 24.04**.

Il met en évidence :

- la version Python par défaut :
  - Ubuntu 22.04 → Python 3.10
  - Ubuntu 24.04 → Python 3.12
- les paquets `venv` spécifiques :
  - `python3.10-venv`
  - `python3.12-venv`
- l’option recommandée `python3-full`
- les différences et points communs entre les deux versions d’Ubuntu.

Ce fichier sert à comprendre ce qui change réellement entre les deux systèmes.

---

## Lecture conseillée

### Si la personne travaille sur Windows CMD
Lire :

1. `05-commandes-cmd-setup-fastapi-streamlit-python-3.12.md`
2. `06-commandes-cmd-setup-fastapi-streamlit-py-3.12.md`

### Si la personne travaille sur Windows PowerShell
Lire :

1. `07-commandes-powershell-setup-fastapi-streamlit-python-3.12.md`
2. `08-commandes-powershell-setup-fastapi-streamlit-py-3.12.md`

### Si la personne travaille sur Ubuntu
Lire :

1. `09-commandes-linux-ubuntu24-setup-fastapi-streamlit-python3.md`
2. `11-comparaison-ubuntu22-ubuntu24-python-venv.md`

### Si la personne ne comprend pas les différences entre les commandes Python
Lire :

1. `10-guide-py-vs-python-vs-python3-ubuntu24-windows.md`

---

## Résumé final

| Type de fichier | Rôle |
|---|---|
| Fichiers `05`, `06`, `07`, `08`, `09` | Exécution pratique des commandes |
| Fichier `10` | Explication théorique des commandes Python selon le système |
| Fichier `11` | Comparaison entre deux versions d’Ubuntu |

---

## Conclusion

Ces fichiers ne font pas tous la même chose :

- certains servent à **exécuter** le projet ;
- certains servent à **comprendre** les commandes ;
- certains servent à **comparer** les environnements.

L’idée est donc :

- choisir le bon fichier selon le terminal ;
- choisir le bon fichier selon le système d’exploitation ;
- lire les guides explicatifs lorsqu’il y a confusion sur `py`, `python`, `python3` ou `venv`.
