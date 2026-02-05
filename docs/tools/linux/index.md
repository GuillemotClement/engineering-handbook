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

### Lien symbolique

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



---
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

#### Retourner les service en erreur 
```bash
systemctl --failed
```

#### Erreur typique 

Le service nécessaire n'est pas installé, ou que son nom est incorrect 
```text
Failed to start nginx.service: Unit nginx.service not found.

# pour vérifier le nom 
systemctl list-unit-files | grep nginx
```

---

## JOURNALISATION 

Les journaux ou logs enregistre chaque opération importante dans le système. Ils consignent les actions du kernel, des applications, des services et d'autres composants du système. 

Ils permettent de diagnostiquer les problèmes et surveiller le fonctionnement du système.

### journalctl

Cette commande permet de consulter et filtrer les logs. 

Elle affiche tous les logs systèmes, triés par ordre chronologique. 

```bash
journalctl
```

#### Filtrage par intervalle de temps 
Consulter les logs de la dernière heure
```bash
journalctl --since "1 hour ago"
```

Consulter les logs d'un jour spécifique
```bash
journalctl --since "2023-10-31"
```

Indiquer une page de temps spécifique, par exemple les event de 8h à 9h
```bash
journalctl --since "2023-10-31 08:00:00" --until "2023-10-31 09:00:00"
```

#### Filtrage par service 
On peut venir spécifier le service pour afficher ces logs 
```bash
journalctl -u <service>
journalctl -u nginx

# afficher les logs depuis 1 heures 
journalctl -u nginx --since "1 hour ago"
```

#### Logs temps réel 
```bash
journalctl -f
```

#### Rechercher une erreur 
```bash
journalctl | grep "ERROR"
```

Avec cette commande, seule les logs avec le mot `ERROR` seront affichées.

#### Voir les entrée uniquement après redémarrage 
Permet de voir les logs après le dernier démarrage du système
```bash
journalctl -b

# voir avant dernier démarrage 
journalctl -b -1
```

#### Filtrer par utilisateur ou PID 
```bash
journalctl _UID=<id_user>
journalctl _UID=1000

journalctl _PID=<PID>
journalctl _PID=1234
```

---

## GESTION TEMPS 

La configuration est très importante dans un journal système, pour le fonctionnement du système, la synchronisation des fichiers, le maintien de la sécurité (certificat SSL) et la coordination des tâches de travails.

Dans Linux, le temps pour être représenté sous deux formes :
- **Temps système (System Time)**: le temps que le système d'exploitation utilise pour ces tâches
- **Temps matériel (Hardware Time)** : stocké au niveau du matériel (BIOS/UEFI)

### timedatectl 

Utilitaire CLI qui fournit une interface pratique pour gérer l'heure. Il permet de :
- Vérifier les paramètres actuel de l'heure et du fuseau horaire 
- Configurer l'heure système et matériel 
- Paramétrer les fuseaux horaire
- Activer et désactiver la synchro de l'heure aec NTP

Pour vérifier l'heure actuel, on utilise cette la commande 

```bash
timedatectl
```

On obtient cette sortie 

```text
Local time: Tue 2023-10-31 12:34:56 MSK
Universal time: Tue 2023-10-31 09:34:56 UTC
RTC time: Tue 2023-10-31 09:34:56
Time zone: Europe/Moscow (MSK, +0300)
System clock synchronized: yes
NTP service: active
RTC in local TZ: no
```

- `Local time`: heure locale (système)
- `Universal time`: heure au format UTC 
- `RTC time`: heure matérielle 
- `Time zone`: fuseau horaire actuel 
- `System clock synchronized`: si l'horloge est syncronisée via NTP 
- `NTP service`: si le service de synchro de l'heure est actif 
- `RTC in local TZ`: si l'heure matérielle correspond au fuseau d'horaire local 

#### Lister les fuseaux horaire disponible 

Les fuseau horaire sont listé dans le répertoire `/usr/share/zoneinfo`.

Pour lister les fuseaux 
```bash
timedatectl list-timezones
```

#### Définir un nouveau fuseau 
```bash
sudo timedatectl set-timezone Europe/Moscow
```

### Configuration manuelle de l'heure 

Il est parfois nécessaire de régler manuellement l'heure. C'est utile dans le cas où le serveur se trouve sur un réseau isolé sans accès à internet.

#### Afficher l'heure du système 

```bash#
date
```

#### Définir une nouvelle heure 
```bash
sudo timedatectl set-time "YYYY-MM-DD HH:MM:SS"

# exemple
sudo timedatectl set-time "2023-11-01 12:00:00"
```

### Configuration de l'horloge matérielle 
L'horloge matériel (RTC) est géré au niveau du BIOS. Si l'horloge matériel n'est pas synchronisée avec l'heure système, cela peut provoquer des problèmes lors du redémarrage.

#### Synchronisation des horloges 
Cette commande vient définir l'horloge matérielle en fonction de l'heure système.
```bash
sudo hwclock --systohc
```

Pour synchroniser l'heure système avec l'horloge matérielle : 
```bash
sudo hwclock --hctosys
```

### Synchronisation du temps avec NTP 
Cette synchronisation du temps avec NTP est une méthode pour garder automatiquement l'heure à jour en utilisant des serveurs distants. Généralement activée par défaut.

```bash
# affiche la configuration
timedatectl status

# activer la synchro
sudo timedatectl set-ntp true

# désactiver 
sudo timedatectl set-ntp false

# vérifier si le système est actif (en cas d'erreur )
sudo systemctl status systemd-timesyncd
```

---

## SCRIPT BASH 
Les scripts bash sont utilisé pour automatiser le travail en écrivant une séquence de commande dans un seul fichier. 

Il permet de créer des routines, des commandes personalisées pour les sauvegardes, la surveillance des ressources ou la gestion des fichiers.

### Création d'un script bash 

Un script bash est un fichier de texte avec une liste de commande. Pour que Linux comprenne que c'est un script, il faut respecter certaines règles.

Les scripts Bash utilise généralement une extension `.sh`. 

Ils doivent obligatoirement commencer par une ligne d'en tête, qui s'appelle le `shebang` et qui indique au système qu'il doit utiliser l'interpréteur `/bin/bash` pour exécuter le script.

```bash
#!/bin/bash
```

Pour définir un nouveau script, on commence par écrire le shebang, puis on ajoute ensuite les commandes.

```bash 
# hello_world.sh
-------------------
#!/bin/bash
echo "Hello world"
```

Une fois le script écrit, on doit ensuite le rendre exécutable avec cette commande : 
```shell
chmod +x hello_world.sh
```

On peut ensuite lancer le script avec cette commande : 
```shell
./hello_world.sh
```
Avec cette commande, on lance l'exécution du script.

### Utilisation des variables 
Pour déclarer une variable 
```bash
NOM_DE_VARIABLE="valeur"

# exemple 
NAME="Linux"
echo "Bienvenue à $NAME!"
```

