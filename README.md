#ULFS Mutilib

Version 0.2.1 (Linux From Scratch 12.1 systemd)

## About

Scripts to build multilib support for Linux From Scratch.

Multilib support is needed to run x86 binaries in amd64 environment:

* Wine
* Some compilers & build utils
* Old GNU/Linux games and emulators

## Current status

Current status is almost done. 

All is needed is fix issue with systemd-255 build.

## Installation

* place LFS source packages and patches in source/Sources directory.
* download and place isl-0.24.tar.bz2 in source/Sources directory.
* run install script:

        ./install

Note: it is possible to run scripts one by one by placing it in scripts directory

## Default lib32 installation script

If you wish to add new package you can use followed template:

        CC="gcc -m32" ./configure \
            --prefix=/usr \
            --disable-static \
            --libdir=/usr/lib32

        make

        make DESTDIR=$PWD/DESTDIR install
        cp -Rv DESTDIR/usr/lib32/* /usr/lib32

## Useful links

* [https://www.linuxfromscratch.org/~dj/lfs-systemd-multilib/](https://www.linuxfromscratch.org/~dj/lfs-systemd-multilib/)
* [https://www.linuxfromscratch.org/~thomas/multilib/](https://www.linuxfromscratch.org/~thomas/multilib/)
* [https://systemd-devel.freedesktop.narkive.com/bEGtbucg/cross-building-systemd](https://systemd-devel.freedesktop.narkive.com/bEGtbucg/cross-building-systemd)
