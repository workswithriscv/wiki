# Banana Pi BPI-F3

This is a Spacemit K1 based board, and has all the major specs of any K1 based system. Both Ethernet ports are exposed and available as RJ45 connectors, as are two PCIexpress ports: a miniPCIe slot on the top for a SATA controller, and on the bottom of the board is an M.2 port for an NVMe drive supporting the 2230, 2245, 2260, and 2280 size variants. The mounting post is included, but the screw for the post is not.

The BPI-F3 does not have any OS on the eMMC by default. The ZBL (Zero-th stage Boot Loader) prefers the microSD (TF) card slot over the eMMC. So if you want, you can use an [Fedora-V Force Image](https://images.fedoravforce.com/Banana%20Pi%20BPI-F3) or the Official Board Support Packagg (BSP) of [Bianbu](http://archive.spacemit.com/image/k1/version/bianbu/) (you want the `.img.zip` variant) on the SD card to boot. This will be slow, but it's an excellent recovery method.

# Flashing Bianbu on the eMMC

You can flash Bianbu (the BSP or Board Support Package) onto the eMMC using the TitanFlash tool from SpacemIT. This tool is an electron app which works best on Linux or Windows 7/10 (Windows requires Secure Boot to be disabled as the drivers are not signed). When using the Linux version, it tends to work better if you go into the settings page and set the temporary directory to something within your home directory and you tend to have less errors.

You can also flash with the command line tools from android, specifically the `fastboot` tool to flash the image to the eMMC. You will need the `.zip` variant [image](http://archive.spacemit.com/image/k1/version/bianbu/) and _not_ the `.img.zip` variant image (that is for an SDcard). Once you have that file, you will need to extract it into a temporary direcrtory. In that same directory extract the [burn_tools.zip](https://objects.workswithriscv.guide/lpi4a-20231210/burn_tools.zip) and run `chmod 755 burn_tools/linux/fastboot`. Now you can connect your device via the USB-C port. 

If the device is new, it typically has no OS on it, won't display anything on the HDMI port and comes up in fastboot mode showing up as a `361c:1001` device in `lsusb -v`. If that is the case, you can skip to the next paragraph. If the board is coming up to an OS, you will need to reboot the board while holding down the "s" key via the serial console until you get a prompt (usually full of the letter "s"). Then enter the command `fastboot usb 0`. At that point you should get several messages and the device should appear in `lsusb` as listed above.

You can now burn the image segements using the following `fastboot` command sequence from your Linux host: (NOTE: always use the bootinfo_sd.bin even if you plan to boot off of eMMC)

```shell
sudo burn_tools/linux/fastboot flash gpt partition_universal.json

sudo burn_tools/linux/fastboot flash bootinfo factory/bootinfo_sd.bin

sudo burn_tools/linux/fastboot flash fsbl factory/FSBL.bin

sudo burn_tools/linux/fastboot flash env env.bin

sudo burn_tools/linux/fastboot flash opensbi fw_dynamic.itb

sudo burn_tools/linux/fastboot flash uboot u-boot.itb

sudo burn_tools/linux/fastboot flash bootfs bootfs.ext4

sudo burn_tools/linux/fastboot flash rootfs rootfs.ext4

sudo burn_tools/linux/fastboot reboot

```

At this point, the board should reboot into a fresh Bianbu install and start the new setup experience. If you want to login via the serial console, the default credentials are `root` with the password of `bianbu`. Note that the default language is Simplified Chinese until you change it in the new setup experience or via the `LANG` environment variable.