### Variable intégrées
- `$USER` : utilisateur actuel 
- `$HOME`: répertoire personnel 
- `$PWD` : répertoire de travail actuel 

```bash
`#!/bin/bash 
echo "Salut, $USER!" 
echo "Ton répertoire personnel : $HOME" 
echo "Tu travailles ici en ce moment : $PWD"`
```

### Récupérer une saisis utilisateur 
Pour récupérer une saisie utilisateur, on utilise `read`
```bash
#!/bin/bash
echo "Comment tu t'appelles ?"
read NAME
echo "Salut, $NAME !"
```
Lorsque l'on lance le script, cela viens ajouter un prompt permettant de récupérer la saisie utilisateur.

### Utiliser des commandes dans un script 
Bash permet d'exécuter des commandes comme si on les tapais dans un terminal
```bash
#!/bin/bash
echo "Date et heure actuelles :"
date
```

### Enregistrer le résultat d'une commande dans une variable 
Il est possible d'enregistrer la sortie d'une commande dans une variable en utilisant `$()`.
```bash
#!/bin/bash
CURRENT_DATE=$(date)
echo "Maintenant : $CURRENT_DATE"
```

### Commentaire 
Le caractère `#` permet d'ajouter un commentaire dans un script 
```bash
#!/bin/bash
# Ceci est un script de bienvenue
echo "Bienvenue dans le monde de Bash !"
```

#### Exemple d'un script qui vérifie si un fichier existe 
Dans un nouveau fichier `file_checker.sh`
```bash
#!/bin/bash

# Demande du nom de fichier
echo "Entrez le nom du fichier :"
read FILE_NAME

# Vérifie si le fichier existe
if [ -f "$FILE_NAME" ]; then
    echo "Le fichier $FILE_NAME existe."
else
    echo "Le fichier $FILE_NAME est introuvable."
fi
```

On rend ensuite le script exécutable et on le lance 
```bash
chmod +x file_checker.sh
./file_checker.sh
```

On obtient cette sortie dans le terminal :
```text
Entrez le nom du fichier :
test.txt
Le fichier test.txt existe.
```

### Condition 

Permet de prendre des décision en se basant sur des données entrantes, des variables ou des résultats d'exécution de commande. 
Par exemple, vérifier si un fichier existe ou s'assure qu'un serveur est accessible avant de passer à l'action suivante.

```bash
if [ condition ]; then
    # Ici, le code s'exécute si la condition est vraie
    echo "La condition est remplie !"
else
    # Ici, le code s'exécute si la condition est fausse
    echo "La condition n'est pas remplie !"
fi
```

#### Opérateur de comparaison Bash 

Pour les nombres :
- `-eq` : égal
- `-ne`: différent 
- `-lt` : inférieur à
- `-le` : inférieur ou égale
- `-gt` : supérieur 
- `-ge` : supérieur ou égale 

Pour les chaîne :
- `=` ou `==` : égale à
- `!=`: différent de

#### Vérification de l'existence d'un fichier 

```bash
#!/bin/bash

FILE="/etc/passwd" # on passe dans une variable le fichier rechercher

# si le fichier existe
if [ -f $FILE ]; then
    echo "Le fichier $FILE existe."
else
    echo "Le fichier $FILE est introuvable."
fi
```

- `-f` : vérifie si le fichier existe 

#### Condition avec des commandes 

On peut utiliser le résultat d'exécution d'une commande comme condition.

Dans ce script, on viens vérifier la disponibilité d'un site web
```bash
#!/bin/bash

if ping -c 1 example.com &> /dev/null; then
    echo "Le site est accessible."
else
    echo "Le site est inaccessible."
fi
```

- `ping -c 1`: envoie une requête au serveur. Si le serveur répond, la condition est vraie 
- `&> /dev/null`: cache la sortie de la commande pour ne pas flood le terminal

#### Condition sur la sortie d'une commande 

```bash
# on fais une commande
git pull

# on viens vérifie que la commande n'as pas retourné d'erreur 
if [ $? -eq 0 ]; then 
	echo "La commande à réussis"
else 
	echo "La commande à échouer"
fi 
```

On peut également utiliser cette forme 

```bash
# on vérifie directement le retour de la commande 
if git pull; then 
	echo "La commande à réussis"
else 
	echo "La commande à échouer"
fi 
```


### Elif 

Permet de vérifier plusieurs condition à la suite.

Dans ce script, on viens déterminer le moment de la journée
```bash
#!/bin/bash

HOUR=$(date +%H)

if [ $HOUR -lt 12 ]; then
    echo "Bonjour !"
elif [ $HOUR -lt 18 ]; then
    echo "Bon après-midi !"
else
    echo "Bonsoir !"
fi
```

- `date +%H` : retourne l'heure actuelle au format 24h
- on compare l'heure actuel avec des valeurs fixe

### WHILE 

Permet d'exécuter des actions tant qu'une condition est valider
```bash
#!/bin/bash

SECRET=5
GUESS=0

while [ $GUESS -ne $SECRET ]; do
    echo "Saisis ton estimation (nombre de 1 à 10) :"
    read GUESS
done

echo "Tu as deviné !"
```

- La boucle continue tant que le nombre saisie `GUESS` n'est pas égale au nombre secret 

### Combiner des combinaison et des boucles 
Dans ce script, on veux venir vérifier plusieurs site web 
```bash
#!/bin/bash

SITES=("example.com" "google.com" "nonexistent.website")

for SITE in ${SITES[@]}; do
    if ping -c 1 $SITE &> /dev/null; then
        echo "$SITE est accessible."
    else
        echo "$SITE est inaccessible."
    fi
done
```

- Le tableau `SITES` contient une liste de site web
- `for` parcourt chaque site du tableau 
- `if` vérifie l'accessibilité du site avec `ping`

#### Vérification de domaine 
Le script prends un nom de domaine comme argument et vérifie sa disponibilité 
```bash
if ping -c 1 $1 &> /dev/null; then
    echo "Domaine disponible."
else
    echo "Domaine indisponible."
fi
```

#### Travail avec des fichiers 
Un script qui vérifie l'existence de plusieurs fichiers et on affiche le résultat pour chacun d'eux 
```bash
FILES=("file1.txt" "file2.txt" "/etc/passwd")
for FILE in ${FILES[@]}; do
    if [ -f $FILE ]; then
        echo "$FILE existe."
    else
        echo "$FILE pas trouvé."
    fi
done
```

#### Table de multiplication 
Un script avec des boucles imbriquées qui affiche une table de multiplication pour les nombres de 1 à 10
```bash
for i in {1..10}; do
    for j in {1..10}; do
        echo -n "$((i * j)) "
    done
    echo ""
done
```

---

## AUTOMATISATION 

### cron

Demon qui fonctionne en arrière plan et exécute des tâches programmées à des moments spécifiques. 
Les horaire de ces tâches sont stockés dans un `crontab` qui sont des fichiers texte.

