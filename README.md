# Windows2usb
**Burn Windows ISO to USB Flash Drive on Linux**

## What is this?

Windows2usb is a bash script which writes Microsoft Windows 7/8/8.1/10/11 installation DVD images to USB Flash Drive or external HDD on Linux. It was designed with compatibility in mind, and should work in all cases, contrary to other popular Linux tools.

Features:

* Fully automatic, no preparations required
* Supports BIOS and UEFI, FAT32 and NTFS
* Supports custom Windows ISOs with install.wim > 4GiB
* Uses stock Windows bootloaders where possible
* Does not break UEFI Secure Boot chain

```
Windows 7/8/8.1/10/11 ISO to Flash Drive burning utility
WARNING: this program will delete all existing data on your drive!

windows2usb <device> <windows iso> [mbr/gpt/gptntfs/gpt+uefintfs]

mbr mode: the most universal, RECOMMENDED and DEFAULT method.
   This mode creates MBR partition table with FAT32 partition,
   installs BIOS and UEFI bootloaders, supports Secure Boot.
   install.wim file larger than 4 GiB will be split.
   Suitable for all computers (UEFI/CSM/BIOS).

gpt mode: less universal mode, for modern (UEFI) computers.
   GPT+FAT32, UEFI only, supports Secure Boot.

gptntfs mode: all the same as 'gpt' but NTFS is used.
   GPT+NTFS, UEFI only, supports Secure Boot.
   Large install.wim file will not be split.
   NOTE: not all UEFI are compatible with this mode,
   NTFS driver should be present on the motherboard.

gpt+uefintfs mode: alternative hacky installation method, not recommended.
   This mode uses NTFS partition and third-party 'uefintfs' bootloader.
   GPT+NTFS(data)+FAT32(efi), UEFI only, NO Secure Boot support.
   Large install.wim file will not be split.
```

## How to use?

Download latest [portable AppImage version](https://github.com/ValdikSS/windows2usb/releases) from the **Releases** page, set *execution bit* (`chmod +x windows2usb-*.AppImage`) and run it from the terminal.

`windows2usb <device> <windows iso>`

For example:

`./windows2usb.AppImage /dev/sdz /home/valdikss/windows10.iso`

The program prints removable storage list if no arguments are supplied.

If you don't want to use AppImage, you'll need to install all dependencies and download [uefi-ntfs.img](https://github.com/pbatard/rufus/tree/master/res/uefi) from Rufus project.

### Feature Matrix

|Modes       |Legacy/UEFI-CSM Boot|UEFI Boot|Large ISO (>4GB)|Secure Boot|File System|Part Table|
|:----------:|:------------------:|:-------:|:--------------:|:---------:|:---------:|:--------:|
|mbr(hybrid) |Supported           |Supported|Supported       |Supported  |FAT32      |MBR       |
|gpt         |No                  |Supported|Supported       |Supported  |FAT32      |GPT       |
|gptntfs     |No                  |Partial  |Supported       |Supported  |NTFS       |GPT       |
|gpt+uefintfs|No                  |Supported|Supported       |No         |NTFS       |GPT       |

### BIOS Boot

BIOS Boot (Legacy Boot/UEFI-CSM) uses stock Windows 7 MBR and FAT32 bootloader, courtesy of [ms-sys](http://ms-sys.sourceforge.net/) project.

**BIOS boot is supported only in recommended 'mbr' mode.**

To burn ISO compatible with BIOS boot (and UEFI boot as well), run:

`windows2usb <device> <windows iso> mbr`

### UEFI Boot

**UEFI Boot is supported in all modes. Recommended mode: 'mbr'.**

To burn ISO in this mode, run:

`windows2usb <device> <windows iso> mbr`

'MBR', 'GPT' and 'GPTNTFS' modes utilize Windows UEFI bootloader from the ISO file, with Secure Boot support.

### UEFI Boot with NTFS Partition and uefi-ntfs bootloader

A special 'gpt+uefintfs' mode uses [uefi-ntfs](https://github.com/pbatard/uefi-ntfs) bootloader from Rufus project. **Windows2usb** creates 2 partitions in this mode, small 1 MiB FAT32 partition with **uefi-ntfs** and huge NTFS partition with ISO data.

This mode was included earlier to work with installation disks where install.wim file is greater than 4 GiB, but since then split WIM file functionality was introduced, and now this mode may be considered obsolete.

This mode does not support Secure Boot (uefi-ntfs bootloader is not signed by Microsoft or other trusted party).

## Alternatives

[WoeUSB](https://github.com/WoeUSB/WoeUSB)—uses GRUB for BIOS Boot.  
[bootiso](https://github.com/jsamr/bootiso/)—supports Windows and Linux ISOs.  
[Ventoy](https://www.ventoy.net/en/index.html)—Booting multi-tool with brilliant support of Windows ISO direct loading.

## Credits

This script uses:

* **lsblk** and **sfdisk** from [util-linux](https://mirrors.edge.kernel.org/pub/linux/utils/util-linux/)
* [**ms-sys**](http://ms-sys.sourceforge.net/) for native Windows 7 MBR and NTFS bootloaders
* [**p7zip**](https://www.7-zip.org/) for ISO extraction
* [**autofsync**](https://github.com/i-rinat/autofsync/) to prevent filesystem bufferbloat and properly show copying progress (AppImage only)
* [**wimlib**](https://wimlib.net/) to split large install.wim files to fit FAT32 partition
