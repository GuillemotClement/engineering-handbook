# Droit d'acces

Linux permet de gerer qui a droit d'acceder au fichier et dossier. Chaque fichiers et dossier sous Linux a trois ensemble d'octets qui determine qui peut lire, ecrire ou executer.
- `r` : lecture
- `w` : ecriture 
- `x` : execution 

Chacun des ses droits s'applique a trois groupes d'utilisateur :
- `owner` : celui qui a creer 
- `group` : utilisateurs regroupe dans un groupe auquel le fichier est attribue 
- `others` : les autres utilisateurs du systeme 

Les droits d'acces sont representer de cette maniere :
```
-rwxr-xr--
```

- `-` : indique que c'est une fichier 
- `rwx` : tous les droits pour le proprietaire 
- `r-x` : lecture et execution pour le groupe 
- `r--` : lecture pour les autres

Un utilisateur n'est pas forcement un humain, cela peut etre un service ou un programme (serveur web, bdd, serveur SSH, etc).


## chmod 

Permet de modifier les permissions des fichiers et repertoire.

```shell
chmod [qui][action][droits] [file_name]

# droit execution a tous 
chmod a+x script.sh

# ajoute droit ecriture pour le groupe 
chmod g+w file.txt

# retire le droit de lecture pour les autres 
chmod o-r document.txt
```

- **Qui**: `u` proprietaire, `g` groupe, `o` autre, `a` tous
- **Action**: `+` ajoute un droit, `-` retire un droit, `=` definis exactement les droits 
- **Droits** : `r` lecture, `w` ecriture, `x` execution 

### Format octal 

Le format octal est une maniere plus compact pour definir les droits. Chaque combinaison de droits est representee par un chiffre.

- lecture `r` = 4 
- ecriture `w` = 2 
- execution `x` = 1 

Les droits s'additionnent pour definir l'ensemble des droits 
- **lecture ecriture execution** `rwx` = 7 
- **lecture ecriture** `rw-` = 6 
- **lecture execution** `r-x` = 5
- **lecture** `r--` = 4
- **ecriture execution** `-wx` = 3 
- **ecriture** `-w-` = 2
- **execution** `--x` = 1
- `---` = 0

```shell
chmod [chiffre][chiffre][chiffre] [file_name]

# rwx owner, r-x groupe, r-- autre
chmod 754 file.sh
```

## chown

Permet de changer le proprietaire d'un fichier ou dossier.

Par exemple, `Nginx` tourne avec l'utilisateur `www-data`, et les fichiers appartiennent a `root`. Le serveur web ne peut pas ecrire dedans, et il modifier le proprietaire du dossier.

```shell
chown [user]:[groupe] [file_name]

# transferer la propriete a user1 
chown user1 myfile.txt 

# transferer la propriete et le groupe 
chown user1:group1 myfile.txt

# changer uniquement le groupe
chowm :group2 myfile.txt
```

## umask 

Lorsqu'un fichier ou un dossier est creer, Linux part d'un ensemble de permission par defaut :
- fichier -> `666`
- dossier -> `777`

Linux applique ensuite le `umask` pour indiquer quels droit doivent etre retirer par defaut.

Le resultat donne les permission finales du nouveau fichier et dossier.

Par exemple : 
- ensemble des permissions de base pour un fichier : `666`  
- masque: `022` 
- permission finale : `644`

```shell
# voir le masque actuel 
umask 

# configuration du masque pour que les nouveaux fichier aient rw-r--r-- 
# les nouveau dossier auront rwxr-xr-x
umask 0022
```

- `022` : ou chaque chiffre correspond a un groupe

## sudo 

Commande qui permet a un utilisateur d'executer des commandes en tant que root. Il fournit un acces temporaire aux capacite d'admin sans avoir besoin de travailler sous le compte `root`.

Les configuration de sudo est placer dans `/etc/sudoers`.

```shell
sudo [commande]

# installation 
sudo apt install nginx

# executer une commande au nom d'un autre user 
sudo -u [username] [commande]
sudo -u user1 ls /home/user1

# obtenir un terminal root 
sudo -i
sudo -su 

# voir la configuration sudo 
sudo -l

# modifier la configuration sudo 
sudo visudo
```

