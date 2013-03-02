---
title: Session sur audio
layout: default
---

Session sur l'audio
===================

Codecs Audio
============

WAV
---

 * Format non compressé
 * Le plus volumineux
 * Losless codec
 * De facto pour les sources de données "bruts"

MP3
---

 * Format compressé
 * Asujeti aux brevets
 * Lossy codec
 * Encodeur sous linux : lame

Encodage en console :

    lame -h -b 128 fichieraudio.wav fichieraudio.mp3

AAC
---

 * Format compressé
 * Fait parti du portfolio MPEG-2 et MPEG-4
 * Meilleur qualité que MP3 pour
 * Utilisé dans la plupart des appareils modernes (ipod, iphone, ps3,
         nintendo, iTunes, youtube)
 * Encodeur sous linux : faac

Encodage en console :

    faac -b 128 fichieraudio.wav -o fichieraudio.m4a

OGG Vorbis
----------

 * Format compressé
 * Brevets libres
 * Logiciel libre
 * Algorithme de compression supérieur au MP3
 * Encodeur sous linux : ogg

Encodage en console :

    oggenc -b 116 fichieraudio.wav fichieraudio.ogg

Encodage avec un facteur de qualité :

    oggenc -q 3 fichieraudio.wav fichieraudio.ogg

FLAC
----

 * Format compressé
 * Lossless codec
 * Logiciel libre
 * Garanti qualité pareil à l'original
 * Un peu plus volumineux que ogg
 * Moins que WAV
 * Un peu plus de traitement CPU

Encodage en console :

    flac -8 fichieraudio.wav -o fichieraudio.flac

Autres formats
--------------

 * AIFF
 * RealAudio
 * TTA
 * WMA


Lecteurs Audio
==============

 * Audacious
 * Clementine
 * Rythmbox
 * Banshee
 * Exaile
 * Miro
 * MPD
 * Amarok

Lecteurs audio
=============

2 types de lecteur audio:

 * Émulation disque dur
 * Protocole MTP

 Appareils:

 * Appareils sansa
    * Sansa clip+
 * Certains modèles iPod
 * Téléphones android
 * Appareils Coby
 * Appareils Cowon

Éditeurs Audio
==============

 * Audacity
 * Ardour

Synthetiseurs Audio
===================

 * Rosegarden
 * LMMS
 * Hydrogen
 * timidity

Composition de musique
======================

 * Lilypond
  * frescobaldi
 * Musescore
 * abc (http://abc.sourceforge.net/)

