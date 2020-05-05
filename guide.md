# How to Dual Boot Macbook Pro with Ubuntu 20.04 Focal Fossa

This guide walks through how to install Ubuntu 20.04 Focal Fossa alongside Mac OS X (Snow Leopard in my case) on a mid 2010 Macbook Pro (7,1).
The main points of the setup are as follows:

* The stock boot manager will be used instead of rEFIt/rEFInd.
* The boot manager will be customised with icons and labels for each OS.
* Ubuntu will be booted using EFI (not CSM/BIOS compatibility mode).
* Proprietary nVidia driver will be used (instead of the open source nouveau driver which casues heat and battery issues).

Some minor issues that crop up are also detailed with fixes.

## Starting Point

To start with, I found my old MacBook that had a bunch of rubbish on it and nothing of particular importance or value. 
My preference was to start with a fresh install of Mac OS X using the install disc that came with the Macbook 10 years ago. 
I think the newest macOS for my Macbook supported is High Sierra. After inserting the disc, I restart and then hold the
option key after the chime to get to the boot menu. After a while a cd icon will appear and you can boot into the disc. 
Before selecting the partition to install on, I use Disk Utility (from Utilities in the toolbar) to erase and repartition the 
hard drive. I name the new partition "Macintosh HD" (because that is what it was named when I first booted the machine 10 
years ago and this project has me feeling nostalgia) even though I change it to "Snow Leopard" later in the process. After 
this you can close Disk Utility and select the new partion for the install. After the main part of the installation you can 
configure things like language, username, timezone etc. You can skip over all the detailed fields like (Full Name, setting up 
an AppleID etc. by clicking continue and then continue when asked are you sure). I just fill in the bare minimum. Eventually 
you will see the Welcomevideo (that hit me right in the feels) and get to the desktop. This stage along with having a USB 
with the Ubuntu 20.04 iso on it is essentially the starting point for me with this project. 

## Macbook Preconfiguration

Since I've now got a fresh installation of OS X, the first thing I do is change trackpad settings to have "tap to click" 
enabled and right-click as the bottom right area of the trackpad. Next, I got rid of some rubbish apps from the dock and 
added both Terminal and Disk Utility to the dock. I also downloaded Chrome since Safari was complaining about not being 
supported or something and throwing occasional errors. I found it was handy being able to see hidden files so I enabled this 
using terminal defaults write com.apple.finder AppleShowAllFiles YES.

After that you can hold the Option key and right-click Finder in the dock to relaunch Finder and hidden files should now be 
visible. 

Next task is to repartition the hard drive. Open Disk Utility, highlight the hard disk drive (not the Macintosh HD partiton) 
and you will be able to select the partition tab. Need to add two new partitions. Hit the "+" button twice. I resized my Macintosh HD 
partition to be 100GB so that Ubuntu installation will have the majority of space (I plan to use it more). The next partition 
only needs 1GB (proabably a few hundred MB but 1GB to be safe), can be renamed to "Ubuntu" and needs HFS+ filesystem same as 
Macintosh HD. This partition is needed to house the .efi files for Ubuntu. The last partition can be formatted as free space. This will be where Ubuntu is installed.

## Ubuntu Installation

Finally, what we came here for. Restart the Macbook and hold down the option key after the chime. Get your USB stick with the 
Ubuntu iso on it and plug that badboy in. Select and boot into "EFI Boot" when it appears. After loading click "Install 
Ubuntu" instead of "Try Ubuntu". One of the main installation screens asks you if you want to erase, install alongside mac 
or "Something Else". Choose "Something Else". You should now be able to configure partitions for the Ubuntu install. The 
first consideration is how big the swap partition should be. I have 4GB of RAM so I need twice that (8GB or 8192MB) for the
swap partition. Why 2x? Because that's what the internet says. So with that in mind highlight the free space, click add, 
leave as EXT4 and start of space, set the mount point as root "/" and set the space to as much space that will leave roughly 
the amount that you need for the swap partition leftover. After, click the leftover free space, click use as swap area and I
clicked at end of space (I'm not sure that it matters though). Now proceed to install.

I also selected install additional drivers, third-party software and formats.

After a while you'll be asked to set a username, machine name and password etc. 

A restart occurs before you can use it. When this happens, don't hold the option key, just let it run, it will boot into Ubuntu.
Login with your password and you will be greeted with a welcome window that will explain a few things, ask you if you want to 
send error information etc. keep clicking next until this closes.

Hooray!!! Ubuntu is installed.

## Ubuntu Configuration

Now that we have Ubuntu, lets get Ubuntu with WiFi drivers that work. For this you're going to need to plug the Macbook into 
the router with an ethernet cable or potentially use a USB dongle. Once you're on the interwebs:

open up a terminal with Ctrl+Alt+T
sudo apt-get update
sudu apt-get install firmware-b43-installer
sudo apt-get install bcmwl-kernel-source

After this disconnect the ethernet cable and then connect using WiFi.

It's going to be handy to have a couple of other command line tools. 

sudo apt-get install lshw inxi

To confirm that current graphics device and driver:

inxi -G 

To be able to right-click:

sudo apt-get install gnome-tweaks

Open up tweaks (from Applications) to set mouse click emulation to area.

Next, for some reason unbeknownst to me, the graphical grub menu doesn't display (wasn't an issue when trying 18.04) so I modified the /etc/default/grub file to get a text based menu:

