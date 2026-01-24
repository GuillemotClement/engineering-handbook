## Fedora 

### Installation de paquets

```shell
sudo dnf install <package>
```

---

## CLI 

### Terminal 

C'est le programme qui donne accès à la ligne de commande. Il permet d'intéragir avec le système d'exploitation en mode texte.

### Shell 

C'est le logiciel qui interprète les commandes et les transmets au système d'exploitation.

### Structure d'une commande 

```text
[Commande] [Options] [Arguments]
```

- `commande`: c'est ce qu'on demande de faire au système
- `option`: ou flags, permet de modifier le comportement de la commande.
- `arguments` : info supplémentaire passé à la commande

---
## COMMANDE DE BASE 
### WHOAMI

Affiche l'utilisateur actuel

```bash
$ whoami
étudiant
```

### Raccourcis 

- `Ctrl + C`: termine la commande en cours 
- `Ctrl + d`: termine la session en cours

---

## NAVIGATION DANS LE SYSTÈME DE FICHIER

### Répertoire principale 

- `/`: répertoire racine 
- `/home`: répertoire personnel d'un utilisateur (`/home/debian` par exemple)
- `/etc`: répertoire des fichiers de configuration du système
- `/var`: Répertoire des données dynamique comme les logs et fichier temporaire 
- `/tmp`: Répertoire des fichiers temporaires, supprimés régulièrement par le système
- `/dev`: périphérique connectées au système
- `/mnt`: points de montages pour les périphérique externes

### Chemin absolus et relatifs 

- **Chemin absolu**: Commence à partir de la racine `/` => `/home/student/documents`
- **Chemin relatif**: commence à partir du répertoire courant (celui dans lequel on est placé)


### pwd

Cette commande `Print Working Directory` permet d'afficher le répertoire de travail actuel. 

Permet de voir le path vers le folder dans lequel on se trouve.

```bash
$ pwd
/home/étudiant
```

### ls

Cette commande permet de lister le contenu d'un répertoire. Elle affiche les fichiers et les dossiers qui se trouvent dans le répertoire actuel.

```shell
$ ls 
#Documents  Téléchargements  Musique

$ ls -l
#drwxr-xr-x 2 student student 4096 Oct 21 10:00 Documents
#-rw-r--r-- 1 student student  234 Oct 21 09:45 notes.txt

$ ls -a
# .  ..  .bashrc  Documents  Downloads

$ ls -lh
#-rw-r--r-- 1 student student 4.0K Oct 21 10:00 file.txt

# on peut combiner les options
$ ls -la 
```

- `-l` : info détaillé sur les dossiers et fichiers. Affiche les droits d'accès, le groupe, la taille, la date de modification et le nom du fichier
- `-a`: affiche les dossiers cachées. Ils commencent par une `.`
- `-h`: affiche les tailles dans un format lisible

### cd

Cette commande permet de se déplacer dans les folders.

```bash
# changement de folder
$ cd /home

# accéder au répertoire personnel
$ cd ~ 

# retour d'un niveau 
$ cd ..

# accéder à la racine 
$ cd /
```

---

## OPERATIONS FICHIERS

### mkdir 

Cette commande permet de créer un nouveau répertoires. Elle créer le nouveau répertoire dans le répertoire courant.

```bash
mkdir [nom_dossier]

# exemple 
mkdir my_folder
```

Pour créer plusieurs dossiers imbriqués en une seule fois, on peut utiliser l'option `-p`

```bash
mkdir -p parent_folder/child_folder/sub_child
```

### touch 

Cette commande permet de créer un nouveau fichier vide. Le fichier sera créer dans le répertoire courant.

La commande met également à jour leur timestamps (données sur la date de création et de modification) même s'il n'existent pas.

```bash
touch [nom_fichier]

# exemple
touch my_file.txt

# création de plusieurs fichier 
touch file1.txt file2.txt
```

### rm 

#### Suppression de fichier

Cette commande permet de supprimer des fichiers ou des dossiers.

```shell
rm <file_name>

# exemple 
rm file1.txt
# supression multiple 
rm file1.txt file2.txt 
```

#### Suppression de dossier

