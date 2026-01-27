## Connexion SSH 

```shell
ssh <username>@<ip>

# en précisant le port 
ssh -p 50256 <username>@135.125.100.226
```

https://help.ovhcloud.com/csm/fr-vps-security-tips?id=kb_article_view&sysparm_article=KB0047708

## Mise à jour du serveur 

```shell
sudo apt update && sudo apt upgrade
```

## Clé SSH

Paire de clés privée et publique générée sur la machine.

Cette clé doit être généré.

https://help.ovhcloud.com/csm/fr-dedicated-servers-creating-ssh-keys?id=kb_article_view&sysparm_article=KB0043385

Les clé ssh sont stocké dans `~/.ssh`.

Il faut ensuite venir récupérer la clé ssh .pub avec `cat ~/.ssh/<name_cle_ssh>.pub`

Toutes les machines devant ce connecter sur le serveur, devront avoir la clé ssh d'ajouter
## Fingerprint 

Il permet d'authentifier le serveur. C'est la clé publique du serveur, affiché par le client SSG pour que l'on puisse vérifier que l'on parles bien au bon serveur.

## Fish 

```shell
# installation 
sudo apt update 
sudo apt install fish

# lancement 
fish 
 
# Vérifier le chemin 
which fish 
# /usr/bin/fish 

#Changer le shell 
chsh -s /usr/bin/fish

# revenir à bash 
chsh -s /usr/bin/bash
```

## Automatisation install serveur 

Utiliser un script bash pour installer les tools et les différentes configuration.

## Mise en prod 

Dans le dossier `home/<user>`, on créer un repo `projects`.

Dans celui ci on viens y placer les repo du coup.

Les fichiers build, sont à placer dans `var/www/<mon_projet>`

Pour ajouter le fichier de configuration , on le place dans le dosier `/etc/nginx/sites-available/mon-app`

Pour mettre à jour en copiant :
```shell
# Copiez le CONTENU de dist (notez le /* à la fin) 
sudo cp -r ~/projects/Ubrun/front/dist/* /var/www/html/ubrun/ 
# Vérifiez 
ls -la /var/www/html/ubrun/
```