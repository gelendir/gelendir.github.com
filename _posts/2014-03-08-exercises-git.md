---
title: Exercises d'introduction Git
layout: default
category: linuq
---

Exercice 1
==========

Créez un nouveau dépôt appelé "helloworld" avec la commande "git init"

Exercice 2
==========

Créez un nouveau fichier nommé "README". Ce fichier doit contenir
un mot de bienvenue avec votre nom et votre profession. Ensuite,
commitez ce fichier.

Voici un exemple de README:

    Bonjour ! Je m'appelle <nom>. Voici un exemple de dépôt git

Exercice 3
==========

Créez un script bash qui affiche la ligne "Hello world !" lorsqu'on
l'exécute. Commitez ce fichier

Voici un exemple de script bash:

    #!/bin/bash
    echo "Hello world !"

Exercice 4
==========

Modifiez le script bash pour demander le nom de la personne, puis
ensuite afficher le nom sous la forme "Bonjour <<nom de la personne>>!".
Commitez les modifications une fois fini.

Voici un exemple de script bash:

    #!/bin/bash
    echo "Quel est votre nom ?"
    read nom
    echo "Bonjour $nom !"

Exercice 5
==========

Modifiez le script bash pour ajouter le nom de la personne à un fichier
à chaque fois qu'il est éxécuté. Ajoutez un gitignore au dépôt
pour ignorer le fichier généré par le script. Commitez le tout.

Voici un exemple de script bash:

    #!/bin/bash
    echo "Quel est votre nom ?"
    read nom
    echo "Bonjour $nom !"
    echo $nom >> list_noms


Exercice 6
==========

Supprimez le fichier README et commitez la modification

Exercice 7
==========

Supprimez le fichier hello_world.sh (mais ne committez pas !). Vous avez perdu
tout votre travail ! Utilisez git pour récupérer le fichier supprimé.

Exercice 8
==========

Créez une nouvelle branche appelé "affichage_date". Modifiez
le script pour afficher la date avant de demander le nom de la personne,
puis commitez vos modifications dans cette nouvelle branche.

Voici un exemple de ligne à ajouter au script bash

    echo "La date actuel:"
    echo $(date)

Exercice 9
==========

Revenez à la branche "master". Créez une nouvelle branche appelé "affichage_repertoire".
Modifiez le script pour afficher le répertroire dans lequel vous vous situez,
puis commitez vos modifications dans cette nouvelle branche.

Voici un exemple de ligne à ajouter au script bash:

    echo "Répertoire actuel:"
    echo $(pwd)

Exercice 10
===========

Fusionnez la branche "affichage_date" dans "master". Ensuite, fusionnez aussi
la branche "affichage_repertoire" dans "master".


Défi Github
===========

Défi 1: création d'un blogue
----------------------------

But: utiliser git et github pour vous créer votre petit blogue.

1. Créez vous un compte sur http://github.com/
2. Choissisez un thème pour votre blogue sur http://jekyllthemes.org/
3. Créez un dépôt git avec le thème téléchargé
4. Suivez les instructions sur http://pages.github.com/ pour créez votre blogue sur github

Défi 2: contribuer au blogue d'une autre personne
-------------------------------------------------

Modifier le dépôt d'une autre personne et soumettre ces modifications.

1. Mettez vous en équipe avec une autre personne dans la salle
2. Sur github, forkez leur dépôt de blogue
3. Ajoutez un article de plus au blogue
4. Envoyez une pull request à votre coéquipier