Pour supprimer des dossiers et leur contenu, il faut utiliser le flag `-r` pour la récursion.

```shell
$ rm -r my_folder
``` 

### cp 

Cette commande permet de copier des fichiers.

```bash
cp [source] [destination]

cp my_file.txt backup_file.txt

# copie de plusieurs fichiers dans un dossier 
cp file1.txt file2.txt file3.txt backup/

# copie récursive 
cp -r my_folder backup_folder
```

Avec l'option `-r`, permet de copier tous le contenu du dossier

### mv 

Cette commande permet de déplacer un fichier ou un dossier d'un endroit à un autre 

```bash
mv [source] [destination]

mv my_file.txt /home/user/documents/
```

#### Renommage

On peut également utiliser cette commande pour renommer un fichier 

```bash
mv old_name.txt new_name.txt
```

---

## AFFICHAGE ET MODIFICATION DE FICHIER

### cat 

Cette commande permet d'afficher rapidement et simplement le contenu d'un fichier.

La commande affiche le contenu du fichier entierement, sans division de page. Si le fichier est trop gros, tout sera afficher dans le terminal de manière incontrôlée.

Si on oublie de préciser une entrée de donnée, on peut appuyer sur `Ctrl+D` pour terminer la commande.

```bash
cat [nom_fichier]

# exemple 
echo "Salut, Linux !" > example.txt
cat example.txt

# résultat 
Salut, Linux !

# combinaison de fichier dans un nouveau fichier 
cat file1.txt file2.txt > combined.txt
```

### less 

Lorsque le fichier est gros, on peut utiliser cette commande. Permet d'avoir une pagination qui rends la lecture du fichier plus simple.

```shell
less [nom_du_fichier]

# exemple 
less example.txt
```

|Touche|Action|
|---|---|
|`Space`|Aller à la page suivante|
|`b`|Revenir à la page précédente|
|`/texte`|Rechercher du texte dans le fichier|
|`q`|Quitter le programme `less`|

### nano 

Permet d'ouvrir l'éditeur de texte de base. 

```bash
nano [nom_du_fichier]

# exemple 
nano example.txt
```

|Combinaison|Action|
|---|---|
|`Ctrl+O`|Sauvegarder le fichier|
|`Ctrl+X`|Quitter l’éditeur|
|`Ctrl+K`|Couper une ligne|
|`Ctrl+U`|Coller une ligne|

### vim 

Éditeur dans le terminal. 

```bash
vim [nom_fichier]

# exemple 
vim exemple.txt
```

| Commande | Action                   |
| -------- | ------------------------ |
| `i`      | Passer en mode édition   |
| `Échap`  | Revenir en mode commande |
| `:w`     | Enregistrer le fichier   |
| `:q`     | Quitter l'éditeur        |
| `:wq`    | Enregistrer et quitter   |
| `:q!`    | Quitter sans sauvegarder |

---

## DROITS D'ACCES

Lorsque l'on travail avec des fichiers ou des répertoires sous Linux, il est parfois nécessaire de gérer qui peut y accéder. Ces droits d'accès permettent d'éviter des accès ou modification non autorisées.

Chaque répertoire et fichiers à trois ensembles de droits d'accès qui déterminent qui peut les lire, écrire ou exécuter.

- **Lecture (r)**: permet de voir le contenu d'un fichier ou la liste des fichiers dans un répertoire
- **Ecriture (w)**: autorise la modification du contenue du fichier, ou l'ajout/suppression des fichiers du répertoire 
- **Exécution (x):** donne la possibilité d'exécuter le fichier comme un programme ou d'entrer dans le répertoire

![[Pasted image 20260124112543.png]]

Chaque droit s'applique à trois groupes d'utilisateurs :

- **Propriétaire (owner)**: celui qui a créé le fichier 
- **Groupe (group)**: les utilisateurs regroupés dans un groupe auquel le fichier est attribué 
- **Autre (others)**: tous les utilisateurs du système

Exemple de droits d'accès pour un fichier :

```text
-rwxr-xr--
```

- Le premier caractère `-` indique que c'est un fichier 
- `rwx` - droit pour le propriétaire (lecture, écriture, exécution)
- `r-x` - droit pour le groupe (lecture, exécution)
- `r--` - droit pour les autres (lecture)

