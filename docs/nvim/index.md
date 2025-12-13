# Nvim 

IDE surpuissant, mais avec une prise en main plus difficile que VScode par exemple. 

Une fois compris, et optmiser avec des plugins, il deviens super agréable à utiliser, même si parfois il faut se batter avec les commandes.

## Commande de base 

### Mode 

### Déplacement 
- `gg` : permet d'aller en haut d'un fichier 
- `v`: mode visuel
- `G`: permet d'aller à la fin du fichier

### Suppression 

- `d`: supprimer 
- `dd`: supprimer la ligne (N)

### Traitement de texte
- `yy`: copier la ligne (N)
- `dd`: couper la ligne (N)
- `p`: colle après le curseur (N) 
- `P`: colle avant le curseur (N)

- `d`: couper (V)
- `y`: copier (V)


## Recherche 

on utilise telescope pour la recherche.

```shell
# recherche de fichier 
<leader>ff

# recherche d'une chaîne 
<leader>fg
```

## Tree 

Permet d'ajouter la hiérarchie des fichiers sur le côté.

- `a` : créer un fichier. On définit également son extension. Pour créer un dossier, il suffit d'ajouter un `/` à la fin du nom.
- `d` : supprime un fichier ou dossier 
- `<ctrl>h`: passe du fichier à tree 
- `r`: renommer 
