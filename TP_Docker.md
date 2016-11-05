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
$ docker run -d ubuntu /bin/sh -c "while true; do echo 'I am a daemon 😈'; sleep 1; done"
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

Selon votre OS de la machine host, vous n'avez pas acces à internet depuis le container. Sur les machines du départements informatique, il vous faudra rajouter l'option ```--net=host```. Ainsi, les ports de la machine host seront directement bindé au port du container et vous pourrez avoir accès à internet depuis le container.

```
$ docker run --net=host -it ubuntu /bin/bash
```

## Binding

Le but de cette partie est de vous faire installer un serveur web (Nginx, Apache ou autre) sur un container docker.


1. Lancer un shell interactif à partir d'une image Ubuntu sur un container (et "bind" le port 80 du container sur le port 80 du host si vous n'êtes pas sur une machine du département).
2. Installer un Apache ou un Nginx ou autre selon votre choix
3. Ouvrez votre navigateur favoris et rendez vous sur [http://0.0.0.0](http://0.0.0.0/). Vous devriez voir votre page d'accueil.

Sur les machines du département :

```
$ docker run --net=host -it  ubuntu /bin/bash
```

Sur une autre machine :

```
$ docker run --net=host -it -p 9001:80 ubuntu /bin/bash
```

Vous accedez alors à votre serveur qui tourne sur le container. C'est super mais ca sert à quoi ? Tkt morray. tu vas voir ça après !

## Dockerfile 

Nous avons vu comment créer des containers, les lancer et les binder. Mais le vrai interet de Docker, c'est de pouvoir définir des containers qui tourneront sur n'importe quelle machine. Finis les :

> "But it worked on my machine"

En vous aidant du cours, créer un fichier ```Dockerfile``` qui fait tourner sur le port 80 un serveur web Nginx, qui est bind au port 9001 de la machine host.

```
FROM ubuntu
MAINTAINER FTM version: 0.1
RUN apt-get update && apt-get install -y nginx
EXPOSE 80
RUN "nginx"
```

Buildez l'image de votre container basé sur le Dockerfile en utilisant la commande suivante :

```
$ docker build -t <name:version> .
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


## RubyChat

Dans cette deuxième partie, vous allez devoir monter une architecture avec un client web, une API Ruby et une base de données _moderne_ mongoDB.

Commencez par créer un répertoire nommé ```RubyChat```. Clonez les deux GIT suivants dans ce repertoire :

```
git clone https://github.com/fthomasmorel/RubyChat-frontend.git RubyChat-Front
git clone https://github.com/fthomasmorel/RubyChat-backend.git RubyChat-Back
```

### RubyChat

RubyChat est une application en passe de rivaliser avec les plus grandes applications _moderne_ de messageries instantanné tel que AIM, MSN ou ICQ.

La particularité de RubyChat est qu'il n'existe qu'un seul et unique canal. En outre, tous les messages sont centralisé sur une seule fenetre de conversation. Cela permet de communiquer avec le monde entier de manière très simple 🖖🏼.

D'un point de vue technique, l'application est composée d'une API REST Ruby qui met a disposition 2 endpoints:

```
GET /messages # return all the messages of the chat
POST /messages # post a message from the json body on the chat
```

Un message a pour structure :

```
{
	username: String 		# the sender username
	content: String 		# the message content
	date: Date 				# the date when the message has been posted
}
```

De l'autre coté, une partie front-end, écrite en AngularJS, permet d'afficher l'ensemble des messages ainsi que d'en envoyer.

L'application est en train de grimper dans les charts, et l'équipe de développeur commence a penser au future. 

- Comment rendre l'application RubyChart scalable pour supporter la charge des futures 100 000 000 d'utilisateurs ? 
- Comment permettre aux développeurs de maintenir facilement leur code ?
- Quel workflow adopté ?

### RubyChat-Back

La partie backend de l'application a été écrite en ```ruby```. Ce langage utilise un gestionnaire de paquet appelé ```gem```. Cela permet d'installer des dépendences facilement :

```
gem install <package> # install the given package on the machine
```

Voici les dépendences de l'API :

```
sinatra						# framework for the API REST design
mongo						# ruby client for mongoDB
bson_ext					# mongoDB stuff
sinatra-cross_origin		# sinatra stuff to handle cross_origin 
json						# json lib
```

Le code entier de l'API est contenu dans ```rubychat.rb```. L'application recoit des requetes HTTP sur le port ```8080```.

### RubyChat-Front

L'application web, quant a elle, a été ecrite avec le framework AngularJS (v1). Voici l'architecture du repertoire RubyChat-Front :

```
|
|--css/ 				# contains the CSS for the web app
|--js/
|	 |---login.js 		# contains the code to log in the chat
|	 |---chat.js 		# contains the code to read and send messages
|
|--templates/
|	 |---login.html 	# contains the code for the login view in html
|	 |---chat.html 		# contains the code for the chat view in html
|
|--app.config.js 		# define constant for the web app (API URL)
|--app.js				# define the AngularJS app
|--index.html			# the index page of the web app
|--LICENCE				# who cares?
```

### Architecture 

![](img/rubychat_architecture.png)

Le serveur web ```nginx``` permet de servir les fichiers de la partie web. Aussi, il redirige les requetes qui arrive sur ```/api``` vers l'application ```ruby```. Cette dernière communique avec la base de données ```mongodb```.

## Docker-compose

Afin de répondre au problématique posées par les developpeurs de RubyChat, vous allez devoir dockerizer les differents module de l'application. Le but de l'exercice est de voir comment on peut monter une architecture tel que celle de RubyChat en utilsant des containers.

### RubyChat-Front

La première étapes consiste a créer des containers pour chacunes des instances suivantes :

- l'application ruby
- mongoDB
- nginx

### Ruby

Créez un ```Dockerfile``` dans le repertoire ```RubyChat-Back```. En partant de l'image docker de base ```ruby```, vous allez devoir définir l'envirionement necessaire a la bonne execution du script ```rubychat.rb``` (cf. définition plus haut). Vous devez :

- Installer les dépendences sur le containers
- Copier le code source sur le containers
- Exposer le port de l'API
- Définir un point d'entré pour executer ```rubychat.rb```

Une fois le ```Dockerfile``` définit, vous pouvez le build avec la commande suivante :

```
docker build -t ruby_api:v1 .
```

Le container ```ruby``` n'ayant pas accès à la base de données mongoDB, il ne peut fonctionner pour l'instant.

### nginx

Pour le container ```nginx```, nous allons partir de l'image de base docker ```nginx```. Dans le repertoire ```RubyChat-Front```, créer un fichier ```Dockerfile```. Puis, vous devez :

- Partir de l'image de base ```nginx```
- Copier un fichier de configuration pour nginx dans ```/etc/nginx/nginx.conf```
- Copier l'ensemle des fichiers ```angularJS``` sur le container
- Exposer le port d'écoute de votre ```nginx```.

Enfin, vous pouvez build ce container :

```
docker build -t rubychat_nginx:v1 .
```
Pour le moment, nginx ne connait pas encore le host pour joindre l'API ruby, il ne vous ai pas possible de lancer le container pour l'instant.

### mongoDB

Pour le container ```mongoDB```, rien de plus simple. Il suffit d'utiliser l'image de base docker ```mongodb```. Elle vous permettra de faire tourner une instance de mongoDB facilement.

### Configurer docker-compose

```docker-compose``` a besoin de deux fichiers pour être configuré. Un fichier ```docker-compose.yml``` qui permet de définir les containers et leur liens, et un fichier ```requirements.txt``` qui permet de lister les images necessaires à l'architecture définit dans le ```docker-compose.yml```.

À la racine du dossier ```RubyChat```, créez ces deux fichiers. Dans le fichier ```requirements.txt```, listez les images de base que vos containers utilises :

```
ruby
mongo
nginx
```

Dans le fichier ```docker-compose.yml```, completez le squelette suivant et supprimez les commentaires :

```
version: '2'
services:
  nginx:
    build: ...		# Path to your web server Dockerfile
    ports:
     - ...			# Port binding between the host machine and the nginx 
    depends_on:
      - ... 		# Container on which nginx depends on
  ruby_api:
    build: ...		# Path to your ruby app Dockerfile
    depends_on:
     - ...			# Container on which ruby_api depends on
  mongo:
    image: ...		# Image for the mongodb container
```

Penser à mettre à jour les URL de l'API dans la partie AngularJS et aussi l'host pour la base de donnée dans le code de l'API ruby. Pour tester votre installation, utilisez :

```
docker-compose up # use the --build option to force rebuilding images
```

Utilisez votre instance de RubyChat sur [http://localhost:PORT/login](). 

# 🐳