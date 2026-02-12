# Docker 

## COMPOSANT PRINCIPAUX 

### Docker Engine 

C'est le logiciel principal qui permet de faire fonctionner les conteneurs. Il fournit une plateforme pour développer, livrer et exécuter des applications dans des conteneurs isolés.

Il contient plusieurs composants qui assurent les fonctionnalités de Docker 
- **Docker Daemon**: processus qui effectue les opérations principales avec les conteneurs
- **Docker API** : interface qui permet d’interagir avec Docker Daemon via des appels 
- **Docker CLI**: permet de travailler avec Docker 

Le Docker engine gère le cycle de vie des conteneurs, de la création, du lancement à la suppression. Il assure l'isolation des conteneurs, et permet de lancer plusieurs conteneur sur un même h^te sans conflit.
### Docker Daemon 

C'est le processus serveur principal, il réalise toutes les opérations Docker. Il fonctionne en arrière plan et gère les conteneurs, les images, les réseaux et d'autres ressources Docker.

1. Création et gestion des conteneurs: il traite les requêtes pour créer, démarrer, arrêter et supprimer des conteneurs. Il assure l'isolation des conteneurs et distribue les ressources entre eux
2. Travail avec les images : Il télécharge et stocke les images des conteneurs, qui sont utilisées pour créer des conteneurs. Il gère également le cache des images pour optimiser l'utilisation de l'espace disque.
3. Gestion réseau: il crée et gère les réseaux Docker, permettant aux conteneurs d’interagir entre eux et avec des systèmes externes. Il supporte différents drivers réseau et paramètres pour garantir flexibilité et sécurité
4. Gestion du stockage: gère les volumes et autre type de stockage, qui sont utilisés par les conteneurs pour stocker des données. Il assure une gestion fiable et efficace des données à l'intérieur des conteneurs.

Le client envoie des commandes au Docker Daemon, qui les traite et les exécute.

Il joue un rôle dans la sécurité des conteneurs. Il gère les droits d'accès, les paramètres réseau, et l'isolation des conteneurs, permettant de limiter les menaces potentielles et les vulnérabilités. En utilisant les namespaces et les cgroups, il isole les processus et contrôle l'utilisation des ressources par les conteneurs, garantissant leur fonctionnement en toute sécurité.

---

## TERME DE BASE 

### Images 
Les images sont un template utilisé pour créer un conteneur. Elle contient tout ce qui est nécessaire pour faire tourner une application (os, application, dépendances, fichier de config).

Caractéristique principale :
- **Structure par couche**: les images sont constitué de plusieurs couches. Une couche de base, et chaque suivante représente des modif par rapport à la précédente. Cela permet de réduire la place et réduit le temps de téléchargement
- **Réutilisation**: la même image peut servir à créer plusieurs conteneurs 
- **Portabilité**: on peut utiliser une image entre plusieurs machines 

Pour créer une image, on écrit un `Dockerfile` où l'on vient préciser les configurations de l'application et ses dépendances.

Pour utiliser une image, on peut les récupérer sur Docker Hub permettant de déployer des applis standard comme des serveurs ou des base de données.

```shell
docker pull <image>
```

---

### Conteneur 

Un conteneur est une instance en cours d'exécution d'une image. Il permet d'isoler les applications et leurs dépendances dans un environnement d'exécution distinct, ce qui permet de les exécuter indépendament du système hôte et des autres conteneurs.

Ils sont légérs car ils utilisent le noyau du système hôte, au lieu de créer un noyau séparé comme des dans VM.

---

### Registries 

Le registre Docker est un stockage pour les images Docker. Il permet de stocker, gérer et distribuer des images de conteneur.

Par exemple Docker Hub

---

### Orchestration 

C'est le processus de gestion de plusieurs contenaires dans un environnement distribué. Ils permettent d'automatiser le déploiement, le scaling et la gestion des containers

- **Kubernetes**: permet de gérer un cluster de containers sur différents noeuds.
- **Docker Swarn**

---
### Network 
Permet aux conteneurs d'intéragir entre eux et avec le monde extérieur. Docker fournit plusieurs types de réseaux pour différents scénario.
- **Bridge** : réseau par défaut, les conteneurs dans un réseau en bridge peuvent intéragir entre eux
- **Host**: le conteneur utilise les interfaces réseau de la machine hôte 
- **Overlay**: utilisé pour créer des réseaux entre plusieurs daemon, nécessaire pour l'orchestration 
- **None**: désactive toutes les interface du conteneur, pour des tâches isolées