Chaque utilisateur du système peut avoir son propre `crontab`.

```bash
crontab -e
```

Cette commande ouvre le `crontab` de l'utilisateur actuel pour modification. On peut ajouter des tâches en indiquant le format de l'horaire.

Une ligne dans `crontab` se compose de 6 champs
```bash
* * * * * COMMANDE
- - - - -
| | | | └─ Jour de la semaine (0–7, où 0 et 7 représentent dimanche)
| | | └── Mois (1–12)
| | └─── Jour du mois (1–31)
| └──── Heures (0–23)
└───── Minutes (0–59)
```

Par exemple, exécuter la commande chaque jour à midi : 
```bash
0 12 * * * echo "Hello, Linux"
```

Dans un autre exemple, on ajoute une nouvelle ligne de log toutes les 5 minutes :
```bash
*/5 * * * * echo "Hello, world! The time is $(date)" >> ~/cron_test.log
```

Pour vérifier que la tâche à démarré :
```bash
tail -f ~/cron_test.log
```

Pour vérifier les tâches planifiées 
```bash
crontab -l
```

Pour supprimer des tâches 
```bash
crontab -r
```

#### Archivage des fichiers
On souhaite archiver des fichiers du répertoire `/var/logs` chaque nuit à 3 heures:
```bash
#!/bin/bash
tar -czf /backup/logs_$(date +\%Y-\%m-\%d).tar.gz /var/logs
```
On définit ensuite un horaire dans le `crontab`
```bash
0 3 * * * /path/to/script.sh
```

#### Notification de charge système
On souhaite recevoir une notification si la charge CPU dépasse 80%
```bash
#!/bin/bash
LOAD=$(uptime | awk '{print $10}' | sed 's/,//')
if (( $(echo "$LOAD > 0.80" | bc -l) )); then
    echo "Charge CPU élevée : $LOAD" | mail -s "Alerte CPU" you@example.com
fi
```
On configure le cron
```bash
*/10 * * * * /path/to/cpu_check.sh
```

### at 

Utilitaire pour des tâches uniques qui doivent être exécutées à un moment précis. Les tâches ne se répètent pas.

```bash
echo "echo 'Hello, Linux!'" | at now + 1 minute
```
Cette commande exécute la commande dans une minute.

Il est possible de spécifier une heure et une date précise :
```bash
echo "echo 'Backup completed!'" | at 10:30 AM tomorrow
```

#### Gestion des tâches
Après avoir ajouté une tâches, on peut voir la liste :
```bash
atq
```

Pour supprimer une tâche
```bash
atrm <job_id>
```

### Script bash pour automatiser des tâches avec cron 

On souhaite écrire un script qui vérifie la disponibilité d'un site web et enregistre le résultat dans un fichier log puis on automatise son exécution avec cron.

On commence par écrire un script bash.
```bash
#!/bin/bash

# Définir l'adresse du site web
WEBSITE="example.com"

# Fichier pour enregistrer les résultats
LOG_FILE="/var/log/site_status.log"

# Vérification de la disponibilité du site avec ping
if ping -c 1 $WEBSITE &> /dev/null; then
    # Si le site est disponible
    echo "$(date): $WEBSITE est joignable" >> $LOG_FILE
else
    # Si le site est indisponible
    echo "$(date): $WEBSITE est non joignable" >> $LOG_FILE
fi
```

On vient ensuite créer une tâche planifié avec cron 
```bash
*/5 * * * * /path/to/site_check.sh
```

- `*/5`: indique que la tâche doit s'effectuer toutes les 5 minutes 
- `/path/to/site_check.sh`: chemin complet vers le script.

---

## TRAVAIL RESEAU

Un réseau est un ensemble d'ordinateurs connectés pour échanger des données. 
- **Adresse IP**: identifiant unique d'un appareil dans le réseau
- **Sous-Réseau**: regroupement logique d'appareil dans un réseau. 
- **Passerelle**: c'est la sortie vers le monde, par laquelles toutes les appareils du réseau local peuvent accéder à internet ou se connecter à d'autre sous-réseaux

### ping 

Utilitaire qui permet de vérifier si un noeu du réseau est accessible. Il envoie une requête `ICMP` et attends une réponse.

```bash
ping 8.8.8.8
```

Cette commande envoie un ping vers le serveur, et retourne le résultat :
```plaintext
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=10.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=117 time=10.2 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=117 time=10.3 ms
```

- `icmp_seq`: le numéro de la requête envoyée
- `ttl` : le time to live du paquet (combien de saut il peut effectuer)
- `time`: le temps en ms pour envoyer et recevoir la réponse

On peut limiter le nombre de requête avec `-c`

```bash
ping -c 4 8.8.8.8
```

Cette commande enverra 4 requêtes.

### id addr 

L'interface réseau est ce qui permet à un ordinateur de parler avec le réseau. Cela peut être une interface pour Ethernet ou WLAN, ou des interfaces virtuelles créées pour des tâches spécifiques.

Cette commande affiche la configuration actuelle des interfaces : 
```bash
ip addr
```

Elle retourne ce type de résultats : 

```text
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic enp0s3
    inet6 fe80::1a2b:3c4d:5e6f:f7g8/64 scope link
```

- `lo`: interface local (localhost).
- `enp0s3`: nom de l'interface réseau de l'adaptateur Ethernet 
- `inet`: adresse IPV4
- `inet6`: adresse IPV6

#### Configurer une adresse IP temporaire 
```bash
sudo ip addr add 192.168.1.101/24 dev enp0s3
```

L'adresse sera valable jusqu'au prochaine redémarrage 

---

### ifconfig 
C'est l'ancienne commande, qui est progressivement remplacer par `ip addr`. Il permet également de gérer les interfaces réseau.

Pour vérifier l'interface:
```bash
ifconfig
```

On obtient un résultat similaire.

#### Désactiver ou activer une interface 
```bash
sudo ifconfig enp0s3 down
sudo ifconfig enp0s3 up
```

---

## CONFIGURATION DES ROUTES

Le routage est ce qui permet de diriger les requêtes. Chaque appareil sur le réseau à besoin de savor comment atteindre les autres appareils. 

Si un ordinateur veut envoyer une requête au site `example.com`, il doit connaitre :
- L'adresse de destinations 
- Par quelle passerelles ou quelle interface le faire 

Il existe deux types de routes :
- **Routes locales**: indications pour l'appareil du sous réseau 
- **Routes externes**: indications pour les appareils en dehors du sous réseau 

### ip route 
La table de routage est une table avec des règles qui définissent où envoyer les paquets en fonction des adresse IP.

Pour afficher la table de routage : 
```bash
ip route show
```

La commande retourne : 
```text
default via 192.168.1.1 dev eth0 proto dhcp metric 100
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.100 metric 100
```

