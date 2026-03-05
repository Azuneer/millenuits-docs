# Déployer une documentation MkDocs sur GitHub Pages

> 👤 **Fiche d'installation rédigé par** : GADONNAUD Ewen
> 🎓 **Formation** : BTS SIO 1ère année - Option SISR
> 🏫 **Établissement** : Lycée Paul-Louis Courier, Tours
> 📅 **Date** : Novembre 2025

Ce guide couvre l'installation, la configuration et l'automatisation du déploiement d'un site de documentation [MkDocs](https://www.mkdocs.org/) avec le thème Material sur GitHub Pages.

---

## Prérequis

Avant de commencer, vérifier que les outils suivants sont disponibles :

- **Python 3** — `python3 --version`
- **pip** — `pip --version`
- **Un repository GitHub** existant (public ou privé)

---

## 1. Installation

`mkdocs-material` est un thème pour MkDocs qui embarque MkDocs lui-même : un seul paquet suffit.

```bash
pip install mkdocs-material
```

---

## 2. Initialisation du projet

```bash
mkdocs new nom-du-projet
cd nom-du-projet
```

La commande génère la structure minimale suivante :

```
nom-du-projet/
├── docs/
│   └── index.md       ← page d'accueil
└── mkdocs.yml         ← fichier de configuration principal
```

---

## 3. Configuration de `mkdocs.yml`

C'est le fichier central du projet. Voici une configuration de base :

```yaml
site_name: Documentation Mille Nuits

theme:
  name: material
  logo: assets/images/logo.png

nav:
  - Accueil: index.md
```

> Les pages ajoutées dans `docs/` doivent être déclarées dans la section `nav` pour apparaître dans la navigation.

---

## 4. Coloration syntaxique des blocs de code (optionnelle)

### Installation

```bash
pip install pymdown-extensions
```

### Configuration dans `mkdocs.yml`

```yaml
markdown_extensions:
  - pymdownx.highlight:
      anchor_linenums: true
  - pymdownx.superfences
```

>`pymdownx.superfences` est **obligatoire** pour que `pymdownx.highlight` fonctionne. Sans lui, les blocs de code ne seront pas colorés.

### Langage à utiliser pour les commandes Cisco IOS

Pygments (le moteur de coloration utilisé par MkDocs) **ne reconnaît pas** `cisco` comme identifiant de langage. Utiliser `console` à la place :

````markdown
```console
Switch>enable
Switch#configure terminal
```
````

---

## 5. Prévisualisation locale

```bash
mkdocs serve
```

Le site est accessible sur [http://127.0.0.1:8000](http://127.0.0.1:8000/) et se recharge automatiquement à chaque modification d'un fichier `.md`.

---

## 6. Déploiement manuel sur GitHub Pages

Pour un premier déploiement ou un déploiement ponctuel :

```bash
mkdocs gh-deploy
```

Cette commande effectue deux choses :

1. **Build** — génère le HTML statique
2. **Push** — pousse le résultat sur la branche `gh-pages` du repo

Ensuite, dans **Settings → Pages** du repo GitHub, vérifier que la source est :

- **Branch :** `gh-pages`
- **Folder :** `/ (root)`

---

## 7. Automatisation avec GitHub Actions

Plutôt que de lancer `gh-deploy` manuellement, on peut configurer GitHub Actions pour que le site se mette à jour automatiquement à chaque `push` sur `main`.

### Création du fichier workflow

Créer le fichier `.github/workflows/deploy.yml` avec le contenu suivant :

```yaml
name: Deploy MkDocs to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'

      - name: Install dependencies
        run: pip install mkdocs-material pymdown-extensions

      - name: Deploy
        run: mkdocs gh-deploy --force
```

### Détail des étapes

|Étape|Rôle|
|---|---|
|Déclencheur `on: push`|S'exécute à chaque push sur `main`|
|`actions/checkout@v4`|Récupère le code source du repo|
|`actions/setup-python@v5`|Installe Python sur le runner GitHub|
|`Install dependencies`|Installe `mkdocs-material` et `pymdown-extensions`|
|`mkdocs gh-deploy --force`|Build le site et pousse le HTML sur `gh-pages`|

### Push du workflow

```bash
git add .github/workflows/deploy.yml
git commit -m "ci: add GitHub Actions deployment workflow"
git push
```

L'avancement du déploiement est visible dans l'onglet **Actions** du repo GitHub.

---

## 8. Workflow au quotidien

Une fois l'automatisation en place, le processus se résume à :

```bash
# 1. Modifier ou ajouter des fichiers .md dans docs/
# 2. Commit et push
git add .
git commit -m "docs: description des modifications"
git push
```

GitHub Actions se déclenche automatiquement et met le site à jour en moins d'une minute.

---

## Récapitulatif des commandes

|Commande|Usage|
|---|---|
|`pip install mkdocs-material`|Installation initiale|
|`mkdocs new nom-du-projet`|Créer un nouveau projet|
|`mkdocs serve`|Prévisualisation locale|
|`mkdocs gh-deploy`|Déploiement manuel|
|`git push`|Déploiement automatique (si Actions configuré)|
