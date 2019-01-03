# RPI3
## YOCTO Project on Raspberry PI 3
- gitclone -b pyro git://git.yoctoproject.org/poky.git
- mkdir sources
- cd sources
- git clone -b pyro git://git.openembedded.org/meta-openembedded
- git clone -b pyro https://github.com/agherzan/meta-raspberrypi.git
- cd ..

We source:
> source poky/oe-init-build-env rpi-estei-build
> We give access to our 'layers':

bitbake-layers add-layer ../sources/meta-raspberrypi  
bitbake-layers add-layer ../sources/meta-openembedded/meta-oe/
bitbake-layers add-layer ../sources/meta-openembedded/meta-python/
bitbake-layers add-layer ../sources/meta-openembedded/meta-networking.
in the ' conf/local.conf' file: -replace MACHINE? = "qemux86" by MACHINE? = "raspberrypi3" -Add package-management to the variable like this--->

EXTRA_IMAGE_FEATURES? = 'package-management debug-tweaks'
Then we start the generation of the image:

bitbake rpi-basic-image
The image that you should have on the raspberry terminal :

Loading cache: 100%|########################################################################################################################################################################|Time: 0:00:00
Loaded 2637 entries from dependency cache.
NOTE:Resolving any missing task queue dependencies
Build Configuration :

BB_VERSION       = "1.34.0"
BUILD_SYS         ="x86_64-linux"
NATIVELSBSTRING   = "ubuntu-16.04"
TARGET_SYS        ="arm-poky-linux-gnueabi"
MACHINE           ="raspberrypi3"
DISTRO            ="poky"
DISTRO_VERSION    = "2.3.2"
TUNE_FEATURES     = "arm armv7ve vfp thumb neon vfpv4callconvention-hard cortexa7"
TARGET_FPU        = "hard"
meta             
meta-poky         
meta-yocto-bsp    ="pyro:827eb5b232d54909377e2b18d39d34d6c1c21413"
meta-raspberrypi  ="pyro:ed3b254454494b36d4205818e369f59718704e60"
meta-oe           
meta-python       
meta-networking   ="pyro:dfbdd28d206a74bf264c2f7ee0f7b3e5af587796"
Once our image is created, it only remains to put on an SD card with the software win32diskimager :

https://sourceforge.NET/projects/win32diskimager/

Then we put our SD card in the Raspberry pi 3.

Later in the project, we preferred to used Raspbian to try and see the packages we need:

Raspberry IP configuration

Open the console from the Raspberry Pi
We begin by updating the Raspberry Pi
sudo apt update
sudo apt upgrade
Once the updates have been completed, we will create public and private folders that will be accessible on the NAS.

sudo mkdir/home/shares
sudo mkdir/home/shares/public
sudo chown-r/home/shares/public root:users
sudo chmod-r ug = rwx, o = rx/home/shares/public
Creation of the NAS with Samba

The basic configuration of the Raspberry pi being made, we will now be able to install the NAS. To do this, so we will use Samba, a software able to manage the networking of a hard drive to be able to access it from any computer connected to the network or operating system.

So, let's start by installing Samba on the Raspberry Pi using the following command:

sudo apt install samba samba-common-bin
After that, we will edit the configuration file.

sudo nano /etc/samba/smb.conf
If you want to limit connections to your NAS requesting authentication, go to the line

# Authentication #
and add the following line right below

security = use
To manage storage space private, meeting now in the [homes] section. If you want to be able to write (send files) on your NAS, make sure that the file contains the following line:

read only = no
Finally, at the bottom of the file, we will add public access to part of the Sin-related settings:

[public]
comment = Public Storage
path = / home/shares/public
valid users = @users
force group = users
create mask = 0660
Directory mask = 0771
read only = no
Close the file saving and then restart samba

sudo /etc/init.d/samba restart
Now, he must add a user to samba. In our case, we'll add the IP user.

sudo smbpasswd-a pi
Add a multimedia device

You can add a USB or a hard drive to have more storage. To simplify things if you want to connect multiple devices, it is preferable to connect/configure devices one by one.

First connect your device to your pi raspberry. The first thing to do after that is to detect what is the name the raspberry associated with him. To do this run the command

dmesg
Generally, the name of the media is sda1 but this may change especially if you have several devices connected.

Warning, your device must be formatted with ext3 file system. If this isn't the case, you can format it by using the following command (replacing sda1 with the name of your device if it is different).

mkfs.ext3/dev/sda1
Then create a directory in which the device will be mounted so that it is accessible via the NAS

sudo mkdir/home/shares/public/disk1
The name of the folder (in the example disk1) doen't matter, put the one you want to be able to find it easily on the network. Once that is done, get the device in this folder.

sudo mount/dev/sda1/home/shares/public/disk1
Mount the devices at startup of the pi Raspberry

At this stage of the installation, it actually remains a problem. When restarting the raspberry pi, the tapes do not rise is mandatory. To fix this, edit the fstab file.

sudo nano/etc/fstab
and for each device, add at the bottom of the file the following line (well, being careful not to any error on the device name and the directory in which it must be mounted)

/ dev/sda1/home/shares/public/disk1 auto noatime 0 0
Connect to the NAS Server

Our NAS is now set up and just to connect from his PC. From Windows, on the window "Ce PC" click on the computer tab then click Connect network drive.

We need to be able to connect to the public folder by entering \\raspberrypi\public and the directory with the user name (in our example pi) informing \\raspberrypi\pi.

Thus, we have the Raspberry IP configured NAS server and we can view the files contained on this Raspebrry from another PC which itself to connect to the same wifi network