- `default via 192.168.1.1`: si un paquet n'as pas de destination précise, envoie le via le gateway avec l'adresse `192.168.1.1`
- `dev eth0`: on utilise l'interface `eth0`
- `192.168.1.0/24`: route pour tout le sous réseau. 
- `proto` et `metric`: paramètre supplémentaire 

#### Voir les routes 

Pour visualiser la table de routage, on utilise la commande, mais on peut également utiliser des filtres, pour identifier une route via le réseau local : 
```bash
ip route show match 192.168.1.0/24
```

#### Ajouter des routes 
Il est possible d'ajouter une route statique, pour par exemple rediriger toutes les requêtes vers un sous réseau spécfique via une certain passerelle, lorsque le routage automatique ne connaît pas certain réseau, qu'on souhaite préciser quel chemin doit utiliser le trafic, on as plusieurs passerelles
```bash
sudo ip route add 10.0.0.0/24 via 192.168.1.1 dev eth0
```

- `10.0.0.0/24` : le sous réseau de destination
- `via 192.168.1.1` : la passerelle par laquelle les paquets sont envoyés
- `dev eth0`: interface par laquelle les paquets sortent 

#### Supprimer des routes 
```bash
sudo ip route del 10.0.0.0/24
```

### netstat 
Cette commande permet d'analyser les connexions courante.

#### Vérifier les connexions actives 
```bash
netstat -tun
```

- `-t` : affiche les connexions TCP 
- `-u` : affiche les connexions UDP 
- `-n`: utiliser les adresse numérique au lieu des nom d'hôtes

```text
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 192.168.1.100:22        192.168.1.50:50240      ESTABLISHED
```

Avec ce retour : 
- L'adresse `192.168.1.100:22` c'est l'ordinateur qui écoute sur le port 22
- L'adresse distante `192.168.1.50:50254` est l'hôte distant connecté au port 
- `ETABLISHED` : connexion active, les données sont en cours de transmission 

#### Ports en écoute
Pour voir quels ports écoutent sur la machine 
```bash
netstat -ltn
```

- `-l` : affiche uniquement les ports en écoute

### ss

Evolution de `netstat`. Il permet d'affiche plus d'informations, et plus rapidement 
```bash
ss -tln
```

---

## DNS 
Le DNS s'occupe de convertir une adresse IP en nom de domaine.

Enregistrements DNS : 
- **Enregistrement A (Adress Record)**: associe un nom de domaine à une adresse IPV4
- **Enregistrement AAAA** : associe un nom de domaine à une adresse IPV6 
- **Enregistrement CNAME (Canonical Name)**: indique un alias pour le domaine 
- **Enregistrement MX (Mail Exchange)**: pointe vers les serveurs de messagerie 

La résolutions de noms c'est lorsque l'on tate le nom d'un domaine dans le navigateur, il envoie une requête au DNS pour obtenir l'adresse IP.

### nslookup 

Utilitaire qui permet de vérifier le fonctionnement du DNS. 
```bash
nslookup [options] [domaine ou adresse IP]

# exemple 
nslookup google.com
```

On obtient ce type de retour : 
```txt
Server:         8.8.8.8 
Address:        8.8.8.8#53  
Non-authoritative answer: 
Name:   google.com 
Address: 142.250.74.206
```

- `Server`: le serveur DNS qui traite la demande
- `Non-authoritative answer`: signifie que l'information a été otenue non pas à partir du serveur DNS racine mais via le cache d'un autre serveur 

#### Vérification du serveur DNS 
On peut utiliser un serveur DNS spécifique en le précisant dans la commande 
```bash
nslookup google.com 8.8.8.8
```

#### Trouver l'adresse IP d'un site 
```bash
nslookup <nom_domaine>
```

### dig 

Domain Information Groper - outil plus avancée pour travailler avec DNS. Il fournit des informations détaillées sur les requêtes et réponse DNS 
```bash
dig [domaine] [options]

# exemple 
dig google.com
```

Il retourne ce type de réponse : 
```bash
;; Question section:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		300	IN	A	142.250.74.206

;; Query time: 35 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Thu Oct 19 10:00:00 UTC 2023
;; MSG SIZE  rcvd: 68
```

- `ANSWER SECTION`: adresse IP associées au domaine 
- `Query Time`: temps de traitement de la requête 
- `SERVER`: serveur DNS qui a traité la requête 

#### Vérfifier les enregistrement DNS 

##### Enregistrement A (IPV4)
```bash
dig linux.org A
```
Retourne les adresse IPV4 pour le domaine 

##### Enregistrement AAAA (IPV6)
```bash
dig linux.org AAAA
```
Retourne les adresse IPV6

##### Enregistrement MX (mail)
```bash
dig linux.org MX
```
Permet de vérifier quels serveurs gèrent le courrier pour le domaine 

---

## SSH 

Le SSH (Secure Shell) est un outil qui permet de gérer les serveurs à distance. 

C'est un protocole réseau qui permet de se connecter en sécurité à des ordinateurs distants. La connexion se fait à l'aide d'un chiffrement, de cette manière les données sont protégées. 

### Connexion via SSH 
```bash
ssh <username>@<hostname>
```

- `username` : nom d'utilisateur sur le serveur 
- `hostname`: adresse IP ou nom de domaine du serveur 

### Installation du serveur SSH 

Sur le serveur, il faut venir l'installer si il n'est pas présent.
```shell
sudo apt update 
sudo apt install openssh-server
```

On vérifie ensuite le statut du service SSH 
```shell
sudo systemctl status ssh
```

On obtient un message qui affiche sur le service est lancer.

Si le serveur n'est pas démarré, on utilise :
```sh
sudo systemctl start ssh 
sudo systemctl enable ssh 
```

### Configuration 

Le fichier de configuration se trouve dans `etc/ssh/sshd_config`. 

Il contient plusieurs paramètres :
- `Port 22`: le port sur lequel fonctionne SSH 
- `PermitRootLogin no`: interdiction de connexion pour root
- `PasswordAuthentication yes` : autorisation de connexion par mot de passe 

Une fois les paramètre modifiés, il faut redémarrer SSH :
```sh
sudo systemctl restart ssh
```

### Authentification par clé
Pour se connecter au serveur, il est plus sécurisé d'utiliser une clé SSH.

#### Générer une clé SSH 
Sur la machine client, on utilise la commande :
```sh
ssh-keygen
```

Le script demande où sauvegarde la clé (par défaut `~/.ssh/id_rsa`).

Une fois la commande exécutée, les fichiers de clés seront crées:
- `id_rsa`: clé prévié => à ne surtout pas rendre publique !
- `id_rsa.pub`: clé publique, celle que l"on utilise pour se connecter 

#### Transférer la clé sur le serveur 
Une fois la clé généré, on peut venir l'envoyer sur le serveur. c'est la clé publique qui doit être envoyé ! 
```sh
ssh-copy-id username@hostname
```

Cette commande ajoute la clé publique dans le fichier `~/.ssh/authorized_keys` sur le serveur. 

On peut ensuite se connecter sur le serveur sans mot de passe.

