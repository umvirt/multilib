# ULFS Mutilib

ULFS Version 0.2.2 (Linux From Scratch 12.2 systemd)

## About

Multilib is needed to run x86 binaries in amd64 environment:

* Wine
* Some compilers & build utils
* Old GNU/Linux games and emulators

Scripts to build multilib support for Linux From Scratch.

This scripts covers only base system which contain packages from LFS book.

Multilib is optional subsystem it can be installed at any time. The earlier the better.

Warning: Installed packages can affect on multilib packages build.
Build system of some 32-bit packages can detect 64-bit packages and start to use it as dependencies this lead to errors.
In order to avoid that we disable some checks in multilib packages build scripts.
If you get such error then fix build scripts manualy.


## Installation

* prepeare a disk space (~ 10GB is needed)
* point a build directory path in SRCROOT variable in config.sh file
* place LFS source packages and patches in build directory.
* download and place isl-0.24.tar.bz2 in build directory.
* run install script:

    ./install

Note: it is possible to run scripts one by one by placing it in scripts directory

## Quality check

There is no error handler. Multilib installation have to be verified. Check that all multilib packages have installed files with command

    wc -l log/*.files | grep " 0"

This command will print packages which not installed properly.

If this command don't print output that means that all packages was installed properly.

## Cleanup

If multilib installed properly then remove */opt/temptools* directory.

## Default lib32 installation scripts

If you wish to add new package you can use followed templates according with a build system

### Autotools (configure)

    CC="gcc -m32" CXX="g++ -m32" \
    PKG_CONFIG_PATH="/usr/lib32/pkgconfig" \
    ./configure \
        --prefix=/usr \
        --disable-static \
        --libdir=/usr/lib32

    make

    make DESTDIR=$PWD/DESTDIR install
    cp -Rv DESTDIR/usr/lib32/* /usr/lib32

### CMake
    mkdir build
    cd build

    export CC="gcc -m32"
    export CXX="g++ -m32"
    export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"
    
    cmake \
         -DCMAKE_INSTALL_PREFIX=/usr       \
         -DCMAKE_INSTALL_LIBDIR=/usr/lib32 \
    ..

    make

    make DESTDIR=$PWD/DESTDIR install
    cp -Rv DESTDIR/usr/lib32/* /usr/lib32

### Meson

    mkdir build
    cd build

    CC="gcc -m32" CXX="g++ -m32" \
    PKG_CONFIG_PATH="/usr/lib32/pkgconfig" \
    meson setup --libdir /usr/lib32 \
        --prefix=/usr           \
        --libexecdir /usr/lib32     \
    ..


    meson compile

    meson install --destdir DESTDIR
    cp -Rv DESTDIR/usr/lib32/* /usr/lib32

## Usage

After installing Multilib you can install various packages.

To install WINE subsystem in ULFS just type:

    chimp install wine:lib32_amd64


## Useful links

* [https://www.linuxfromscratch.org/~dj/lfs-systemd-multilib/](https://www.linuxfromscratch.org/~dj/lfs-systemd-multilib/)
* [https://www.linuxfromscratch.org/~thomas/multilib/](https://www.linuxfromscratch.org/~thomas/multilib/)
* [https://systemd-devel.freedesktop.narkive.com/bEGtbucg/cross-building-systemd](https://systemd-devel.freedesktop.narkive.com/bEGtbucg/cross-building-systemd)
