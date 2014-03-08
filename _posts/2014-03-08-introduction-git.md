---
title: Introduction à Git et Github
layout: default
category: linuq
---

Concepts
========

 * Workflow
 * Distributed

Preparation
===========

    git config --global user.name "Firstname Lastname"
    git config --global user.email "email@example.com"

Optionel:

    echo "export EDITOR=nano" >> ~/.bashrc

Creation depot
==============

    mkdir helloworld.git
    cd helloworld.git
    git init

Ajouter un nouveau fichier
==========================

Commencer par creer un nouveau fichier

    nano README

Préparer le fichier au commit

    git add README

Commiter le tout

    git commit
    #Alternative sans ouvrir editeur
    git commit -m "Ajout du README"


Modification d'un fichier
=========================

Commençons par modifier un fichier:

    nano README

Preparons le fichier pour le commit:

    git add README

Créons le commit:

    git commit -m "modification du README"

Raccourci pour ajouter le fichier et commiter en une seule commande:

    git commit README -m "modification du README"


Suppression d'un fichier
========================

Commande git:

    git rm README

***ATTENTION***: utiliser la commande "rm" n'est pas suffisant. Il faut
quand-même utiliser la commande "git rm" par la suite. 

État du dépôt
=============

Commande pour savoir l'état de tous les fichiers modifés

    git status


Des nouveaux fichiers sont crées, mais n'ont pas étés
commités :

    On branch master
    Untracked files:
      (use "git add <file>..." to include in what will be committed)

        nouveau_fichier

Un fichier déja existant est modifié, mais les modifications
n'ont pas encore été commités:

    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   fichier_avec_modification

Un fichier est prêt à être commité:

    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        modified:   fichier_avec_modification


Revenir à l'état du dernier commit
==================================

Utile lorsque vous avez modifié un fichier et que
vous voulez revenir à la dernière version sauvegardé.

Pour restaurer un seul fichier:

    git checkout nom_du_fichier

Pour restaurer TOUT les fichiers (ATTENTION: efface tout vos modifications
pas commités):

    git reset --hard

Historique
==========

Affichier l'historique de tous les commits:

    git log

Affichier l'historique avec les modifications effectués pour chaque commit:

    git log -p

Utilitaire graphique pour visualiser l'historique:

    gitk

Gitignore
=========

Le gitignore sert à ignorer certains types de fichiers qui n'ont pas besoin
d'être conservés dans un projet. Par exemple: fichiers compilés, fichiers 
temporaires, logs, etc.


Exemple d'un gitignore pour ignorer tout les fichiers python compilés:

    *.pyc

Exemple d'un gitignore pour ignorer tout les fichiers dans le dossier "tmp":

    tmp/*

Création d'un gitignore:

    nano .gitignore
    git add .gitignore
    git commit -m 'ajout du gitignore'


Branches
========

Le concept de branches permet de travailler sur des modifications
de manière isolé, puis de réintégrer ce travail à un moment futur.

Création d'une branche
======================

Pour créer une branche:

    git branch nom_de_la_branche

Pour changer la branche sur laquelle nous travaillons:

    git checkout nom_de_la_branche

Raccourci pour effectuer la création changer de branche en une étape:

    git checkout -b nom_de_la_branche


Fusionnement d'une branche
==========================

Le fusionnement de branches permet de réintégrer les modifications d'une branche
dans une autre. En d'autres mots, on fuisonne le travail de 2 branches
pour en former une seule.

On doit commencer par changer de branche de branche vers laquelle
on veut fusionner les changements:

    git checkout master

Puis ensuite utiliser la commande 'merge' pour fusionner une branche

    git merge nom_de_la_branche

Conflits
========

Parfois des conflits peuvent survenir suite à la fusion de 2 branches.
Vous pouvez résoudre un conflit soit à la main, soit avec un outil de diff.

Pour résoudre à la main, commencez par lister les fichiers en conflits:

    git status

Modifiez les fichiers en conflits, puis une fois fini commitez les changements

    git add fichier_en_conflit
    git commit

Sinon, vous pouvez utiliser un outil de diff pour faciliter la résolution
de conflits avec la commande suivante:

    git mergetool
    git commit

N'oubliez pas que vous pouvez restaurer votre branche à son état précédent
avec un reset.

    git reset --hard


Github
======

Concepts à couvrir:

 * Clone
 * Push/pull
 * Fork
 * Merge request

