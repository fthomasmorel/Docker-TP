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

On parle de _machine hôte_ ou _host machine_ lorsqu'il s'agit du système d'exploitation qui va faire tourner les _conteneurs_ ou _containers_. Ces _conteneurs_ sont générés à partir d'_images_. Docker est installé sur la _machine hôte_ et permet de se provisionner en _images_ à partir de _hub_ comme _docker hub_ ou bien en générant ses propres _images_ à partir d'un _dockerfile_. C'est à partir de ces _images_ que l'on peut générer et déployer des _conteneurs_ qui correspondent à un instantané d'une _image_.