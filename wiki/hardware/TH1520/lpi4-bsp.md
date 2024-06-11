# Updating the BSP on the eMMC of the Sipeed Lichee Pi 4A

The BSP (or Board Support Package) is the OS shipped by Sipeed for the Lichee Pi 4A

# Images

As of 2024-06-10, these are the latest images (and their SHA156 checksums):
6fe4d7a4ff75b51d5be78ea852836adc56b939e7946d2ab47efbc66f5df9e1ee  [boot-lpi4a-20231210_134926.ext4](https://objects.workswithriscv.guide/lpi4a-20231210/boot-lpi4a-20231210_134926.ext4)
1a860f9704523e1d02746643ce7e95f9e29e4e5e08ffe19ad51072e06ff734c7  [root-lpi4a-20231210_134926.ext4](https://objects.workswithriscv.guide/lpi4a-20231210/root-lpi4a-20231210_134926.ext4)
52f99ea3a5e68db81aa3cca0e30f82a1148d40873caac6d3e8c44553a4800eef  [u-boot-with-spl-lpi4a-16g.bin](https://objects.workswithriscv.guide/lpi4a-20231210/u-boot-with-spl-lpi4a-16g.bin)
9d510de0e2035553950d9aea2529a90c5e3aaa7a44ffab6ee800e823097295f9  [u-boot-with-spl-lpi4a.bin](https://objects.workswithriscv.guide/lpi4a-20231210/u-boot-with-spl-lpi4a.bin)

This is the tooling for getting the fastboot tool to upload the images:
c018507979a32eaa929739dbaf89d83f7690e56775fbd031d9094a0b43049c63  [burn_tools.zip](https://objects.workswithriscv.guide/lpi4a-20231210/burn_tools.zip)

## Entering fastboot mode

Flashing the eMMC requires putting the device in fastboot mode and then using the fastboot tool to load image(s) on to the eMMC device.

The documentation for the Lichee Module 4A says you can get the system to come up in fastboot mode by holding the BOOT button while applying power via the USB-C port which will have the device appear on the host. This did not work on any of the boards edolnx has, so here is an alternative:

Connect to the RX0 and TX0 of the board to a host/terminal. The serial settings are baud rate of 115200 bps, 8 data bits, no parity bits, and 1 stop bit (aka 115200n81). Then apply power via the USB-C port, and press any key to stop the "Autoboot" process of U-Boot. You should then get a U-Boot prompt, and run the following command:

`fastboot 0`

This will put the board in fastboot mode and it should enumerate as a fastboot device on the host of the USB-C cable. From there, you can run the fastboot commands.

## Uploading images

The images provided by Sipeed come as three parts: boot, root, u-boot. The burn_tools package includes the fastboot tool and a script to flash the three images. Modifying the script is the best bet, as the process used within it is unreliable and doesn't typically work. Here is a better solution:

Start by updating the U-Boot image on the board:
`sudo ./fastboot flash uboot ./images/u-boot-with-spl-lpi4a.img`
**Note**: If you are using the 16GB RAM variant of the board, you need to use the `u-boot-with-spl-lpi4a-16g.bin` file instead.

Next, run `sudo ./fastboot reboot` and then follow the original steps to get back into fastboot mode. This procedure only needs to be done if you are running the original U-Boot image. If you are running a newer image, the reboot step can be skipped. There are bugs in older versions that cannot load the larger images.

Now you can load the root and boot images:
`sudo ./fastboot flash boot ./images/boot-lpi4a-20231210_134926.ext4`
`sudo ./fastboot flash root ./images/root-lpi4a-20231210_134926.ext4`
The boot image is small and will happen rather quickly. The root image is much larger and will take considerable time. Once these are done you should power cycle the board.

## Fixing the autoboot of Sipeed images

The new images from Sipeed don't autoboot due to differences in the filenames in the boot partition. This can be fixed with a few steps that only need to be done after the fresh images are burned on to the eMMC. Once these chages are made, the board will autoboot every time.

Ensure you have a terminal connected to the RX0/TX0 lines. Once the board is powered on, the U-Boot will fail with a `Bad Linux RISCV Image magic!` error and drop to a U-Boot prompt. The following commands will get the image to boot manually from this point:

```
ext4load mmc 0:2 $dtb_addr dtbs/linux-image-5.10.113-lpi4a/thead/light-lpi4a.dtb
ext4load mmc 0:2 $kernel_addr vmlinux-5.10.113-lpi4a
booti $kernel_addr - $dtb_addr
```

After this, linux will start booting. But the next boot will fail. The easiest step at this point is to login to the image as the `debian` user from the serial console and run the following commands for everything except the 16GB boards:

```
cd /boot
sudo ln -s vmlinux-5.10.113-lpi4a Image
sudo cp dtbs/linux-image-5.10.113-lpi4a/thead/light-lpi4a.dtb .
sudo apt-mark hold linux-image-5.10.113-lpi4a
sudo apt-mark hold linux-headers-5.10.113-lpi4a
sudo apt-mark hold th1520-boot-firmware
sudo reboot
```

At this point, the system should restart and autoboot will function correctly. The hard coded U-Boot configuration is looking for a file called `Image` at the root directory of the boot partition. We are making a symlink from the current kernel to this file which resoves that issue. The hard-coded Device Tree path is also wrong, here we copy the DTB to the root of the boot partition because a symlink is not supported. Now the hard coded U-Boot load commands will function.

### Differences for the 16GB RAM variant

The process is the same as above for the 16GB boards, but the commands differ slightly. Here are the U-Boot commands you need:

```
ext4load mmc 0:2 $dtb_addr dtbs/linux-image-5.10.113-lpi4a/thead/light-lpi4a-16gb.dtb
ext4load mmc 0:2 $kernel_addr vmlinux-5.10.113-lpi4a
booti $kernel_addr - $dtb_addr
```

These are the linux commands to use:

```
cd /boot
sudo ln -s vmlinux-5.10.113-lpi4a Image
sudo cp dtbs/linux-image-5.10.113-lpi4a/thead/light-lpi4a-16gb.dtb .
sudo apt-mark hold linux-image-5.10.113-lpi4a
sudo apt-mark hold linux-headers-5.10.113-lpi4a
sudo apt-mark hold th1520-boot-firmware
sudo reboot
```
