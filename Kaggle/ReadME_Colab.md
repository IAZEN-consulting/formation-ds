# Titanic Kaggle - Authentification, téléchargement et soumission (Google Colab)

Guide pour l'environnement **Google Colab**.

Tout se fait depuis les cellules du notebook. Le token est stocké dans les **secrets Colab**, jamais écrit en clair dans une cellule.

---

## 1. Générer un token Kaggle

1. Se connecter sur https://www.kaggle.com
2. Ouvrir https://www.kaggle.com/settings/api
3. Section **API**, cliquer sur **Generate New Token**
4. Copier le token (il commence par `KGAT_`). Il n'est affiché qu'une seule fois.

## 2. Accepter le règlement de la compétition

Ouvrir https://www.kaggle.com/competitions/titanic et cliquer sur **Join Competition**.

Sans cette étape, le téléchargement échoue avec une erreur `403 Forbidden`, même si l'authentification est correcte.

## 3. Installer les paquets

```python
!pip install -q uv
!uv pip install --system -q kaggle kagglehub pandas scikit-learn
```

L'option `--system` installe dans l'environnement Python du notebook, celui qu'utilisent les cellules.

## 4. Enregistrer le token dans les secrets Colab

1. Dans le menu de gauche, cliquer sur l'icône en forme de clé (**Secrets**)
2. Cliquer sur **Add new secret**
3. Nom : `KAGGLE_API_TOKEN`
4. Valeur : le token copié à l'étape 1
5. Activer l'interrupteur **Notebook access**

`kagglehub` lit ce secret automatiquement, sans code supplémentaire.

Pour que le CLI `kaggle` le voie aussi, exporter le secret en variable d'environnement :

```python
import os
from google.colab import userdata

os.environ["KAGGLE_API_TOKEN"] = userdata.get("KAGGLE_API_TOKEN")
```


## 5. Vérifier l'authentification

```python
import kagglehub

print(kagglehub.whoami())
```

La sortie doit contenir votre nom d'utilisateur Kaggle.

## 6. Télécharger les données

### Option A - Avec kagglehub

```python
import kagglehub

path = kagglehub.competition_download("titanic", output_dir="/content/data")

print("Fichiers telecharges dans :", path)
```


## 7. Contrôler les fichiers

```python
import pandas as pd

train = pd.read_csv("/content/data/train.csv")
test = pd.read_csv("/content/data/test.csv")

print(train.shape, test.shape)
train.head()
```


## 9. Soumettre

```python
!kaggle competitions submit -c titanic -f /content/submission.csv -m "Premier essai"
!kaggle competitions submissions -c titanic
```

## 10. Conserver les résultats

L'environnement Colab est effacé à la fin de la session. Pour récupérer le fichier de soumission :

```python
from google.colab import files

files.download("/content/submission.csv")
```

Ou monter Google Drive et y copier les fichiers :

```python
from google.colab import drive

drive.mount("/content/drive")
```

---

## Fichiers de la compétition

| Fichier                  | Contenu                                  |
|---------------------------|------------------------------------------|
| `train.csv`              | 891 passagers avec la cible `Survived`   |
| `test.csv`               | 418 passagers sans la cible              |
| `gender_submission.csv`  | Exemple de fichier de soumission         |

Le fichier de soumission doit contenir exactement deux colonnes, `PassengerId` et `Survived` (0 ou 1), et 418 lignes plus l'en-tête :

```
PassengerId,Survived
892,0
893,1
...
```


## Références

- CLI Kaggle : https://github.com/Kaggle/kaggle-cli/blob/main/docs/README.md
- kagglehub : https://github.com/Kaggle/kagglehub
- Compétition Titanic : https://www.kaggle.com/competitions/titanic