---
### Volume 
Méchanisme de stockage persistant des données des conteneurs. Il permettent de sauvegarder les données en dehors du conteneur pour éviter leur perte lors du redémarrage ou de la suppression du conteneur 

Le volume c'est le disque dur virtuel qu'on peut connecter à une machine virtuelles ou à un conteneur. Il est généralement stocké sur le système hôte sous forme de fichier ordinaire


---
## DEMARRAGE DOCKER 

```shell
# vérifier la version de docker 
sudo docker --version

# lancement du conteneur de test 
docker run hello-world

# vérifier l'état du daemon
sudo systemctl status docker 

# lister les réseaux existant 
docker network ls 

# lister les volumes 
docker volume ls 
```

---

## COMMANDE PRINCIPALE 

### Travailler avec des conteneurs
|Commande|Description|
|---|---|
|docker run|Lancer un nouveau conteneur|
|docker ps|Liste des conteneurs en cours d'exécution|
|docker stop|Arrêter un conteneur en cours d'exécution|
|docker start|Démarrer un conteneur arrêté|
|docker restart|Redémarrer un conteneur|
|docker rm|Supprimer un conteneur arrêté|
|docker logs|Voir les logs d’un conteneur|
|docker exec|Exécuter une commande à l'intérieur d'un conteneur|

```shell
# lancer un nouveau conteneur en arrière plan 
docker run -d --name my_container nginx

# arrête un conteneur 
docker stop my_container

# démarrer un conteneur arrêté 
docker start my_container

# redémarrer un conteneur 
docker restart my_container

# supprimer un conteneur 
docker rm my_container

# voir les logs d'un conteneur 
docker logs my_container

# exécuter une commande dans un conteneur 
docker exec -it my_container /bin/bash
```
Il permet de 

---

### Travailler avec une images
|Commande|Description|
|---|---|
|docker pull|Téléchargement d’une image depuis Docker Hub|
|docker build|Construction d’une image à partir de Dockerfile|
|docker images|Liste des images locales|
|docker rmi|Suppression des images|
|docker tag|Association d’un nouveau tag à une image|

```shell
# télécharger une image 
docker pull nginx:latest

# construction d'une image => dans le repertoire actuel avec un tag
docker build -t my_image .

# liste des images locale
docker images

# suppression des images
docker rmi my_image

# associer un tag à une image
docker tag my_image my_repo/my_image:latest
```

---

### Travail avec les réseaux 

|Commande|Description|
|---|---|
|docker network ls|Liste de tous les réseaux Docker|
|docker network create|Création d'un nouveau réseau|
|docker network inspect|Voir les détails d'un réseau|
|docker network connect|Connecter un container au réseau|
|docker network disconnect|Déconnecter un container du réseau|

```shell
# lister les réseau
 docker network ls
 
 # créer un nouveau réseau 
 docker network create my_network
 
 # voir les détails d'un réseau 
 docker network inspect my_network
 
 # connecter un container au réseau 
docker network connect my_network my_container

# déconnecter un container au réseau 
 docker network disconnect my_network my_container
```

---

### Travailler avec les volumes 
|Commande|Description|
|---|---|
|docker volume ls|Liste de tous les volumes Docker|
|docker volume create|Créer un nouveau volume|
|docker volume inspect|Afficher les détails d’un volume|
|docker volume rm|Supprimer un volume|

```shell
# lister les volumes
docker volume ls

# créer un nouveau volume 
docker volume create my_volume

# afficher les détails d'un volume 
docker volume inspect my_volume

# supprimer un volume 
docker volume rm my_volume
```

---

## DOCKER HUB

Service cloud de Docker. Il sert pour le stockage et la distribution des images Docker, simplifie les processus de build et de déploiement des applications. 

Il permet aux utilisateurs de télécharger leurs images, et de les partager avec d'autres, mais également de rechercher et télécharger des images créer par d'autre utilisateurs.

### Repositories

Les repo dans Docker Hub sont des endroits où les images Docker sont stockées. Il est possible de créer des repo publics ou privée selon les besoins.

Pour créer un nouveau reop, il faut le créer sur le Docker Hub. On vient ensuite construire une image localement avec un Dockerfile.
```shell
# création du repo 
docker build -t yourusername/repositoryname:tag .

# login
docker login 

# téléchargement de l'image dans le repo 
docker push yourusername/repositoryname:tag
```

