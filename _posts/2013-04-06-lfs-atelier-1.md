---
title: LFS - Atelier 1
layout: default
category: linuq
---

LFS Atelier 1
=============

 * [Installation des machines virtuelles](#installation_des_machines_virtuelles)
 * [Configuration des machines virtuelles](#configuration_des_machines_virtuelles)
 * [Partitionnement](#partitionnement)
 * [Distribution des sources](#distribution_des_sources)
 * [Configuration environnement de base](#configuration_environnement_de_base)

Paquets
=======

 * [Binutils (phase 1)](#binutils_phase_1)
 * [GCC (phase 1)](#gcc_phase_1)
 * [Linux API Headers](#linux_api_headers)
 * [Glibc](#glibc)
 * [La danse de la compilation](#la_danse_de_la_compilation)
 * [Binutils (phase 2)](#binutils_phase_2)
 * [GCC (phase 2)](#gcc_phase_2)
 * [TCL](#tcl)
 * [Expect](#expect)
 * [DejaGNU](#dejagnu)
 * [Check](#check)
 * [NCurses](#ncurses)
 * [Bash](#bash)
 * [Bzip2](#bzip2)
 * [Coreutils](#coreutils)
 * [Diffutils](#diffutils)
 * [File](#file)
 * [Findutils](#findutils)
 * [Gawk](#gawk)
 * [Gettext](#gettext)
 * [Grep](#grep)
 * [Gzip](#gzip)
 * [M4](#m4)
 * [Make](#make)
 * [Patch](#patch)
 * [Perl](#perl)
 * [Sed](#sed)
 * [Tar](#tar)
 * [Texinfo](#texinfo)
 * [Xz](#xz)

Installation des machines virtuelles
====================================

Importer le fichier lfs-atelier1.ova (File -> Import Appliance)

Configuration des machines virtuelles
=====================================

Bouton droit sur la machine virtuelle -> Settings. Paramètres à changer:

 * System -> Processor (nombre de CPU)
 * Network -> Adapter 1 -> Attached to: Bridged Adapter

Pour accèder à la machine virtuelle en SSH, éxécuter la commande
suivante dans la vm:

    ifconfig eth0

puis utiliser l'adresse IP avec la commande suivante:

    ssh root@192.168.2.150
    #mot de passe: root

Partitionnement
===============

    cfdisk /dev/sda #create a 7.7 GB partition
    mkfs.ext4 /dev/sda3

Une fois les partitions crée, la monter:

    EXPORT lfs=/mnt/lfs
    mkdir -pv $LFS
    mount -v -t ext4 /dev/sda3 $LFS

Distribution des sources
========================

Créer le dossier pour les sources sur votre vm:

    mkdir -v $LFS/sources

Clé USB
-------

À partir de votre poste, récupérer les sources d'une clé USB et les copier à l'aide de
SSH:

    scp -r /media/clefusb/sources/* root@192.168.1.150:/mnt/lfs/sources

Réseau
------

Récupérer les sources directement du réseau sur la vm:

    cd $LFS/sources
    wget http://adresse.du.serveur/lfs/wget-list
    wget -i wget-list -P $LFS/sources

Vérifier l'intégrité des paquets:

    md5sum -c md5sums

Configuration environnement de base
===================================

Toolchain
---------

Préparer le dossier qui va contenir notre toolchain:

    mkdir -v $LFS/tools
    ln -sv $LFS/tools /

LFS user
--------

Créer un utilisateur "vierge" qui sera utilisé pour compiler le
toolchain

    groupadd lfs
    useradd -s /bin/bash -g lfs -m -k /dev/null lfs
    passwd lfs
    chown -v lfs $LFS/tools
    chown -v lfs $LFS/sources

Se logger avec l'utilistaeur:

    su - lfs

LFS bashrc
----------

Mettre en place un profile bash:

    cat > ~/.bash_profile << "EOF"
    exec env -i HOME=$HOME TERM=$TERM PS1='\u:\w\$ ' /bin/bash
    EOF

Mettre en place un bashrc:

    cat > ~/.bashrc << "EOF"
    set +h
    umask 022
    LFS=/mnt/lfs
    LC_ALL=POSIX
    LFS_TGT=$(uname -m)-lfs-linux-gnu
    PATH=/tools/bin:/bin:/usr/bin
    export LFS LC_ALL LFS_TGT PATH
    EOF

recharger le nouveau bashrc:

    source ~/.bash_profile
    source ~/.bashrc

Script lfsenv
-------------

Ce script servira à rentrer dans l'environnement LFS plus facilement
lorsque vous redémarrez votre machine virtuelle.

Si vous n'êtes pas l'utilisateur root, sortez de votre session et
reloguez vous:

    exit

Mise en place du script:

    cat > /root/lfsenv.sh << "EOF"
    #!/bin/bash
    mount -t ext3 /dev/sda3 /mnt/lfs
    su - lfs
    EOF

La danse de la compilation
==========================

Patron générique pour compiler un logiciel:

    tar xf paquet-1.2.3.tar.gz
    cd paquet-1.2.3
    ./configure --prefix=/usr/bin
    make
    make install

Si vous avez 2 CPUs ou plus:

    make -j2

Paquets
=======

Binutils phase 1
----------------

**SBU: 1.00**

**Temps de compilation: 4m30s**

    mkdir binutils-build
    cd binutils-build

    ../binutils-2.23.1/configure     \
        --prefix=/tools            \
        --with-sysroot=$LFS        \
        --with-lib-path=/tools/lib \
        --target=$LFS_TGT          \
        --disable-nls              \
        --disable-werror

    make
    make install

GCC phase 1
-----------

**SBU: 5.5**

**Temps de compilation: 20m**

GCC nécessite les librairies mpfr, gmp et mpc. Nous allons les
décompresser dans le dossier de sources de GCC

    tar -Jxf ../mpfr-3.1.1.tar.xz
    mv -v mpfr-3.1.1 mpfr
    tar -Jxf ../gmp-5.1.1.tar.xz
    mv -v gmp-5.1.1 gmp
    tar -zxf ../mpc-1.0.1.tar.gz
    mv -v mpc-1.0.1 mpc

Modifier GCC pour utiliser le dynamic linker dans /tools (compilé avec binutils)

    for file in \
     $(find gcc/config -name linux64.h -o -name linux.h -o -name sysv4.h)
    do
      cp -uv $file{,.orig}
      sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' \
          -e 's@/usr@/tools@g' $file.orig > $file
      echo '
    #undef STANDARD_STARTFILE_PREFIX_1
    #undef STANDARD_STARTFILE_PREFIX_2
    #define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"
    #define STANDARD_STARTFILE_PREFIX_2 ""' >> $file
      touch $file.orig
    done


désactiver le "stack protection", sinon on ne pourra pas compiler glibc.

désactiver la compilation des pages info.

    sed -i '/k prot/agcc_cv_libc_provides_ssp=yes' gcc/configure
    sed -i 's/BUILD_INFO=info/BUILD_INFO=/' gcc/configure

Compiler GCC

    mkdir -v ../gcc-build
    cd ../gcc-build
    ../gcc-4.7.2/configure         \
        --target=$LFS_TGT          \
        --prefix=/tools            \
        --with-sysroot=$LFS        \
        --with-newlib              \
        --without-headers          \
        --with-local-prefix=/tools \
        --with-native-system-header-dir=/tools/include \
        --disable-nls              \
        --disable-shared           \
        --disable-multilib         \
        --disable-decimal-float    \
        --disable-threads          \
        --disable-libmudflap       \
        --disable-libssp           \
        --disable-libgomp          \
        --disable-libquadmath      \
        --enable-languages=c       \
        --with-mpfr-include=$(pwd)/../gcc-4.7.2/mpfr/src \
        --with-mpfr-lib=$(pwd)/mpfr/src/.libs

    make
    make install

vu que GCC est compilé statiquement, la librairie gcc_eh n'est pas
installé automatiquement. Nous allons la placer manuellement dans
/tools

    ln -sv libgcc.a `$LFS_TGT-gcc -print-libgcc-file-name | sed 's/libgcc/&_eh/'`

Linux API Headers
-----------------

**SBU: 0.1**

**Temps de compilation: 30s**

    make mrproper
    make headers_check
    make INSTALL_HDR_PATH=dest headers_install
    cp -rv dest/include/* /tools/include

Glibc
-----
**SBU: 5.4**

**Temps de compilation: 25m**

    mkdir glibc-build
    cd glibc-build

    ../glibc-2.17/configure                             \
      --prefix=/tools                                 \
      --host=$LFS_TGT                                 \
      --build=$(../glibc-2.17/scripts/config.guess) \
      --disable-profile                               \
      --enable-kernel=2.6.25                          \
      --with-headers=/tools/include                   \
      libc_cv_forced_unwind=yes                       \
      libc_cv_ctors_header=yes                        \
      libc_cv_c_cleanup=yes

    make
    make install

Binutils phase 2
----------------

**SBU: 1.1**

**Temps de compilation: 5m**

    cd $LFS/sources
    tar xvf binutils-2.23.1.tar.bz2
    mkdir binutils-build
    cd binutils-build

    CC=$LFS_TGT-gcc            \
    AR=$LFS_TGT-ar             \
    RANLIB=$LFS_TGT-ranlib     \
    ../binutils-2.23.1/configure \
        --prefix=/tools        \
        --disable-nls          \
        --with-lib-path=/tools/lib

    make
    make install

Préparer notre nouveau linker pour le nouveau GCC (prochaine étape)

    make -C ld clean
    make -C ld LIB_PATH=/usr/lib:/lib
    cp -v ld/ld-new /tools/bin 

GCC phase 2
-----------

**SBU: 7.1**

***Temps de compilation: 30m***

Créer le fichier "limits.h"

Forcer l'utilisation de l'option "-fomit-frame-pointer" vu que nous ne
compilons pas GCC 3 fois de suite

    cp -v gcc/Makefile.in{,.tmp}
    sed 's/^T_CFLAGS =$/& -fomit-frame-pointer/' gcc/Makefile.in.tmp \
      > gcc/Makefile.in

Ajuster GCC pour utiliser notre nouveau linker (étape précédente)

    for file in \
     $(find gcc/config -name linux64.h -o -name linux.h -o -name sysv4.h)
    do
      cp -uv $file{,.orig}
      sed -e 's@/lib\(64\)\?\(32\)\?/ld@/tools&@g' \
      -e 's@/usr@/tools@g' $file.orig > $file
      echo '
    #undef STANDARD_STARTFILE_PREFIX_1
    #undef STANDARD_STARTFILE_PREFIX_2
    #define STANDARD_STARTFILE_PREFIX_1 "/tools/lib/"
    #define STANDARD_STARTFILE_PREFIX_2 ""' >> $file
      touch $file.orig
    done

Installer une fois de nouveau les dépendances de GCC

    tar -Jxf ../mpfr-3.1.1.tar.xz
    mv -v mpfr-3.1.1 mpfr
    tar -Jxf ../gmp-5.1.1.tar.xz
    mv -v gmp-5.1.1 gmp
    tar -zxf ../mpc-1.0.1.tar.gz
    mv -v mpc-1.0.1 mpc

Ne pas construire les info pages (inutile)

    sed -i 's/BUILD_INFO=info/BUILD_INFO=/' gcc/configure

Compiler

    mkdir -v ../gcc-build
    cd ../gcc-build

    CC=$LFS_TGT-gcc \
    AR=$LFS_TGT-ar                  \
    RANLIB=$LFS_TGT-ranlib          \
    ../gcc-4.7.2/configure          \
        --prefix=/tools             \
        --with-local-prefix=/tools  \
        --with-native-system-header-dir=/tools/include \
        --enable-clocale=gnu        \
        --enable-shared             \
        --enable-threads=posix      \
        --enable-__cxa_atexit       \
        --enable-languages=c,c++    \
        --disable-libstdcxx-pch     \
        --disable-multilib          \
        --disable-bootstrap         \
        --disable-libgomp           \
        --with-mpfr-include=$(pwd)/../gcc-4.7.2/mpfr/src \
        --with-mpfr-lib=$(pwd)/mpfr/src/.libs

    make
    make install

Pour des fins de rétrocompatibilité, créer un lien symbolique 'cc'

    ln -sv gcc /tools/bin/cc

TCL
---

**SBU: 0.4**

**Temps de compilation: 4m**

    cd tcl8.6.0/unix/
    ./configure --prefix=/tools
    make
    make install

ajouter la permission d'écriture sur la librarie pour pouvoir
enlever les symboles de debug plus tard

    chmod -v u+w /tools/lib/libtcl8.6.so

installer les headers nécessaires pour l'installation de expect
(prochain paquet)

    make install-private-headers

Pour des fins de rétrocompatibilité, créer un lien symbolique 'cc'

    ln -sv tclsh8.6 /tools/bin/tclsh

Expect
------

**SBU: 0.1**

**Temps de compilation: 15s**

Forcer expect à utiliser /bin/stty à la place de /usr/local/bin/stty

    cp -v configure{,.orig}
    sed 's:/usr/local/bin:/bin:' configure.orig > configure

Configurer et compiler

    ./configure --prefix=/tools --with-tcl=/tools/lib \
      --with-tclinclude=/tools/include

    make
    make SCRIPTS="" install

DejaGNU
-------

**SBU: 0.1**

**Temps de compilation: 20s**

    cd dejagnu-1.5
    ./configure --prefix=/tools
    make install

Check
-----

**SBU: 0.1**

**Temps de compilation: 20s**

    ./configure --prefix=/tools
    make
    make install

Ncurses
-------

**SBU: 0.5**

**Temps de compilation: 2m**

    ./configure --prefix=/tools --with-shared \
        --without-debug --without-ada --enable-overwrite
    make
    make install

Bash
----
**SBU: 0.4**

**Temps de compilation: 1m**

Appliquer une patch pour regler certains bugs de bash

    patch -Np1 -i ../bash-4.2-fixes-11.patch

Compiler

    ./configure --prefix=/tools --without-bash-malloc
    make
    make install

Pour des fins de rétrocompatibilité mettre en place un lien de 'sh' vers
'bash'

    ln -sv bash /tools/bin/sh

Bzip2
-----

**SBU: 0.1**

**Temps de compilation: 10s**

    make
    make PREFIX=/tools install

Coreutils
---------

**SBU: 0.8**

**Temps de compilation: 4m**

    ./configure --prefix=/tools --enable-install-program=hostname
    make
    make install

Diffutils
---------

**SBU: 0.2**

**Temps de compilation: 2m**

Règler un problème d'incompatibilité avec glibc 2.17

    sed -i -e '/gets is a/d' lib/stdio.in.h

Compiler

    ./configure --prefix=/tools
    make
    make install

File
----

**SBU: 0.1**

**Temps de compilation: 50s**

    ./configure --prefix=/tools
    make
    make install

Findutils
---------

**SBU: 0.2**

**Temps de compilation: 1m30**

    ./configure --prefix=/tools
    make
    make install

Gawk
----

**SBU: 0.2**

**Temps de compilation: 1m**

    ./configure --prefix=/tools
    make
    make install

Gettext
-------

**SBU: 0.6**

**Temps de compilation: 3m20**

    cd gettext-0.18.2/gettext-tools
    EMACS="no" ./configure --prefix=/tools --disable-shared
    make -C gnulib-lib
    make -C src msgfmt

Pour l'instant, seulement le programme msgfmt est nécessaire. Nous
allons donc seulement installer celui-ci

    cp -v src/msgfmt /tools/bin

Grep
----

**SBU: 0.2**

**Temps de compilation: 2m**

    ./configure --prefix=/tools
    make
    make install

Gzip
----

**SBU: 0.2**

**Temps de compilation: 2m**

    ./configure --prefix=/tools
    make
    make install

M4
--

**SBU: 0.2**

**Temps de compilation: 2m**

Règler un problème de compilation avec glibc 2.16

    sed -i -e '/gets is a/d' lib/stdio.in.h

Compiler

    ./configure --prefix=/tools
    make
    make install

Make
----

**SBU: 0.1**

**Temps de compilation: 1m**

    ./configure --prefix=/tools
    make
    make install

Patch
-----

**SBU: 0.1**

**Temps de compilation: 2m**

    ./configure --prefix=/tools
    make
    make install

Perl
----

**SBU: 1.8**

**Temps de compilation: 7m**

Patch pour corriger des chemins vers la librarie C codé en dur

    patch -Np1 -i ../perl-5.16.2-libc-1.patch

Configuration et compilation

    sh Configure -des -Dprefix=/tools
    make

Pour l'instant, nous avons seulement besoin du programme 'perl' et de
ces librairies

    cp -v perl cpan/podlators/pod2man /tools/bin
    mkdir -pv /tools/lib/perl5/5.16.2
    cp -Rv lib/* /tools/lib/perl5/5.16.2

Sed
---

**SBU: 0.1**

**Temps de compilation: 2m**

    ./configure --prefix=/tools
    make
    make install

Tar
---

**SBU: 0.4**

**Temps de compilation: 3m**

Règler un problème de compilation avec glibc 2.16

    sed -i -e '/gets is a/d' gnu/stdio.in.h

Compiler

    ./configure --prefix=/tools
    make
    make install

Texinfo
-------

**SBU: 0.3**

**Temps de compilation: 2m**

    ./configure --prefix=/tools
    make
    make install

Xz
--

**SBU: 0.2**

**Temps de compilation: 2m**

    ./configure --prefix=/tools
    make
    make install


Topics to discuss
-----------------

* linker
* glibc bug 2.16
* open source jeopardy

