# Fish Shell

Remplace `bash` et `zsh`. 

## Alias

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
