# Fish Shell

Remplace `bash` et `zsh`. 

- `https://blog.stephane-robert.info/docs/admin-serveurs/linux/shells-linux/fish/`

---

# Raccourcis clavier 

## Historique 

- `flèche haut | flèche bas` : parcourir l'historique de commande 
- `Ctrl + R` : recherche dans l'historique avec une recherche incrémentale 

## Edition de commande 

- `Ctrl + a`: curseur en début de ligne 
- `Ctrl + e`: curseur en fin de ligne 
- `Ctrl + u`: efface tout avant le curseur 
- `Ctrl + k`: efface tout après le curseur 
- `Alt + fs | Alt + fe`: déplace le curseur mot par mot 

## Suppression 

- `Ctrl + w`: supprime le mot avant le curseur 
- `Ctrl + d`: supprime le caractère sous le curseur ou ferme le terminal si la ligne est vide 

## Répertoire 

- `Alt + ft | Alt + fb`: navigue dans l'historique des répertoire 
- `Ctrl + Alt + d`: accède directement au dernier repertoire utilisé 

## Processus 

- `Ctrl + c`: stop une commande ou un script en cours 
- `Ctrl + z`: met un processus en pause (en arrière plan)
- `jobs`: liste les processus en arrière plan 
- `fg`: relance un processus mis en pause avec `ctrl + z`

## Raccourcis 

- `bind \ct 'date`: ajoute un raccourcis personnalisé => par exemple `Ctrl + t` pour afficher l'heure 
- `bind`: liste les raccourcis actif

---

# Alias

Les alias permettent de simplifier les commandes. Pour créer un nouvel alas : 
```shell
alias ll="ls -la"
```

- `ll` : alias qui permet de déclencher la nouvelle commande 
- `ls -la`: commande déclencher avec l'alias 

Une fois déclarer, l'alias est automatiquement disponible. Pour le rendre permanent, il suffit ensuite de l'ajouter à la configuration utilisateur.

```shell 
funcsave ll
```