### Alias SSH 
Lorsque l'on se connecte souvent sur des serveurs, on peut utiliser des alias pour simplifier la connexions au serveur.

Les alias sont ajouter dans le fichier `~/.ssh/config` : 
```bash
Host myserver # alias 
    HostName 192.168.1.10 # ip du serveur 
    User student # nom d'utilisateur sur le serveur 
    IdentityFile ~/.ssh/id_rsa # clé privé associé 
```

Une fois l'alias configurer, on peut se connecter au serveur via cette commande :
```bash
ssh myserver
```

---
## NETCAT

### nc 

`nc` ou `netcat` permet de diagnostiquer les connexions, de vérifier la disponibilité des ports ou d'envoyer des message via `TCP/UDP`.

Ces principales fonctions sont : 
- Vérification de la disponibilité des ports 
- L'établissement de connexions entre deux hôtes
- Le diagnostic des problèmes réseau
- Le lancement de serveur de test pour échanger des données

#### Vérification des connexions 
Chaque service écoute sur un port spécifique. Le serveur web par défaut sur le port 80 ou 443, SSH sur le port 22.

La vérification de port permet de savoir si le service est disponible et si le client peut y accéder.

#### Tester une connexion avec un serveur distant et un port 
```bash
nc -zv <ip> <port>
# exemple
nc -zv 192.168.1.100 22

# retour si succes 
Connection to 192.168.1.100 22 port [tcp/ssh] succeeded!

# retour si echec 
nc: connect to 192.168.1.100 port 22 (tcp) failed: Connection refused
```

- `-z` : indique que l'on veux tester le port 
- `-v` : affiche les détails 

#### Tester une plage de ports 
```bash
nc -zv <ip> <port_start-post_end>

nc -zv 192.168.1.100 20-30
```

#### Lancer un serveur pour recevoir des données 
On peut utiliser `netcat` pour créer un mini serveur. 
```bash
nc -l <port>

nc -l 12345
```
Ce test attends que les connexions entrantes sur ce sport, et tout ce qui est reçus s'affiche dans le terminal

#### Envoyer des données via des connexions TCP/UDP 
On peux envoyer des message 
```bash
echo "Salut, monde !" | nc <ip> <port>

echo "Salut, monde !" | nc 192.168.1.100 12345
```

Si sur la machine cible, le port est ouvert, et que `nc -l <port>` tourne, alors le client reçoit le message.

---

## SÉCURITÉ LINUX

La sécurité sous Linux, c'est d'abord une stratégie de minimisation des privilèges et un contrôle d'accès fiable. On peut imaginer Linux comme une forteresse à plusieurs couches : plus on avance, et plus il y a une vérification. 

### Division des droits d'accès 

- **Utilisateur (user)**: compte individuel
- **Groupes (groups)**: ensemble d'utilisateurs
- **Root** : super utilisateur avec tous les droits.

### Minimisation des privilèges 

On accorde aux utilisateurs uniquement les droits nécessaires pour accomplir leur tâches.

### Prévention des menaces 

- **Mise à jour régulière du systèmes**: la mise à jour du système permet une "mise à jour" de l'antivirus.
- **Contrôle d'accès aux fichiers importants**
- **Restriction de l'accès root**: privilégier un utilisateur pour travailler sur le serveur.

### Configuration de la politique de mot de passe 

La longueur minimal et la complexité d'un mot de passe peuvent être configurées avec PAM
```bash
sudo nano /etc/security/pwquality.conf
```

### ufw

Uncomplicated Firewall. est un outil qui permet de configurer les règles de pare feu. 

```bash
# installation 
sudo apt update
sudo apt install ufw

# aciver
sudo ufw enable

# vérifier son statut 
sudo ufw status

# autoriser un port 
sudo ufw allow 22

# bloquer un port 
sudo ufw deny 8080

# afficher le numéro d'une règle
sudo ufw status numbered

# supprimer une règle
sudo ufw delete 1
```

### iptables 

Permet de contrôler le trafic, routage, filtrage et modification des paquets 

On retrouve quelques concepts de base :

1. Tables 
Ce sont les ensembles des fonction pour gérer les paquets réseau.
- `filtrer`: gestion et filtrage des paquets 
- `nat`: transformation des adresses/ports

2. Chaînes 
Ce sont les règles appliqués aux paquets 
- `INPUT`: autoriser le trafic entrant 
- `OUTPUT`: pour le trafic sortant
- `FORWARD`: pour le trafic redirigé 

2. Cibles 
Ce sont les actions exécutées sur un paquet 
- `ACCEPT`: autoriser le paquet
- `DROP`: jeter le paquet 
- `REJECT`: jeter le paquet tout en envoyant une notification 

### SELinux 

Module de sécurité qui permet de renforcer la gestion des droits d'accès en ajoutant le concept de contrôle obligatoire. 
Il permet à un admin de configurer des restrictions plus strictes pour les users et les processus, afin de minimiser les dégâts potentiels en cas d'attaques.

Il peut fonctionne dans trois modes : 
- **Enforcing** : actif, applique la politique de sécurité et bloque les processus qui ne respectent pas les règles
- **Permissive**: enregistre les violations dans les logs, mais ne bloque pas les actions 
- **Disabled**: désactivé 

#### Vérification du status
##### getenforce 

Retourne le mode actuel 
```shell
getenforce
```

##### setstatus 
Permet d'obtenir un rapport plus détaillé sur le statut. Elle indique l'état actuel, le mode actif et la politique utilisée.
```shell
$ sestatus
SELinux status:                 enabled
SELinuxfs mount:                /selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
```

#### Changer les modes 

Pour passer en mode permissive 
```shell
$ sudo setenforce 0
```

Pour réactiver le contrôle strict 
```shell
$ sudo setenforce 1
```

a finir 

### ACL 

### Surveillance des users 

### Configuration pare feu, SELinux et ACL de base 

---

## SYSTEME DE FICHIERS

Le système de fichier organise et gère l'accès aux données sur les supports. Il définit comment les données sont stockées, lues et écrites.

Il existe plusieurs systèmes de fichiers :
- **ext4**: système standard pour les distributions Linux
- **NTFS**: système utilisé par Windows
- **FAT32**: populaire pour les supports externes et clé USB 
- **XFS, btrfs** : alternative plus avancées pour Linux avec des fonctionnalités comme les snapshots

Lorsque l'on branche un disque ou une clé USB sous Linux, le système de fichier doit être monté pour que le système puisse commencer à travailler avec.

### lsblk - Lister les périphériques
Cette commande permet de lister les périphériques de bloc. Elle affiche un tableau de tous les périphériques de bloc du système, incluant les disque durs, SSD, clé USB et partitions.

```shell
lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0  500G  0 disk
├─sda1   8:1    0   50G  0 part /
├─sda2   8:2    0  200G  0 part /home
└─sda3   8:3    0  250G  0 part
sdb      8:16   1   16G  0 disk
└─sdb1   8:17   1   16G  0 part /media/usb

# affiche le type du systeme file et UUID
lsblk -f 

# affiche uniquement les colonnes 
lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINT
```

