# Pipe

Les pipes `|` permet de passer une sortie d'une commande comme entree pour une autre commande.
Cet outil permet de combiner des commandes en creant des pipelines pour le traitement des donnes.

Linux redirige la stdout de la premiere commande vers la stdin de la commande suivante. Cela permet de traiter de gros volumes de donnees sans fichiers temporaires.

```shell
cmd1 | cmd2 | cmd3
```

- `cmd1` : genere de la donnee
- `cmd2` : traite les donnes recues
- `cmd3` : effectue un traitement supplementaire

```shell
# afficher la liste des fichiers et filtrer par modele
ls -l | grep ".txt"

# compter les lignes contenant un mot specifique
cat file.txt | grep "Linux" | wc -l
```
