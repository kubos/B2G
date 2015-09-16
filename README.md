# Boot to Gecko (B2G)

Boot to Gecko aims to create a complete, standalone operating system for the open web.

You can read more about B2G here:

  http://wiki.mozilla.org/B2G

follow us on twitter: @Boot2Gecko

  http://twitter.com/Boot2Gecko

join the Mozilla Platform mailing list:

  http://groups.google.com/group/mozilla.dev.platform

and talk to us on IRC:

  #B2G on irc.mozilla.org

## Prerequisites

### Linux - Strongly recomend using ubuntu/linux for builds.

* A 64 bit linux distro
  * See http://source.android.com/source/initializing.html on configuring USB access.
* 20GB of free disk space
* autoconf-2.13
* git
* ccache
* gcc/g++ __4.6.3 or older__
* bison
* flex
* 32bit ncurses
* zlib
  * You need both zlib1g and zlib1g-dev for both i386 and amd64. That is, for
    Ubuntu 12.04 Precise or older, they are zlib1g, zlib1g-dev, zlib1g:i386, and
    zlib1g-dev:i386; for Ubuntu 12.10 Quantal or newer, they are zlib1g:amd64,
    zlib1g-dev:amd64, zlib1g:i386, and zlib1g-dev:i386.
* make

Additionally, if you're building the emulator, you probably need the the Mesa
implementation of OpenGL.  On Ubuntu, this is the __libgl1-mesa-dev__ package.

Ubuntu 12.10 ships with gcc 4.7 by default, which causes build errors pretty
early in the process.  To use gcc 4.6, edit .userconfig and add

    export CC=gcc-4.6
    export CXX=g++-4.6

Of course, you'll need the g++-4.6 package installed.

On Ubuntu 13.04, for example, you might find `apt-get install zlib1g{,-dev}:i386`
is going to do something seriously wrong like removing all your toolchain
packages. You may execute either `apt-get install --no-install-recommends <packages>`
to explicitly disallow installing recommended packages, or `aptitude` to
interactively select the very necessary packages you need.

For full lists of minimum required packages that build B2G emulator on all
recent Ubuntu releases, see https://bugzilla.mozilla.org/show_bug.cgi?id=866489 .

### OSX

* XCode
* 20GB of free space
* homebrew
  * git (if not using XCode 4)
  * gpg
  * ccache
  * autoconf-2.13 - brew install https://raw.github.com/Homebrew/homebrew-versions/master/autoconf213.rb

#### Note: Builds for some devices require case-sensitive file systems

Some B2G subrepositories contain files whose names differ only in case.
This causes build failures when building for some target phones (such as the
Hamachi) if you're on a case-insensitive FS (the default on Mac).  You'll see
an error like the following during the ./build.sh step:

> [entering kernel]
> ERROR: You have uncommited changes in kernel
> You may force overwriting these changes
> with |source build/envsetup.sh force|
> 
> ERROR: Patching of kernel/ failed.

If you hit this error, the easiest way around it is to build on a
case-sensitive file-system.  Doing so doesn't require any re-partitioning; you
can simply create a disk image and build within it using the following
commands.

    $ hdiutil create -volname 'firefoxos' -type SPARSE -fs 'Case-sensitive Journaled HFS+' -size 40g ~/firefoxos.sparseimage
    $ open ~/firefoxos.sparseimage
    $ cd /Volumes/firefoxos/

See https://bugzilla.mozilla.org/show_bug.cgi?id=867259 for details.

# Fetching Source/Building

### Download Repo:
    $ mkdir ~/bin
    $ PATH=~/bin:$PATH
    $ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    $ chmod a+x ~/bin/repo


### Initialize Repo:

    $ repo init -u https://github.com/openkosmosorg/b2g-manifest/ -b BBB-port -m beaglebone-black.xml
    $ repo sync -cfj 16
All the source code is almost 20 Gb so this will take quite a while to download. Repo will probably fail at least once or multiple times, if this happens just restart it.
The options -cfj 16 gave me the best results but it may be different for you.

### Build the Kernel:
    $ cd kernel
    $ make ARCH=arm CROSS_COMPILE=arm-eabi- am335x_evm_android_defconfig
    $ make ARCH=arm CROSS_COMPILE=arm-eabi- -j4 uImage
if you get a an error similar to: 
>make: arm-eabi-gcc: Command not found

add 'prebuilts/gcc/linux-x86/arm/arm-eabi-4.7/bin/' to your PATH

### Build U-boot: 
    $ cd u-boot
    $ make CROSS_COMPILE=arm-eabi- distclean
    $ make CROSS_COMPILE=arm-eabi- am335x_evm_config
    $ make CROSS_COMPILE=arm-eabi- 

### Build b2g:

Where I'm stuck currently. Will update when progress is made.