### chmod 

Cette commande permet de modifier les permissions des fichiers et des répertoires

```text
chmod [qui][action][droits] nom_fichier

# Donnons les droits d'exécution à tous
chmod a+x script.sh

# Ajoutons les droits d'écriture uniquement pour le groupe
chmod g+w file.txt

# Retirons le droit de lecture pour les autres
chmod o-r document.txt
```

- **Qui**: `u` - propriétaire; `g` - groupe; `o` - autre; `a` - tous
- **Action**: `+` - ajouter un droit; `-` retirer un droit; `=` - définir exactement les droits 
- **Droits**: `r` - lecture; `w` - écriture; `x` - exécution 

#### Format octal 

![[Pasted image 20260124113329.png]]

Le format octal (numérique) est plus compact mais est moins lisible. Dans ce format, chaque combinaison de droits est représentée par un chifre.

- `r = 4`
- `w = 2`
- `x = 1`

Les droits s'additionnent :
- `rwx = 7`
- `rw- = 6`
- `r-- = 4`

```text
chmod [chiffre][chiffre][chiffre] nom_fichier

# Donnons les droits rwx au propriétaire, r-x au groupe, r-- aux autres
chmod 754 file.sh
```

### chown 

Cette commande permet de transférer un fichier à un autre utilisateur.

```text
chown [utilisateur]:[groupe] nom_fichier

# Transférer la propriété à l'utilisateur user1
chown user1 myfile.txt

# Transférer la propriété et le groupe
chown user1:group1 myfile.txt

# Changer uniquement le groupe
chown :group2 myfile.txt
```

### umask 

`umask` détermine quelles permissions ne seront pas définie pour les nouveaux fichiers. C'est avec cette commande que les permissions sont définie par le système.

```text
# Configuration du masque pour que les nouveaux fichiers aient rw-rw-r--
umask 0002
```

### sudo 

Cette commande permet d'avoir les droits administrateurs pour une commande. 

```bash
# lancer une commande avec des privileges 
sudo <commande>

# exécuter une commande au nom d'un autre utilisateur 
sudo -u <nom_utilisateur> <commande>

# ouvrir un terminal admin 
sudo -i 
# ou 
sudo su

# voir la configuration sudo 
sudo -l

# modifier la configuration 
sudo visudo
```

La configuration `sudo` est placé dans le path `/etc/sudoers`

---

## GESTION UTILISATEURS

Souvent sous Linux, il y a plusieurs utilisateurs qui travaillent sur le même serveur. Le système leur attribue des droits spécifiques pour prôtéger les données et éviter le chaos. 

### adduser 

Cette commande permet de créer de nouveau utilsateur.

```bash
adduser nom_utilisateur

# exemple 
sudo adduser dev_user

# on confirme la création de l'utilisateur 
cat /etc/passwd | grep dev_user # retourne des informations sur l'user crée
```

Lorsqu'un nouvel utilisateur est créer : 
1. Une nouvelle entrée est créer dans le fichier `/etc/passwd` pour l'utilisateur 
2. Un répertoire personnel est généré (par exemple `/home/username`)
3. Les paramètres de base sont configurés 
4. On demande une saisis de mot de passe et des informations supplémentaires facultative

### usermod 

Lorsqu'un utilisateur est crée, il peut être nécessaire de modifier ces paramètres. Par exemple, changement de nom, spécifie un shell, l'ajouter à un groupe.

#### Changement de nom 
```shell
# changement de nom
sudo usermod -l nouveau_nom ancien_nom
sudo usermod -l developer dev_user #exemple
```

#### Changement du shell par défaut 
```shell
# changement de shell 
sudo usermod --shell /bin/zsh developer
# check si le shell à changé 
cat /etc/passwd | grep developer
```

#### Changement du repertoire personel
```bash
# modification du repertoire personel
sudo usermod -d /nouveau/chemin developer
# si le repo n'existe pas, on utilise l'option -m
sudo usermod -d /home/new_developer -m developer
```

### passwd 

Cette commande permet de configurer le mot de passe d'un utilisateur. 

