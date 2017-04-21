# Linux Kernel For Pine 64 (A64, A64+, SoPine)

This scripting helps in configuring and compiling the Kernel for Pine64. To
compile, you need a properly set up gcc-aarch64-linux-gnu toolchain. The
recommended version to compile the Kernel is 5.3.

The scripting included was created by Pine 64 community member longsleep
with very minor changes/additions to.


## Compiling  Kernel 3.10.x  BSP With DRM & Mali

While mainlining is in the works you might want to try the Kernel which is
released in the BSP. I have included the DRM portions from AllWinner and
the Mali portions.

This tree is based on mainline 3.10.x with the changes rolled in from longsleep
and various Pine 64 community members.

```bash
git clone  https://github.com/lennyraposo/pine64.git pine64-build
cd pine64-build
cd linux-kernel
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- sun50iw1p1smp_linux_defconfig
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- LOCALVERSION= clean
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j4 LOCALVERSION= Image
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j4 LOCALVERSION= modules
cd modules/gpu
LICHEE_KDIR=$(pwd)/../.. ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- LICHEE_PLATFORM=Pine64 make build
```


## Compiling Busybox

```bash
cd ../../../busybox
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j4 oldconfig
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j4
```


## Install The New Kernel To SD Card

Now you can start installing your brand new kernel onto your Pine 64 SD card.

Remember to mount your SD card's Boot and Root FS so that you can install
the new kernel.

SD Card Mount Example (for reference only)

```bash
cd /tmp
mkdir BOOT
mkdir ROOTFS
mount /dev/mmcblk0p1 /tmp/BOOT
mount /dev/mmcblk0p2 /tmp/ROOTFS
```

WIth the above in mind adapt the following to your liking.

```bash
cd ../pine64-tools/kernel-install-tools
./step1-create-initrd.sh
./step2-kernel-install.sh  /tmp/BOOT ../../linux-kernel
./step3-kernel-headers-install.sh  /tmp/ROOTFS ../../linux-kernel
./step4-kernel-modules-install.sh  /tmp/ROOTFS ../../linux-kernel
sync
umount /tmp/BOOT
umount /tmp/ROOTFS
```


## Create A Tarball To Install Onto Your Pine 64 (Alternative Method)

This method can be used to update multiple Pine 64 boards via longsleep's
kernel update script provided you make a few modifications to it.

The below example creates the tarball into the tmp directory.


```bash
cd ../pine64-tools/kernel-install-tools
./step4-kernel-modules-install.sh  /tmp ../../linux-kernel
```
