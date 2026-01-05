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
## Commande de base 
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

## Navigation dans le système de fichier 

### PWD 

Affiche le dossier courant 

```bash
$ pwd
/home/étudiant
```

### LS 

Affiche le contenu du dossier courant 

```shell
$ ls 
Documents  Téléchargements  Musique

$ ls -la 
```

- `-l` : info détaillé sur les dossiers et fichiers 
- `-a`: affiche les dossiers cachées

### CD 

Permet de se déplacer dans les dossiers

```bash
$ cd /home
$ pwd
/home
```

