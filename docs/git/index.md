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

