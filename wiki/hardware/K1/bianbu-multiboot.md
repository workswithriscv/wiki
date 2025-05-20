# Bianbu Multi-boot setup

The U-Boot included with the Bianbu distribution has most of the components necessary to support a more flexible boot process using the [extlinux boot method](https://docs.u-boot.org/en/stable/develop/bootstd/extlinux.html). You will need to make some adjustments to the environment file, and be aware that updating Bianbu will frequently overwrite these changes so you may need to apply them from Bianbu again after any new "release" is installed. Also note that as of November 2024, every U-Boot release included in Bianbu has a broken USB driver. While these changes will allow you to see the boot menu on the HDMI display, you will not be able to successfully make a selection via a USB Keyboard. You can make a selection via the UART interface without issue. If you do not have the UART connected, you can also change the default from any operating system, but be aware that recovery is difficult. Best to test with a UART connection by trying a non-default option first. Then a reboot will bring you back into a working configuration by default.

## Create an extlinux configuration file
This step is critical to be done first. If there is no extlinux file, the changes made in the next section will render your device unusable and recovery is possible but annoying. Do this step first, and you should be fine.

First. you will need to look at the `/boot` directory. Some critical file locations change from one Bianbu release to another; specifically the DTB path. In early versions it is in `/boot/spacemit/k1-x_lpi3a.dtb` and in later versions is it located at `/boot/spacemit/6.1.15/k1-x_lpi3a.dtb`, and it's expected that later versions will change this still. Use `ls` to find the location for your system and adjust the configuration below as necessary.

1.	Create a new directory: `mkdir /boot/extlinux`
1.	Create a new file `/boot/extlinux/extlinux.conf` with the following contents (be sure to ensure the `fdtdir` and `fdt` paths are correct):

```
menu title Boot Options (Default in 5sec)

timeout 50
prompt 1

default bianbu

label bianbu
        kernel ../vmlinuz-6.1.15
        initrd ../initrd.img-6.1.15
        fdtdir ../spacemit/6.1.15
        fdt ../spacemit/k1-x_lpi3a.dtb
        append earlycon=sbi earlyprintk quiet splash plymouth.ignore-serial-consoles plymouth.prefer-fbcon console=ttyS0,115200 loglevel=8 clk_ignore_unused swiotlb=65536 rdinit=/init workqueue.default_affinity_scope=system root=/dev/mmcblk2p6 rootwait rootfstype=ext4
```

## Changes required to U-Boot Environment File

Add the following lines to the end of the bottom of `/boot/env_k1-x.txt`:

```
stdout=serial,vidconsole

fdt_addr_r=0x1F000000
fdtoverlay_addr_r=0x01000000
kernel_addr_r=0x10000000
kernel_comp_addr_r=0x18000000
pxefile_addr_r=0x0c200000
ramdisk_addr_r=0x20000000
autoboot=usb start; nvme scan; nvme part; sysboot mmc 2:5 ext2 0x20000000 /extlinux/extlinux.conf
```

At this point, you should be able to safely reboot and after the U-Boot Autoboot, it should then give you a 5 second countdown and boot into Bianbu as normal. If not, the _easiest_ path forward is to just re-flash the boot partition using `fastboot` as shown on the [BPI-F3](BPI-F3.html) page.

*NOTE:* This file is replaced when Bianbu does a release upgrade. You can simply replace these lines to return functionality - but also be sure to verify the `extlinux.conf` kernel name and FDT paths align before rebooting.

## Adding additional operating systems

Once you have Bianbu working, now you can add additional operating systems. This is the easiest way to install operating systems on the NMVe drive. It will still boot off the eMMC device, and I recommend mounting `/boot` from there, but then you can setup whatever root filesystem you want on the NVMe drive.

Examples of easy to deploy Operating Systems: [Arch Linux for RISC-V ](https://riscv.mirror.pkgbuild.com/), [Fedora-V Force Images](https://images.fedoravforce.com/), [Debian (or Ubuntu) Image Builder](https://github.com/pyavitz/debian-image-builder)

Here is an example of adding an Ubuntu root filesystem on the 2nd partition of the NVMe drive using the same kernel and device tree from Bianbu (the BSP):

```
label ubuntu
        kernel ../vmlinuz-6.1.15
        initrd ../initrd.img-6.1.15
        fdtdir ../spacemit/
        fdt ../spacemit/k1-x_lpi3a.dtb
        append earlyprintk earlycon=sbi console=tty1 console=ttyS0,115200 console=both rw root=/dev/nvme0n1p2 rootwait rootfstype=ext4 fsck.repair=yes loglevel=5 net.ifnames=0 init=/sbin/init
```

Once that is added, you will get two options during boot on both the HDMI console and the UART console. You can then try selecting the number for "ubuntu" (should be 2) and press enter over the UART Console. If that works, you are welcome to change the `default` line to use `ubuntu` as the default instead of `bianbu`.
