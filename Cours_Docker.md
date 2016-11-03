Docker - Build, Ship, Run
----------------

**Pr�sentation**

L'objectif de ce cours est de pr�senter et d'utiliser Docker afin d'automatiser le d�ploiement d'applications dans des conteneurs logiciels. De plus en plus populaire, cet outil permet de faire de la virtualisation dite "l�g�re" et donc d'am�liorer les performances de ses applications. Dans un premier temps, nous allons d�couvrir les particularit�s de Docker et sa prise en main. Dans un second temps, nous allons mettre en pratique 

**Sommaire**

 1. Historique de la virtualisation
 2. Virtualisation lourde
 3. Virtualisation l�g�re
 4. Comparaison
 4. Un peu de vocabulaire
 5. Le Dockerfile
 6. Docker Compose
 7. Les bonnes pratiques
 8. Docker Hub: Comment automatiser son workflow

**Un peu de vocabulaire**

Docker repose sur diff�rents concepts bien distincts. Il est important de bien distinguer ces derniers.

On parle de _machine h�te_ ou _host machine_ lorsqu'il s'agit du syst�me d'exploitation qui va faire tourner les _conteneurs_ ou _containers_. Ces _conteneurs_ sont g�n�r�s � partir d'_images_. Docker est install� sur la _machine h�te_ et permet de se provisionner en _images_ � partir de _hub_ comme _docker hub_ ou bien en g�n�rant ses propres _images_ � partir d'un _dockerfile_. C'est � partir de ces _images_ que l'on peut construire et d�ployer des _conteneurs_ qui correspondent � un instantan� d'une _image_. On verra tout au long de ce cours qu'il est possible d'exposer des _ports_ et des _volumes_ afin de clairement isoler nos _conteneurs_.

**Le Dockerfile**

Docker utilise les _directives_ d'un _Dockerfile_ afin de construire un _conteneur_. Ces _directives_ sont ex�cut�es � la suite les unes des autres. Le _contexte_ dans lequel une _image_ est construite est tout aussi important. Enfin, Docker d�compose chacune des �tapes de la construction d'un image en _layers_ ou __couches_. Ce m�canisme permet d'augmenter de mani�re significative des constructions ult�rieures. En effet, un _layer_ pr�-existant pourra �tre utilis� afin d'augmenter la vitesse de construction.

####FROM

	FROM <image>:<tag>

La directive __FROM__ indique � partir de quelle _image_ va �tre construite la nouvelle. Le _tag_ correspond a la version de l'image qui va �tre utilis�e. Le _tag_ est facultatif et est _latest_ par d�faut.

####MAINTAINER 

	MAINTAINER <name>

La directive __MAINTAINER__ permet de mentionner l'auteur de l'image.

####RUN 

	RUN <command>
	RUN ["executable", "param1", "param2"]

La directive __RUN__ poss�de deux syntaxes diff�rentes. Celle-ci, comme son nom l'indique, permet d'ex�cuter des commandes au sein de l'image lors de la construction. A noter que le r�pertoire d�ex�cution de ces commandes peut �tre d�fini � l'aide de la directive __WORKDIR__.

####WORKDIR 

	WORKDIR /path/to/workdir

La directive __WORKDIR__ permet de d�finir le r�pertoire courant pour l'ensemble des autres directives suivantes.

####USER

	USER elucterio

La directive __USER__ permet de d�finir l'utilisateur qui ex�cutera les directives __RUN__, __CMD__ and __ENTRYPOINT __ qui suivent dans le _Dockerfile_.

####CMD 

	CMD ["executable","param1","param2"]
	CMD command param1 param2

La directive __CMD__ d�finie la premi�re commande qui sera ex�cut�e par un _conteneur_ construit � partir de cette _image_.  A noter que si plusieurs directives__CMD__ sont pr�sente sans le Dockerfile, seule la derni�re sera prise en compte.

####EXPOSE 

	EXPOSE <port> [<port>...]

La directive __EXPOSE__ quant � elle, permet d'exposer des ports qui seront accessibles en dehors du _conteneur_ par le syst�me d'exploitation _h�te_. Il faut noter qu'un port expos� doit tout de m�me �tre rendu accessible � l'aide du flag `-p <port>:<public_port>` lors de l'ex�cution du _conteneur_.

####VOLUME

	VOLUME ["/data"]

La directive __VOLUME__ permet d'exposer des volumes qui seront accessibles en dehors du _conteneur_ par le syst�me d'exploitation _h�te_. Il faut noter qu'un volume expos� doit tout de m�me �tre rendu accessible � l'aide du flag `-v <volume>:<public_volume>` lors de l'ex�cution du _conteneur_.

####ENV

	ENV <key> <value>
	ENV <key>=<value> ...

La directive __ENV__ permet de d�finir des variables d'environnement pour la construction mais qui persisteront aussi dans les _conteneurs_ construits � partir du _Dockerfile_.

####COPY

	COPY <src>... <dest>
	COPY ["<src>",... "<dest>"]

