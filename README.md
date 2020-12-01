# OpenCore Guide

![Big Sur Success](https://i.ibb.co/5vphKWX/Screenshot-2020-11-30-at-23-13-08.png)

### Hardware:

 - Motherboard: ROG Strix Z270G Gaming 
 - CPU: Intel i7 Kaby Lake 7700k 
 - GPU: Radeon RX Vega 56 8GB 
 -  Memory: 32GB Vengeance DDR4  
 - SSD: Samsung 840 Series Pro 256GB 
 - Wifi/Bluetooth: Fenvi FV-T919

----------------

**Follow this video tutorial, the notes below detail where I did things slightly differently.** 
https://www.youtube.com/watch?v=jqg7MX3FS7M

----------------

### Tools:

| Tool | Description | Link |
|--|--|--|
| **OC Gen-X** | Creates the initial EFI files for a great starting point | [Link](https://github.com/Pavo-IM/OC-Gen-X) |
| **GenSMBIOS** | Generates unique SMBOIS details for serials numbers | [Link](https://github.com/corpnewt/GenSMBIOS) | 
| **ProperTree** | GUI for modifying the config.plist. It's what's referenced in the official OpenCore guides. Other apps like "OpenCore Configurator" shows promise, but didn't seemed entirely necessary really. | [Link](https://github.com/corpnewt/ProperTree) | 
| **EFI Mounter** | Allows you to quickly open EFI partitions, but isn't as intuitive as "Clover Configurators" EFI Mounting feature which shows you the names of the drives.  | [Link](https://www.tonymacx86.com/resources/efi-mounter-v3-1.447/) | 
| **Clover Configurator** | Used solely for it's more intuitive EFI Mounter as an alternative to the above | [Link](https://mackie100projects.altervista.org/download-clover-configurator/) | 


----------------

### Install Big Sur to USB Drive Command:

This assumes you've named your USB stick "USB"

`sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/USB -- /Applications/Install\ macOS\ Big\ Sur.app`

----------------

### OpenCore Config:

Use the Kaby Lake official install guide: 
https://dortania.github.io/OpenCore-Install-Guide/config.plist/kaby-lake.html

Only differences I made were:

- **NVRAM > boot-args:** `-v keepsyms=1 debug=0x100 alcid=1 npci=0x3000 -wegnoegpu -s`
- **NVRAM > csr-active-config:** `<FF0F0000>`

`npci=0x3000` and `-wegnoegpu` flags seemed to resolve freezing / black screens. Possibly relating to the GPU. After Big Sur was installed successfully, I was able to remove `-wegnoegpu`

`-s` and the `csr-active-config` puts the installation into "**single user mode**" this was needed to bypass an error that looked something "Disk4 : device is write locked".

When booting into the "Install macOS Big Sur (external)" with this single user mode switched on, you'll be asked to enter an option. Just type 'exit' and press Enter.
 
See here: https://www.olarila.com/topic/10793-macos-big-sur-11-beta-10-20a5395g/page/2/?tab=comments#comment-124857

**IMPORTANT: After I managed to install Big Sur to the SSD, the second reboot would go into an infinite loop. Restarting the PC and making no progress. At this point I needed to remove the `-s` argument. In the OpenCore Boot Menu I also selected the "Reset NVRAM" option, which changed the BIOS's boot order and renamed the UEFI USB drive to "OpenCore" instead of the USB manufacturer (SanDisk) name. You may not need to do any of this at all if you don't get the "device is write locked" error.** 

After saving the config.plist file for the last time, and before booting into BIOS (16:42), I'd recommend checking your config using this sanity checker:

https://opencore.slowgeek.com/

It highlights common mistakes or typos. My config file shows no errors or warning. 

I have a few config files to choose from:

| Config File |  |
|--|--|
| config.plist | Includes -v and -s "Single User Mode" |
| config-post-installation.plist | Includes -v |
| config-final.plist | Excludes -v |


----------------

### Kexts: 

Obviously make sure all your kexts are up to date. Especially "WhateverGreen.kext", older versions would result in a black screen.

#### Required Kexts: 

| Kext | Website  |
|--|--|
| AppleALC | https://github.com/acidanthera/applealc/releases |
| IntelMausiEthernet | https://www.insanelymac.com/forum/files/file/396-intelmausiethernet/ |
| Lilu | https://github.com/acidanthera/lilu/releases/tag/1.4.9 |
| USBInjectAll | https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/ |
| VirtualSMC | https://github.com/acidanthera/VirtualSMC/releases/tag/1.1.8 |
| WhateverGreen | https://github.com/acidanthera/WhateverGreen/releases/tag/1.4.4 |

Assuming this site is still live, all the latest versions of the above kexts can be found here: https://kext.me/ 

Previous Clover installs for Catalina needed: 

- FakePCIID_XHCIMux.kext
- FakePCIID.kext

The config.plist sanity checker suggests I no longer needed them. I think these may be part of one of the other Kexts now (Lilu maybe)

----------------

### Bios: 

- Advanced Mode > USB Configuration > XHCI Hand-off : Enabled
- Advanced Mode > Boot > CMS (Compatibility Support Module) > Launch CSM : Disabled

Turning CSM off seems to be controversial. It may make no difference in the end, but mine is off and everything's ok so far. 

----------------

### Formatting SSD and USB drives:

Perhaps a little overkill, but I am never satisfied a USB or SSD I intend to install an OS onto is truly "clean" unless I'm able to format the drive and it's hidden EFI partition. Using a Windows 10 PC You can fully format all partitions in a way I've never been able to do with Macs DiskUtility. 

How to Format EFI Partition in Windows:

`cmd + R` to open "Run" window

Type in: `diskpart` to open a command line tool for managing partitions.  

Then type in the following commands, making sure the disk number is the one you're after.

`list disk`
`sel disk 0`
`list partition`
`sel partition 1`
`SET ID=ebd0a0a2-b9e5-4433-87c0-68b6b72699c7`
`list partition`
`sel partition 1`
`delete partition override`

Opening "Disk Manager" will give you to option to format and visually see the partitions on all drives.  

https://www.easeus.com/partition-master/delete-efi-system-partition.html

----------------

### Extra Resources:

Prebuilt EFI's for Clover and OpenCore.
The Clover option worked for Catalina 10.15.7 without any amends, but OpenCore
Builds did not work. I'm sharing it anyway because they may be better in the future.

https://www.olarila.com/topic/5676-folders-for-all-chipsets-clover-and-opencore/

----------------

### Helpful Terminal Commands:

Allow short passwords:
`pwpolicy -clearaccountpolicies`

----------------

### To Do:

- Test everything actually works. Messages, FaceTime, etc
- Install and make a cool Boot Menu theme. 
