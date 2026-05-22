# Grep 

Utilitaire CLI permettant de rechercher des lignes dans des fichiers qui correspond a un modele specifie.
Il permet de filtrer et rechercher des donnees via des regex.

`grep` prend en entree un fichier texte ou la `stdin`, recherche les lignes qui match au modele specifier et les affiche.
Si aucun match, la commande ne retourne rien 

## Filtrage de fichier 

```shell
# affiche la liste des fichier filtrer
ls -l | grep ".txt"
```

1. `ls -ls` s'execute et affiche la liste des fichiers 
2. la sortie de `ls` est redirigee comme entree pour la seconde commande `grep` via le pipe
3. `grep` vient filtrer les lignes retourner par la commande `ls` et cherche celle contenant `.txt`

## Compter des lignes 

```shell
cat file.txt | grep "Linux" | wc -l
```

1. `cat file.txt` affiche le contenu du fichier 
2. la sortie est redirige vers `grep` 
3. `grep` vient filtrer les ligne envoye et affiche uniquement celle contenant "Linux"
4. `wc` compte ensuite les lignes retourner par `grep`