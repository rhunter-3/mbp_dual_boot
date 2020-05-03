# How to Dual Boot Macbook Pro with Ubuntu 20.04 Focal Fossa

This guide walks through how to install Ubuntu 20.04 Focal Fossa alongside Mac OS x (Snow Leopard in my case) on a mid 2010 Macbook Pro (7,1).
The main points of the setup are as follows:

* The stock boot manager will be used instead of rEFIt/rEFInd.
* The boot manager will be customised with icons and labels for each OS.
* Ubuntu will be booted using EFI (not CSM/BIOS compatibility mode).
* Proprietary nVidia driver will be used (instead of the open source noveau driver which casues heat issues).

Some minor issues that crop up are also detailed with fixes.

## Starting Point

To start with, I found my old MacBook that had a bunch of rubbish on it and nothing of particular importance or value. 
My preference was to start with a fresh install of Mac OS X using the install disc that came with the Macbook 10 years ago. 
I think the newest macOS for my Macbook supported is High Sierra. After inserting the disc, I restart and then hold the option 
key after the chime to get to the boot menu. After a while a cd icon will appear and you can boot into the disc. Before selecting 
the partition to install on, I use Disk Utility (from Utilities in the toolbar) to erase and repartition the hard drive. I name the
new partition "Macintosh HD" (because that is what it was named when I first booted the machine 10 years ago and this project 
has me feeling nostalgia) even though I change it to "Snow Leopard" later in the process. After this you can close Disk Utility 
and select the new partion for the install. After the main part of the installation you can configure things like language, 
username, timezone etc. You can skip over all the detailed fields like (Full Name, setting up an AppleID etc. by clicking 
continue and then continue when asked are you sure). I just fill in the bare minimum. Eventually you will see the Welcome
video (that hit me right in the feels) and get to the desktop. This stage along with having a USB with the Ubuntu 20.04 iso 
on it is essentially the starting point for me with this project. 

## Macbook Preconfiguration

Since I've now got a fresh installation of OS X, the first thing I do is change my trackpad settings to have "tap to click" 
enabled and right-click as the bottom right area of the trackpad. Next, I got rid of some rubbish apps from the dock and 
added both Terminal and Disk Utility to the dock. I also downloaded Chrome since Safari was complaining about not being 
supported or something and throwing occasional errors. I found it was handy being able to see hidden files so I enabled this 
using terminal [].

After that you can hold the Option key and right-click Finder in the dock to relaunch Finder and hidden files should now be 
visible. 

Next task is to repartition the hard drive using Disk Utility. There will be ways to do this using terminal (don't ask me).
When in Disk Utility, highlight the hard disk drive (not the Macintosh HD partiton) and you will be able to select partition.
We're going to need to add two new partitions. Hit the "+" button twice. I resized my Macintosh HD partition to be 100GB so that 
Ubuntu installation will have the majority of space (I plan to use it more). The next partition only needs 1GB, can be renamed 
to "Ubuntu" and needs HFS+ filesystem same as Macintosh HD. We need this partition to house the .efi files for Ubuntu. This 
is where Ubuntu will be installed.

## Ubuntu Installation

Finally, what we came here for. Restart the Macbook and hold down the option key after the chime. Get your USB stick with the 
Ubuntu iso on it and plug that badboy in. Select and boot into "EFI Boot" when it appears. After loading click "Install 
Ubuntu" instead of "Try Ubuntu". One of the main installation screen asks you if you want to erase, install alongside mac osX 
or "Something Else". We want "Something Else". You should now be able to configure partitions for the Ubuntu install. The 
first consideration is how big the swap partition should be. I have 4GB of RAM so I need twice that (8GB or 8192MB) for the
swap partition. Why 2x? Because that's what the internet says. So with that in mind highlight the free space, click add, leave 
as EXT4 and start of space, set the mount point as root "/" and set the space to as much space that will leave roughly the 
amount that you need for the swap partition. After, click the free space, click use as swap area and I clicked at end of 
space (I'm not sure that it matters though). Now we can proceed to install.

I also selected install additional drivers, third-party software and formats.

After a while you'll be asked to set a username, machine name and password etc. 

A restart occurs before you can use it. When this happens, don't hold the option key, just let it run, it will boot into Ubuntu.
Login with your password and you will be greeted with a welcome window that will explain a few things, ask you if you want to 
send error information etc. keep clicking next until this closes.

Hooray!!! We now have Ubuntu installed.


