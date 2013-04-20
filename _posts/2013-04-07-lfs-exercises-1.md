---
title: LFS - Exercises 1
layout: default
category: linuq
---

Exercice 1 : bashrc avec du style.
----------------------------------
En équipe de 2

Le but est de rendre le login en console d'un utilisateur le plus
intéressant possible. 

Commencez par écrire un minimum de 3 scripts shell qui affichent de
l'information à l'écran. Voici des idées de scripts :

 * Afficher le nom de l'utilisateur
 * Afficher la date
 * Du ASCII Art
 * Le proverbe du jour (faites une recherche sur google pour la commande "fortune")
 * Le nombre de paquets à mettre à jour
 * La version du kernel qui roule
 * Du lorem ipsum

Une fois fini, placez ces scripts à des endroits aléatoires sur la
machine virtuelle de votre coéquipier.


Une fois les scripts en place, créer un nouvel utilisateur sur votre vm.
Le PATH dans le bashrc doit être configuré de sorte à pouvoir éxécuter
tout les scripts aléatoires de votre coéquipier. À la fin du bashrc,
faites éxécuter chacun des scripts.


Exercice 2 : linking avec du style
----------------------------------
En équipe de 2

Reprenez les scripts bash du premier exercice, mais cette fois ci
reprogrammez chacun des script en un fichier de code c (un ficher c par
script). Une fois chacun de vos fichier écrits, écrivez un script bash
qui génère chacun des fichiers objets puis qui les assemble ensemble à
la fin
