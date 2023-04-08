---
title: Docker Formation Notes
cover: https://img.hendryshi.com/i/2023/04/6431c79d67ba8.webp
categories:
  - Tech
tags:
  - Docker
  - Français
abbrlink: e7994309
date: 2023-01-29 12:23:57
---

I took part in a formation for docker technology last week. Here I will list the notes I take during the formation.


## De la virtualisation à docker

"Build, Ship, and Run Any App, Anywhere", docker est un outil de packaging, pour résoudre le problème: Environnements différents, Librairies manquantes, Versions logicielles différentes

{% note info modern no-icon %}
 => De la nécessité d'avoir des environnements d'exécution identiques
{% endnote %}

 <!-- more -->

### Différents types de virtualisation

- Scripts  d'installation automatisée
  - Ansible
  - D: il faut temps en temps faire un reset
- Machines virtuelles
  - Linux, Windows
  - A: Il y a plus de chose dedans: système d'exploitation
    - Peut déployer plusieurs applications dans le même VM
    - pratique pour tester des logiciels
  - D: Consommer beaucoup de ressources 
    - Lent
    - Tâches répétitives
    - Configuration manuelle
    - Dur d'apporter de la scalabilité
- Conteneurs
  - Partager un même noyau de linux (le noyau le plus bas)
  - Image embarque l'application et dépendances
  - Configuration est portée par l'image
  - A: Beaucoup plus léger que le VM
    - Meilleur performances
    - Plus simple à déployer
    - Facilité de configuration/stop/redémarrer/Reproductibilité
  - D: Toutes les applications ne peuvent pas tourner dans un conteneur
    - Faire confiance aux images téléchargées

### La Conteneurisation

- Fonctions Kernel: Namespaces
  - mnt (montage): `docker run --v hostpath:dockerpath`
    - The volumn share the same directory
  - pid(processus)
  - net(réseau)
  - ipc(communication inter processus)
  - uts(hostnames)
  - user(UIDs)
- Cgroups
  - Limitation des ressources: CPU, RAM
  - Priorisation: ressources processeur, I/O
  - Comptabilité: quantité de ressources consommées
  - Isolation: séparation par espace de nommage pour les groups

### Le positionnement de docker

- Docker ne remplace pas les conteneur, il les utilise s'il existes dans le cache
- Registre d'image: https://hub.docker.com/search

### Des outils pour développeurs

- Docker avant le compilation, ensuite compiler dans un docker => enchainer dans le deuxième docker pour fabriquer le containeur
- Choix de version de compilateurs/Système

## L'architecture de docker

