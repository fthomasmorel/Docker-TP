Docker - Build, Ship, Run
----------------

**Présentation**

L'objectif de ce cours est de présenter et d'utiliser Docker afin d'automatiser le déploiement d'applications dans des conteneurs logiciels. De plus en plus populaire, cet outil permet de faire de la virtualisation dite "légère" et donc d'améliorer les performances de ses applications. Dans un premier temps, nous allons découvrir les particularités de Docker et sa prise en main. Dans un second temps, nous allons mettre en pratique 

**Sommaire**

 1. Historique de la virtualisation
 2. Virtualisation lourde
 3. Virtualisation légère
 4. Comparaison
 4. Un peu de vocabulaire
 5. Le Dockerfile
 6. Docker Compose
 7. Les bonnes pratiques
 8. Docker Hub: Comment automatiser son workflow

**Un peu de vocabulaire**

Docker repose sur différents concepts bien distincts. Il est important de bien distinguer ces derniers.

On parle de _machine hôte_ ou _host machine_ lorsqu'il s'agit du système d'exploitation qui va faire tourner les _conteneurs_ ou _containers_. Ces _conteneurs_ sont générés à partir d'_images_. Docker est installé sur la _machine hôte_ et permet de se provisionner en _images_ à partir de _hub_ comme _docker hub_ ou bien en générant ses propres _images_ à partir d'un _dockerfile_. C'est à partir de ces _images_ que l'on peut construire et déployer des _conteneurs_ qui correspondent à un instantané d'une _image_. On verra tout au long de ce cours qu'il est possible d'exposer des _ports_ et des _volumes_ afin de clairement isoler nos _conteneurs_.

**Le Dockerfile**

Docker utilise les _directives_ d'un _Dockerfile_ afin de construire un _conteneur_. Ces _directives_ sont exécutées à la suite les unes des autres. Le _contexte_ dans lequel une _image_ est construite est tout aussi important. Enfin, Docker décompose chacune des étapes de la construction d'un image en _layers_ ou __couches_. Ce mécanisme permet d'augmenter de manière significative des constructions ultérieures. En effet, un _layer_ pré-existant pourra être utilisé afin d'augmenter la vitesse de construction.

####FROM

	FROM <image>:<tag>

La directive __FROM__ indique à partir de quelle _image_ va être construite la nouvelle. Le _tag_ correspond a la version de l'image qui va être utilisée. Le _tag_ est facultatif et est _latest_ par défaut.

####MAINTAINER 

	MAINTAINER <name>

La directive __MAINTAINER__ permet de mentionner l'auteur de l'image.

####RUN 

	RUN <command>
	RUN ["executable", "param1", "param2"]

La directive __RUN__ possède deux syntaxes différentes. Celle-ci, comme son nom l'indique, permet d'exécuter des commandes au sein de l'image lors de la construction. A noter que le répertoire d’exécution de ces commandes peut être défini à l'aide de la directive __WORKDIR__.

####WORKDIR 

	WORKDIR /path/to/workdir

La directive __WORKDIR__ permet de définir le répertoire courant pour l'ensemble des autres directives suivantes.

####USER

	USER elucterio

La directive __USER__ permet de définir l'utilisateur qui exécutera les directives __RUN__, __CMD__ and __ENTRYPOINT __ qui suivent dans le _Dockerfile_.

####CMD 

	CMD ["executable","param1","param2"]
	CMD command param1 param2

La directive __CMD__ définie la première commande qui sera exécutée par un _conteneur_ construit à partir de cette _image_.  A noter que si plusieurs directives__CMD__ sont présente sans le Dockerfile, seule la dernière sera prise en compte.

####EXPOSE 

	EXPOSE <port> [<port>...]

La directive __EXPOSE__ quant à elle, permet d'exposer des ports qui seront accessibles en dehors du _conteneur_ par le système d'exploitation _hôte_. Il faut noter qu'un port exposé doit tout de même être rendu accessible à l'aide du flag `-p <port>:<public_port>` lors de l'exécution du _conteneur_.

####VOLUME

	VOLUME ["/data"]

La directive __VOLUME__ permet d'exposer des volumes qui seront accessibles en dehors du _conteneur_ par le système d'exploitation _hôte_. Il faut noter qu'un volume exposé doit tout de même être rendu accessible à l'aide du flag `-v <volume>:<public_volume>` lors de l'exécution du _conteneur_.

####ENV

	ENV <key> <value>
	ENV <key>=<value> ...

La directive __ENV__ permet de définir des variables d'environnement pour la construction mais qui persisteront aussi dans les _conteneurs_ construits à partir du _Dockerfile_.

####COPY

	COPY <src>... <dest>
	COPY ["<src>",... "<dest>"]

La directive __COPY__ permet d'ajouter des fichiers ou dossiers dans _l'image_. Le chemin _src_ correspond à un chemin d'accès absolu ou relatif de la _machine hôte_ et le chemin _dest_ correspond à chemin d'accès absolu ou bien relatif au chemin de la directive __WORKDIR__.

####ADD

	ADD <src>... <dest>
	ADD ["<src>",... "<dest>"]

La directive __ADD__ ressemble beaucoup à la directive __COPY__. Cependant, la directive __ADD__ autorise les url pour _src_ et si c'est un archive, elle sera automatiquement décompressée.

####Exemple de Dockerfile

	# Firefox over VNC
	#
	# VERSION               0.3

	FROM ubuntu

	# Install vnc, xvfb in order to create a 'fake' display and firefox
	RUN apt-get update && apt-get install -y x11vnc xvfb firefox
	RUN mkdir ~/.vnc
	# Setup a password
	RUN x11vnc -storepasswd 1234 ~/.vnc/passwd
	# Autostart firefox (might not be the best way, but it does the trick)
	RUN bash -c 'echo "firefox" >> /.bashrc'

	EXPOSE 5900
	CMD    ["x11vnc", "-forever", "-usepw", "-create"]

Vous pouvez retrouver l'intégralité des directives et d'autres exemples dans la documentation officielle de Docker : https://docs.docker.com/engine/reference/builder/
