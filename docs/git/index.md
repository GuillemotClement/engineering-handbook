# Git 

## Configuration 

```shell
# affiche la configuration Git sur la machine 
git config --list

# appliquer une configuration local  
git config user.name "<username>" 
git config user.email "<email>"

# appliquer une configuration global 
git config --global user.name "<username>"
git config --global user.email "<email>"
```

### Appliquer une configuration local 

Il est possible de déclarer une configuration Git spécifique pour un dossier.

```shell
# appliquer les bonne information pour ce projet
git config user.name "Clément Perso"
git config user.email "perso@example.com"

# lister si ok 
git config --list --local
```

---
## PROJET 

### Récupérer un repo distant sur un repo local 

```shell
# name est le nom donné à la connexion
git remote add <name> <url_repo_distant>

# check si connexion bien créer
git remote -v

# récupérer les ref distante
git fetch <name>

# création d'une nouvelle branche 
# le code de la branche distant est récupérer sur cette nouvelle branche
git switch -c <branche_name> <name_connexion>/<name_branche>
```

----
## BRANCHES 

### Suppressions de branches 

#### Suppression branche locale 

```bash
git branch -d <nameBranch>

# forcer la supression d'une branche même si non commit
git branch -D <nameBranche>
```

#### Suppression branche distante 

```bash
git push origin --delete <namebranche>
```

#### Nettoyage des branches

Pour nettoyé le repo distant, et effacer les anciennes branches on peut utiliser une la commande 

```shell
git fetch --prune 
```

---
## Tag 

Le tag permet d'ajouter un tag pour un état particulier d'un projet. Par exmeple, si on as terminer le MVP, on peut ajouter le tag.
Cela permet d'avoir un tag spécifique sur le commit pour avoir une meilleur lisibilité sur l'historique du projet.

On viens créer le tag sur le git local.
```shell
git tag -a <name_tag> -m "commentaire associé au tag"
```

- `-a` : signifie Annotated 
- `<name_tag>` : le nom du tag
- `-m` : message associé 

Une fois le tag créer, il faut forcer l'envoie sur Github 

```shell 
git push origin <name_tag> 
```

---
## STASH 

### Accéder au stash 

```shell
# accéder au supprimer le stash 
git stash pop
```

### Ajouter un stash avec un nom 

```shell
git stash push -m "message de stash"

# ancienne syntaxe
git stash save "message de stash"
```

### Voir la liste des stash 

```shell
git stash list 
```

### Supprimer les stash 

```shell
# supprimer tous les stash 
git stash clear 

# supprimer un stash précis 
git stash drop stash@{2} # ou 2 est l'index du stash

# supprimer le dernier 
git stash drop 
```

---
## SQUASH 

Le squash permet de nettoyer l'historique de commit, en fusionnant plusieurs commit dans une seul.

```shell
 # affichage historique de commit 
 git log --oneline
 
 # on lance le rebase 
 git rebase -i HEAD~5 #5 étant le nombre de commit à remonter
 # ou directement pour inclure tous les commit local 
 git rebase -i master
```

```shell
# selectionner le commit à partir duquel on souhaite fusionner
git rebase -i <SHA_id>

# retourner a la main et recupere tous les commit local 
git rebase main
```

Avec cette commande, tous les commits après celui qui est référencer seront rassembler dans un seul commit.

Une fois lancer, la commande ouvre un editeur, et on peut indiquer ce que l'on souhaite faire.

```nvim
pick 5eb2c27 feat: possibiliter de definir manuellement 
squash 6d4307f refacto: nettoyage du code 
s d55e812 feat: ajout btn pour ajout dun controle produit suivi 
s 58f444b feat: add date de controle pour un produit suvi squash 
```

En utilisant `squash` ou `s`, on indique que l'on souhaite fusionner les commit dans le `pick`.

Une fois terminer, un nouvel éditeur s'ouvre permettant de réecrire le message de commit.
Il suffit de commenter les anciens nom de commit, et écrire si besoin un nouveau message de commit.

Une fois terminer, l'historique de commit est mis à jour, et on peut push.