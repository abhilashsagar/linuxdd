==============
Building embedded linux system using mainline kernel for beaglebone black
==============

ARM Cross Compiler Setup
==============

Before jumping deep into the wonderful world of building embedded Linux from scratch, let us set up an ARM cross compiler. Create a directory and name it `lfs`. Please make sure to use this directory to try this post so that it will be easy to keep track of things. Now traverse into the `lfs` directory:

.. code-block:: bash

   cd lfs

I have used Debian Distribution for compiling and building embedded Linux. I suggest you use the same or else you can use a virtual machine. You can download virtual image copies at `osboxes`. You can either download for VMWare or for VirtualBox.

Download ARM GCC cross compiler and untar it:

.. code-block:: bash

   wget -c https://releases.linaro.org/components/toolchain/binaries/6.2-2016.11/arm-linux-gnueabihf/gcc-linaro-6.2.1-2016.11-i686_arm-linux-gnueabihf.tar.xz
   tar xf gcc-linaro-6.2.1-2016.11-i686_arm-linux-gnueabihf.tar.xz
   export CC=`pwd`/gcc-linaro-6.2.1-2016.11-i686_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-

To check the version, type:

.. code-block:: bash

   ${CC}gcc --version

You should see something like:

.. code-block:: bash

   arm-linux-gnueabihf-gcc (Linaro GCC 6.2-2016.11) 6.2.1 20161016
   Copyright (C) 2016 Free Software Foundation, Inc.
   This is free software; see the source for copying conditions. There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

Downloading mainline kernel source and untar it:

.. code-block:: bash

   wget https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.10.2.tar.xz
   tar xvf linux-4.10.2.tar.xz
   cd linux-4.10.2

Compiling the kernel with BeagleBone default configuration:

.. code-block:: bash

   sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- omap2plus_defconfig
   sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- uImage dtbs LOADADDR=0x80008000 -j

Move up one level:

.. code-block:: bash

   cd ..

Clone bootloader U-Boot:

.. code-block:: bash

   git clone git://git.denx.de/u-boot.git u-boot/
   cd u-boot

Compiling U-Boot with BeagleBone default configuration:

.. code-block:: bash

   make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- am335x_boneblack_defconfig
   make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-

After compiling U-Boot, if you list the files, you should see something like this:

.. code-block:: bash

   ls
   api drivers MAINTAINERS spl u-boot.lds
   arch dts Makefile System.map u-boot.map
   board examples MLO test u-boot-nodtb.bin
   cmd fs MLO.byteswap tools u-boot.srec
   common include net u-boot u-boot.sym
   config.mk Kbuild post u-boot.bin
   configs Kconfig README u-boot.cfg
   disk lib scripts u-boot.cfg.configs
   doc Licenses snapshot.commit u-boot.img

Prepare microSD card:

If you have come this far, I assume that you have successfully cross-compiled the Linux kernel and U-Boot. The next step is pretty simple. All you need to do is take an SD card and make two partitions. The first partition is FAT32, containing U-Boot files, and the second partition is ext4, containing the root file system. Use GParted tool:

1. Open GParted and select the SD card.
2. Right-click on the partition and unmount all the partitions. Delete all the partitions.
3. Right-click and make a new partition. The first is of size 100 MB. Enter 100 in "New size (MiB)" and choose file system as fat32. Name the label as "BOOT."
4. Right-click on the unallocated space and make a new partition. Enter 1000 in "New size (MiB)" and choose file system as ext4. Label it as "rootfs."

Next step is to mark the first partition as boot. Copy MLO, u-boot.img from `lfs/u-boot` directory, `am335x-boneblack.dtb` from `lfs/linux/arch/arm/boot/dts` directory, `uImage` from `lfs/linux/arch/arm/boot` to BOOT partition of the microSD card.

Higher Level block diagram of microSD card:

And make a file called `uEnv.txt` in the BOOT partition and copy the below code:

.. code-block:: text

   loadaddr=0x82000000
   fdtaddr=0x88000000
   arg=setenv bootargs console=ttyO0,115200n8 root=/dev/mmcblk0p2 rw rootfstype=ext4 rootwait
   image=load mmc 0:1 ${loadaddr} uImage ;
   fdt=load mmc 0:1 ${fdtaddr} am335x-bonegreen.dtb ;
   uenvcmd=run arg;load image;load fdt;bootm ${loadaddr} - ${fdtaddr};

Now you need to download the root file system. I have created a minimal file system using BusyBox:

See :download:`Rootfs <rootfs.7z>`.


First you need to decompress it using 7z and then copy the rootfs.tar to rootfs partition and untar it:

.. code-block:: bash

   sudo cp rootfs.tar /media/$USER/rootfs/
   cd /media/$USER/rootfs/
   sudo tar xvf rootfs.tar
   sudo rm rootfs.tar

Make sure you are in `lfs/linux` directory, and the ARM GCC compiler is in the environment variable. If not, go to the `lfs` directory and export it:

.. code-block:: bash

   export CC=`pwd`/gcc-linaro-6.2.1-2016.11-i686_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-
   ${CC}gcc --version

Compile the kernel modules and install the modules:

.. code-block:: bash

   sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j4 modules
   sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=/media/$USER/rootfs/ modules_install
   sync

Unmount the microSD card:

.. code-block:: bash

   sudo umount /media/$USER/BOOT
   sudo umount /media/$USER/rfs

Plug the microSD card into the BeagleBone Black, press the S2 button, and power it up. Make sure you are using a serial cable adapter. Now, you will see Linux booting in the serial terminal. Check the Linux version by typing `uname -a`.
