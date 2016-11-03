# TP Docker

## Objectif

Le but à la fin de ce TP est de pouvoir monter une architecture composé d'un serveur, une application et une base de données. En utilisant Docker, cette architecture sera facilement deployable et scalable.

## Premiers Pas

### Installer Docker

Rendez vous sur la [documentation de Docker](https://docs.docker.com) et suivez les étapes pour installer Docker sur votre machine.

Verifier que Docker est bien installé en executant la commande suivante :

```
$ docker -v            
```
Cela devrait retourner la sortie suivante :

```
Docker version 1.12.1, build 6f9534c
```

### Lancer un container

Un container Docker est une instance qui permet d'isoler une partie des ressources du système. Un container est different d'une machine virtuelle de part son architecture avec le système (cf. Cours). 

Pour commencer, nous allons lancer un container Ubuntu.

```
docker run ubuntu /bin/echo 'Hello world'
```

Ici, on lance un container basé sur une image Ubuntu et qui va executer la commande "echo" afin d'afficher un "Hello World" à l'écran. À la suite de ça, le container meurt.

### Daemoniser un container

Executer la commande suivante :

```
$ docker run -d ubuntu /bin/sh -c "while true; do echo "I am a daemon 😈"; sleep 1; done"
```

L'option ```-d``` permet de daemoniser le container. Celui-ci tourne en background. Vous ne voyez pas d'output mise a part un ```containerID```.

### Qui est là ?

Afficher la liste des containers actifs sur votre machine en utilisant ```docker ps```. Vous devriez retrouver votre container Ubuntu.

### Reprenez le dessus

Retrouver la sortie standard de votre container en l'attachant :

```
$ docker attach <CONTAINER_ID>
```

### Interactive

Lancer un shell sur un container en interactive en utilisant la commande suivante :

```
$ docker run -it ubuntu /bin/bash
```

Vous pouvez alors executer des commandes sur un system linux qui tourne sur un container Docker **#inception**

## Binding

Le but de cette partie est de vous faire installer un serveur web (Nginx, Apache ou autre) sur un container docker.

```
$ docker run -it -p 9001:80 ubuntu /bin/bash
```

1. Lancer un shell interactif à partir d'une image Ubuntu sur un container et "bind" le port 80 du container sur le port 9001 du host. (cf. Cours)
2. Installer un Apache ou un Nginx ou autre selon votre choix
3. Ouvrez votre navigateur favoris et rendez vous sur [http://0.0.0.0:9001/](http://0.0.0.0:9001/). Vous devriez voir votre page d'accueil.

Vous accedez alors à votre serveur qui tourne sur le container. C'est super mais ca sert à quoi ? Tkt morray. tu vas voir ça après !

## Dockerfile 

Nous avons vu comment créer des containers, les lancer et les binder. Mais le vrai interet de Docker, c'est de pouvoir définir des containers qui tourneront sur n'importe quelle machine. Finis les :

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

Vous devriez voir apparaitre votre nouvelle image. Voilà, vous pouvez desormais lancer plusieurs petit bébé nginx sur votre machine en utilisant :

```
$ docker run -dit -p 9002:80 <name:version> nginx -g "daemon off;"
```

> L'option de ```-g``` permet de rajouter une direction en dehors du fichier de configuration. Ici, on mentionne que ```nginx``` ne tourne pas en tant que daemon ce qui permet de garder le container actif.

Executer un ```docker ps``` pour voir votre liste de container actif. Vous pouvez arreter un container à l'aide de :

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

Pour télécharger une image, executer :

```
$ docker pull nginx
```

Désormais, lancer le container basé sur l'image nginx en executant :

```
$ docker run -dit -p 9001:80 nginx
# docker run -dit -p PORT:80 <img_name>
```

## Volume partagé 

Super, on sait monter un container nginx, mais on aimerait bien pouvoir le configurer un petit peu et mettre une page d'accueil.

Il suffit d'utiliser l'option ```-v``` pour executer l'équivalent d'un binding entre la machine host et le container.

Créer un répertoire ```nginx```. Dans ce répertoire, créer un fichier html (```test.html``` par exemple) et ajoutez-y du code (faites pas un truc qui clignottes, ca fait mal aux yeux 😎). Depuis le répertoire ```nginx```, lancer un container "nginx" avec l'option ```-v```, tel que :

```
$ docker run -dit -p 9001:80 -v $PWD:/usr/share/nginx/html nginx 
```

Ici, on indique que notre répertoire courant (```$PWD```) sera relié à ```/usr/share/nginx/html```. Ce dernier répertoire est le repertoire du serveur web nginx. Rendez-vous sur [http://0.0.0.0:9001/test.html](http://0.0.0.0:9001/test.html).

Il est possible de partager des fichiers, ou de copier des fichers sur le container en utilsant ```docker cp``` ou la directive ```COPY``` dans le Dockerfile.

## Exercice

Pour débloquer la suite de ce TP, vous devez créer un container à partir de l'image ```tp_docker_insa```. Lancer ce container afin qu'il execute la commande ```decrypt``` qui prend en paramètre la chaine suivante :

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