Il est possible de configurer le builds automatiquement du repo. De cette manière, à chaque modifications dans le code source, Docker Hub créera et téléchargera automatiquement une nouvelle image.

### Recherche d'image 

Il est possible de rechercher des images prête. Une fois l'image trouvé, on peut la télécharger localement :
```shell
docker pull imagename:tag

docker pull nginx:latest
```

---

## CAS D'UTILISATION DOCKER 

### Microservice 

Docker permet d'implémenter une architecture de microservice en emballant chaque service dans un conteneur séparé. 

### CI/CD 
Docker aide à créer un environnement identiques pour toutes les étapes du développement. Cela garantit que l'application fonctionnera de la même manière partout.

### Portabilité et cohérence

Docker permet d'emballer une application avec toutes ses dépendances dans un conteneur qui onctionne de manière garantie dans n'importe quel environnement 
Il permet de 

### Garantis la sécurité 

Docker garantit l'isolation des containers, ce qui réduit les risques. Les container fonctionne de manière indépendante, limitant leur impact sur le système

### Test et automatisation 

Docker permet de créer des environnement de tests isolés sans affecter le système principale

### Calculs dans le cloud et mise à l'échelle

Docker simplifie la mise à l'échelle via l'intégration avec des plateforme cloud.

---

## CONTAINER 

### docker run 

Cette commande permet de lancer un nouveau conteneur en utilisant une images existante. 

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

