---
title: Plan atelier introduction au terminal
layout: default
category: linuq
---

Qu'est-ce qu'un terminal ?
==========================

 * Première Interface Utilisateur
 * Encore la norme pour l'administration système
 * Environnement de travail

Avantages
---------

 * Rapidité
 * Productivité
 * Debug d'un environnement linux
 * Programmer des tâches répétitives (scripts)
 * Combinaisons d'outils

Terminologie
------------

 * Terminal
 * Console
 * Ligne de commande
 * Shell

Ouverture de la console
-----------------------

TODO: explain prompt

Navigation dans le terminal
===========================

 * cd
 * cd ..
 * ls
 * ls -l
 * ls -a
 * pwd

Autocompletion
==============

 * Tab
 * Tab Tab
 * fleche vers le haut
 * history

Aide
====

 * ls --help
 * man ls

Navigation dans le système de fichier
=====================================

 * cd /
 * cd /home/user
 * ls /home/user
 * cd ~

Manipulation de fichiers/dossiers
=================================

 * mv
 * cp
 * cp -R
 * rm
 * mkdir

Création/Édition de fichiers
============================

 * cat
 * less
 * nano

Permissions sur les fichiers
============================

 * ls -l
 * chmod u+rwx
 * chmod g+rwx
 * chmod o+rwx
 * chown user
 * chgrp group

Recherche de fichier
====================

 * find -iname
 * updatedb && locate
 * grep

Tuer des processus
==================

 * ps
 * ps aux
 * kill
 * ps aux | grep

Changer d'utilisateur
=====================

 * su
 * sudo

Installation de paquets
=======================

 * aptitude search
 * apt-get update
 * apt-get install
 * apt-get upgrade

Manipulation d'utilisateur
======================

 * adduser
 * passwd
 * gpasswd

Info système
============

 * mount
 * df
 * du
 * top, htop
 * cat /proc/cpuinfo
 * free

Liens symboliques
=================

 * ls -l
 * ln -l

Filtres
=======

 * sort
 * wc
 * uniq
 * grep
 * head
 * tail
 * sed
 * awk

Expansion et wildcards
======================

 * etoile
 * brackets \[]
 * brackets {}



Raccourcis claviers
===================

 * Ctrl+C : arrêter la commande
 * Ctrl+R : recherche historique
 * Ctrl+A : debut de la ligne
 * Ctrl+E : fin de la ligne
 * Ctrl+U : supprimer jusqu'au curseur
 * Ctrl+K : supprimer jusqu'a la fin de la ligne
 * Ctrl+D : EOF ou supprimer le charactere devant le curseur
 * Ctrl+W : supprimer le mot avant le curseur

Plus avancé
===========

 * alias
 * tar
 * the asterisk
 * chaining commands (; &&)
 * pipes ( | )
 * redirection ( > )
