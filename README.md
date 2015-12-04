# webthree-umbrella-cross

Repository containing Docker files for cross-compilation of the
components within the "webthree umbrella".

[Gavin Wood](https://github.com/gavofyork) says ...

    The many names reflect the fact that it is not a single project.
    Rather it's a collection of software that just happens to have
    a team (mostly) in common.
    
    cpp-ethereum is the historical name.
    webthree is the name for the repository implementing the webthree framework
    eth is the name for the webthree client (historical; should be renamed to web3 at some point)
    ++eth is its sometime "stylish" name.
    Aleth* are the GUIs
    TurboEthereum is the stylish name for the C++ core.

This cross-build support was developed for
[doublethinkco](http://doublethink.co) by
[Bob Summerwill](http://bobsummerwill.com)
and
[Anthony Cros](https://github.com/anthony-cros)
to bring Ethereum to mobile/wearable Linux platforms for the benefit
of the whole Ethereum community, current and future.

It is released as open source software under the permissive
[MIT license](https://github.com/doublethinkco/webthree-umbrella-cross/blob/master/LICENSE).

See [Porting Ethereum to Mobile Linux](http://doublethink.co/2015/09/22/porting-ethereum-to-mobile-linux/)
blog for an overview of our efforts.

# How to use it

Clone this repo and use [Dockerfile-eth](https://github.com/doublethinkco/webthree-umbrella-cross/blob/master/Dockerfile-eth)
to build 'armel' binaries or 'armhf' binaries:

    $ git clone https://github.com/doublethinkco/webthree-umbrella-cross.git
    $ cd webthree-umbrella-cross
    $ sudo ./build-armel.sh

or

    $ git clone https://github.com/doublethinkco/webthree-umbrella-cross.git
    $ cd webthree-umbrella-cross
    $ sudo ./build-armhf.sh

That generates a Docker *image* called 'cross-eth', which is not the same as
a Docker *container*.  Docker *images* are immutable binary images, which are
analogous to VM snapshots.  Docker *containers* are particular instances
of those images.

To get an instance of that newly created image running you need to do:

    $ sudo docker run -i -t cross-eth /bin/bash

Which creates an instance of that container and drops you into a BASH shell.
Within that shell you will see the HASH which has been assigned to that
particular container instance.   Note it, because you will need it (see
next step).   In the root of your ~ directory you will see a TGZ file which
you can copy out to the host machine with the following command from
another shell instance on your host machine.  Your "docker run" *must*
still be running for this copy step to work.    If somebody who has more
Docker experience knows how to streamline this experience, please speak up!

    $ sudo docker cp HASH_OF_CONTAINER:/FILENAME.tgz ~/

Then you can "exit" the "docker run" session, and stop that Docker
container running.   It has served its purpose.

[Dockerfile-eth](https://github.com/doublethinkco/webthree-umbrella-cross/blob/master/Dockerfile-eth)
makes use of the [crosstool-NG](http://crosstool-ng.org/) toolchain-building
scripts to generate a cross-compiler which is then used in the rest of the
build process.  The cross-compiler was originally built and then used as
part of the same Docker flow, but that was slow and unnecessarily, so has
been split into its own [Dockerfile-xcompiler](https://github.com/doublethinkco/webthree-umbrella-cross/blob/master/Dockerfile-xcompiler)
process, which can be run in a similar manner:

    $ git clone https://github.com/doublethinkco/webthree-umbrella-cross.git
    $ cd webthree-umbrella-cross
    $ sudo ./build-xcompiler-armel.sh

or

    $ git clone https://github.com/doublethinkco/webthree-umbrella-cross.git
    $ cd webthree-umbrella-cross
    $ sudo ./build-xcompiler-armhf.sh

The results of a particular run of this process for 'armel' are
[hard-coded in the Dockerfile-eth](https://github.com/doublethinkco/webthree-umbrella-cross/blob/master/Dockerfile-eth#L46)
where they are copied directly into the container.


# Dependency graph for the webthree components

![Webthree](https://github.com/doublethinkco/webthree-umbrella-cross/blob/master/dependency_graph.svg)

# Smartwatch status

| Form factor     | Vendor                  | Device                | OS                | ABI     | SoC                        | Core          | Native  | Cross |
| --------------- |:-----------------------:|:---------------------:|:-----------------:|:-------:|:--------------------------:|:-------------:|:-------:|:-----:|
| Smartwatch      | Samsung                 | Gear S2               | Tizen 2.3.1       | armv7   | Qualcomm MSM8x26           | 2 x Cortex-A7 | N/A     | [Broken #20](https://github.com/doublethinkco/webthree-umbrella-cross/issues/20)
| Smartwatch      | Apple                   | Apple Watch Sport     | watchOS 2.0       | armv7k  | Apple S1                   | 1 x ARM-v7k   | N/A     | [TODO #41](https://github.com/doublethinkco/webthree-umbrella-cross/issues/41)

# Smartphone status

| Vendor                  | Device                | OS                | ABI     | SoC                        | Core          | Native  | Cross |
|:-----------------------:|:---------------------:|:-----------------:|:-------:|:--------------------------:|:-------------:|:-------:|:-----:|
| Samsung                 | Samsung Z1            | Tizen 2.3.0       | armv7   | Spreadtrum SC7727S         | 2 x Cortex-A7 | N/A     | [Broken #20](https://github.com/doublethinkco/webthree-umbrella-cross/issues/20)
| Samsung                 | Samsung Z3            | Tizen 2.4.0       | armv7   | Spreadtrum SC7730S         | 4 x Cortex-A7 | N/A     | [Broken #20](https://github.com/doublethinkco/webthree-umbrella-cross/issues/20)
| LG                      | Nexus 5               | Sailfish 2.0      | armv7   | Qualcomm Snapdragon 800    | 4 x Krait 400 | [Working](https://twitter.com/vgrade/status/671818784055889921) | [Broken #21](https://github.com/doublethinkco/webthree-umbrella-cross/issues/21)
| Jolla                   | Jolla Phone           | Sailfish 2.0      | armv7   | Qualcomm Snapdragon 400    | 2 x Krait 300 | [WIP](https://build.merproject.org/project/show/home:vgrade:ethereum) | [Broken #21](https://github.com/doublethinkco/webthree-umbrella-cross/issues/21)
| Intex                   | Aquafish              | Sailfish 2.0      | armv7   | Qualcomm Snapdragon ???    |               | TODO    | TODO | Due for release in Q1 2016
| Meizu                   | MX4 Ubuntu Edition    | Ubuntu Touch      | armv7   | MediaTek MT6595            | 4 x Cortex-A17, 4 x Cortex-A7 | TODO | [TODO #3](https://github.com/doublethinkco/webthree-umbrella-cross/issues/3)
| Samsung                 | Galaxy S3             | Android 4.3       | armel   | Samsung Exynos 4412 Quad   | 4 x Cortex-A9 | N/A     | [TODO #35](https://github.com/doublethinkco/webthree-umbrella-cross/issues/35)
| Samsung                 | Galaxy S4             | Android 4.4       | armel   | Qualcomm Snapdragon 600    | 4 x Krait 300 | N/A     | [TODO #35](https://github.com/doublethinkco/webthree-umbrella-cross/issues/35)
| Samsung                 | Galaxy S6             | Android 5.0.2     | aarch64 | Samsung Exynos 7420        | 4 x Cortex-A57, 4 x Cortex-A53 | N/A | [TODO #35](https://github.com/doublethinkco/webthree-umbrella-cross/issues/35)
| Apple                   | iPhone 3GS            | iOS 3             | armv7   | Samsung S5PC100            | 1 x Cortex-A8 | N/A     | [TODO #36](https://github.com/doublethinkco/webthree-umbrella-cross/issues/36)
| Apple                   | iPhone 5              | iOS 6             | armv7   | Apple A6                   | 2 x ARMv7A    | N/A     | [TODO #36](https://github.com/doublethinkco/webthree-umbrella-cross/issues/36)

# Developer phone status

| Vendor                  | Device                | OS                | ABI     | SoC                        | Core          | Native  | Cross |
|:-----------------------:|:---------------------:|:-----------------:|:-------:|:--------------------------:|:-------------:|:-------:|:-----:|
| Samsung                 | RD-210                | Tizen 2.2.0       | armv7   | Samsung Exynos 4210        | 2 x Cortex-A9 | N/A     | [Broken #20](https://github.com/doublethinkco/webthree-umbrella-cross/issues/20)
| Samsung                 | RD-PQ                 | Tizen 2.3.0       | armv7   | Samsung Exynos 4412 Quad   | 4 x Cortex-A9 | N/A     | [Broken #20](https://github.com/doublethinkco/webthree-umbrella-cross/issues/20)
| Samsung                 | TM1                   | Tizen 2.4.0       | armv7   | Spreadtrum SC7730S         | 4 x Cortex-A7 | N/A     | [Broken #20](https://github.com/doublethinkco/webthree-umbrella-cross/issues/20)

# Tablet status

| Vendor                  | Device                | OS                | ABI     | SoC                        | Core          | Native  | Cross |
|:-----------------------:|:---------------------:|:-----------------:|:-------:|:--------------------------:|:-------------:|:-------:|:-----:|
| Jolla                   | Jolla Tablet          | SailfishOS 2.0    | x86_64  | Intel Atom Z3735F          | 4 x Atom      | [WIP](https://build.merproject.org/project/show/home:vgrade:ethereum) | [Broken #21](https://github.com/doublethinkco/webthree-umbrella-cross/issues/21)
| Asus                    | Nexus 7               | Android 5.1.1     | armel   | Nvidia Tegra 3             | 4+1 x Cortex-A9 | N/A   | [TODO #35](https://github.com/doublethinkco/webthree-umbrella-cross/issues/35)
| Samsung                 | Galaxy Tab S 10.5     | Android 5.0.2     | armel   | Samsung Exynos 5 Octa 5420 | 4 x Cortex-A15, 4 x Cortex-A7 | N/A | [TODO #35](https://github.com/doublethinkco/webthree-umbrella-cross/issues/35)
| Apple                   | iPad Air 2            | iOS 8.1           | aarch64 | Apple A8X                  | 3 x ARMv8-A   | N/A     | [TODO #36](https://github.com/doublethinkco/webthree-umbrella-cross/issues/36)

# SBC (Single Board Computer) status

| Vendor                  | Device                | OS                | ABI     | SoC                        | Core          | Native  | Cross |
|:-----------------------:|:---------------------:|:-----------------:|:-------:|:--------------------------:|:-------------:|:-------:|:-----:|
| Raspberry Pi Foundation | Raspberry Pi Model A  | Raspbian          | armv6   | Broadcom BCM2835           | 1 x ARMv6     | TODO    | TODO
| Raspberry Pi Foundation | Raspberry Pi Model B+ | Raspbian          | armv6   | Broadcom BCM2835           | 1 x ARMv6     | Working | TODO
| Raspberry Pi Foundation | Raspberry Pi Zero     | Raspbian          | armv6   | Broadcom BCM2835           | 1 x ARMv6     | TODO    | [Working](https://twitter.com/vgrade/status/670677685622939649)
| Raspberry Pi Foundation | Raspberry Pi 2        | Raspbian          | armv7   | Broadcom BCM2836           | 4 x Cortex-A7 | Working | [Working](https://twitter.com/EthEmbedded/status/670628642125438977)
| Odroid                  | Odroid XU3            | Ubuntu 15.04 MATE | armv7   | Samsung Exynos 5422        | 4 x Cortex-A15, 4 x Cortex-A7 | Working | [Working](https://twitter.com/BobSummerwill/status/670585217384628224)
| Odroid                  | Odroid XU4            | Tizen 3.x         | armv7   | Samsung Exynos 5422        | 4 x Cortex-A15, 4 x Cortex-A7 | TODO    | TODO
| Beaglebone              | Beaglebone Black      | Debian            | armv7   | Texas Instruments AM3358/9 | 1 x Cortex-A8 | Working | Working
| Wandboard               | Wandboard Quad        | Debian            | armv7   | Freescale i.MX6            | 4 x Cortex-A9 | Working | [Working](https://twitter.com/BobSummerwill/status/670573142914519040)
| C.H.I.P.                | C.H.I.P.              |                   | armv7   | Allwinner R8               | 1 x Cortex-A8 | TODO    | [Broken #40](https://github.com/doublethinkco/webthree-umbrella-cross/issues/40)
| Intel                   | Intel NUC DCP847SKE   |                   | i386    | Intel QS77 Express         |               | TODO    | TODO
| Intel                   | Intel Galileo         |                   | i386    | Intel Quark X1000          |               | TODO    | TODO
| Intel                   | Intel Edison          | Yocto             | x86_64  | Intel "Tangier" Z34XX      |               | Working | TODO
| Intel                   | Intel Curie           |                   | i386    | Intel Quark SE             |               | TODO    | TODO

NOTE - Here is some information on [ARM options in GCC](https://gcc.gnu.org/onlinedocs/gcc/ARM-Options.html).

See also [ARM Infocenter](http://infocenter.arm.com) and [ARM Achitecture](https://en.wikipedia.org/wiki/ARM_architecture) page on Wikipedia.

# Limitations

At the time of writing, this cross-compilation process only supports
the building of ARM binaries, and specifically 'armel' binaries for
armv5 and software floating-point ABI.  These binaries are very
generic and should work on many, many devices.   We are adding support
for other ABIs right now:

- [Support armhf](https://github.com/doublethinkco/webthree-umbrella-cross/issues/10)
- [Support i386 and x86_64](https://github.com/doublethinkco/webthree-umbrella-cross/issues/37)

BEWARE - boot2docker on OSX does not "just work" when installed.
Here are the commands you will inevitably forget whenever you reboot
or start a new terminal session:

http://stackoverflow.com/questions/29594800/docker-tls-error-on-mac/


Copyright (c) 2015 Kitsilano Software Inc