- **NAME**: nom du périphérique. 
- **SIZE**: taille du périphérique 
- **TYPE**: type de périphérique. `disk` correspond à un disque physique, et `part` une partition
- **MOUNTPOINT** : chemin où le périphérique est monté dans le système de fichier 


### blkid - Identifier les périphériques

Cette commande permet d'identifier les périphériques en fonction de leur système de fichier et de leur UUID.

```shell
$ blkid

/dev/sda1: UUID="1111-2222-3333-4444" TYPE="ext4"
/dev/sda2: UUID="5555-6666-7777-8888" TYPE="ext4"
/dev/sda3: UUID="9999-AAAA-BBBB-CCCC" TYPE="swap"
/dev/sdb1: UUID="AAAA-BBBB" TYPE="vfat" LABEL="USB_DISK"
```

- **UUID**: identifiant unique de la partition
- **TYPE**: type du system file 
- **LABEL**: étiquette de la partition

Les UUID sont important sur Linux car les périphérique peuvent changer dynamiquement de nom au démarrage. 
Avec cet id, on peut utiliser une identification stables pour monter les périphérique.

### Travailler avec des périphérique non montés

Parfois, lorsque l'on branches un disque ou une clé, elle n’apparaît pas comme montée. Cela peut arriver si le system file n'est pas actif.

On connecte une clé USB, et le résultat de la commande est : 

```shell
$ lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sdb      8:16   1   16G  0 disk
```

Cela signifie que le périphérique `sdb` n'a pas de partition active. Il faut utiliser la seconde commande pour savoir si un system file est dessus 

```shell
blkid /dev/sdb
```

Si rien ne s'affiche, le périphérique n'est pas encore formaté.

### Formatage 

Lorsque l'on formate une partition, on créer une "page blanche" pour les données. Il supprime tout le contenu de la partition et créer un nouveau système de fichiers qui détermine comment les fichiers seront organisées et comment y accéder.

On vient faire un formatage :
- **Préparer un nouvel appareil**
- **Changer le system file**
- **Effacer les anciennes données**

#### mkfs

L'outil `mkfs` permet de formater des partitions.

```shell
mkfs.<type_de_système_de_fichiers> <dispositif>

# exemple 
mkfs.ext4 /dev/sdb1
```

Dans cet exemple, on formate la partition `/dev/sdb1` en `ext4`.

Le tool fonctionne avec la plupart des types de systèmes de fichiers courant : 