Au changement de mot de passe, le système demande deux saisie.

```bash
sudo passwd nom_utilisateur
# exemple 
sudo passwd developer
```

#### Forcer le changement de mot de passe à la prochaine connexion 

Lorsque l'utilisateur se connecte, il voit un message lui demandant de changer son mot de passe.
```bash
sudo passwd -e developer
```

---

## GESTION DES GROUPES 

Les groupes permettent de regrouper des utilisateurs par intérêts, responsabilités ou d'autres critères et de leur attribuer des droits d'accès communs.

Lorsque le système vérifie si un utilisateur peut accéder à un fichier, il regarde trois niveau de droits : propriétaire du fichier, groupe et les autres.
Si l'utilisateur fait partie du groupe spécifié pour le fichier et que le groupe a les droits d'accès, alors il peut accéder.

Les changements prennent effet avec une reconnexion.

Les groupes sont particulierement utile pour : 
- diviser les droits d'accès aux ressources serveur entre les développeurs, testeurs et admin
- configurer des repo de travail partagés pour les équipes
- gérer les accès aux base de données, fichiers de configurations, script
### groupadd

Cette commande permet de créer un nouveau groupe. Les groupes sont stocké dans  un fichier `/etc/group`

```bash
# création d'un nouveau groupe
sudo groupadd developers

# vérification 
cat /etc/group | grep developers # retourne: developers:x:1001:

```

- `developers`: le nom du groupe
- `x` : champ pour le mot de passe 
- `1001`: ID unique du groupe (GID)
- L'espace vide avec le deuxième `:` : c'est ici que sont ajoutée les utilisateurs du groupe 

### usermod -aG

Cette commande avec ces options permettant d'ajouter un utilisateur dans un groupe.

```bash
sudo usermod -aG <group> <user>
sudo usermod -aG developers john
```

- `-a` : signifie append. Si ce flag est oublier, on risque de supprimer l'utilisateur de tous les groupes sauf celui spécifié
- `-G` : spécifie le groupe auquel l'utilisateur est ajouté 

Pour vérifier à quels groupe appartient l'utilisateur, on utilise `groups`

```bash
groups john
```

Pour vérifier via l'id 
```bash
id john
```

On obtient en sortie ce type de retour 

```text
uid=1002(john) gid=1002(john) groups=1002(john),1001(developers)
```

### gpasswd

Cette commande permet de retirer un utilisateur du groupe. 

```bash
sudo gpasswd -d <user> <group>
sudo gpasswd -d john developers
```

Une fois supprimer, on peut également vérifier si l'user est bien supprimer du groupe 

```bash
groups <user>
```

### groupdel

Cette commande permet de supprimer un groupe
```bash
sudo groupdel <groupe>
```


---

## VERIFICATION DES DROITS 

### whoami 

Cette commande permet de savoir sous quel compte on est connecté sur le serveur. Elle affiche le nom de l'utilisateur actuel sous lequel le travail est effectué dans le système.

```bash
whoami
```

### id 

Cette commande retourne une liste d'informations plus détaillées. Par exemple, les droits et privilèges dans le système.

Elle retourne : 
- `UID` : User id - l'identifiant unique de l'utilisateur 
- `GID`: Group ID - identifiant du groupe principal de l'utilisateur 
- Liste des groupes: tous les groupes auxquels appartient l'utilisateur 
```bash
id [utilisateur]

# uid=1001(student) gid=1001(student) groupes=1001(student),27(sudo),1002(developers)
```

Si le nom d'utilisateur n'est pas spécifié, la commande affiche les informations sur l'utilisateur actuel. 

### groups 

Cette commande permet de connaitre les groupes auxquels appartient l'utilisateur actuel

```bash
groups [utilisateur]
# student developers sudo
```

Si on ne passe pas d'utilisateur, on obtient la liste des groupes de l'utilisateur actuel.

---

## PROCESSUS SYSTEME

Un processus est un instance exécutable d'un programme qui existe en mémoire. Lorsque l'on lance une application ou exécute un fichier en ligne de commande, un processus est créé, qui effectue ensuite ses tâches jusqu'a sa terminaison.

