---
title: Atelier d'introduction à Wordpress
layout: default
category: linuq
---

Choisir son hébergeur
=====================

Questions à se poser lorsqu'on cherche pour un hébergeur:

 * Est-ce qu'il supporte les prérequis LAMP ?
    * Linux
    * Apache
    * MySQL
    * PHP
    * Vérifier les dépendnaces avec l'installateur wordpress
 * Comment puis-je accèder à mes fichiers sur l'hébergement ?
  * FTP
  * cPanel
  * SSH
  * Client web
 * Combien d'espace disque est offert ?
 * Est-ce qu'il y a des limites sur la taille de la base de données ?
 * Est-ce qu'on s'attend à beaucoup d'achalandage sur le site web ?
 * Quel est le matériel utilisé par le serveur ?

Réserver son nom de domaine
===========================

Un nom de domaine n'est pas obligatoire pour expérimenter avec wordpress. Par contre,
c'est une composante essentiel si vous voulez partager facilement l'adresse de votre site web
avec le grand publique.

Plusieurs hébergeurs offrent un service de nom de domaine avec leurs forfaits.

Autres ressources pour se procurer des noms de domaines :

 * namecheap.com
 * dreamhost.com
 * gandi.net
 * godaddy.com

cPanel
=======

TODO

Installation Wordpress
======================

installation debian/ubuntu :

    apt-get update
    apt-get upgrade
    apt-get install mysql-server mysql-client apache2 php5 php5-mysql libapache2-mod-php5
    a2enmod rewrite
    service apache2 restart

création db mysql :

    mysql -u root -p
    CREATE DATABASE wordpress CHARACTER SET 'utf8' COLLATE utf8_general_ci;
    GRANT ALL PRIVILEGES ON wordpress.* to 'wordpress'@'localhost' identified by 'wordpress';

décompression des fichiers :

    wget http://wordpress.org/latest.tar.gz
    tar xf latest.tar.gz -C /var/www

**NOTE**: À fin de simplifier la démonstration, nous décompressons directement wordpress dans
l'espace d'hébergement par défaut de debian, mais il est préférable de configurer un VirtualHost
sécurisé pour tout site web PHP.

Par la suite, ouvrir votre navigateur puis entrez l'adresse de votre serveur

Effectuer une configuration de base du site
===========================================

Pour accèder à l'interface d'administration de votre site web, ajoutez `/wp-admin`
à la fin de l'URL. Exemple :

    http://192.168.1.197/wordpress/wp-admin

Authentification pour VM bitnami :

    utilisateur: user
    mot de passe: bitnami


Paramètres Globaux
------------------

Allez faire un tour de Settings / General

Utilisateurs
------------

Rôles disponibles :

Administrateur
 : Tous les droits

Éditeur
 : Lecture/Modification/Publication de tout les posts

Auteur
 : Lecture/Modification/Publication de ses propres posts

Contributeur
 : Lecture/Modification de ses propres posts

Abonné (Subscriber)
 : Ajouter des commentaires sur un post

**ATTENTION**: Assurez-vous d'avoir au moins un compte dee type administrateur avec 
un mot de passe sécuritaire !

Thèmes
------

Changer facilement l'allure de votre site web avec plusieurs thèmes wordpress
disponibles gratuitement.

Widgets
-------

Sections qui apparaissent à droite sur la page principale. Faire la démonstration
d'ajout de widgets.

Explorer les thèmes et extensions disponibles
=============================================
