# ULFS Mutilib Builder

ULFS Version: 0.3

LFS Version: 13.0

License: GPL

## Preface

Multilib is subsystem which needed to run 32-bit x86 binaries in 64-bit amd64 Linux environment:

* WINE
* User mode Linux (UML)
* Some compilers & build utils
* Old GNU/Linux games and emulators

Binary GNU/Linux distributions are provide Multilib in proper packages.

LFS project is provide MLFS book. MLFS is LFS book version with multilib support. 
It should be built from zero as alternative to LFS.

Enabling Multilib support for every amd64 system is redundant. In many cases it not needed.
It can became a heavy ballast which eats disk space and which makes backups bigger.

## About

ULFS is provide alternative to MLFS Multilib subsystem which can be installed on exist LFS system on demand.

* It replaces a common exist packages to packages with multilib support which was installed on LFS stage.
* It installs a multilib versions of libraries which was istalled on LFS stage.

ULFS Multilib is based on early version of MLFS.

* Many packages from MLFS are skiped because them already installed on LFS stage.
* MLFS instructions has been edited to work with current environment.

Software packages which was installed on BLFS stage is not replaced.
Packages multilib versions are can be installed anytime with archpackages directly by user or indirectly as dependencies.

ULFS Multilib is optional subsystem it can be installed at any time. The earlier the better.

To install Multilib on ULFS or other BLFS-based system this ULFS Multilib builder is can be used.

Warning: Installed packages can affect on multilib packages build.
Build system of some 32-bit packages can detect 64-bit packages and start to use it as dependencies this lead to errors.
In order to avoid that we disable some checks in multilib packages build scripts.
If you get such error then fix build scripts manually.

## Preparation

### Downloading ULFS Multilib Builder

Select a directory for ULFS Multilib Builder. 
To reduce impact on root filesystem it's recomended to place ULFS Multilib installer on additional partition.

Use *Git* to download ULFS Multilib builder and place it on multilib-builder directory:

    git clone https://gitlab.com/Umvirt/multilib multilib-builder

Select proper branch:

    git checkout 0.2.4

After downloading via *Git* you have to make some directories:

    make dirs

### Source packages

Get LFS book source packages and put them in some directory.
A path to this directory is should be stored in $SRCROOT config file variable.
Like in case with multilib-builder directory it's recomended to use an additional partition.

Also this directory will be used as build directory.

This directory should have at least 10 GB free space.

### Configuration

You can use "config/config.sh" file to set configuration options.

If file is not available copy config file template.

    cd config
    cp config.sh.sample config.sh

After editing config.sh file check configuration with dumpconfig script:

    ./dumpconfig

Sample dumpconfig output:

    Temporary directory: /sources/multilib
    Sources directory: /sources

### Build environment init

Build directory should be created according to config file before running customization.

To init environment run:

    ./envinit

## Build and installation

After build environment initialization run install script:

    ./install

Note: it is possible to run scripts one by one by placing it in scripts directory

## Quality check

There is no error handler. Multilib installation have to be verified. Check that all multilib packages have installed files with command

    wc -l log/*.files | grep " 0"

This command will print packages which not installed properly.

If this command don't print output that means that all packages was installed properly.

## Cleanup

If multilib installed properly then you can remove temporary directory and contents of sources directory.

For debugging purposes in future it's recommended to copy contents of build/log directory to /var/log/multilib directory. 

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

### Wine

To install WINE subsystem in ULFS just type:

    chimp install wine:lib32_amd64

### User mode Linux

Copy or extract Linux kernel in some directory.

Configure it to run as application.

    make ARCH=um defconfig

Build Linux as application

    make ARCH=um

Check and run "linux" file in kernel directory. It should ouput an error message:

    Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(98,0)

This message means that Linux was successfuly built as application but it can't run a disk system.

Check User mode Linux documentation at user\_mode\_linux\_howto\_v2.rst file in Documentation/virt/uml Linux kernel directory.

## Notes

### Virtualization

In some cases when 32-bit binaries are not mixed with 64-bit ones a virtualization technology is can be used as alternative to multilib.
It's possible to run 32-bit environment in Virtual Machine (VM) inside 64-bit environment.

### GCC extended version

ULFS Multilib installs basic multilib version of GCC. Which equalent for basic nonmultilib version of GCC received after LFS build.

Some packages installs an extended nonmultilib GCC version with additional languages which brokes multilib support.
To prevent this gcc-multilib package should be installed before this packages. It blocks nonmultilib version install.

### Why ULFS Multilib is installed after LFS install

ULFS is built for all CPU architectures from one LFS recipe.
MLFS usage is significantly increase complexity to support and conflicting with LFS.
Each subsystem sholud have own objectives and responsibility area.
Subsystems shouldn't have to conflict each others.

## Useful links

* [https://www.linuxfromscratch.org/~thomas/multilib-systemd/](https://www.linuxfromscratch.org/~thomas/multilib-systemd/)
* [https://systemd-devel.freedesktop.narkive.com/bEGtbucg/cross-building-systemd](https://systemd-devel.freedesktop.narkive.com/bEGtbucg/cross-building-systemd)