Les processus sont géré par le noyaux Linux, et chacun possède son propre identifiant `PID`

### Trouver le PID d'un processus 
```bash
ps aux | grep firefox
```

Cette commande permet de trouver le processus de Firefox et affiche son PID 
### ps 

Cette commande permet d'avoir un vue statique des processus. Elle permet d'obtenir des infos sur les processus en cours : ID, propriétaire, ligne de commande et ressources utilisées

```bash
# command de base 
ps

# affiche plus de détails 
ps aux 
```

- `a` : montre les processus d'autres utilisateurs
- `u`: ajoute un format lisible 
- `x` : inclut les processus qui ne sont pas attachés à un terminal

Exemple de sortie : 
```plaintext
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.1  22556  1044 ?        Ss   12:34   0:01 /sbin/init
student      2345  0.1  0.2  34567  2048 pts/0    S+   12:35   0:02 bash
student      4567  0.3  0.5  56789  5144 pts/0    R+   12:36   0:03 ps aux
```

- `USER` : utilisateur ayant lancé le processus 
- `PID` : identifiant du processus 
- `%CPU`: pourcentage d'utilisation du CPU 
- `%MEM`: pourcentage d'utilisation de la mémoire 
- `COMMAND`: commande ayant initié le processus 

#### Structure en arbre des processus 

```bash
ps -ejH
```

Cela permet d'afficher quels processus sont les enfants d'autre processus 

#### Filtrer par PID 
```bash
ps -p 1234
```

Affiche uniquement le processus spécifié 

#### Affiche tous les processus 
```bash
ps -ef
```

### top 

Cette commande permet d'affiche la surveillance dynamique des processus. Elle permet de voir comment évolue les procesus en temps réel. 

Lorsque des ralentissements sont présent, on peut utiliser cette commande pour vérifier quel processus consomment le plus de CPU et de mémoire.

![[Pasted image 20260124132628.png]]

- La première ligne montre la charge du système 
- La seconde donne des informations sur les tâches (active, total, en sommeil, zombies)
- Affiche la mémoire 
- La bloc du bas est la liste de processus 

On peut utiliser des commande pour exécuter des opérations : 
- `k`: permet de terminer un processus en passant le PID 
- `P`: permet de trier les processus par CPU 
- `M`: permet de trier par utilisation de mémoire
- `q` : permet de quitter 

### htop 

Amélioration de `top`. Elle affiche une interface plus visuel pour les processus.

Généralement, il est nécessaire de l'installer pour l'utiliser 

```shell
sudo apt-get install htop # Pour les distributions basées sur Debian sudo yum install htop     # Pour les distributions basées sur RedHat
```

Pour lancer l'outil :

```bash
htop
```

### kill

Cette commande permet de terminer un processus en utilisant le PID. 

La commande envoie une requête au noyau appeler un signal. Par défaut, le signal est `SIGTERM(15)`.

```bash
kill <PID>
```

Linux utilise des signaux pour communiquer avec les processus :
- `SIGTERM(15)`: termine le processus
- `SIGKILL(9)` : force l'interuption du processus 
- `SIGHUP(1)` : utilsié pour redémarrer certains processus sans les arrêter 

```bash
kill -9 1234
```

### killall 

Cette commande permet de terminer tous les processus avec un nom spécifié. 

```bash
killall <nom_processus>
```

### pkill

Cette commande permet de terminer les processus qui correspondent à un certain modèle.

```bash
pkill fire
```

La commande termine tous les processus qui contiennent le mot "fire" dans leur nom.

On peut également terminer des processus appartenant à l'utilisateur 

```bash
pkill -u username
```

---

## GESTION DES SERVICES 

Les services sont la base de toute distribution Linux. Par exemple, Nginx est un service, Postgres est un service, etc. 

C'est un programme qui fonctionne en arrière plan de manière persistante (`daemon`).
Ces programmes peuvent se lancer de manière automatique au démarrage du système ou manuellement si besoin.

Ils sont responsable de tâches spécfiques comme :
- Gestion des requête web (Nginx)
- Support d'accès à distance (`openssh`)
- Lancement de tâche planifiées (`cron`)

