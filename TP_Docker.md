# TP Docker

## Objectif

Le but de ce TP est de prendre en main Docker et de comprendre pourquoi ce système de virtualisation est de plus en plus utilisé. Pour cela, une première partie va vous permettre de vous familiariser avec le principe de Docker. Dans une deuxième partie, vous allez monter une architecture complète composée d'un serveur, d'une application et d'une base de données. En utilisant Docker, cette architecture sera facilement déployable et "scalable".

## Premiers Pas

### Installer Docker

Si Docker n'est pas installé, rendez-vous sur la [documentation de Docker](https://docs.docker.com) et suivez les étapes pour l'installer sur votre machine.

Pour vérifier que Docker est bien installé, exécutez la commande suivante :

```
$ docker -v            
```
Cela devrait vous retourner la sortie suivante :

```
Docker version 1.12.3, build 6b644ec
```

### Lancer un container

Un container Docker est la brique de base. C'est une instance qui permet d'isoler une partie des ressources du système. Comme vue en cours, un container est différent d'une machine virtuelle de part son architecture avec le système. (TODO)

Pour commencer, nous allons lancer un container Ubuntu.

```
docker run ubuntu /bin/echo 'Hello world'
```

Cette commande permet de lancer un container basé sur une image Ubuntu et va exécuter la commande "echo" afin d'afficher un "Hello World" à l'écran (pour ceux qui n'ont pas le TOEIC, "Bonjour le monde"). À la suite de ça, le container meurt (snif).

### Daemoniser un container

Exécutez la commande suivante :

```
$ docker run -d ubuntu /bin/sh -c "while true; do echo "I am a daemon 😈"; sleep 1; done"
```

L'option ```-d``` permet de daemoniser le container. Celui-ci tourne en background. Vous ne voyez pas d'output mise a part un ```containerID```.

### Toc toc toc ! Qui est là ?

Affichez la liste des containers actifs sur votre machine en utilisant ```docker ps```. Vous devriez retrouver votre container Ubuntu.

### Soyez fort, reprenez le dessus

Retrouvez la sortie standard de votre container en l'attachant à la console :

```
$ docker attach <CONTAINER_ID>
```

### Interactive

Lancez un shell sur un container en interactif en utilisant la commande suivante :

```
$ docker run -it ubuntu /bin/bash
```

Vous pouvez alors exécuter des commandes sur un système Linux qui tourne sur un container Docker de votre machine Linux **#inception**

## Binding

Maintenant, nous allons vous faire installer un serveur web (Nginx, Apache ou autre) sur un container Docker (comme dans la vraie vie).

```
$ docker run -it -p 9001:80 ubuntu /bin/bash
```

1. Lancer un shell interactif à partir d'une image Ubuntu sur un container et "binder" le port 80 du container sur le port 9001 du host. (cf. Cours)
2. Installer un serveur Apache ou Nginx ou autre selon votre choix
3. Ouvrez votre navigateur favoris et rendez vous sur [http://0.0.0.0:9001/](http://0.0.0.0:9001/). Vous devriez voir votre page d'accueil.

Vous accédez alors à votre serveur qui tourne sur le container. C'est super mais ça sert à quoi ? Tkt morray, tu vas voir ça après !

## Dockerfile

Pour résumer, nous avons vu comment créer des containers, les lancer et les binder. Mais le vrai intéret de Docker, c'est de pouvoir définir des containers qui tourneront sur n'importe quelle machine. Fini les :

> "But it worked on my machine"

En vous aidant du cours, créer un Dockerfile qui fait tourner sur le port 80 un serveur web Nginx, qui est bind au port 9001 de la machine host.

```
FROM ubuntu
MAINTAINER FTM version: 0.1
RUN apt-get update && apt-get install -y nginx
EXPOSE 80
RUN "nginx"
```

Buildez l'image de votre container basé sur le Dockerfile en utilisant la commande suivante :

```
$ docker build <name:version> -t .
```

Consultez la liste des images disponible localement avec :

```
$ docker images
```

Vous devriez voir apparaître votre nouvelle image. Voilà, vous pouvez désormais lancer plusieurs petit bébé nginx sur votre machine en utilisant :

```
$ docker run -dit -p 9002:80 <name:version> nginx -g "daemon off;"
```

> L'option de ```-g``` permet de rajouter une direction en dehors du fichier de configuration. Ici, on mentionne que ```nginx``` ne tourne pas en tant que daemon ce qui permet de garder le container actif.

Exécutez un ```docker ps``` pour voir votre liste de container actif. Vous pouvez arrêter un container à l'aide de :

```
$ docker stop <CONTAINER_ID>
# OR
$ docker stop <CONTAINER_NAME>
```

## Docker Hub

Une manière plus simple de monter un container ```nginx``` est d'utiliser l'image officiel disponible sur ```DockerHub```. Pour se faire, rendez-vous sur [https://hub.docker.com](https://hub.docker.com). Vous pourrez alors chercher des images préfaites tel que celle de ```nginx```. 

Vous pouvez également utiliser le terminal pour explorer Docker Hub.

```
$ docker search <name> # for instance nginx
```

Pour télécharger une image, exécutez :

```
$ docker pull nginx
```

Désormais, lancez le container basé sur l'image nginx en exécutant :

```
$ docker run -dit -p 9001:80 nginx
# docker run -dit -p PORT:80 <img_name>
```

## Volume partagé 

Super, on sait monter un container nginx, mais on aimerait bien pouvoir le configurer un petit peu et mettre une page d'accueil.

Il suffit d'utiliser l'option ```-v``` pour exécuter l'équivalent d'un binding entre la machine host et le container.

Créez un répertoire ```nginx```. Dans ce répertoire, créez un fichier HTML (```test.html``` par exemple) et ajoutez-y du code (faites pas un truc qui clignotte, ça fait mal aux yeux 😎). Depuis le répertoire ```nginx```, lancer un container "nginx" avec l'option ```-v```, tel que :

```
$ docker run -dit -p 9001:80 -v $PWD:/usr/share/nginx/html nginx
```

Ici, on indique que notre répertoire courant (```$PWD```) sera relié à ```/usr/share/nginx/html```. Ce dernier répertoire est le repertoire du serveur web nginx. Rendez-vous sur [http://0.0.0.0:9001/test.html](http://0.0.0.0:9001/test.html).

Il est possible de partager des fichiers, ou de copier des fichers sur le container en utilsant ```docker cp``` ou la directive ```COPY``` dans le Dockerfile.

## Exercice

Pour débloquer la suite de ce TP, vous devez créer un container à partir de l'image ```tp_docker_insa```. Lancez ce container afin qu'il exécute la commande ```decrypt``` qui prend en paramètre la chaîne suivante :

> "tIzFzHFzSaaF9vaHKC1F01HF39aaF6IzHWFI3F01HF3IWwH"

Il rendra alors le message dechiffré sur la sortie standard. Donnez ce message au responsable de TP pour acceder à la suite.

## Cheatsheet Docker

### Docker in CLI
```
docker run <IMAGE> <COMMAND_TO_EXECUTE> # start a container
docker run -it <IMAGE> /bin/bash # start a container and get the shell
docker run -d <IMAGE> <COMMAND_TO_EXECUTE> # start a container in the background
docker run [...] -p PORT_HOST:PORT_DEST <IMAGE> <COMMAND_TO_EXECUTE> # bind port of a container
docker run [...] -v path/to/host/dir:path/to/container/dir <IMAGE> <COMMAND_TO_EXECUTE> # share volume between host machine and container

docker ps # list all running container
docker stop <CONTAINER_ID> # stop a given container its ID
docker stop <NAME_ID> # stop a given container with its name

docker build -t <NAME:VERSION> <PATH/TO/DOCKERFILE> # build an image from the dockerfile located in the given directory
docker images # list all local available images

docker search <IMAGE_NAME> # serach image_name through Docker Hub
docker pull <IMAGE_NAME> # download image_name locally from Docker Hub
```

### Dockerfile
```
FROM <image_base>
MAINTAINER <your_name> version: <version_number>
RUN <command_to_execute>
EXPOSE <port_to_expose>
```


## Docker-compose

```
git clone git@github.com:fthomasmorel/RubyChat-frontend.git RubyChat-Front
git clone git@github.com:fthomasmorel/RubyChat-backend.git RubyChat-Back
```

```

```