- `OPTIONS`: paramètre pour configurer le conteneur (ports, volumes, variables d'environnement)
- `IMAGE`: nom de l'image à partir duquel le conteneur sera crée
- `COMMAND`: commande qui sera exécutée à l'intérieur du conteneur 
- `ARG`: argument pour la commande 

#### Lancement simple 

```shell
docker run hello-world
```

Cette commande télécharger l'image `hello-world` depuis Docker Hub et la lance. Le conteneur exécute la commande définir dans l'image et affiche le message à l'écran.

#### Lancement en mode background 

Par défaut, les conteneurs se lance en mode intéractif, et leur sortie est affichée dans le terminal. Le mode background est le mode détaché, les sortie ne seront pas affiché.

```shell
docker run -d nginx
```

La commande démarre un serveur nginx en mode détaché. Docker retourne l'identifiant du conteneur, que l'on peut utiliser pour gérer le conteneur plus tard.

#### Attribuer un nom au conteneur 

Pour simplifier la gestion des conteneur, on peut lui attribuer un nom avec le parametre `--name`
```shell
docker run -d --name my_nginx nginx
```

En ajoutant un nom, on peut y faire reference dans d'autre commande Docker.

#### Demarrer en mode interactif 
On peut lancer un conteneur en mode interactif avec la l'option `-i` et `-t`
```shell
docker run -it ubuntu bash
```

La commande denarre un conteneur base sur une image ubuntu et ouvre un terminal interactif Bash a l'interieur du conteneur. On peux ensuite executer ses commandes dans ce terminal.

#### Lancer un conteneur avec une variable env 
```shell
docker run -e MY_VAR=HelloDocker ubuntu
```

On passe dans le conteneur la variable d'environnement

#### Lancer un conteneur en montant un volume 
```shell
docker run -v /host/data:/container/data ubuntu
```

On viens passer le volume `/host/data` dans le container dans le repo `/container/data` avec l'option `-v`

---

### docker start 
Permet de démarrer des containers qui ont été arrêtés auparavant. Cela signifie que l'on peut réutiliser un container déjà créé sans avoir besoin de le recréer à chaque fois que nécessaire 

```shell
docker start [options] container [container...]
```

- `container`: le nom ou identifiant du container à lancer 
```shell
# lancer un container 
docker start my_container 

# lancer plusieurs container 
docker start container1 container2

# lance un container avec affichage des sortie 
docker start -a my_container
```

---

### docker stop 

Cette commande permet d'arrpeter des container en cours d'exécution. Elle donne au conteneur le temps de s'arrêter correctement en envoyant le signal SIGNTERM et SIGKILL si le container ne s'arrête pas dans le délai imparti

```shell
docker stop [option] container [container...]

# arrêter un container 
docker stop my_container 

# arrêter plusieurs container 
docker stop container1 container2

# arrête avec délais en seconde
docker stop -t 30 my_container
```

---

### docker restart 

Cette commande permet de redémarrer  les conteneurs. Permet d'appliquer rapidement des changements ou résoudre des erreurs 
```shell
docker restart [options] container [container...]

# redémarrer un container 
docker restart my_container

# redémarrer plusieurs container 
docker restart container1 container2

# redémarrer avec timeout en seconde
docker restart -t 20 my_container
```

---

### docker rm 
Cette commande permet de supprimer des container arrêtés. Cela permet de libérer les ressources utilisées par le container.
Avant la supression, il doit être arrêté.

```shell
# Suppression d'un conteneur avec les volumes
docker rm -v old_service
```

---

### docker ps 

Cette commande affiche la liste des conteneurs en cours d'exécution et fournit des informations à leur sujet. 

Elle affiche :
- **Container ID**: identifiant du conteneur 
- **Image**: image de base du conteneur 
- **Command**: commande exécuté dans le container 
- **Created**: durée depuis la création du container 
- **Status**: état actuel du conteneur 
- **Ports** : ports redirigés
- **Name**: nom du conteneur 

```shell
docker ps [options]

# utilisation de base 
docker ps 

# liste les conteneur arrête et en exe
docker ps -a

# filtrage par status 
docker ps -f "status=exited"

# filtrage par nom 
docker ps -f "name=my_container"

# filtrage par image
docker ps -f "ancestor=nginx"

# formatage de sortie 
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"

# affiche les identifiant des conteneur en cours
docker ps -q

# affiche les conteneur démarré dans les 24h
docker ps --filter "since=24h"

# affiche les conteneur dans un état particulier et mise en forme de la sortie 
docker ps -f "status=running" --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# recherche et suppression des conteneur arrêtés 
docker rm $(docker ps -a -f "status=exited" -q)

# génération rapoort d'état 
if [ $(docker ps -q -f "name=my_container) ]; then
	echo "Le conteneur est en cours exécution"
else
	docker start my_container
fi
```

- **{{.ID}}**: identifiant du conteneur 
- **{{.Image}**: image du conteneur 
- **{{.Commande}}**: commande exécuté dans le conteneur 
- **{{.CreatedAt}}**: date de création
- **{{.RunningFor}}**: durée de fonctionnement
- **{{.Status}}**: état actuel
- **{{.Ports}}**: ports redirigés
- **{{.Names}}**: nom du conteneur 

--- 

### docker logs 

Affiche les logs d'un conteneur. 
```shell
docker logs [options] container

# affiche touts les logs du container 
docker logs my_container 

# affichage des logs en temps réel 
docker logs -f my_container 
# suivre les flux de request et response
docker logs -f web_server_container

# limitation du nombre de ligne affiché 
docker logs --tail 100 my_container

# affiche les logs depuis un certain moment 
docker logs --since "2023-07-20T15:00:00" my_container

# -- until permet d'afficher des logs jusqu'à un moment précis
docker logs --until "2023-07-20T16:00:00" my_container
docker logs --until 10m my_container

# afficher les message du flux de sortie standard
docker logs --stdout my_container 
# affiche les message du flus de sortie erreur 
docker logs --stderr my_container 
```

### docker exec - Interaction avec un conteneur en cours d'exécution 

Cette commande permet d'exécuter des commandes et d'ouvrir des sessions interactives dans un conteneur en cours d'exécution, de la même manière que si on travaillait directement avec un serveur.

```shell
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

- `CONTAINER`: nom ou identifiant du container 
- `COMMANDE`: commande à exécuter dans le conteneur 
- `ARG...`: arguments pour la commande 

```shell
# exécute la commande ls /app dans le conteneur ce qui affiche le contenu du conteneur 
docker exec my_container ls /app 

# ouverture d'une session interactive avec un terminal bash 
docker exec -it my_container /bin/bash

# exécution de commande en mode détaché. Permet d'exécuter une commande sans avoir besoin d'attendre qu'elle se termine.
# la commande vient crér un fichier vide dans le repertoire /app
docker exec -d my_container touch /app/newFile.txt

# transmettre une variable d'environnement 
# la variable MY_VAR est transmises au conteneur et lance la commande env pour afficher toutes les variable env dans le conteneur
docker exec -e MY_VAR=value my_container env 
```

On pourras de cette manière créer des scripts pour automatiser des tâches : 
```bash 
#!/bin/bash 
for container in $(docker ps -q); do 
    docker exec $container uptime 
done
```
Ce script exécute la commande `uptime` sur tout les conteneur en cours d'exécution et affiche leur durée de fonctionnement


