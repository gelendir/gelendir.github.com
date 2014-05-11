---
title: Atelier TDD python
layout: default
category: linuq
---

Introduction
============

 * Présentations individuels
 * But de la journée
 * Histoire d'horreur
 * Règles


But
===

Le but ultime n'est pas de réussir, mais d'apprendre des nouvelles techniques
et de pratiquer sans avoir peur de se tromper !

 * Framework de test
 * Développement TDD
 * Travail en pair
 * Trucs et astuces pour écrire du code de qualité


Règles
======

 * Sessions de TDD d'environ 45 minutes
 * Équipes de 2 personnes
 * Changer de coéquipier à chaque session
 * Alterner la personne qui est au clavier dans la mesure du possible
 * Supprimer son code à la fin de chaque session !


TDD
===

Les 3 règles du TDD :

1. Pas le droit d'écrire du code, à moins que ça soit pour faire passer le test
2. Pas le droit d'écrire plus que le minimum nécessaire pour faire échouer un test unitaire
3. Pas le droit d'écrire du code de production de plus que nécessaire pour faire passer un test

Le cycle TDD peut se résumer en 3 étapes :

1. Écrire un test qui échoue
2. Faire passer le test
3. Refactorer au besoin

Autre trucs et astuces du TDD :

 * Toujours commencer par le test minimale le plus simple
 * Ajouter des tests de plus en plus complexes au fur et à mesure de manière itérative
 * Si le test ne vous force pas à modifier votre code, il ne vaut peut-être pas la peine d'être écrit.

Setup environnement
===================

Prérequis :

 * Un éditeur texte
 * Python 2.7 / 3

Installation py.test :
 
    virtualenv env
    source env/bin/activate
    pip install pytest

Structure de projet :

    projet
    ├── code.py
    ├── __init__.py
    ├── module
    │   ├── __init__.py
    │   ├── librairie.py
    │   └── tests
    │       ├── __init__.py
    │       └── test_librairie.py
    └── tests
        ├── __init__.py
            └── test_librairie.py

Rouler les tests:

    cd projet
    py.test tests/test_code.py
