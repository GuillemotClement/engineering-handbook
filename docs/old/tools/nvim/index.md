# Nvim 

IDE surpuissant, mais avec une prise en main plus difficile que VScode par exemple. 

Une fois compris, et optmiser avec des plugins, il deviens super agréable à utiliser, même si parfois il faut se batter avec les commandes.

## Commande de base 

### Mode 

- `i` : mode insertion 
- `;;` : retour au mode normal 
- `v` : mode visuel 

### Déplacement 
- `gg` : permet d'aller en haut d'un fichier 
- `G`: permet d'aller à la fin du fichier
- `w` : mot suivant 
- `b` : mot précédent 
- `0` : debut de ligne 
- `$` : fin de ligne 
- `{` : paragraphe suivant 
- `}` : paragraphe précédent 

### Suppression 

- `d`: supprimer 
- `dd`: supprimer la ligne (N)

### Traitement de texte

- `i` : insertion avant curseur 
- `a` : insertion après curseur 
- `I` : insertion début de ligne 
- `A` : insertion fin de ligne 
- `o` : insertion ligne en dessous 
- `O`: insertion ligne au dessu 

- `x` : supprimer le caractère 
- `dd` : supprimer la ligne 

- `u` : undo 
- `Ctrl+r` : redo 

- `ciw`: changer le mot sous le curseur 
- `diw` : supprimer le mot sous le curseur 
- `ci` : changer entre guillemets 
- `ci{` : changer entre accolades 

- `yy`: copier la ligne (N)
- `dd`: couper la ligne (N)
- `p`: colle après le curseur (N) 
- `P`: colle avant le curseur (N)

- `d`: couper (V)
- `y`: copier (V)

- `Ctrl+space` : ouvrir l'autocomplète 
- `Ctrl+j` : selection suivante 
- `Ctrl+k` : selection précédente 
- `Ctrl+j` : descendre dans les résultats 
- `Ctrl+k` : monter dans les résultats 

## LSP 

### Navigation dans le code 

- `gD` : aller à la définition 
- `dD` : aller à la déclaration 
- `gi` : aller à l'implémentation 
- `gt` : aller à la définition du type 
- `gR` : afficher les références 
- `K` : afficher la documentation 
- `gs` : afficher la signature de la fonction 

### Action et refacto 

- `<leader>ca` : code action 
- `<leader>rn` : renommer 
- `<leader>F`: formatter le code 
- `<leader>rs` : redémarrer le lsp 

### Diagnostics et erreurs 

- `<leader>d` :afficher diagnotics de la ligne 
- `<leader>D`: affiche les diagnostics du buffer 
- `]d` : disgnostic suivant 
- `[d` : diagnotics précédent 
- `<leader>xx` : ouvrir Trouble (all)
- `<leader>xX` : ouvrir trouve (buffer)
- `<leader>cs` : affiche les symboles (functions et classe)

## Recherche 

on utilise telescope pour la recherche.

```shell
# recherche de fichier 
<leader>ff

# recherche d'une chaîne 
<leader>fg
```
- `/` : recherche dans le fichier 
- `n` : occurence suivante 
- `N` : occurence précédente 
- `Ctrl+j` : descendre dans les résultats 
- `Ctrl+k` : monter dans les résultats 

## Tree 

Permet d'ajouter la hiérarchie des fichiers sur le côté.

- `a` : créer un fichier. On définit également son extension. Pour créer un dossier, il suffit d'ajouter un `/` à la fin du nom.
- `d` : supprime un fichier ou dossier 
- `<ctrl>h`: passe du fichier à tree 
- `r`: renommer 

## Installation de plugins 

Avec `lazy.nvim`, l'installation de nouveau plugin est simple.

On commence par ajouter un nouveau fichier dans le folder `nvim/lua/plugins/<plugins_name>.lua`. Par exemple, pour installer ce plugin [gitsigns](https://github.com/lewis6991/gitsigns.nvim), je viens nommer le fichier `gitsigns.lua`. 

Pour connaitre le nom du plugin, il faut utiliser `<OWNER/REPO>`. Pour dans le nouveau fichier, utiliser le nom pour que Lazy puisse venir le recupérer :

```lua 
return {
  {
    "lewis6991/gitsigns.nvim",
    opts = {}, //permet d'ajouter des options 
    keys = {}, // permet d'ajouter des raccourcis clavier 
  }
}
```

On viens dans ce fichier ajouter la configuration du plugin, généralement fournis dans la docuement du plugins. Cela permet d'installer le plugin avec `lazy`.

Une fois la config ajouter, on peut utiliser la commande `:Lazy sync` ou `:Lazy` puis `s` pour télécharger et synchroniser notre configuration.