![dockerArcht](https://img.hendryshi.com/i/2023/04/6431c7c2d11ea.webp)

- Le client
  - docker [OPTIONS] COMMAND [ARG...]
    docker [ --help | -v | --version ]
- Le daemon (serveur)
  - *On peut configurer le hostname pour manager le docker dans autre machine*, sinon il prends localhost
- Docker Hub (dépots d'images)
  - Il faut mieux de créer son registry privée (https + login + password)

- Fichier de configuration: Situé à ~/.docker/config.json

## Les commandes essentielles

### Docker commande

- Docker run
  - docker run => docker pull (télécharger l'image) + docker create (crée le conteneur) + docker start (lancer le conteneur)
- Lister les images
  - `docker image ls / docker images`
- Lister les conteneurs
  - `docker ps / docker container ls`
  - `docker ps -a`  => lister tous (actifs et arrêtés)
- Lister les logs d'un conteneur
  - `docker logs container_name`
- Inspecter un conteneur
  - `docker inspect container_name`
- Démarrer un conteneur
  - `docker start --**attach** container_name` 
    - attach: bloquer le shell de l'host
- Arrêter/tuer/supprimer un conteneur
  - `docker stop container_name`
  - `docker kill container_name`
  - `docker rm container_name`
- Supprimer conteneur après qu'il ait terminé
  - `docker run --**rm** image_name`

### Docker Hub

- Registre publique des images officielles fournies par Docker
- Fournit des services supplémentaires aux utilisateurs connectés
  - Gestion de repositories, publiques ou privés
  - Build automatique d'images depuis un dépôt Git
- Rechercher: `docker search key_word`
- Télécharger: docker `pull private_sitename/image_name:tagversion`

### Espace de stockage

2 options pour spécifier points de montage dans le conteneurs: volumns/bind mouts

#### Volumn

- Stocké sur le filesystem hôte : /var/lib/docker/volumes
- Gérés par `docker volume`

```sh
$ # Créer un volume nommé "myvol"
$ docker volume create myvol
$ # Créer un volume nommé par Docker
$ docker volume create
$ # Lister tous les volumes
$ docker volume ls
DRIVER VOLUME NAME
local a50e5c5512afb75eba405436f3e9cf26eb318dbb71e3df8436ca050a
local myvol
$ # Supprimer un volume
$ docker volume rm myvol
mystorage
$ # Supprimer tous les volumes non utilisés
$ docker volume prune
WARNING! This will remove all local volumes not used by at least one contain
Are you sure you want to continue? [y/N] y
Deleted Volumes:
a50e5c5512afb75eba405436f3e9cf26eb318dbb71e3df8436ca050ad765f346
```

- Montage: 
  - `docker run -v myvol:/data`
  - `docker run --mount 'source=myvol,target=/data'`
  - Si myvol existe pas, crée automatiquement

#### Bind mount

- Dossier quelconque du filesystem hôte
- Non géré par Docker, peut donner accès à des fichiers sensibles
- Montage:
  - `docker run -v /home/toto:/data`
  - `docker run --mount 'type=bind,source=/home/toto, target=/data'`
  - Source doit exister

#### Le commande cp

```sh
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```

- Exemple avec arguments -
  - `docker cp dbstore:/dbdata -> dbdata.tar`

### Networks

![dockerNetwork](https://img.hendryshi.com/i/2023/04/6431c7ca9a56d.webp)

#### Publier le port:

```sh
$ # Lancement du serveur nginx
$ docker run \
--name some-nginx \
-v $(pwd):/usr/share/nginx/html:ro \
-d \
--publish 1234:80 \
nginx
$ # Mapping visible dans la liste des conteneurs
$ docker ps
CONTAINER ID IMAGE … PORTS NAMES
2fa02eb3c662 nginx … 0.0.0.0:1234->80/tcp some-nginx
$ # Lancement d’une requête vers le serveur
$ curl -I http://localhost:1234
HTTP/1.1 200 OK
Server: nginx/1.15.8
```

#### 3 type de réseaux

- Bridge: driver par défaut
- host: permet accès direct aux interface réseau de l'hôte
- none (pour empêche l'accès au réseau)

##### Bridge

- Permet l'accès à l'extérieur de l'hôte
- Les conteneurs peuvent communiquer entre eux via ce réseaur, mieux éviter de tout brancher sur bridge
- Utiliser un réseau bridge spécifique
  - docker network create mynet
  - docker run --name mycontainer --network mynet
- Tous les conteneurs branché sur **mynet** se voient

## Création de conteneurs personnalisés

### Dockerfile

- FROM : précise l’image de départ
- LABEL : annote l’image
- RUN : exécute une commande pour modifier l’image
- EXPOSE : définit un port utilisé par le conteneur
- ENV : positionne une variable d’environnement de l’image
- COPY et ADD : copient des fichiers/dossiers du hôte vers l'image
- CMD et ENTRYPOINT : définissent le programme lancé au
- démarrage du conteneur
- VOLUME : crée un dossier destiné à être monté sur FS hôte
- WORKDIR : positionne le dossier courant du build

```dockerfile
FROM node:18.13.0
WORKDIR /usr/src/app
COPY server.js ./
EXPOSE 3000
CMD  ["node", "server.js"]
```

### Construire une image

- `docker build -t myImage:tagversion` .
- `docker image push registry-host:5000/myadmin/myImage:latest`

### Layers et image cache

- Chaque instruction du Dockerfile crée une image intermédiarie
- Une image intermédiarie est mies en cache pour rebuild rapide
- Faut meux d'uiltiser 1 seule instruction RUN
  - `RUN apt update && apt install … && rm -rf /var/lib/apt/lists/*`

### ENTRYPOINT et CMD

#### CMD

- Définit la commande lancé par défaut au démarrage

- ```dockerfile
  FROM alpine
  CMD echo Hello
  ```

  `docker build -t hello .`

  `docker run hello => Hello`

- Override si commande arrière

  - docker run hello echo Goodbye => Goodbye

#### ENTRYPOINT

- Définit la commande lancée au démarrage

- Peut être override par option --**entrypoint** de create ou run

- ```dockerfile
  FROM alpine
  ENTRYPOINT echo Hello
  ```

- `docker build -t hello .`

  `docker run hello => Hello`

  `docker run --entrypoint echo Goodbye => Goodbye`

#### Exec Form

- ```dockerfile
  FROM ALPINE
  CMD ["echo", "Hello"]
  # ou
  ENTRYPOINT ["echo", "Hello"]
  ```

#### Utilisation conjointe

- ENTRYPOINT: définit programme à lancer

- **CMD: définit arguments par défault**

- ```dockerfile
  FROM alpine
  CMD ["World"]
  ENTRYPOINT ["echo", "Hello"]
  ```

- `docker build -t hello .`

- `docker run hello` => Hello World
  `docker run hello there` => Hello there

### Multi-stage build

- Possibilité d'avoir plusieurs **FROM** dans un Dockerfile
- Chaque **FROM** commence un stage du build
- => 1 seul Dockerfile suffit pour compiler/empaqueter l'application

```dockerfile
#-------------------------------------
# Étape de "compilation" de l’application

FROM openjdk:8-jdk-alpine AS build
COPY src /home
WORKDIR /home
RUN ["javac", "Main.java"]
#-------------------------------------
# Étape de "packaging" de l’application

FROM openjdk:8-jre-alpine
COPY --from=build /home/Main.class /Main.class
ENTRYPOINT [ "java", "Main" ]
```

## Mettre en oeuvre une application multiconteneur

### Docker compose

- Outil pour définir et lancer des applications multi-conteneurs
- 1 ficher YAML pour définir tous les services
- Commandes pour composer et gérer l'application

```yaml
version: "3.2"
volumes:
  datevol:
services:
  datelooper:
  image: alpine
  volumes:
    - "datevol:/data"
  command: sh -c 'while true; do
      echo $$(date) > /data/index.html;
      sleep 1 ;
    done'
dateserver:
  image: nginx:alpine
  volumes:
    - type: volume
      source: datevol
      target: /usr/share/nginx/html
      read_only: true
  ports:
    - "8081:80"
```

- Start multi-composant: `docker-compose up`

### Commandes de compostion utiles

```dockerfile
$ # Construire, créer et démarrer les conteneurs, en détaché
$ docker-compose up -d
$ # Tester le docker-compose.yml (conteneurs créés tout de même)
$ docker-compose up --no-start
$ # Arrêter et supprimer les conteneurs, networks,
$ # volumes, images créés par up
$ docker-compose down
$ # Arrêter un service en particulier
$ docker-compose stop datelooper
$ # Démarrer un service
$ docker-compose start datelooper
$ # Suivre les logs (Ctrl + C pour arrêter)
$ docker-compose logs -f
$ # Supprimer les conteneurs des services arrêtés
$ docker-compose rm
```

## Administraters des conteneurs en production

### Docker Registry

- Permet de stocker et distribuer images Docker "chez soi"
- Alternative à un accès privé Docker Hub

Mise en place

```sh
$ # Démarrer registry
$ docker run -d -p 5000:5000 --name registry registry:2
$ # Tagguer une image pour qu’elle pointe sur le registre
$ docker image tag my-image localhost:5000/my-image
$ # Envoyer l’image sur le registre
$ docker push localhost:5000/my-image
$ # Récupérer l’image du registre
$ docker pull localhost:5000/my-image
```

### Automatiser le démarrage des conteneurs

- Option --**restart** de docker run ou create
- **no** par défaut
- **on-failure**: redémarre en cas de plantage
- **always**: redémarre toujours sauf si arrêt manuel

### Gérer l'affectation des ressources

- Limiter l'empreinte mémoire
  - Option -m, --memory=
- Limiter le CPU
  - `docker run --cpus=".5" my-image`
- Gestion des logs
  - `docker logs my-container`
- Configuration des logs
  - Utiliser des plugins pour écrire les logs
    - **none** : désactiver les logs
    - **syslog** : utiliser le service syslog de l’hôte
    - **awslog** : envoyer les logs sur Amazon CloudWatch Logs