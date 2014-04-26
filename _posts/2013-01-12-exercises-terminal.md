---
title: Exercises introduction au terminal
layout: default
category: linuq
---

Questions
=========

Navigation
----------

Quel commande affichera tout les fichiers dans un dossier ?

 1. ls
 2. ls -a
 3. ls -h
 4. ls -l

George vient d'ouvrir un terminal. Il est présentement situé dans son dossier personnel
sur son utilisateur. Lorsque George tappera la commande ```pwd```, quel chemin sera affiché
dans la console ?

 1. /home
 2. /george
 3. /home/george
 4. ~/george

Robert a utilisé la commande ```cd Photos``` pour se rendre dans son dossier de photos. Il aimerait
maintenant revenir dans son dossier personnel, mais ne se rappelle plus quel commande tapper.
Quel commande est-ce que Robert devrait utiliser ?

 1. ```cd .```
 2. ```cd /home/robert```
 3. ```cd ..```
 4. ```cd ~```

 Manipulation et creation de fichiers
 ------------------------------------

 Marie a copié une photo de sa caméra au mauvais emplacement. L'image est 
 présentement dans le dossier ```/home/marie/images/automne/fleur.jpg```. Elle voudrait
 le déplacer dans le dossier /home/marie/images/printemps. Quel commande doit elle effectuer ?

 1. mv /home/marie/images/automne/fleur.jpg printemps
 3. mv /home/marie/images/automne/fleur.jpg /home/marie/images/printemps.jpg
 2. mv printemps /home/marie/images/automne/fleur.jpg
 4. mv images/automne/fleur.jpg images/printemps

Quel commande doit on utiliser pour supprimer un dossier au complet ?

 1. rm dossier
 2. mv dossier /poubelle
 3. rm -R dossier
 4. rmdir dossier

Vous avez un fichier qui s'appelle README qui fait 12 pages, mais vous êtes seulement
intéressé à lire la première page. Quel commande est le plus approprié pour lire la première
page du fichier ?

 1. cat
 2. less
 3. nano
 4. head

Permissions
-----------

Dans le système de permissions sous UNIX, à quoi sert le 'x' ?

 * Éxécuter un programme
 * Supprimer un fichier
 * Lire un fichier
 * Créer un fichier

vous voyez les lignes suivantes après avoir éxécuté la commande ```ls -l```:

    -r-xr---w- 1 gregory http 0 Apr 25 16:42 fichier1
    -rw-r-x--x 1 http    http 0 Apr 25 16:42 fichier2
    -r--rwx-w- 1 nobody  http 0 Apr 25 16:42 fichier3

Lequel des fichiers est seulement modifiable par le groupe http ?

 1. fichier1
 2. fichier2
 3. fichier3
 4. fichier1 et fichier3

Quel commande dois-je executer pour que le programme ```monprogramme``` soit
utilisable par tout le monde ?

 1. chmod a+rx monprogramme
 2. chmod ugo+x monprogramme
 3. chmod o+rw monprogramme
 4. chmod u+rwx monprogramme

Exercises
=========

Navigation
----------

 * Utiliser la console pour vous rendre dans le dossier /var. Quels sont les dossiers affichés ?
 * Dans le dossier /boot, quel est le fichier avec la plus grande taille ?
 * Trouver au moins 3 dossiers cachés dans votre dossier personnel.
 * Trouver le dernier fichier ou dossier crée le plus récemment dans le dossier /var/log

Autocompletion
--------------

 * En utilisant seulement 10 lettres ou moins, rendez vous dans le dossier /usr/lib/firefox/browser/defaults/preferences/
 * Combien de commandes commencent avec les lettres "ch" ?

Aide
----

 * quel est l'option que l'on doit utiliser avec la commande ```ls``` pour afficher des couleurs ?
 * quel option de la commande ```cd``` permet de revenir au dernier dossier visité ?

Manipulation de fichiers/dossiers
---------------------------------

 * Rendez vous dans le dossier /tmp et créez un nouveau dossier nommé "terminal"
 * déplacez le dossier "terminal" dans votre dossier utilisateur (dossier personnel)
 * à l'interieur du dossier terminal, créez un dossier "backup1" et copiez le 5 fois (backup1, backup2, backup3, backup4, backup5)
 * effacer le dossier "terminal" en une seule commande

Création/Édition de fichiers
----------------------------

 * Affichez le contenu du fichier /etc/passwd en terminal. Quel est votre numero d'utilisateur ?
 * Affichez seulement la première page du fichier /boot/grub/menu.cfg
 * Créez un fichier "hello_world" dans le dossier /tmp qui contient la phrase "Hello world !". Une fois crée, déplacez le dans votre dossier utilisateur
 * Créez un dossier "backup" dans votre dossier personnel et copiez y des fichiers que vous voulez conserver.

Permissions sur les fichiers
----------------------------

 * Créez un fichier "super_seceret". Changez les permissions pour qu'il soit lisible seulement par vous-même
 * Créez un fichier "hello_world" avec comme contenu "echo hello world". Modifiez les permissions sur le fichier pour qu'il soit éxécutable, puis éxécuter le fichier.
 * À quel groupe appartient le fichier /var/log/auth.log ? qui a le droit d'écrire dans le fichier ?

Recherche de fichier
--------------------

 * Trouvez le chemin du programme "libreoffice"
 * Trouvez toutes les fichiers avec l'extension ```.img```
 * Trouvez toutes les fichiers avec l'extension ```.sqlite``` dans votre dossier personnel


Processus
---------

 * Combien de processus nommé "bash" sont en train de rouler sur votre système ?
 * Démarrez firefox et arrêtez le à partir de la console
 * Démarrez libreoffice et arrêtez le à partir de la console

Filtres
-------

 * Combien de lignes sont dans le fichier /var/log/Xorg.0.log ?
 * Comptez le nombre de ligne ou le mot "linux" apparait dans le fichier /var/log/dmesg.log
 * Créez un fichier contenant 10 prénoms (chaque prénom sur une ligne). Affichez le fichier trié alphabétiquement
