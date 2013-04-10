---
title: Atelier d'installation Arch Linux
layout: default
category: linuq
---

Atelier Installation Arch Linux
===============================

Téléchargement
==============

https://www.archlinux.org/download/

Installation par CD
===================

 * Télécharger l'ISO et le graver
 * modifier les options de BIOS pour démarrer sur CD

Installation par USB
====================

 * Télécharger l'ISO
 * trouver le device dans /dev (exemple: /dev/sdb)
 * Éxécuter la commande suivante:

    dd bs=4M if=/path/to/archlinux.iso of=/dev/sdb

Boot du CD
==========

Changer le clavier pour un layout canadien:

    loadkeys cf

Partitonnement
==============

Outils disponibles en console:

 * fdisk
 * cfdisk
 * gdisk
 * parted

gdisk
-----

    gdisk /dev/sda
    #creer table de partition
    o
    #creer partition de swap
    n
    1
    2048
    +512M
    8200
    #creer partition de systeme
    n
    2
    <enter>
    +3500M
    8300
    #creer table de partition
    w

Créer filesystem ext4
---------------------

    mkfs.ext4 /dev/sda2

Montage des partitions
======================

    mount -t ext4 /dev/sda2 /mnt
    #si une partition séparé pour /boot, /home, etc
    mkdir /mnt/home
    mount -t ext4 /dev/sda3 /mnt/boot

Réseau
======

DHCP par défaut. DHCP sur une autre interface:

    systemctl start dhcpcd@eth0

IP statique:

    ip addr add 192.168.1.2/24 dev eth0
    ip route add default via 192.168.1.1

Réseau sans-fil
---------------

utiliser la commande `wifi-menu`

Dépots
======

    nano /etc/pacman.d/mirrorlist

Pour réorganiser la liste selon la vitesse des mirroirs:

    mv /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
    rankmirrors /etc/pacman.d/mirrorlist.bak > /etc/pacman.d/mirrorlist

Installation des paquets de base
================================

    pacstrap /mnt base base-devel

Génération de fstab
===================

    genfstab -U -p /mnt >> /mnt/etc/fstab

Installation GRUB
=================

    arch-chroot /mnt pacman -S grub-bios

Configuration de base
=====================

Avant de configurer, il faut chrooter dans notre nouveau système

    arch-chroot /mnt

GRUB BIOS
---------

Installation des paquets

    pacman -S grub-bios os-prober

Installation de GRUB au MBR

    modprobe dm-mod
    grub-install --target=i386-pc --recheck --debug /dev/sda
    mkdir -p /boot/grub/locale
    cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo

Génération du fichier de configuration

    grub-mkconfig -o /boot/grub/grub.cfg

Dual-boot avec windows
----------------------

Commencer par trouver le UUID de la partition windows:

    grub-probe --target=fs_uuid /media/windows/bootmgr
    #windows xp
    grub-probe --target=fs_uuid /media/windows/ntdlr

Creer une entrée grub dans /etc/grub.d/40_customn ou
/boot/grub/custom.cfg (dans notre exemple, 69B235F6749E84CE représente
notre UUID windows

    menuentry "Microsoft Windows Vista/7/8 BIOS-MBR" {
        insmod part_msdos
        insmod ntfs
        insmod search_fs_uuid
        insmod ntldr     
        search --fs-uuid --no-floppy --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 69B235F6749E84CE
        ntldr /bootmgr
    }

Hostname
--------

    nano /etc/hostname

Fuseau Horaire
--------------

    ln -s /usr/share/zoneinfo/America/Montreal /etc/localtime

Locale
------

Décommenter fr_CA dans /etc/locale.gen:

    nano /etc/locale.gen

Générer les locales:

    locale-gen

configurer la locale dans /etc/locale.conf:

    nano /etc/locale.conf
    LANG="fr_CA.UTF-8"

Clavier
-------

ajuster le layout dans /etc/vconsole.conf

    nano /etc/vconsole.conf
    KEYMAP=cf

Mot de passe root
-----------------

    passwd root

Créer utilisateur
-----------------

    useradd -m -g users -s /bin/bash utilisateur
    passwd utilisateur

Autre éléments utiles à configurer
=================================

sudo
----

Installation:

    pacman -S sudo

décomenter la ligne suivante avec l'utilitaire visudo:

    visudo
    %wheel ALL=(ALL) ALL

Ajout de l'utilisateur au groupe wheel

    gpasswd -a utilisateur wheel

alsa
----

    pacman -S alsa-utils

dhcp
----

Trouver le nom de l'interface:

    ip link

Activer le DHCP:

    sudo systemctl enable dhcpcd@enp0s3
    sudo systemctl start dhcpcd@enp0s3

Interface graphique
===================

Xorg
----

Installation xorg:

    pacman -S xorg-server xorg-xinit xorg-server-utils mesa

Liste des drivers graphiques disponibles:

    pacman -Ss xf86-video

Installation Synaptics (pour laptop):

    pacman -S xf86-input-synaptics

Installation de polices de base

    pacman -S ttf-dejavu

Tester si X fonctionne:

    startx

LXDE
----

Installation LXDE:

    pacman -S lxde gamin

Fichiers de configuration de base:

    mkdir -p ~/.config/openbox
    cp /etc/xdg/openbox/menu.xml /etc/xdg/openbox/rc.xml /etc/xdg/openbox/autostart ~/.config/openbox

Activer lxdm:

    sudo systemctl enable lxdm.service

