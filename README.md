
# OpenCore 6.4 Big Sur Installation Guide

![Big Sur Success](https://i.ibb.co/5vphKWX/Screenshot-2020-11-30-at-23-13-08.png)

## Hardware:  

- Motherboard: ROG Strix Z270G Gaming
- CPU: Intel i7 Kaby Lake 7700k
- GPU: Radeon RX Vega 56 8GB
- Memory: 32GB Vengeance DDR4
- SSD: Samsung 840 Series Pro 256GBq
- Wifi/Bluetooth: Fenvi FV-T919

## Youtube Guide
Follow this great video tutorial from [TECHNolli](https://www.youtube.com/c/TechNolli/featured). My guide is just an extension to this video, where I detail the things I did differently and the troubleshooting I made along the way.

[![How to install macOS Big Sur on a PC using OpenCore` ](https://i.ibb.co/TrpSDXq/youtube-video.jpg)](https://www.youtube.com/watch?v=jqg7MX3FS7M)

**Youtube Video: https://www.youtube.com/watch?v=jqg7MX3FS7M**
*[Mirror Video File](https://mega.nz/file/1aphhKTI#qBSRgmn9OSulPpmormAfOXECbZnAmQkHfSSAfP4z5jI) (just in case)* 

## Formatting SSD and USB drives

This is perhaps a little overkill, but I am never satisfied a USB or SSD I intend to install an OS onto is truly "clean" unless I'm able to format the drive and it's hidden EFI partition fully. Using a Windows 10 PC you can fully format all partitions in a way I've never been able to do with the Mac's DiskUtility app or Terminal commands.

How to Format EFI Partition in Windows 10+:

`cmd + R` to open "Run" window

Type in: `diskpart` to open a command line tool for managing partitions.  

Then type in the following commands, making sure the disk number is the one you're after.  

-  `list disk`
-  `sel disk 0`
-  `list partition`
-  `sel partition 1`
-  `delete partition override`

Opening "Disk Manager" will give you to option to format and visually see the partitions on all drives. 

Source: https://www.easeus.com/partition-master/delete-efi-system-partition.html

## Install Big Sur to USB Drive Command

This assumes you've named your USB stick "USB"  

`sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/USB -- /Applications/Install\ macOS\ Big\ Sur.app`  

## Tools

| Tool | Description | Link |
|--|--|--|
| **OC Gen-X** | Creates the initial EFI files for a great starting point | [Link](https://github.com/Pavo-IM/OC-Gen-X) |
| **GenSMBIOS** | Generates unique SMBIOS details giving your Mac a unique serials number | [Link](https://github.com/corpnewt/GenSMBIOS) |
| **ProperTree** | GUI for modifying the config.plist. It's what's referenced in the official OpenCore guides. Other apps like "OpenCore Configurator" shows promise, but didn't seemed entirely necessary really. | [Link](https://github.com/corpnewt/ProperTree) |
| **EFI Mounter** | Allows you to quickly open EFI partitions, but isn't as intuitive as "Clover Configurators" EFI Mounting feature which shows you the names of the drives. | [Link](https://www.tonymacx86.com/resources/efi-mounter-v3-1.447/) |
| **Clover Configurator** | Used solely for it's more intuitive EFI Mounter as an alternative to the above | [Link](https://mackie100projects.altervista.org/download-clover-configurator/) |  


## OpenCore Config

Use the Kaby Lake official install guide: https://dortania.github.io/OpenCore-Install-Guide/config.plist/kaby-lake.html

Only difference I made was adding `npci=0x3000` to the end of the **NVRAM → boot-args**

This resolved an issue with the graphics card; which would give me a black screen.

After saving the config.plist file for the last time and before booting into BIOS ([16:42](https://youtu.be/jqg7MX3FS7M?t=1001)), I'd recommend checking your config with this handy sanity checker:

https://opencore.slowgeek.com/

It highlights common mistakes or typos. My config file shows no errors or warning.

I have a two config files to choose from:

| Config File | |
|--|--|
| config.plist | Includes the `-v` [verbose argument](https://dortania.github.io/OpenCore-Install-Guide/config.plist/kaby-lake.html#add-4) for installation and debugging  |
| config-final.plist | Excludes the `-v` argument. Clean config and should be used once everything is setup succesfully. |

## Kexts

Obviously make sure all your kexts are up to date. 

### Required Kexts:

| Kext | Website |
|--|--|
| AppleALC | https://github.com/acidanthera/applealc/releases |
| IntelMausiEthernet | https://www.insanelymac.com/forum/files/file/396-intelmausiethernet/ |
| Lilu | https://github.com/acidanthera/lilu/releases/tag/1.4.9 |
| USBInjectAll | https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/ |
| VirtualSMC | https://github.com/acidanthera/VirtualSMC/releases/tag/1.1.8 |
| WhateverGreen | https://github.com/acidanthera/WhateverGreen/releases/tag/1.4.4 |  

Assuming the site is still live, all the latest versions of the above kexts can be found here: https://kext.me/

## Bios  

- Advanced Mode → USB Configuration → XHCI Hand-off : **Enabled**
- Advanced Mode → Boot → CMS (Compatibility Support Module) → Launch CMS : **Disabled**

Turning CSM off seems to be controversial. It may make no difference in the end, but mine is off and everything's ok so far.

## SMBIOS

The SMBIOS config properties in these config.plist files are blank. 
- Platforminfo → Generic → **SystemUUID, SystemProductName**,  and **SystemSerialNumber** 

Thats because this EFI guide is public and I don't want anyone just using my EFI files and creating a serial number conflict. So be sure to follow the last part of the video guide [(28:58)](https://youtu.be/jqg7MX3FS7M?t=1738) where he uses GenSMBIOS to create new Platform Information. Also check out the [OpenCore's post install guide](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#generate-a-new-serial) on how to get a unqiue serial. You can use a VPN like [TunnelBears](https://www.tunnelbear.com/) free version to help with that too.

Just a note, you will need to use `iMac18,3` when setting GenSMBIOS.

## Helpful Terminal Commands

**Allow short passwords:**
`pwpolicy -clearaccountpolicies`

**Hide folder/file:**
`chflags hidden [path/to/file.ext]`

## What Works?

**Everything works.** All iCloud and related services are working well. Bluetooth, Wifi, Graphics Card, Audio, Ethernet, etc... it all works. I haven't come accross anything that hasn't worked as expected. Admittiedly I haven't yet tested all USB ports and their respective read/write speeds to confrim USB 3 is active on all ports, but the ones I have used are good.   

## Problems & Solutions

### 1. Glitchy Screen during bootup
There were some instances where the screen would glitch and freeze just before booting into the desktop. It's hard to reproduce as it's only ever happened twice during my entire time working with OpenCore. Rebooting the PC resolved the issue, but I'm a little unsatisfied I don't have a proper explanation.

![Glitchy Screen](https://i.ibb.co/3hrFcD8/glichy-screen.jpg)

### 2. Device is write locked
In the early stages of experimenting and testing out OpenCore for the first time I experienced an error that would stall the installation. It said "*Disk4 : device is write locked*". Sometimes the disk number would be different. 

I found a forum thread that explained that using "Single User Mode" would help. I had to update the following values in the config.plist: 

-  **NVRAM → boot-args:**  `-v keepsyms=1 debug=0x100 alcid=1 npci=0x3000 -s`

-  **NVRAM → csr-active-config:**  `<FF0F0000>`  

`-s` and the `csr-active-config` puts the installation into "**single user mode**" 

When booting into the "Install macOS Big Sur (external)" with this single user mode switched on, you'll be asked to enter an option. Just type 'exit' and press Enter.

Source: https://www.olarila.com/topic/10793-macos-big-sur-11-beta-10-20a5395g/page/2/?tab=comments#comment-124857

After I managed to install Big Sur to the SSD, the second reboot would go into an infinite loop. Restarting the PC and making no progress. At this point I needed to remove the `-s` argument. In the OpenCore Boot Menu I also selected the "Reset NVRAM" option, which changed the BIOS's boot order and renamed the UEFI USB drive to "OpenCore" instead of the USB manufacturer (SanDisk) name. You may not need to do any of this if you don't come accross the "device is write locked" error. 

### 3. iCloud not uploading files
Immidiatly after logging into iCloud for the first time, everything seemed to work. One-by-one Messages started to work, FaceTime, Contacts, Calander, etc... However iClouds did not seem to want to upload any files. The little cloud icon would appear next to the filename but next upload. I tried:

 - Messing with the 'bird' process. [Source](https://apple.stackexchange.com/questions/264915/icloud-drive-is-stuck-uploading-items-and-no-longer-syncs)
- Signing out of all iCloud services across all devices.
- Reseting my SMBIOS with a "Purchase Date not Validated" serial number. [Source](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#generate-a-new-serial)
- Booting in SafeMode and "repairing" all harddrives. 

Eventually I logged back into iCloud on my genuine iMac at work and found the same problem was happening. iCloud files would not upload. Coincendally my work iMac is registered in my name and is as a 2017 model. The same "SystemProductName" as my Hackintosh. So I called up Apples' technical support only to go over everything I had already done. There was no solution.

iCloud is important to me, I use it all the time... but I had exhausted all my options and had to crack on with work. Then about one day later and for no reason at all, it just started working. Not had any problems since.  