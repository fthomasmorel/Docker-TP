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

On parle de _machine h�te_ ou _host machine_ lorsqu'il s'agit du syst�me d'exploitation qui va faire tourner les _conteneurs_ ou _containers_. Ces _conteneurs_ sont g�n�r�s � partir d'_images_. Docker est install� sur la _machine h�te_ et permet de se provisionner en _images_ � partir de _hub_ comme _docker hub_ ou bien en g�n�rant ses propres _images_ � partir d'un _dockerfile_. C'est � partir de ces _images_ que l'on peut g�n�rer et d�ployer des _conteneurs_ qui correspondent � un instantan� d'une _image_.