Les services peuvent avoir différents état :
- **Active (running)**: le service fonctionne 
- **Inactive (dead)**: le service est arrêté 
- **Failed** : une erreur s'est produite au démarrage, de l'arrêt ou de l'exécution du service 

### systemctl 

C'est la commande principale pour gérer les services dans les systèmes utilisant `systemd`. 
Il a pour fonction principale : 
- **Démarrage des services**
- **Arrêt d'un service**
- **Redémarrage d'un service**
- **Vérification du statut**

```bash
sudo systemctl [action] [nom_du_service]
```

#### Démarrage d'un service 

```bash
sudo systemctl start <service>
sudo systemctl start nginx
```

#### Arrêt d'un service 

```bash
sudo systemctl stop <service>
sudo systemctl stop nginx
```

#### Redémarrage d'un service 
Le redémarrage est utile pour appliquer des modifications ou simplement rafraîchir le service.
```bash
sudo systemctl restart <service>
sudo systemctl restart nginx
```

#### Vérification du statut 

Permet de vérifier le status d'un service. Il fournit le PID, l'heure de démarrage et les logs erreurs

```bash
sudo systemctl status <service>
sudo systemctl status nginx
```

#### Activer le démarrage automatique 
Pour que le service démarre automatiquement lorsque le système démarre 
```bash
sudo systemctl enable <service>
sudo systemctl enable nginx
```

#### Désactiver le démarrage automatique 
```bash
sudo systemctl disable <service>
sudo systemctl disable nginx
```

#### Vérifier si le démarrage automatique est activé 
```bash
systemctl is-enabled <service>
systemctl is-enabled nginx
```






## PIPE 

Les pipes `|` sont un mécanisme qui permet de passer la sortie d'une commande (stdout) comme entrée (stdin) pour une autre commande.

Cet outil permet de combiner des commandes en créant des pipelines pour le traitement des données.

Lorsque l'on utilise un pipe entre des commandes, Linux redirige la sortie standard de la première commande vers l'entrée standard de la suivante.
Cela permet de traiter efficacement de gros volumes de données sans fichier temporaires.

```bash
commande1 | commande2 | commande3
```

- `commande1` : génère des données 
- `commande2` : traite les données reçues
- `commande3`: effectue un traitement supplémentaire ou enregistre le résultat

#### Afficher la liste des fichiers et filtrer par modèle

```bash
ls -l | grep ".txt"
```

Ici `ls -l` affiche la liste des fichiers, et `grep` filtre uniquement les fichiers avec l'extension `.txt`

#### Compter les lignes contenant un mot spécifique 
```bash
cat file.txt | grep "Linux" | wc -l
```

On affiche le contenu du fichier, on cherche les lignes contenant "Linux" et on compte leur nombre 

---

## GREP 

`grep` (Global Regular Expression Print) est un utilitaire en ligne de commande pour rechercher les lignes dans des fichiers qui correspondent à un modèle spécifié. Il est utilisé pour filtrer et rechercher des données grâce au support des expression régulières et à ses paramètres flexibles.

- `grep` prend en entrée un fichier texte ou une `stdout`, recherche les lignes qui correspondent au modèle spécifié et les affiche.
- Si la modèle n'est pas trouvé, la commande de produit aucune sortie.























## LIEN SYMBOLIQUE

Les liens symbolique permettent de lier deux dossier. L'un contient les fichier, et le second est synchroniser avec le premier.

De cette manière, on place le projet dans le folder des projets. On viens `pull` les updates, et on viens build le projet.

Le symlink fera la syncronisation automatique, et le folder qui est fournis par Nginx aura la mise à jour.

```shell
ln -s <path_projet_parent> <path_esclave>

# exemple
sudo ln -s /home/debian/projects/Ubrun/front/dist /var/www/html/ubrun
```

Une fois le lien symbolique créer, on peut lancer la commande pour vérifier si le lien est bien créer 

```shell
ls -la <folder_esclave>

# exemple 
ls -la /var/www/html

# result 
mon-site -> /home/user/mon-app/dist
```

Pour supprimer, on utilise la commande 

```shell
sudo rm -rf /var/www/html/ubrun
```