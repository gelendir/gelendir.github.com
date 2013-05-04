---
title: LFS - Atelier 2
layout: default
category: linuq
---

LFS Atelier 2
=============

Paquets
=======

 * [Linux API Headers](#linux_api_headers)
 * [Man pages](#man_pages)
 * [glibc](#glibc)
 * [binutils](#binutils)
 * [zlib](#zlib)
 * [file](#file)
 * [gmp](#gmp)
 * [mpfr](#mpfr)
 * [mpc](#mpc)
 * [gcc](#gcc)
 * [sed](#sed)
 * [bzip2](#bzip2)
 * [pkg-config](#pkg-config)
 * [ncurses](#ncurses)
 * [util-linux](#util-linux)
 * [psmisc](#psmisc)
 * [procps-ng](#procps-ng)
 * [e2fsprogs](#e2fsprogs)
 * [shadow](#shadow)
 * [coreutils](#coreutils)
 * [iana-etc](#iana-etc)
 * [m4](#m4)
 * [bison](#bison)
 * [grep](#grep)
 * [readline](#readline)
 * [bash](#bash)
 * [libtool](#libtool)
 * [gdbm](#gdbm)
 * [inetutils](#inetutils)
 * [perl](#perl)
 * [autoconf](#autoconf)
 * [automake](#automake)
 * [diffutils](#diffutils)
 * [gawk](#gawk)
 * [findutils](#findutils)
 * [flex](#flex)
 * [gettext](#gettext)
 * [groff](#groff)
 * [xz](#xz)
 * [grub](#grub)
 * [less](#less)
 * [gzip](#gzip)
 * [iproute2](#iproute2)
 * [kbd](#kbd)
 * [kmod](#kmod)
 * [libpipeline](#libpipeline)
 * [make](#make)
 * [man-db](#man-db)
 * [patch](#patch)
 * [sysklogd](#sysklogd)
 * [sysvinit](#sysvinit)
 * [tar](#tar)
 * [texinfo](#texinfo)
 * [vim](#vim)

Préparation chroot
==================

Création du système de fichier virtuel

    mkdir -v $LFS/{dev,proc,sys}
    mknod -m 600 $LFS/dev/console c 5 1
    mknod -m 666 $LFS/dev/null c 1 3

Création des points de montage virtuels pour l'environnement chroot

    mount -v --bind /dev $LFS/dev
    mount -vt devpts devpts $LFS/dev/pts
    mount -vt proc proc $LFS/proc
    mount -vt sysfs sysfs $LFS/sys
    mount -vt tmpfs shm $LFS/dev/shm

chroot vers le nouveau système

    chroot "$LFS" /tools/bin/env -i \
       HOME=/root                  \
       TERM="$TERM"                \
       PS1='\u:\w\$ '              \
       PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin \
       /tools/bin/bash --login +h


Préparation système de fichiers
==============================

Créer l'arboresence de système standard

    mkdir -pv /{bin,boot,etc/{opt,sysconfig},home,lib,mnt,opt,run}
    mkdir -pv /{media/{floppy,cdrom},sbin,srv,var}
    install -dv -m 0750 /root
    install -dv -m 1777 /tmp /var/tmp
    mkdir -pv /usr/{,local/}{bin,include,lib,sbin,src}
    mkdir -pv /usr/{,local/}share/{doc,info,locale,man}
    mkdir -v  /usr/{,local/}share/{misc,terminfo,zoneinfo}
    mkdir -pv /usr/{,local/}share/man/man{1..8}
    for dir in /usr /usr/local; do
      ln -sv share/{man,doc,info} $dir
    done
    case $(uname -m) in
     x86_64) ln -sv lib /lib64 && ln -sv lib /usr/lib64 ;;
    esac
    mkdir -v /var/{log,mail,spool}
    ln -sv /run /var/run
    ln -sv /run/lock /var/lock
    mkdir -pv /var/{opt,cache,lib/{misc,locate},local}

Créer des liens symboliques temporaires pour satisfaire des dépendances
de compilation

    ln -sv /tools/bin/{bash,cat,echo,pwd,stty} /bin
    ln -sv /tools/bin/perl /usr/bin
    ln -sv /tools/lib/libgcc_s.so{,.1} /usr/lib
    ln -sv /tools/lib/libstdc++.so{,.6} /usr/lib
    sed 's/tools/usr/' /tools/lib/libstdc++.la > /usr/lib/libstdc++.la
    ln -sv bash /bin/sh

Préparation fichiers de configuration
=====================================

/etc/mtab
---------

    touch /etc/mtab

    cat > /etc/passwd << "EOF"
    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/dev/null:/bin/false
    nobody:x:99:99:Unprivileged User:/dev/null:/bin/false
    EOF


/etc/group
----------

    cat > /etc/group << "EOF"
    root:x:0:
    bin:x:1:
    sys:x:2:
    kmem:x:3:
    tape:x:4:
    tty:x:5:
    daemon:x:6:
    floppy:x:7:
    disk:x:8:
    lp:x:9:
    dialout:x:10:
    audio:x:11:
    video:x:12:
    utmp:x:13:
    usb:x:14:
    cdrom:x:15:
    mail:x:34:
    nogroup:x:99:
    EOF

bash
----

une fois /etc/mtab et /etc/group en place, nous pouvons recharcher bash
avec notre nouvelle configuration

    exec /tools/bin/bash --login +h


fichiers de log
---------------

    touch /var/log/{btmp,lastlog,wtmp}
    chgrp -v utmp /var/log/lastlog
    chmod -v 664  /var/log/lastlog
    chmod -v 600  /var/log/btmp

Linux API Headers
-----------------

**SBU :** 0.1

**temps de compilation :** 1m

    cd linux-3.8.1
    make mrproper
    make headers_check
    make INSTALL_HDR_PATH=dest headers_install
    find dest/include \( -name .install -o -name ..install.cmd \) -delete
    cp -rv dest/include/* /usr/include

Man pages
---------

**SBU :** 0.1

**temps de compilation :** 2s

    cd man-pages-3.47
    make install

glibc
-----

**SBU : 17.6**

**temps de compilation :** 22m

**temps de test :** 3h30

    cd /sources
    tar xf glibc-2.17.tar.xz
    mkdir -v glibc-build
    cd glibc-build

    ../glibc-2.17/configure  \
        --prefix=/usr          \
        --disable-profile      \
        --enable-kernel=2.6.25 \
        --libexecdir=/usr/lib/glibc

Installation:

    touch /etc/ld.so.conf #to prevent install warnings
    make install


Installation des headers NIS et RPC, nécessaires pour recompiler glibc
dans le futur:

    cp -v ../glibc-2.17/sunrpc/rpc/*.h /usr/include/rpc
    cp -v ../glibc-2.17/sunrpc/rpcsvc/*.h /usr/include/rpcsvc
    cp -v ../glibc-2.17/nis/rpcsvc/*.h /usr/include/rpcsvc

Installation de locales pour les tests :

    mkdir -pv /usr/lib/locale
    localedef -i cs_CZ -f UTF-8 cs_CZ.UTF-8
    localedef -i de_DE -f ISO-8859-1 de_DE
    localedef -i de_DE@euro -f ISO-8859-15 de_DE@euro
    localedef -i de_DE -f UTF-8 de_DE.UTF-8
    localedef -i en_GB -f UTF-8 en_GB.UTF-8
    localedef -i en_HK -f ISO-8859-1 en_HK
    localedef -i en_PH -f ISO-8859-1 en_PH
    localedef -i en_US -f ISO-8859-1 en_US
    localedef -i en_US -f UTF-8 en_US.UTF-8
    localedef -i es_MX -f ISO-8859-1 es_MX
    localedef -i fa_IR -f UTF-8 fa_IR
    localedef -i fr_FR -f ISO-8859-1 fr_FR
    localedef -i fr_FR@euro -f ISO-8859-15 fr_FR@euro
    localedef -i fr_FR -f UTF-8 fr_FR.UTF-8
    localedef -i it_IT -f ISO-8859-1 it_IT
    localedef -i it_IT -f UTF-8 it_IT.UTF-8
    localedef -i ja_JP -f EUC-JP ja_JP
    localedef -i ru_RU -f KOI8-R ru_RU.KOI8-R
    localedef -i ru_RU -f UTF-8 ru_RU.UTF-8
    localedef -i tr_TR -f UTF-8 tr_TR.UTF-8
    localedef -i zh_CN -f GB18030 zh_CN.GB18030
    localedef -i en_CA -f UTF-8 en_CA.UTF-8
    localedef -i en_CA -f ISO-8859-1 en_CA.ISO-8859-1
    localedef -i fr_CA -f UTF-8 en_CA.UTF-8
    localedef -i fr_CA -f ISO-8859-1 en_CA.ISO-8859-1
    localedef -i fr_FR -f UTF-8 fr_FR.UTF-8

Configuration glibc:

    cat > /etc/nsswitch.conf << "EOF"
    # Begin /etc/nsswitch.conf

    passwd: files
    group: files
    shadow: files

    hosts: files dns
    networks: files

    protocols: files
    services: files
    ethers: files
    rpc: files

    # End /etc/nsswitch.conf
    EOF

Configuration des fuseau horaires:

    tar -xf ../tzdata2012j.tar.gz

    ZONEINFO=/usr/share/zoneinfo
    mkdir -pv $ZONEINFO/{posix,right}

    for tz in etcetera southamerica northamerica europe africa antarctica  \
        asia australasia backward pacificnew solar87 solar88 solar89 \
            systemv; do
        zic -L /dev/null   -d $ZONEINFO       -y "sh yearistype.sh" ${tz}
        zic -L /dev/null   -d $ZONEINFO/posix -y "sh yearistype.sh" ${tz}
        zic -L leapseconds -d $ZONEINFO/right -y "sh yearistype.sh" ${tz}
    done

    cp -v zone.tab iso3166.tab $ZONEINFO
    zic -d $ZONEINFO -p America/New_York
    unset ZONEINFO

    cp -v --remove-destination /usr/share/zoneinfo/America/Montreal \
            /etc/localtime

Configuration du dynamic library loader:

    cat > /etc/ld.so.conf << "EOF"
    # Begin /etc/ld.so.conf
    /usr/local/lib
    /opt/lib
    include /etc/ld.so.conf.d/*.conf

    EOF

    mkdir /etc/ld.so.conf.d

Ajustement toolchain
--------------------

Premièrement, créer des backups des anciens outils

    mv -v /tools/bin/{ld,ld-old}
    mv -v /tools/$(gcc -dumpmachine)/bin/{ld,ld-old}
    mv -v /tools/bin/{ld-new,ld}
    ln -sv /tools/bin/ld /tools/$(gcc -dumpmachine)/bin/ld

mettre à jour gcc pour utiliser notre nouveau ld

    gcc -dumpspecs | sed -e 's@/tools@@g' \
        -e '/\*startfile_prefix_spec:/{n;s@.*@/usr/lib/ @}' \
        -e '/\*cpp:/{n;s@$@ -isystem /usr/include@}' > \
        `dirname $(gcc --print-libgcc-file-name)`/specs


(Optionel) tests d'environnement
--------------------------------

dans un dossier vide, éxécuter les commandes suivantes:

    echo 'main(){}' > dummy.c
    cc dummy.c -v -Wl,--verbose &> dummy.log


------------------------------------------------------

test d'interpreteur:

    readelf -l a.out | grep ': /lib'

doit retourner:

    [Requesting program interpreter: /lib/ld-linux.so.2]

------------------------------------------------------

test objets:

    grep -o '/usr/lib.*/crt[1in].*succeeded' dummy.log

doit retourner:

    /usr/lib/crt1.o succeeded
    /usr/lib/crti.o succeeded
    /usr/lib/crtn.o succeeded


------------------------------------------------------

test de headers:

    grep -B1 '^ /usr/include' dummy.log

doit retourner:

    #include <...> search starts here:
    /usr/include


------------------------------------------------------

test paths:

    grep 'SEARCH.*/usr/lib' dummy.log |sed 's|; |\n|g'

doit retourner:

    SEARCH_DIR("/tools/i686-pc-linux-gnu/lib")
    SEARCH_DIR("/usr/lib")
    SEARCH_DIR("/lib");


------------------------------------------------------

test libc:

    grep "/lib.*/libc.so.6 " dummy.log

doit retourner:

    attempt to open /lib/libc.so.6 succeeded


------------------------------------------------------

test linker gcc:

    grep found dummy.log

doit retourner:

    found ld-linux.so.2 at /lib/ld-linux.so.2


------------------------------------------------------

nettoyer les tests:

    rm -v dummy.c a.out dummy.log

binutils
--------

**SBU :** 1.9

**Temps de compilation :** 7m

supprimer vieux fichier info (autoconf contient une version plus à jour)

    rm -fv etc/standards.info
    sed -i.bak '/^INFO/s/standards.info //' etc/Makefile.in

patch pour binutils:

    patch -Np1 -i ../binutils-2.23.1-testsuite_fix-1.patch

configuration et compilation

    mkdir -v ../binutils-build
    cd ../binutils-build
    ../binutils-2.23.1/configure --prefix=/usr --enable-shared
    make tooldir=/usr
    make tooldir=/usr install

installation des headers "liberty", utilisés par quelques paquets

    cp -v ../binutils-2.23.1/include/libiberty.h /usr/include


zlib
----

**temps de compilation :** 1m

    cd /sources
    tar xf zlib-1.2.7.tar.bz2
    cd zlib-1.2.7
    ./configure --prefix=/usr
    make
    make install

déplacer les libraries vers /lib (dépendances de certains paquets)

    mv -v /usr/lib/libz.so.* /lib
    ln -sfv ../../lib/libz.so.1.2.7 /usr/lib/libz.so

file
----

**temps de compilation :** 1m

    cd /sources
    tar xf file-5.13.tar.gz
    cd file-5.13
    ./configure --prefix=/usr
    make
    make install

gmp
---

**temps de compilation :** 5m

    cd /sources
    tar xf gmp-5.1.1.tar.xz
    cd gmp-5.1.1
    ./configure --prefix=/usr --enable-cxx
    make
    make install

mpfr
----

**temps de compilation :** 2m

    cd /sources
    tar xf mpfr-3.1.1.tar.xz
    cd mpfr-3.1.1
    ./configure --prefix=/usr --enable-thread-safe --docdir=/usr/share/doc/mpfr-3.1.1
    make
    make install

Installation de la documentation

    make html
    make install-html

mpc
---

**temps de compilation :** 1m

    cd /sources
    tar xf mpc-1.0.1.tar.gz
    cd mpc-1.0.1
    ./configure --prefix=/usr
    make
    make install

gcc
---

**temps de compilation :** 2m

**temps de test :** estimé à 4h


    cd /sources
    tar xf gcc-4.7.2.tar.bz2
    cd gcc-4.7.2


ne pas installer libiberty, car binutils en installe un

    sed -i 's/install_to_$(INSTALL_DEST) //' libiberty/Makefile.in

ne pas compiler les fichiers info

    sed -i 's/BUILD_INFO=info/BUILD_INFO=/' gcc/configure

compiler avec -fomit-frame-pointer, pour éviter des incompatibilités avec glibc

    case `uname -m` in
        i?86) sed -i 's/^T_CFLAGS =$/& -fomit-frame-pointer/' gcc/Makefile.in ;;
    esac

corriger une erreur dans le makefile

    sed -i -e /autogen/d -e /check.sh/d fixincludes/Makefile.in

configuration et compilation

    mkdir -v ../gcc-build
    cd ../gcc-build
    ../gcc-4.7.2/configure --prefix=/usr            \
                           --libexecdir=/usr/lib    \
                           --enable-shared          \
                           --enable-threads=posix   \
                           --enable-__cxa_atexit    \
                           --enable-clocale=gnu     \
                           --enable-languages=c,c++ \
                           --disable-multilib       \
                           --disable-bootstrap      \
                           --with-system-zlib
    make
    make install
    ln -sv ../usr/bin/cpp /lib

redéplacer un fichier mis à la mauvaise place

    mkdir -pv /usr/share/gdb/auto-load/usr/lib
    mv -v /usr/lib/*gdb.py /usr/share/gdb/auto-load/usr/lib

(Optionnel) tests gcc
---------------------

dans un dossier vide, générer le fichier de tests

    echo 'main(){}' > dummy.c
    cc dummy.c -v -Wl,--verbose &> dummy.log

-----------------------------------------------------

tests interpreteur:

    readelf -l a.out | grep ': /lib'

doit retourner:

    [Requesting program interpreter: /lib/ld-linux.so.2]

-----------------------------------------------------

tests fichier objets:

    grep -o '/usr/lib.*/crt[1in].*succeeded' dummy.log

doit retourner:

    /usr/lib/gcc/i686-pc-linux-gnu/4.7.2/../../../crt1.o succeeded
    /usr/lib/gcc/i686-pc-linux-gnu/4.7.2/../../../crti.o succeeded
    /usr/lib/gcc/i686-pc-linux-gnu/4.7.2/../../../crtn.o succeeded

-----------------------------------------------------

tests fichiers headers:

    grep -B4 '^ /usr/include' dummy.log

doit retourner:

    #include <...> search starts here:
     /usr/lib/gcc/i686-pc-linux-gnu/4.7.2/include
     /usr/local/include
     /usr/lib/gcc/i686-pc-linux-gnu/4.7.2/include-fixed
     /usr/include

tests de path:

    grep 'SEARCH.*/usr/lib' dummy.log |sed 's|; |\n|g'

doit retourner:

    SEARCH_DIR("/usr/i686-pc-linux-gnu/lib")
    SEARCH_DIR("/usr/local/lib")
    SEARCH_DIR("/lib")
    SEARCH_DIR("/usr/lib");

-----------------------------------------------------

tests libc:

    grep "/lib.*/libc.so.6 " dummy.log

doit retourner:

    attempt to open /lib/libc.so.6 succeeded

-----------------------------------------------------

tests de linker gcc:

    grep found dummy.log

doit retourner:

    found ld-linux.so.2 at /lib/ld-linux.so.2

-----------------------------------------------------

nettoyer les tests:

    rm -v dummy.c a.out dummy.log

sed
---

**temps de compilation :** 2m

    cd /sources
    tar xf sed-4.2.2.tar.bz2
    cd sed-4.2.2
    ./configure --prefix=/usr --bindir=/bin --htmldir=/usr/share/doc/sed-4.2.2
    make
    make install

bzip2
-----

**temps de compilation :** 0m13s

    cd /sources/bzip2-1.0.6
    patch -Np1 -i ../bzip2-1.0.6-install_docs-1.patch

transformer les liens symboliques absolus en lien symboliques relatifs

    sed -i 's@\(ln -s -f \)$(PREFIX)/bin/@\1@' Makefile

installer les man pages à la bonne place

    sed -i "s@(PREFIX)/man@(PREFIX)/share/man@g" Makefile

compilation

    make -f Makefile-libbz2_so
    make clean
    make

installation

    make PREFIX=/usr install

    cp -v bzip2-shared /bin/bzip2
    cp -av libbz2.so* /lib
    ln -sv ../../lib/libbz2.so.1.0 /usr/lib/libbz2.so
    rm -v /usr/bin/{bunzip2,bzcat,bzip2}
    ln -sv bzip2 /bin/bunzip2
    ln -sv bzip2 /bin/bzcat

pkg-config
----------

**temps de compilation :** 3m

    cd /sources
    tar xf pkg-config-0.28.tar.gz
    cd pkg-config-0.28
    ./configure --prefix=/usr --with-internal-glib --disable-host-tool --docdir=/usr/share/doc/pkg-config-0.28
    make
    make install

ncurses
-------

**temps de compilation :** 3m23s

    cd /sources/ncurses-5.9
    
    ./configure --prefix=/usr           \
                --mandir=/usr/share/man \
                --with-shared           \
                --without-debug         \
                --enable-pc-files       \
                --enable-widec
    
    make
    make install
    

certains paquets s'attendent à retrouver ncurses dans /lib. Nous allons donc
créer des liens symboliques de ncurses dans /lib

    mv -v /usr/lib/libncursesw.so.5* /lib
    ln -sfv ../../lib/libncursesw.so.5 /usr/lib/libncursesw.so
    

ajuster les librairies pour assurer la rétrocompatiblité des vieux paquets qui utilisent encore
le mode "non-wide-character" de ncurses

    for lib in ncurses form panel menu ; do
        rm -vf                    /usr/lib/lib${lib}.so
        echo "INPUT(-l${lib}w)" > /usr/lib/lib${lib}.so
        ln -sfv lib${lib}w.a      /usr/lib/lib${lib}.a
        ln -sfv ${lib}w.pc        /usr/lib/pkgconfig/${lib}.pc
    done
    ln -sfv libncurses++w.a /usr/lib/libncurses++.a
    

ajuster les librairies pour assurer la retrocompatibilité avec les paquets
qui compilent encore avec l'option -lcurses

    rm -vf                     /usr/lib/libcursesw.so
    echo "INPUT(-lncursesw)" > /usr/lib/libcursesw.so
    ln -sfv libncurses.so      /usr/lib/libcurses.so
    ln -sfv libncursesw.a      /usr/lib/libcursesw.a
    ln -sfv libncurses.a       /usr/lib/libcurses.a

util-linux
----------

**temps de compilation :** 3m15s

    cd /sources/util-linux-2.22.2
    

utiliser /var/lib/hwclock à la place de /etc/adjtime

    sed -i -e 's@etc/adjtime@var/lib/hwclock/adjtime@g' \
        $(grep -rl '/etc/adjtime' .)
    mkdir -pv /var/lib/hwclock
    
    ./configure --disable-su --disable-sulogin --disable-login
    make
    make install

psmisc
------

**temps de compilation :** 0m29s

    cd /sources/psmisc-22.20
    ./configure --prefix=/usr
    make
    make install
    

Selon la FHS, killall devrait se retrouver dans /bin. Nous allons donc le déplacer

    mv -v /usr/bin/fuser   /bin
    mv -v /usr/bin/killall /bin

procps-ng
---------

**temps de compilation :** 0m53s

    cd /sources/procps-ng-3.3.6
    ./configure --prefix=/usr                           \
                --exec-prefix=                          \
                --libdir=/usr/lib                       \
                --docdir=/usr/share/doc/procps-ng-3.3.6 \
                --disable-static                        \
                --disable-skill                         \
                --disable-kill
    make
    make install
    

déplacer les librairies dans /lib à des fins de rétrocompatibilité

    mv -v /usr/lib/libprocps.so.* /lib
    ln -sfv ../../lib/libprocps.so.1.1.0 /usr/lib/libprocps.so

e2fsprogs
---------

**temps de compilation :** 2m14s

    cd /sources/e2fsprogs-1.42.7
    mkdir -v build
    cd build
    ../configure --prefix=/usr         \
                 --with-root-prefix="" \
                 --enable-elf-shlibs   \
                 --disable-libblkid    \
                 --disable-libuuid     \
                 --disable-uuidd       \
                 --disable-fsck
    
    make
    make install
    make install-libs
    

changer les permissions pour permettre de retirer les symboles de debug plus tard

    chmod -v u+w /usr/lib/{libcom_err,libe2p,libext2fs,libss}.a
    

mettre à jour les paths pour les pages info
**TODO** Doesn't work all the time

    gunzip -v /usr/share/info/libext2fs.info.gz
    install-info --dir-file=/usr/share/info/dir /usr/share/info/libext2fs.info


shadow
------

**temps de compilation :** 1m34s

    cd /sources/shadow-4.1.5.1
    
Désactiver la compilation du programme 'groups' (déja installé par util-linux)

    sed -i 's/groups$(EXEEXT) //' src/Makefile.in
    find man -name Makefile.in -exec sed -i 's/groups\.1 / /' {} \;

Utiliser le sha-512 pour l'encryption des mot de passe

    sed -i -e 's@#ENCRYPT_METHOD DES@ENCRYPT_METHOD SHA512@' \
        -e 's@/var/spool/mail@/var/mail@' etc/login.defs
    
    ./configure --sysconfdir=/etc
    make
    make install
    
déplacer passwd dans /bin pour des fins de rétrocompatibilité

    mv -v /usr/bin/passwd /bin
    
Activer les mot de passes dans un fichier "shadow" séparé

    pwconv
    grpconv
    

ATTENTION
---------

À Partir de ce moment ci, vous devez créer un mot de passe pour votre
utilisateur root. Ne l'oubliez surtout pas !

    passwd root

coreutils
---------

**temps de compilation :** 4m47s

    cd /sources/coreutils-8.21
    patch -Np1 -i ../coreutils-8.21-i18n-1.patch
    FORCE_UNSAFE_CONFIGURE=1 ./configure \
                --prefix=/usr         \
                --libexecdir=/usr/lib \
                --enable-no-install-program=kill,uptime
    
    make
    make install
    
déplacer les programmes dans /bin pour des fins de rétrocompatibilité

    mv -v /usr/bin/{cat,chgrp,chmod,chown,cp,date,dd,df,echo} /bin
    mv -v /usr/bin/{false,ln,ls,mkdir,mknod,mv,pwd,rm} /bin
    mv -v /usr/bin/{rmdir,stty,sync,true,uname,test,[} /bin
    mv -v /usr/bin/chroot /usr/sbin
    mv -v /usr/share/man/man1/chroot.1 /usr/share/man/man8/chroot.8
    sed -i s/\"1\"/\"8\"/1 /usr/share/man/man8/chroot.8
    mv -v /usr/bin/{head,sleep,nice} /bin

iana-etc
--------

**temps de compilation :** 0m1s

    cd /sources/iana-etc-2.30
    make
    make install

m4
==

**temps de compilation :** 1m18s

    cd /sources/m4-1.4.16
    

corriger une incompatibilité avec glibc 2.17

    sed -i -e '/gets is a/d' lib/stdio.in.h
    
    ./configure --prefix=/usr
    make
    make install

bison
-----

**temps de compilation :** 2m15s

    cd /sources/bison-2.7
    ./configure --prefix=/usr
    
Activer le support pour l'internationalisation

    echo '#define YYENABLE_NLS 1' >> lib/config.h
    
    make
    make install

grep
----

**temps de compilation :**  3m

    cd /sources
    tar xf grep-2.14.tar.xz
    cd grep-2.14
    ./configure --prefix=/usr --bindir=/bin
    make
    make install

readline
--------

**temps de compilation :** 0m47s

    cd /sources/readline-6.2
    
éviter de déplacer les librairies de readline lors de l'installation

    sed -i '/MV.*old/d' Makefile.in
    sed -i '/{OLDSUFF}/c:' support/shlib-install
    
    patch -Np1 -i ../readline-6.2-fixes-1.patch
    ./configure --prefix=/usr --libdir=/lib
    make SHLIB_LIBS=-lncurses
    make install
    
Déplacer les librairies statiques dans un emplacement plus standard

    mv -v /lib/lib{readline,history}.a /usr/lib
    
Supprimer les vieilles versions des librairies readline et créer des liens
vers /usr/lib pour des fins de rétrocompatibilité

    rm -v /lib/lib{readline,history}.so
    ln -sfv ../../lib/libreadline.so.6 /usr/lib/libreadline.so
    ln -sfv ../../lib/libhistory.so.6 /usr/lib/libhistory.so

bash
----

**temps de compilation :** 2m16s

    cd /sources/bash-4.2
    patch -Np1 -i ../bash-4.2-fixes-11.patch
    
    ./configure --prefix=/usr                     \
                --bindir=/bin                     \
                --htmldir=/usr/share/doc/bash-4.2 \
                --without-bash-malloc             \
                --with-installed-readline
    make
    make install

libtool
-------

**temps de compilation :**  1m

    cd /sources
    tar xf libtool-2.4.2.tar.gz
    cd libtool-2.4.2
    ./configure --prefix=/usr
    make
    make install

gdbm
----

**temps de compilation :**  1m

    cd /sources
    tar xf gdbm-1.10.tar.gz
    cd gdbm-1.10
    ./configure --prefix=/usr --enable-libgdbm-compat
    make
    make install

inetutils
---------

**temps de compilation :** 2m46s

    cd /sources/inetutils-1.9.1
    

corriger une incompatibilité avec glibc 2.17

    sed -i -e '/gets is a/d' lib/stdio.in.h
    
    ./configure --prefix=/usr  \
        --libexecdir=/usr/sbin \
        --localstatedir=/var   \
        --disable-ifconfig     \
        --disable-logger       \
        --disable-syslogd      \
        --disable-whois        \
        --disable-servers
    make
    make install
    

déplacer les programmes dans /bin pour des fins de rétrocompaibilité

    mv -v /usr/bin/{hostname,ping,ping6,traceroute} /bin

perl
----

**temps de compilation :** 8m18s

    cd /sources/perl-5.16.2
    
perl nécessite un /etc/hosts pour la compilation. nous créons donc un /etc/hosts 
temporaire

    echo "127.0.0.1 localhost $(hostname)" > /etc/hosts
    
Utiliser le zlib que nous avons installé à la place d'un zlib interne

    sed -i -e "s|BUILD_ZLIB\s*= True|BUILD_ZLIB = False|"           \
           -e "s|INCLUDE\s*= ./zlib-src|INCLUDE    = /usr/include|" \
           -e "s|LIB\s*= ./zlib-src|LIB        = /usr/lib|"         \
        cpan/Compress-Raw-Zlib/config.in
    
    sh Configure -des -Dprefix=/usr                 \
                      -Dvendorprefix=/usr           \
                      -Dman1dir=/usr/share/man/man1 \
                      -Dman3dir=/usr/share/man/man3 \
                      -Dpager="/usr/bin/less -isR"  \
                      -Duseshrplib
    make
    make install

autoconf
--------

**temps de compilation :**  1m

    cd /sources
    tar xf autoconf-2.69.tar.xz
    cd autoconf-2.69
    ./configure --prefix=/usr
    make
    make install

automake
--------

**temps de compilation :** 1m

    cd /sources
    tar xf automake-1.13.1.tar.xz
    cd automake-1.13.1
    ./configure --prefix=/usr --docdir=/usr/share/doc/automake-1.13.1
    make
    make install

diffutils
---------

**temps de compilation :** 1m43s

    cd /sources/diffutils-3.2
    

corriger une incompatibilité avec glibc 2.17

    sed -i -e '/gets is a/d' lib/stdio.in.h
    
    ./configure --prefix=/usr
    make
    make install

gawk
----

**temps de compilation :** 1m

    cd /sources
    tar xf gawk-4.0.2.tar.xz
    cd gawk-4.0.2
    ./configure --prefix=/usr --libexecdir=/usr/lib
    make
    make install

findutils
---------

**temps de compilation :** 1m34s

    cd /sources/findutils-4.4.2
    ./configure --prefix=/usr                   \
                --libexecdir=/usr/lib/findutils \
                --localstatedir=/var/lib/locate
    make
    make install
    

déplacer le programme dans /bin pour des fins de rétrocompatibilité

    mv -v /usr/bin/find /bin
    sed -i 's/find:=${BINDIR}/find:=\/bin/' /usr/bin/updatedb

flex
----

**temps de compilation :** 0m40s

    cd /sources/flex-2.5.37
    
    patch -Np1 -i ../flex-2.5.37-bison-2.6.1-1.patch
    ./configure --prefix=/usr             \
                --docdir=/usr/share/doc/flex-2.5.37
    make
    make install
    
certains programmes s'attendent à retrouver lex dans /usr/lib. Nous créons
donc un lien symbolique

    ln -sv libfl.a /usr/lib/libl.a
    
Créer un script de rétro-compatibilité pour l'ancien analyseur lexique, lex

    cat > /usr/bin/lex << "EOF"
    #!/bin/sh
    #Begin /usr/bin/lex

    exec /usr/bin/flex -l "$@"

    #End /usr/bin/lex
    EOF

    chmod -v 755 /usr/bin/lex

gettext
-------

**temps de compilation :** 12m

    cd /sources
    tar xf gettext-0.18.2.tar.gz
    cd gettext-0.18.2
    ./configure --prefix=/usr --docdir=/usr/share/doc/gettext-0.18.2
    make
    make install

groff
-----

**temps de compilation :** 2m39s

    cd /sources/groff-1.22.2
    PAGE=letter ./configure --prefix=/usr
    make
    
    mkdir -p /usr/share/doc/groff-1.22/pdf
    make install
    
le programme xman ne fonctionne pas sans ses liens symboliques

    ln -sv eqn /usr/bin/geqn
    ln -sv tbl /usr/bin/gtbl

xz
--

**temps de compilation :** 1m19s

    cd /sources/xz-5.0.4
    ./configure --prefix=/usr --libdir=/lib --docdir=/usr/share/doc/xz-5.0.4
    make
    make pkgconfigdir=/usr/lib/pkgconfig install

grub
----

**temps de compilation :** 5m9s

    cd /sources/grub-2.00
    

corriger une incompatibilité avec glibc 2.17

    sed -i -e '/gets is a/d' grub-core/gnulib/stdio.in.h
    
    ./configure --prefix=/usr          \
                --sysconfdir=/etc      \
                --disable-grub-emu-usb \
                --disable-efiemu       \
                --disable-werror
    
    make
    make install

less
----

**temps de compilation :** 1m

    cd /sources
    tar xf less-451.tar.gz
    cd less-451
    ./configure --prefix=/usr --sysconfdir=/etc
    make
    make install

gzip
----

**temps de compilation :** 1m48s

    cd /sources/gzip-1.5
    ./configure --prefix=/usr --bindir=/bin
    make
    make install
    
déplacer les programmes dans /usr/bin pour des fins de rétrocompatibilité

    mv -v /bin/{gzexe,uncompress,zcmp,zdiff,zegrep} /usr/bin
    mv -v /bin/{zfgrep,zforce,zgrep,zless,zmore,znew} /usr/bin

iproute2
--------

**temps de compilation :** 0m39s

    cd /sources/iproute2-3.8.0
    
désactivation de arpd qui est très peu utilisé de nos jours

    sed -i '/^TARGETS/s@arpd@@g' misc/Makefile
    sed -i /ARPD/d Makefile
    sed -i 's/arpd.8//' man/man8/Makefile
    

corriger une erreur de compilation

    sed -i 's/-Werror//' Makefile
    

compilation

    make DESTDIR=
    make DESTDIR=              \
         MANDIR=/usr/share/man \
         DOCDIR=/usr/share/doc/iproute2-3.8.0 install

kbd
---

**temps de compilation :** 0m38s

    cd /sources/kbd-1.15.5
    patch -Np1 -i ../kbd-1.15.5-backspace-1.patch
    sed -i -e '326 s/if/while/' src/loadkeys.analyze.l
    

désactiver l'option 'resizeicons' qui est très peu utilisé de nos jours

    sed -i 's/\(RESIZECONS_PROGS=\)yes/\1no/g' configure
    sed -i 's/resizecons.8 //' man/man8/Makefile.in
    
    ./configure --prefix=/usr --datadir=/lib/kbd \
                --disable-vlock
    
    make
    make install
    

déplacer les programmes dans /bin pour des fins de rétrocompatibilité

    mv -v /usr/bin/{kbd_mode,loadkeys,openvt,setfont} /bin

kmod
----

**temps de compilation :** 0m39s

    cd /sources/kmod-12
    ./configure --prefix=/usr       \
                --bindir=/bin       \
                --libdir=/lib       \
                --sysconfdir=/etc   \
                --disable-manpages  \
                --with-xz           \
                --with-zlib
    
    make
    make pkgconfigdir=/usr/lib/pkgconfig install
    
créer des liens symboliques pour la rétrocompatibilité avec module-init-tools

    for target in depmod insmod modinfo modprobe rmmod; do
      ln -sv ../bin/kmod /sbin/$target
    done
    ln -sv kmod /bin/lsmod

libpipeline
-----------

**temps de compilation :** 1m

    cd /sources
    tar xf libpipeline-1.2.2.tar.gz
    cd libpipeline-1.2.2
    PKG_CONFIG_PATH=/tools/lib/pkgconfig ./configure --prefix=/usr
    make
    make install

make
----

**temps de compilation :** 0m41s

    cd /sources/make-3.82
    patch -Np1 -i ../make-3.82-upstream_fixes-3.patch
    ./configure --prefix=/usr
    make
    make install

man-db
------

**temps de compilation :** 2m37s

    cd /sources/man-db-2.6.3
    ./configure --prefix=/usr                        \
                --libexecdir=/usr/lib                \
                --docdir=/usr/share/doc/man-db-2.6.3 \
                --sysconfdir=/etc                    \
                --disable-setuid                     \
                --with-browser=/usr/bin/lynx         \
                --with-vgrind=/usr/bin/vgrind        \
                --with-grap=/usr/bin/grap
    
    make
    make install

patch
-----

**temps de compilation :** 2m

    cd /sources
    tar xf patch-2.7.1.tar.xz
    cd patch-2.7.1
    ./configure --prefix=/usr
    make
    make install

sysklogd
--------

**temps de compilation :** 0m0s

    cd /sources/sysklogd-2.7.1
    make
    make BINDIR=/sbin install
    
création du fichier de configuration pour syslog

    cat > /etc/syslog.conf << "EOF"
    #Begin /etc/syslog.conf

    auth,authpriv.* -/var/log/auth.log
    *.*;auth,authpriv.none -/var/log/sys.log
    daemon.* -/var/log/daemon.log
    kern.* -/var/log/kern.log
    mail.* -/var/log/mail.log
    user.* -/var/log/user.log
    *.emerg *

    #End /etc/syslog.conf
    EOF

sysvinit
--------

**temps de compilation :** 0m7s

    cd /sources/sysvinit-2.88dsf
    
ajustement mineur pour affichier des messages plus clairs
lorsque le système change de runlevel

    sed -i 's@Sending processes@& configured via /etc/inittab@g' src/init.c
    
désactiver la compîlation de wall, mountpoint et utmpdump. Ces programmes sont
fournis par util-linux

    sed -i -e '/utmpdump/d' \
           -e '/mountpoint/d' src/Makefile
    
    make -C src
    make -C src install

tar
---

**temps de compilation :** 3m8s

    cd /sources/tar-1.26
    

corriger une incompatibilité avec glibc 2.17

    sed -i -e '/gets is a/d' gnu/stdio.in.h
    

configure

    FORCE_UNSAFE_CONFIGURE=1  \
    ./configure --prefix=/usr \
                --bindir=/bin \
                --libexecdir=/usr/sbin
    
    make
    make install
    make -C doc install-html docdir=/usr/share/doc/tar-1.26

texinfo
-------

**temps de compilation :** 1m54s

    cd /sources/texinfo-5.0
    ./configure --prefix=/usr
    make
    make install
    make TEXMF=/usr/share/texmf install-tex
    
Maintenant que texinfo est installé, nous pouvons regénérer les pages info

    cd /usr/share/info
    rm -v dir
    for f in *
    do install-info $f dir 2>/dev/null
    done

vim
---

**temps de compilation :** 2m7s

    cd /sources/vim73
    
Utiliser /etc/vimrc comme fichier de configuration par défaut pour vim

    echo '#define SYS_VIMRC_FILE "/etc/vimrc"' >> src/feature.h
    
    ./configure --prefix=/usr --enable-multibyte
    make
    make install
    
Créer un lien symbolique pour le programme vi pour des fins de rétrocompatibilité

    ln -sv vim /usr/bin/vi
    for L in  /usr/share/man/{,*/}man1/vim.1; do
        ln -sv vim.1 $(dirname $L)/vi.1
    done
    
cérer un lien symbolique de la documentation de vim vers un emplacement standard

    ln -sv ../vim/vim73/doc /usr/share/doc/vim-7.3
    

Création du fichier de configuration de vim (vimrc)

    cat > /etc/vimrc << "EOF"
    " Begin /etc/vimrc
    
    set nocompatible
    set backspace=2
    syntax on
    if (&term == "iterm") || (&term == "putty")
      set background=dark
    endif
    
    " End /etc/vimrc
    EOF
    
    rm -R /sources/vim73
