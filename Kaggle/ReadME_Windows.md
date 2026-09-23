# Titanic Kaggle - Authentification, téléchargement et soumission (Windows)

Guide pour l'environnement **Windows** (poste local, `uv` déjà installé).

Toutes les commandes sont à lancer dans **PowerShell**.

---

## 1. Générer un token Kaggle

1. Se connecter sur https://www.kaggle.com
2. Ouvrir https://www.kaggle.com/settings/api
3. Section **API**, cliquer sur **Generate New Token**
4. Copier le token (il commence par `KGAT_`). Il n'est affiché qu'une seule fois.

## 2. Accepter le règlement de la compétition

Ouvrir https://www.kaggle.com/competitions/titanic et cliquer sur **Join Competition**.

Sans cette étape, le téléchargement échoue avec une erreur `403 Forbidden`, même si l'authentification est correcte.

## 3. Créer le projet

```powershell
uv add kaggle kagglehub
```

Le CLI `kaggle` demande Python 3.12 ou plus récent.

## 4. Enregistrer le token

Le fichier `access_token` est lu à la fois par le CLI `kaggle` et par la bibliothèque `kagglehub`.

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.kaggle"
Set-Content "$env:USERPROFILE\.kaggle\access_token" "KGAT_xxxxxxxxxxxx"
```

Le fichier se trouve alors dans `C:\Users\<votre-nom>\.kaggle\access_token`.

Remarque : `uv run kaggle auth login` (connexion par navigateur) authentifie uniquement le CLI. `kagglehub` ne lit pas ces identifiants et renvoie `User is not authenticated`. Le fichier token évite ce problème.

Fermer puis rouvrir PowerShell pour que la variable soit prise en compte.

## 5. Vérifier l'authentification

```powershell
uv run kaggle competitions list -s titanic
uv run python -c "import kagglehub; print(kagglehub.whoami())"
```

La seconde commande doit afficher votre nom d'utilisateur Kaggle.

## 6. Télécharger les données

Créer `download.py` :

```python
import kagglehub

path = kagglehub.competition_download("titanic", output_dir="./data")

print("Fichiers telecharges dans :", path)
```

Exécuter :

```powershell
uv run download.py
```

Les fichiers arrivent directement décompressés dans `data\`.

## 7. Soumettre

```powershell
uv run kaggle competitions submit -c titanic -f submission.csv -m "Premier essai"
uv run kaggle competitions submissions -c titanic
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