sudo nano /etc/default/grub

Change the GRUB_TIMEOUT_STYLE=menu and uncomment the line GRUB_TERMINAL=console. Ctrl+X and Y to save and close.

To apply changes:

sudo update-grub.

Now we need to follow the method outlined here to get our setup using the proprietary nvidia driver. For those following along but using another distribution of Linux you may need some slight alterations to allow for the fact you won't have the setpci module loaded. There's a couple forum posts on the interwebs that explain this more.

sudo lshw -businfo -class bridge -class display

sudo nano /etc/grub.d/01_enable_vga.conf

cat << EOF
setpci -s "00:17.0" 3e.b=8
setpci -s "04:00.0" 04.b=7
EOF

Ctrl+X, Y to save and close.

sudo chmod 755 /etc/grub.d/01_enable_vga.conf
sudo update-grub

At this point before restarting, a sudo apt-get upgrade wouldn't go astray.

Restart. The text based grub menu should appear. Just click enter for the first option.

Once we're back, get a terminal and check if the pci registers changed.

sudo setpci -s "00:17.0" 3e.b
sudo setpci -s "04:00.0" 04.b

I actually got 0a instead of 08 for the first command contrary to the guide but another user commented (on a spearate post I lost the link for) that they got the same result and still had success after installing the nvidia driver.

Open up "Software & Updates" from Applications and go to the Additional Drivers tab. In the graphics section select the nvidia driver instead of nouveau and click apply changes. Once installed, we can restart and hopefully you will see a nVidia splash screen before Ubuntu boots. Check the output of inxi -G again in terminal. The nvidia driver should be in use. If thats the case your function keys for screen birghtness probably don't work. To fix this follow the steps from this forum post.

sudo nano /usr/share/X11/xorg.conf.d/10-nvidia-brightness.conf

Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Quadro K1000M"
    Option         "RegistryDwords" "EnableBrightnessControl=1"
EndSection

Ctrl+X, Y

You'll need to restart to see the effect of this chage.

## Macbook Boot Menu Configuration

Now to customise the boot menu. Every reboot now will need the Option key held after the chime so that we can boot into mac os.
First create a directory to mount the EFI partition which is not auto-mounted.
sudo mkdir ~/Desktop/efi

Next mount the efi parition.
sudo mount -t msdos /dev/disk0s1 ~/Desktop/efi/

Now open the efi folder from the desktop. Inside this should be a folder called EFI. In here there should be APPLE, BOOT and ubuntu folders. Drag the BOOT and ubuntu folders out of the folder and onto the desktop, make sure they are deleted in the EFI folder.

The icon used for ubuntu came from here https://sourceforge.net/projects/mac-icns/. I had to download the older release (https://sourceforge.net/projects/mac-icns/files/PreviousVersions/3-1-2016/mac-icns.dmg/download) as the newer version wouldn't work for some reason. I then had the os_ubuntu.icns in my Downloads folder for 9th step below.

I got the snow leopard icon from here https://findicons.com/icon/131415/snow_leopard, also in my downloads folder.

Now with a finder window you should be able to click into the Ubuntu partition from the left hand pane. 

1. Open a finder window.
2. Click into the Ubuntu partition from the left hand pane.
1. Create an EFI folder.
2. Copy the BOOT and ubuntu folders from the desktop into the EFI folder.
3. sudo mkdir -p /Volumes/Ubuntu/System/Library/CoreServices
4. echo "This file is required for booting" > /Volumes/Ubuntu/mach_kernel
5. sudo cp /Volumes/Snow\ Leopard/System/Library/CoreServices/SystemVersion.plist /Volumes/Ubuntu/System/Library/CoreServices/SystemVersion.plist
6. sudo nano /Volumes/Ubuntu/System/Library/CoreServices/SystemVersion.plist 
sudo mv /Volumes/Ubuntu/System/Library
7. sudo mv /Volumes/Ubuntu/System/Library/CoreServices/grubx64.efi /Volumes/Ubuntu/System/Library/CoreServices/boot.efi
8. sudo bless --folder /Volumes/Ubuntu/ --file /Volumes/Ubuntu/System/Library/CoreServices/boot.efi --label Ubuntu
9. sudo mv ~/Downloads/os_ubuntu.icns /Volumes/Ubuntu/.VolumeIcon.icns
10. Right-click Macintosh HD in Finder, click get info and rename it to Snow Leopard
10. sudo mv ~/Downloads/snow_leopard.icns /Volumes/Ubuntu/.VolumeIcon.icns
11. sudo reboot

Hold the Option key so that you can reboot into os x, you should hopefully see your new icons and volume labels.

Links that helped me get this setup working:
https://askubuntu.com/questions/264247/proprietary-nvidia-drivers-with-efi-on-mac-to-prevent-overheating
https://help.ubuntu.com/community/UEFIBooting
https://www.happyassassin.net/posts/2014/01/25/uefi-boot-how-does-that-actually-work-then/
https://ubuntuforums.org/archive/index.php/t-2220101.html
https://glandium.org/blog/?p=2830
https://wiki.debian.org/InstallingDebianOn/Apple/MacBookPro/7-1
https://www.linux.com/training-tutorials/how-rescue-non-booting-grub-2-linux/
