# Commande

## Format d'une commande

```
[commande] [option] [arguments]

# exemple
ls -l /home
```

- `commande` : action demander au systeme (ls)
- `option` : modifie le comportement de la commande (egalement appeler flag) (-l)
- `arguments` : info supplementaire passer a la commande (/home)

## System files linux 
Linux est structurer avec des repertoire :
- `/` : repertoire racine
- `/home` : repo personnal des utilisation, par exemple `/home/[username]` 
- `/etc` : fichiers de configuration du systeme
- `/var` : donnee dynamique comme les logs et fichiers temporaire 
- `/tmp` : fichiers temporaire supprime regulierement par le systeme 
- `/dev` : peripherique connecter au systeme 
- `/mnt` : point de montage des peripherique externe 

### Chemin relatif et absolu 
- **Chemin absolu** : commence a partir de `/` 
- **Chemin relatif** : commence depuis le repertoire courant, par exemple `documents/file.txt` 
 
## ls 
Cette commande permet de voir le contenu du repertoire courant. Il liste les fichiers et dossiers. 

```shell
# affichage du contenu 
ls 

# info detaille 
ls -l 

# fichier cacher 
ls -a 

# affiche les tailles 
ls -h 

# combinaison 
ls -lha
```

## cd 
Permet de se deplacer et passer d'un repertoire a un autre.

```shell
# deplacer dans un dossier 
cd [folder_name]

# retour dans le repertoire personnel 
cd ~

# retour en arriere 
cd ..

# acceder a la racine 
cd /
```


## pwd 

Cette commande affiche le repertoire courant. Il permet de savoir ou on se trouve.

```shell
pwd
```

## mkdir 
Permet de creer de nouveau repertoiress
```shell
# creer un repertoire 
mkdir [folder_name]

# creer des repertoire imbriquer
mkdir -p [parent_folder]/[childe_folder]

# creation d'une structure de dossier 
mkdir -p project/{src,docs,tests}
```

## touch 
Permet de creer un nouveau fichier et mettre a jour leur timestamps 
```shell
# creation d'un fichier txt 
touch [file_name.txt]

# creation de fichier multiple 
touch [file1.txt] [file2.txt]
```

## rm 
Permet de supprimer des fichiers et dossier 

```shell
# suppression de fichier 
rm [file_name.txt]

# supression de plusieurs fichiers 
rm [file1.txt] [file2.txt]

# suppression de dossier
rm -r [folder_name]

# suppression avec confirmation 
rm -ri [folder_name]
```

## cp 
Permet de copier des fichiers 

```shell
# copie 
cp [source] [destination]
# creation d'une copie de my_file.txt dans un nouveau fichier backup_file.txt
cp my_file.txt backup_file.txt

# copie plusieurs fichiers dans un dossier 
cp file1.txt file2.txt file3.txt backup/

# copie dossier 
cp -r [folder_source] [folder_dest]
cp -r my_folder backup_folder
```

## mv 
Deplace ou renomme un fichier ou un dossier d'un endroit a l'autre 

```shell
mv [source] [dest]
mv my_file.txt /home/user/document

# renommer 
mv old_name.txt new_name.txt
```

## cat 
Permet d'afficher le contenu d'un fichier 

```shell
cat [file_name]
cat example.txt

# combiner et inserer dans un fichier 
cat file1.txt file2.txt > combined.txt 
```

## less 
Lorsque le fichier est trop gros pour etre lisible avec `cat`.

- `space` : page suivante 
- `b` : page precedente 
- `/[mot]` : recherche un mot dans le texte 
- `q` : quitter  

```shell
less [file_name]
less example.txt
```

## nano 
Permet de modifier un fichier texte 

- `Ctrl+o` : sauvegarde du fichier 
- `Ctrl+X` : quitter 
- `Ctrl+K` : couper une ligne 
- `Ctrl+U` : coller une ligne 

```shell
nano [file_name]
nane example.txt
```

## vim 
Editeur de texte 

- `i` : mode edition
- `echap` : revenir en mode commande 
- `:w` : enregistrer 
- `:q` : quitter 
- `:wq` : enregistrer et quitter 
- `:q!` : exit forcer


```shell
vim [file_name]
vim example.txt