La directive __COPY__ permet d'ajouter des fichiers ou dossiers dans _l'image_. Le chemin _src_ correspond � un chemin d'acc�s absolu ou relatif de la _machine h�te_ et le chemin _dest_ correspond � chemin d'acc�s absolu ou bien relatif au chemin de la directive __WORKDIR__.

####ADD

	ADD <src>... <dest>
	ADD ["<src>",... "<dest>"]

La directive __ADD__ ressemble beaucoup � la directive __COPY__. Cependant, la directive __ADD__ autorise les url pour _src_ et si c'est un archive, elle sera automatiquement d�compress�e.

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

Vous pouvez retrouver l'int�gralit� des directives et d'autres exemples dans la documentation officielle de Docker : https://docs.docker.com/engine/reference/builder/

**Les bonnes pratiques**

###Pourquoi utiliser Docker? A quoi sert Docker?

Pour construire et partager des images disques.
Pour lancer facilement diff�rents OS.
Parce que contrairement � une VM, c'est tr�s l�ger en terme de requirement system.

###Quand utiliser Docker?

Quand on veut un syst�me de contr�le de version pour un OS complet
Quand on veut distribuer/collaborer sur une OS complet avec une �quipe
Quand on veut �xecuter son code sur son PC dans le m�me environnement que celui du serveur
Quand l'application dispose de plusieurs phases de d�veloppement dev/test/qa/prod

###Cas d'utilisation de Docker

Prenons un cas d'utilisation simple et concret. Une entreprise rennaise, souhaite d�velopper un projet Symfony comme ils savent le faire. Leur �quipe est compos�e de deux personnes.

Valentin est un vieux de la vieille. Lui, Debian 7, c'est parfait. De plus, il est ISO avec les serveurs de production.

 Florent lui est un vrai hippie. Il dispose de la derni�re distribution exotique. Lui, PHP, c'est la toute derni�re version ou rien. Cependant, il peut g�n�rer du code qui ne peut pas s�ex�cuter correctement avec une version plus vieille, comme celle de Valentin.

Jusqu'� aujourd'hui, on disait tous, � faisons des tests unitaires �. Oui, cela r�pondait � une bonne partie de nos probl�mes (� la condition de faire de bons tests unitaires complets).

En effet, �a pose probl�me que deux d�veloppeurs ne travaillent pas sur les m�mes environnements... Du coup, gr�ce � Docker, on peut faire en sorte que Valentin et Florent travaillent sur les m�mes versions Linux sans craindre des probl�mes de compatibilit� entre leurs codes respectifs ?

Exactement! Dans ce cas, le plus simple est de mettre en place un Dockerfile, document "chef d'orchestre", qui permettra � Valentin et � Florent de monter une image similaire. En �tant malin, ce Dockerfile sera calqu� sur les �l�ments pr�sents en production. De ce fait, Valentin et Florent en plus de travailler sur un environnement identique, seront sur un environnement similaire � celui de la production !

###Pourquoi Docker c'est bien?

Comme le container n'embarque pas d'OS, � la diff�rence de la machine virtuelle, il est par cons�quent beaucoup plus l�ger que cette derni�re. Il n'a pas besoin d'activer un second syst�me pour ex�cuter ses applications.
Cela se traduit par un lancement beaucoup plus rapide, mais aussi par la capacit� � migrer plus facilement un container d'une machine physique � l'autre, du fait de son faible poids.
Typiquement, une machine virtuelle pourra peser plusieurs Go, alors qu'un container nu repr�sentera, lui, quelques Mo.
Gr�ce � leur l�g�ret�, les containers Docker sont portables de cloud en cloud.

###C�t� d�veloppement

Docker peut apporter une plus-value en acc�l�rant les d�ploiements parce que les containers Docker sont l�gers. Les basculer d'un environnement de d�veloppement ou de test � un environnement de production peut donc se faire presque en un clic, ce qui n'est pas le cas pour la VM, plus lourde. Du fait de la disparition de l'OS interm�diaire des VM, les d�veloppeurs b�n�ficient aussi m�caniquement d'une pile applicative plus proche de celle de l'environnement de production.

Docker permet dans le m�me temps de concevoir une architecture de test plus agile, chaque container de test pouvant int�grer une brique de l'application (base de donn�es, langages, composants...). Pour tester une nouvelle version d'une brique, il suffit d'interchanger le container correspondant. C�t� d�ploiement continu, Docker pr�sente par ailleurs un int�r�t car il permet de limiter les mises � jour au container n�cessitant de l'�tre.

###C�t� production

Gr�ce � Docker, il est possible de contain�riser une application, avec pour chaque couche des containers isolant ses composants. C'est le concept d'architecture de microservices. Ces containers de composant, du fait de leur l�g�ret�, peuvent eux-m�mes, chacun, reposer sur les ressources machines voulues.

Sources:
http://www.journaldunet.com/solutions/cloud-computing/1146290-cloud-pourquoi-docker-peut-tout-changer/

https://www.wanadev.fr/23-tuto-docker-comprendre-docker-partie1/