| Système de fichiers | Commande    | Application                                                                                         |
| ------------------- | ----------- | --------------------------------------------------------------------------------------------------- |
| **ext4**            | `mkfs.ext4` | Le système de fichiers principal pour Linux, prenant en charge de grands fichiers et disques.       |
| **xfs**             | `mkfs.xfs`  | Haute performance, adapté aux grands fichiers et aux charges serveur.                               |
| **vfat** (FAT32)    | `mkfs.vfat` | Idéal pour l'échange de données entre Linux, Windows et MacOS.                                      |
| **ntfs**            | `mkfs.ntfs` | Pour la compatibilité avec Windows (bien que l'édition depuis Linux puisse parfois poser problème). |

Pour obtenir la liste complète des systèmes pris en charge :
```shell
mkfs -t help
```

#### Formatage d'une partition en ext4

On commence par vérifier le périphérique

```shell
lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0  100G  0 disk
├─sda1   8:1    0   50G  0 part /
├─sda2   8:2    0   45G  0 part /home
└─sda3   8:3    0    5G  0 part [SWAP]
sdb      8:16   0  200G  0 disk
└─sdb1   8:17   0  200G  0 part
```

On souhaite formater la partition `/dev/sdb1`

Avant de formater, le périphérique ne doit pas être monté. 
```shell
sudo umount /dev/sdb1
```

On vient ensuite lancer le formatage.
```shell
sudo mkfs.ext4 /dev/sdb1

mke2fs 1.45.7 (28-Jan-2021)
Creating filesystem with 52428800 4k blocks and 13107200 inodes
Filesystem UUID: 5634f623-7b2d-4d6b-b8f5-abcdef123456
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, ...
```

On vient ensuite vérifier que le system file à bien été créer : 
```shell
sudo blkid /dev/sdb1

# sortie 
/dev/sdb1: UUID="5634f623-7b2d-4d6b-b8f5-abcdef123456" TYPE="ext4"
```

La sortie de la commande vient montrer que `ext4` est bien installé sur le périphérique 

### Choix du système de fichiers 

Selon les besoins, il est conseillé d'utiliser un format plutôt qu'un autre : 
- **ext4**: choix universel pour Linux
- **XFS**: pour les besoin de haute performance avec de gros fichiers ou sur des serveurs 
- **FAT32**: si on doit utiliser un périphérique à la fois sur Windows et Mac. La taille max du fichier est limié à 4Go 
- **NTFS**: pour la compatibilité avec Windows, mais les perf peuvent être inférieur à `ext4` sous Linux

### Vérification et correction du system file 

Le system file peut subit des dommages qui provoque des pertes de données ou d'autres types de problèmes.
Ces problèmes peuvent apparaître à la suite de :
- **Coupure d'alimentation soudaine**: l'appareil s'éteint sans terminer l'écriture des données
- **Erreur de stockage**: une défaillance matériel d'un disque 
- **Erreurs logicielle**: un bug dans un driver

Après ce type d'évènement, le system file peut se retrouver dans un état dégradé, et le system peut demander de vérifier son état.

#### fsck 
Tool qui permet de vérifier l'intégrité du system file et corriger les erreurs détectées. Il peut être utilisé :
- En cas de défaillance des fichiers 
- Si le système ne démarre plus 
- Prévention régulière 

```shell
fsck [options] <périphérique>
```

- `-y`: confirmer automatiquement les corrections.
- `-n`: vérification sans correction
- `-t`: indique le type de system file 
- `-r`: active le mode intéractif

Après avoir exécuté la commande, les fichiers perdu sont placés dans le dossier `lost+found` à la racine du system file.
#### Vérification du system file 

```shell
# vérification sans correction 
fsck -n /dev/sdb1

# exemple de sortie 
#Inodes qui faisaient partie d'une liste orpheline corrompue détectés. Erreurs du système de fichiers détectées. Exécute fsck pour réparer.

#correction automatiue 
fsck -y /dev/sdb1 
```

#### Utilisation sur un system file utilisé

Si on essaie d'utiliser l'outil sur une partition déjà montée, on reçoit un avertissement `fsck: cannot check a mounted filesystem.`.

Cela arrive car la vérification peut entraîner des corruptions de données. Il existe plusieurs manière de résoudre ce problème

- Monté le périphérique en lecture seule
```shell
mount -o remount,ro /dev/sdb1
```
- Utiliser un live CD: on démarre le système depuis un disque ou une clé USB pour effectuer la vérification sur un périph déjà monté
- Utiliser le mode recovery: on redémarre le système en mode recovery 

#### Vérification du fichier racine /

Pour exécuter une vérification sur une partition `/` :
```shell
# passer en mode user unique 
systemctl isolate rescue.target

# lancement fsck
fsck -y /
```

---

### Montage et démontage des system file 

Le montage est le processus de connecter un system file d'un périphérique à l'arborescence des fichiers Linux.
Sous Linux tous les périphériques sont intégrés dans la structure des fichiers à un dossier spécifique appelé point de montage.

#### mount - Monter un system file 

```shell
mount [options] <périphérique> <point de montage>

sudo mount /dev/sdb1 /mnt/usb
```

- `periphérique`: c'est l'endroit où le périphérique est conecté au système (`/dev/sdb1`)
- `point de montage` : le dossier ou les données du périphérique sont accessibles (par exemple `mnt/disk`)

Une fois monté, les fichiers du périphérique sont accessible dans le dossier `/mnt/usb`.

##### Spécifier le system file 

```shell
sudo mount -t ext4 /dev/sdb1 /mnt/usb
```

- `-t`: permet de spécifier le system file  

##### Montage en lecture seule 
Pour protégé les données du périphérique contre toute modification, on utilise `-o ro`
```shell
sudo mount -o ro /dev/sdb1 /mnt/usb
```

##### Montage avec spécification de l'encodage 
Pour les disques `FAT32` ou `NTFS`, il peut être nécessaire de spécifier l'encodage
```shell
sudo mount -o iocharset=utf8 /dev/sdb1 /mnt/usb
```

####  umount - Démonter un system file 
Le démontage permet de déconnecter un périphérique. C'est important que Linux ferme toutes les connexions avec le disque et termine l'écriture.

```shell
umount <périphérique ou point de montage>

# périphérique connecté 
sudo umount /mnt/usb
sudo umount /dev/sdb1
```

##### Echec du démontage
Parfois la commande peut retourner une erreur `Device is busy`. Cela signifie qu'un programme utilise encore le périphérique.

Dans ce cas, on utilise la commande pour montrer les processus bloquant 
```shell
fuser -v /mnt/usb
```
Une fois identifié, on peut venir kill le processus 
```shell
kill <ID de processus>
```

On peut ensuite venir démonter le périphérique

#### Montage automatique 

Lorsque l'on doit connecter un périphérique à chaque démarrage du système, on peut utiliser le fichier `/etc/fstab`.
C'est un fichier de configuration qui contient une liste des périphérique que Linux doit automatiquement connecter.
On peut y définir des paramètres comme le type de system file, le point de montage et les droits d'accès.

Pour ajouter un périphérique, on commence par récupérer son UUID 
```shell
sudo blkid
```

On ouvre ensuite le fichier de configuration : 
```shell
sudo nvim /etc/fstab
```

Et on ajoute une nouvelle ligne : 
```txt
UUID=1234-5678 /mnt/usb ext4 defaults 0 2
```

- `UUID`: identifiant unique 
- `/mnt/usb`: point de montage 
- `ext4`: format 
- `defaults`: paramètre par défaut du montage 
- `0` et `2` : paramètres pour la vérification du périphérique au démarrage

On fois ajouter, on viens tester les paramètres en montant le périphérique 
```shell
sudo mount -a
```

----

### Archivage et compression des fichiers 

L'archivage c'est le processus de regroupement de plusieurs fichiers dans un seul conteneur, et la compression c'est la réduction de sa taille.
C'est utile pour : 
- **Stockage des données**: en emballant des fichiers dans une archives, on peut faciliter le déplacement et les copie comme un seul fichier.
- **Transmission de donnée**: avec la compression, les données prennent moins de place, ce qui accélère le transfert sur le réseau 
- **Backup**: l'archivage permet de protéger les données et simplifier leur restauration 

#### tar - Création et gestion des archives 

Utilitaire qui permet d'archiver des fichiers sous Linux. Il ne compresse pas les fichiers, mais il les assemble dans un fichier unique.

```shell
tar [options] <archive> <fichiers/dossiers>
```

- `-c` : création de la nouvelle archive
- `-v`: affichage détaillé des opérations (affiche les fichiers ajoutés)
- `-f`: spécifie le nom de l'archive 
- `-x`: extraction des fichiers de l'archive
- `-t`: affichage du contenu de l'archive 

##### Création d'une archive 
```shell
tar -cvf archive.tar file1.txt file2.txt
```

La commande vient créer une `archive.tar` à partir des fichiers `file1.txt` et `file2.txt`

##### Archivage d'un dossier 
```shell
tar -cvf project.tar /home/user/project
```

Cette commande créer une archive `project.tar` qui inclut tout le dossier `/home/user/project`

##### Extraction d'une archive 
```shell
tar -xvf archive.tar
```

Décompresse l'archive `archive.tar` dans le répertoire actuel 

##### Affichage du contenu d'une archive 
```shell
tar -tvf archive.tar
```

Affiche la liste des fichiers et des dossiers dans l'archive

#### gzip - compression 

Il permet de réduire les données en utilisant des algo de compression.

```shell
gzip [options] <fichier>
```

##### Compression d'un fichier 
```shell
gzip file1.txt
```

La commande transforme le fichier en `file1.txt.gz`

##### Décompression d'un fichier 
```shell
gunzip file1.txt.gz
```

Restaure le fichier d'origine `file1.txt`

##### Archivage et compression avec tar et gzip
```shell
tar -czvf archive.tar.gz file1.txt file2.txt
```

Cette commande créer une archive `archive.tar.gz` qui combine et compresse les fichier `file1.txt` et `file2.txt`

##### Extraction d'une archive compressée 
```shell
tar -xzvf archive.tar.gz
```

Décompresse et extrait le contenu de l'archive `archive.tar.gz`

#### zip - création d'archives compressées

`zip` crée immédiatement une archive compressée qui peut par défaut être utilisée sur Windows et d'autres systèmes.

```shell
zip [options] <archive> <fichiers/dossiers>
```

##### Création d'une archive zip 

```shell
zip archive.zip file1.txt file2.txt
```

Crée une archive `archive.zip` qui inclut les fichiers `file1.txt` et `file2.txt`

##### Archivage d'un dossier

```shell
zip -r project.zip /home/user/project
```

Crée une archive `project.zip` qui inclut tout le dossier `/home/user/project`.

`-r` : ajout récursif des fichiers et dossier => permet d'ajouter ce qui est présent dans le dossier

|Outil|Archivage|Compression|Approche|Utilisation principale|
|---|---|---|---|---|
|`tar`|✅|❌|Paquet de fichiers|Fusion des fichiers dans une archive sans compression|
|`gzip`|❌|✅|Compression|Réduction de la taille des fichiers ou des archives individuelles|
|`zip`|✅|✅|Tout-en-un|Créer des archives transportables|

---

### Sauvegarde 

Les systèmes et les humains ne sont pas fiables, et les fichiers disparaissent. La sauvegarde permet d'éviter ce genre de problème.

#### rsync - copie de données 

`rsync` est un outils pour  copier et synchroniser des données. Sa principale force réside dans sa rapidité et son efficacité.
Au lieu de copier, `rsync` ne copie que les fichiers modifiés. Cela lui permet d'être efficace pour effectuer des sauvegarde régulière de grande quantité de données.

```shell
rsync [options] source destination
```

- `source` : le chemin vers le fichier ou dossier que l'on souhaite copier 
- `destination`: l'endroit où l'on veux envoyer ces données

Options principales :
- `-a`: (archive); inclut la copie de toutes les métadonnées (permissions, timestamps, etc)
- `-v`: (verbose); active une sortie détaillée pour que l'on puisse voir ce qui se passe 
- `--progress`: montre la progression du transfert 
- `--delete`: supprime côté destination les fichiers qui ne sont plus présent côté source. Utile pour une synchro complète 
- `-z` compress; compresse les données avant de les transmettre, ce qui accélère le processus (important pour les opération réseau)

##### Copie de données locales 

Dans un folder `/backup`, on viens y stocker les sauvegardes. On viens coper le contenu d'un repo dans ce nouveau repo 
```shell
rsync -av /home /backup
```

On viens copier les données du `/home` dans le nouveau dossier `/backup`.
- `-a`: permet de préserver la structure et les permissions des fichiers
- `-v` : fournit les info sur les processus en cours

Si on relance la commande, elle ne copiera que les fichiers modifés ou nouveaux

##### Sauvegarde distante 

On souhaite envoyer une sauvegarde vers un serveur distant. Pour cela `rsync` prends en charge l'utilisation de SSH 

```shell
rsync -av /backup user@remote_server:/remote_backup
```

- `user`: le nom de l'utilisateur pour le serveur distant 
- `remote_server`: l'adresse du serveur 
- `/remote_backup`: le chemin sur le serveur où l'on souhaite sauvegarder les données

#### scp - copie simple 

`scp` (secure Copy Protocol) est un outil pour copier des fichiers entre une machine locale et une machine distante. Cet outil est plus simple que `rsync` et convient pour les cas où une synchronisation complète n'est pas nécessaire.

```shell
scp [options] source destination
```

- `-r` : copie récursive 
- `-C`: compresse avant transfert 
- `-P`: spécifie un post SSH si différent du 22 

##### Copie un fichier sur une machine distante 

```shell
scp document.txt user@remote_server:/remote_folder
```

Cette commande permet de copier `document.txt` dans le dossier `/remote_folder` sur le serveur via SSH

Le processus inverse est également possible 
```shell
scp user@remote_server:/remote_folder/document.txt /home/user/documents
```

#### Différence entre rsync et scp 

|**Particularité**|**rsync**|**scp**|
|---|---|---|
|**Vitesse**|Seuls les fichiers modifiés, plus rapide pour de gros volumes|Copie tout, même si les fichiers n'ont pas changé|
|**Synchronisation**|Support complet de la synchronisation|Ne supporte pas la synchronisation|
|**Compression des données**|Option disponible `-z`|Option disponible `-C`|
|**Simplicité d'utilisation**|Plus complexe à configurer|Interface simple|

#### Configurer une sauvegarde 

On souhaite configurer un processus complet de sauvegarde d'un projet sur un serveur distant.
- Les fichiers du projet sont stockés dans le dossier `/home/user/project`. On souhaite qu'il soient sauvegardés sur le serveur `backup.server.com` dans le dossier `/backups/project`
- Pour que la copie soit rapide, on utilise `rsync`

On viens configurer un script de sauvegarde dans un fichier nommé `backup.sh`

```bash 
#!/bin/bash

SOURCE="/home/user/project"
DESTINATION="user@backup.server.com:/backups/project"

# Effectue une sauvegarde avec rsync
rsync -av --delete "$SOURCE" "$DESTINATION"

# Affiche un message de fin
echo "Sauvegarde terminée !"
```

On rend le script exécutable 
```shell
chmod +x backup.sh

# on peut lancer ensuite le script 
./backup.sh
```

On peut également l'ajouter à `cron` pour une exécution automatique.

### Formatage des disques, montage et sauvegarde avec rsync 

On ajoute un nouveau disque sur un ordinateur, mais celui ci n'est pas encore vide et pas prêt à être utilisé sur le système.

On commence par s'occuper du formatage. 

1. Vérifier les périphérique connectés

On viens identifier où se trouve le nouveau disque :
```shell
lsblk

# sortie 
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0  100G  0 disk
├─sda1   8:1    0   50G  0 part /
├─sda2   8:2    0   50G  0 part /home
sdb      8:16   0  500G  0 disk
```

Ici `sdb` est le nouveau disque. Pour le moment il n'a pas de partition.

2. Création du system file 

Pour notre nouveau disque, on choisit le format `ext4`.
```shell
sudo mkfs.ext4 /dev/sdb
```

Après cette commande, le système sera prêt à travailler avec le disque. 

3. Montage du nouveau disque 
Le nouveau disque est maintenant formaté avec le bon system file.

On viens créer un point de montage, ici on créer un dossier `/mnt`

```shell
# création de l'emplacement où le disque sera connecté => généralement un dossier
sudo mkdir /mnt/newdisk

# montage du disque 
sudo mount /dev/sdb /mnt/newdisk

# check si ok => affiche le disque dans la liste
df -h
```

Pour éviter d'ajouter le disque manuellement à chaque fois, on l'ajoute au fichier `etc/fstab`.

```shell
# on récupère l'UUID
sudo blkid /dev/sdb

# sortie 
/dev/sdb: UUID="abcd-1234-efgh-5678" TYPE="ext4"

# ajoute de la ligne dans `etc/fstab`
UUID=abcd-1234-efgh-5678 /mnt/newdisk ext4 defaults 0 2
```

Le disque sera maintenant monté automatiquement à chaque démarrage du système.

4. Archivage des données 

On as un  dossier `/mnt/newdisk/data` que l'on souhaite archiver et compresser avant de le sauvegarder.

```shell
# création de l'archive 
tar -cvf data_backup.tar /mnt/newdisk/data

# compression de l'archive 
gzip data_backup.tar
```

Le fichier `data_backup.tar.gz` est créer, et est plus simple à transférer comme il est compresser.

5. Sauvegarde avec rsync 

Le fichier est maintenant compresser et prêt pour la sauvegarde. On utilise `rsync` pour le transférer vers un autre serveur ou un emplacement sur le disque.

```shell
# copie local 
rsync -av /mnt/newdisk/data /mnt/backup/

# copie distante 
rsync -av /mnt/newdisk/data username@remote_server:/backup/

# avec scp 
scp data_backup.tar.gz username@remote_server:/backup/
```

