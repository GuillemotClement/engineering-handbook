# CI CD 

## Workflow

1. **Build**: on vérifie que le code compile, et que les dépendances s'installent sans erreur
2. **Test**: on lance les test unitaire et d'intégration du front et du back en parallères
3. **Securité**: on analyse le code pour trouver des failles ou des secrets oubliée (clé api, password)
4. **Validation** : on configure la "Branch Protection" pour bloquer le merge si les étapes précédentes ont échouée

## Glossaire

### Runner

Un runner est une machine éphémère. C'est une VM que github loue pendant le temps du job.
- Installation: `setup` télécharge et installe le binaire Bun dans le runner 
- Durée de vie: dès que le `job` est terminé, la VM est détruite. Pour conserver des donnée, on utilise le cache ou les artifacts.

### Step

Chaque `step` est une étape et une instruction donnée à la VM (runner) utilisé par la pipeline pour qu'il traite le code.

Tous les steps d'un même job s'exécute de manière séquentielle sur le runner.
- Step1 créer le fichier, step2 pourra le lire

| Actions            | Rôle                                                                                                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `actions/checkout` | Etape de copie. Elle clone le projet depuis le github vers le runner.                                                                                                                       |
| `setup`            | Etape d'installation. Elle installe le moteur (runtime) nécessaire. Par exemple, pour un projet `bun`; elle viient installer `bun` pour que le runner comprenne et interpréte les commandes |
 ## Création d'une pipeline

Pour sécurisé la fusion vers la branche `main`, on définis jobs : `frontend_tests` et `backend_test`.

### Backend

La première étape, 


## Exemple 

On implémenter une CI/CD permettant de tester la PR sur la branche `main` est tester. Dans le cas où la elle échoue, alors on empêche la merge.

```yml
name: CI Pipeline

# indique à github que cette pipeline a juste besoin de lire le code, pas le droit d'écrire ou modifier
permissions:
  contents: read

# on définis quel action déclenche la CI => ici sur une PR ou un push sur la branche main
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

# la pipeline doit venir déclencher les tests automatiser pour valider ou non le merge sur la branche main
# si la pipeline échoue, on refuse le merge

# flow
# 1. on ramène le code avec un checkout
# 2. on prépare en installant l'outile de runtime
# 3. installation des lib du projet
# 4. exécution des commandes

jobs:
  api-test: # on nomme le premier job => partie backend
    runs-on: ubuntu-24.04

    # on indique le dossier du back qui sera utiliser par les commandes dans les steps
    defaults:
      run:
        working-directory: ./api

    steps:
      # actions épinglées par SHA
      # tous le code du projet est récupéré et mis dans le runner (la vm)
      - name: Checkout - récupération du code
        # permet de faire appel à une action officiel
        uses: actions/checkout@v4

        # permet d'installer le setup => l'outil utilisé pour lancer les commande dans le projet sur ma machine
      - name: Setup Bun
        uses: oven-sh/setup-bun@3d267786b128fe76c2f16a390aa2448b815359f3

      # - name: Install & Test backend
      #   # on cible le dossier du backend
      #   working-directory: ./api
      #   # permet de lancer des commandes manuel
      #   # on ajoute les commandes bun
      #   # 1. on installe les dépendances
      #   # 2. on lance les test automatisés
      #   run: |
      #     bun install
      #     bun test
      #     bun build

      - name: install depedancie
        run: bun install

      # audit dépendances du projets
      # - name: Security Audit
      #   run: bun audit --level hight # on bloque sur si le warning est hight

      # check du linting du code
      - name: lint & format
        run: bun run lint

      - name: tests automatic
        # on passe la variable env du front end
        env:
          FRONTEND_URL: "http://localhost:5173"
        run: bun test

      - name: build project
        run: bun run build

  front-test:
    runs-on: ubuntu-24.04

    defaults:
      run:
        working-directory: ./front

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Bun
        uses: oven-sh/setup-bun@3d267786b128fe76c2f16a390aa2448b815359f3

      - name: install deps
        run: bun install

        # audit dépendances du projets
      - name: Security Audit
        run: bun audit

      # check du linting du code
      - name: lint & format
        run: bun run lint

      # installation playwright pour les tests browser
      # - name: install Playwright Browsers
      #   run: bun x playwright install --with-deps chromium

      # 📦 Mise en cache des navigateurs Playwright
      - name: Cache Playwright Browsers
        uses: actions/cache@v4
        id: playwright-cache
        with:
          path: ~/.cache/ms-playwright
          key: ${{ runner.os }}-playwright-${{ hashFiles('**/bun.lockb') }}
          restore-keys: |
            ${{ runner.os }}-playwright-

      # 🚀 On n'installe que si le cache n'a pas été trouvé (hit)
      - name: Install Playwright Browsers
        if: steps.playwright-cache.outputs.cache-hit != 'true'
        run: bun x playwright install --with-deps chromium

      # 🛠️ Si le cache est trouvé, on doit quand même installer les dépendances système (OS)
      - name: Install Playwright OS Dependencies
        if: steps.playwright-cache.outputs.cache-hit == 'true'
        run: bun x playwright install-deps chromium

      - name: tests automatic
        run: bun run test

      - name: build project
        run: bun run build

```