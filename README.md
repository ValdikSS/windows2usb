# Windows2usb
**Burn Windows ISO to USB Flash Drive on Linux**

## What is this?

Windows2usb is a bash script which writes Microsoft Windows 7/8/8.1/10 installation DVD images to USB Flash Drive or external HDD on Linux. It was designed with compatibility in mind, and should work in all cases, contrary to other popular Linux tools.

Features:

* Fully automatic, no preparations required
* Supports BIOS and UEFI, FAT32 and NTFS
* Supports custom Windows ISOs with install.wim > 4GiB
* Uses stock Windows bootloaders where possible
* Does not break UEFI Secure Boot chain

## How to use?

Download latest [portable AppImage version](https://github.com/ValdikSS/windows2usb/releases) from the **Releases** page, set *execution bit* (`chmod +x windows2usb-*.AppImage`) and run it from the terminal.

`windows2usb <device> <windows iso> <mbr/gpt/gptntfs>`

The program prints removable storage list if no arguments supplied.

If you don't want to use AppImage, you'll need to install all dependencies and download [uefi-ntfs.img](https://github.com/pbatard/rufus/tree/master/res/uefi) from Rufus project.

### BIOS Boot

BIOS Boot (Legacy Boot/UEFI-CSM) uses stock Windows 7 MBR and NTFS bootloader, courtesy of [ms-sys](http://ms-sys.sourceforge.net/) project.

Use this mode if you have old computer without UEFI support or want maximum compatibility of installation media.

To burn ISO in this mode, run:

`windows2usb <device> <windows iso> mbr`

### UEFI Boot

UEFI Boot (`gpt` mode) creates FAT32 partition with stock Windows UEFI bootloader.

This mode will not work on old computers. Use this mode for new computers with UEFI support.

This mode supports UEFI Secure Boot.

To burn ISO in this mode, run:

`windows2usb <device> <windows iso> gpt`

### UEFI Boot with NTFS Partition

UEFI Boot with NTFS partition uses [uefi-ntfs](https://github.com/pbatard/uefi-ntfs) bootloader from Rufus project.

This mode is made for custom installation disks with install.wim file greater than 4 GiB, which could be found on various torrent trackers. **Windows2usb** creates 2 partitions in this mode, small 1 MiB FAT32 partition with **uefi-ntfs** and huge NTFS partition with ISO data.

This mode does not support Secure Boot (uefi-ntfs bootloader is not signed by Microsoft or other trusted party).

To burn ISO in this mode, run:

`windows2usb <device> <windows iso> gptntfs`

If your ISO contains install.wim greater than 4 GiB, `gpt` mode will automatically detect that and terminate the writing process.

## Alternatives

[WoeUSB](https://github.com/slacka/WoeUSB)—does not support NTFS partitioning and install.wim > 4GiB; uses GRUB for BIOS Boot.  
[bootiso](https://github.com/jsamr/bootiso/)—supports Windows and Linux ISOs.  
[Ventoy](https://www.ventoy.net/en/index.html)—Booting multi-tool with brilliant support of Windows ISO direct loading.

## Credits

This script uses:

* **lsblk** and **sfdisk** from [util-linux](https://mirrors.edge.kernel.org/pub/linux/utils/util-linux/)
* [**ms-sys**](http://ms-sys.sourceforge.net/) for native Windows 7 MBR and NTFS bootloaders
* [**p7zip**](https://www.7-zip.org/) for ISO extraction
* [**autofsync**](https://github.com/i-rinat/autofsync/) to prevent filesystem bufferbloat and properly show copying progress (AppImage only)
