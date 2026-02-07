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