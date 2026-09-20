# Installation du poste - Formation Data Scientist

## Liens

| Outil | Lien |
|---|---|
| Dépôt du projet | https://github.com/IAZEN-consulting/formation-ds.git |
| VS Code | https://code.visualstudio.com/download |
| uv | https://docs.astral.sh/uv/getting-started/installation/ |
| Extension Python | https://marketplace.visualstudio.com/items?itemName=ms-python.python |
| Extension Jupyter | https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter |

Python n'est pas à télécharger : uv l'installe.

---

## 1. Git

Nécessaire en premier pour cloner le dépôt du projet. Vérifier s'il est déjà installé :
```bash
git --version
```

Si la commande échoue :

**Windows**
```powershell
winget install -e --id Git.Git
```

**macOS**
```bash
brew install git
```

**Linux**
```bash
sudo apt install -y git
```

Fermer et rouvrir le terminal, puis revérifier avec `git --version`.

---

## 2. VS Code

**Windows**
```powershell
winget install -e --id Microsoft.VisualStudioCode
```

**macOS**
```bash
brew install --cask visual-studio-code
```

**Linux**
```bash
sudo snap install code --classic
```

Puis dans VS Code, `Ctrl+Shift+X`, installer les extensions **Python** et **Jupyter** (Microsoft).

-----

## 3. uv

**Windows**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**macOS / Linux**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Fermer et rouvrir le terminal, puis :
```bash
uv --version
```

---

## 4. Python 3.12

```bash
uv python install 3.12
```

---

## 5. Projet et bibliothèques

```bash
git clone https://github.com/IAZEN-consulting/formation-ds.git
cd formation-ds
uv init --python 3.12
uv add -r requirements.txt
```

Le dépôt ne contient pas de `pyproject.toml` : chacun initialise son propre projet uv avec `uv init`, qui crée `pyproject.toml` et l'environnement virtuel `.venv/` (avec le Python 3.12 installé à l'étape 3).

Les bibliothèques et leurs bornes de version sont définies dans [requirements.txt](requirements.txt) ; `uv add -r` les ajoute au projet et fige les versions résolues dans `uv.lock`.

---

## 6. Vérifier

**Les versions**
```bash
uv run python -c "import numpy, pandas, sklearn, matplotlib, seaborn, xgboost, lightgbm, mlflow, sys; print(sys.version.split()[0]); [print(m.__name__, m.__version__) for m in (numpy, pandas, sklearn, matplotlib, seaborn, xgboost, lightgbm, mlflow)]"
```

Attendu : Python 3.12.x et neuf lignes sans erreur.

**Que les modèles tournent vraiment**
```bash
uv run python -c "
from sklearn.datasets import make_classification
from xgboost import XGBClassifier
from lightgbm import LGBMClassifier
X, y = make_classification(n_samples=300, random_state=42)
print('xgboost ', XGBClassifier(n_estimators=10).fit(X, y).score(X, y))
print('lightgbm', LGBMClassifier(n_estimators=10, verbose=-1).fit(X, y).score(X, y))
"
```

Deux scores proches de 1.0 : l'installation est bonne.

Erreur `libomp.dylib` sur macOS : `brew install libomp`.

**MLflow**
```bash
uv run mlflow ui --port 5000
```

Ouvrir http://127.0.0.1:5000, puis `Ctrl+C` pour arrêter. Si le port est pris (AirPlay sur macOS), utiliser `--port 5001`.

---

## 7. Brancher VS Code sur l'environnement

`Ctrl+Shift+P` > `Python: Select Interpreter` > choisir le chemin contenant `.venv`.

Pour un notebook : bouton `Select Kernel` en haut à droite > `Python Environments` > le même `.venv`.

---

## Commandes du quotidien

```bash
uv sync              # installer ou remettre l'environnement à jour
uv add <paquet>      # ajouter une bibliothèque
uv run jupyter lab   # lancer Jupyter
uv run python x.py   # exécuter un script
```