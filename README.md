# MNM_install_linux_on_windows

In this guide I write the Mint installation on Windows PC step by step.
The strategy used in this guide could not be the clever one, but it worded for me.

First, we have to install in two different USB keys  gparted-live and Linux-Mint operating system.
Gparted-live is needed in order to properly manage the disk space in the Windows PC.
When the disk is prepared, we use the USB with Linux Mint to perform the final installation.
Just to avoid  any confusion, I define the following objects:

* `PC0`: in order to manage all this operations is better to use Ubuntu-like distribution. I found some bugs using Debian but I don't understand why.
* `PC`: is the computer where we are going to install Linux-Mint side by side with Windows. The PC is composed by two different disks.
In the `/dev/sda` disk we are going to install the operating systems. In the `/dev/sdb` we are going to store only the data.
* `USBG`: is the usb-stick where we are going to install gparted-live.
* `USGM`: is the usb-stick where we are going to install Linux-Mint.

The starting step is to install gparted in our `PC0`.
```
sudo apt-get install gparted
```
Now mount the `USBG` or `USBM` in the your personal PC.
Once the gpbarted is installed type:
```
sudo gparted
```
Format the usb disks in this way:
* `USBG`as `FAT32`. Apply the change and remember to add the `boot` flag.
* `USBM`as `ETX4`. Apply the change and remember to add the `boot` flag.

When both `USBG` and `USBM` are properly formatted, we can start!

## Installing Gparted-live in usb-stick
In order to install gparted-live in the `USBG` we use `tuxboot` or `unetbootin` software.
Using the `PC0` we download the `tuxboot` sofware:
```
sudo apt-add-repository ppa:thomas.tsai/ubuntu-tuxboot
sudo apt-get update
sudo apt-get install tuxboot
```
Insert your `USBG` in you `PC0` and run `sudo tuxboot`.markdown insert image
Surfing on the gui of the `tuxboot` you can install the predefine `gparted-live stable` in the `USBG`.

In the end is possible to test the  boot of `USBG` with `qemu`. Before doing that, unplug and plug the `USBG`.
```
sudo apt-get install qemu
sudo qemu-system-i386 -m 512 -hda /dev/sdb
```
typing this command, a blue pop-up with grub options will appear. If this happend it works.

 `qemu-system-i386` is the architecture, in this case 32bit; `-m 512` is the dedicated RAM used for boot the `USBG`. For Better information type `man qemu`.


If this entire procedure does not work, you can follow alternative procedures in the official gparted usb installation guide at this link:
https://gparted.org/liveusb.php

## Installing Linux-Mint in usb-stick
In order to install Linux-Mint we are going to use a different software: `unetbootin`.
`unetbootin` and `tuxboot` are pretty much the same. We are going to use both in this guide for completeness reasons.


Using the `PC0` we download the sofware:
```
sudo add-apt-repository ppa:gezakovacs/ppa
sudo apt-get update
sudo apt-get install unetbootin
```
Then, download the Linux-Mint.iso at this link: https://linuxmint.com/download.php.
I choose Cinnamon 64bit and then I download the iso with the closer server

One the `linuxmint-version.iso` is downloaded, run `sudo unetbootin` and load the `linuxmint-version.iso` to the `USBM`.
Again, is possible to check the boot of `USBM` using `qemu`. Befor doing that, remember to plug and unplug the `USBM`.
Check the boot of `USBM` with the right architecture.

```
sudo qemu-system-i86_64 -m 512 -hda /dev/sdb
```

## Disks partitioning
Now the funny part. We are going to create new partitions on `PC`, where windows is installed.
In principle we can avoid this step simply installing the Linux-MInt side by side with windows using the default configuration. At this point we have two choices:
1. If you want follow the default procedure you will not manage the different mount point of `/`,`/boot`,and `/home`. If you don't care of this configuration you will simply insert the `USBM` in the `PC` and after changing the bios priority (put USB first). The Linux-Mint live will appear and you can finalize the installation.

2. If you want to handle directly with the disk partitioning of `PC` you will  have to manually prepare the disk space for the different mount point. gparted-live installed on `USBG` will help you.
After changing the bios' privileges and plug the `USBG` on `PC`, you can  start playing with gparted-live.

In this guide I'm going to following the step 2.


The default disk partitions of `PC` are the following:
* `/dev/sda`
![FIG1](/Fig/old_disk_sda.jpg)
* `/dev/sdb`
![FIG2](/Fig/old_disk_sdb.jpg)

if any warning appear as shown in the above figure, reboot the gparted-live.


Our idea is to keep the `/dev/sda` as a disk where we install all the operating system, while the `/dev/sdb` as a disk to store data.


#### /dev/sda

First we resize the `/dev/sda3` and we create an `extended partion` ( not primary). Remember that one disk can have only 4 primary partition. Adding the the `extended partition` can avoid this restriction.

As shown in figure above, the  extended partition `/dev/sda4` is  divide it into 4 different partition:
![FIG3](/Fig/new_disk_sda.jpg)

Inside the `/dev/sda4` we are going to install linux-mint:
* `/dev/sda5` is the boot `EXT4` partition where we are going to mount the `/boot` of linux-mint. Only 500MB of space is sufficient.
* `/dev/sda6` is the root `EXT4` partition where we are going to mount the `/`.
* `/dev/sda7` is the home `EXT4` partition where we are gonig to mount the `/home`. In principle this is not needed but if in the future you want to change you operating system without erase the `/home` data you  have to separate it from  the root partition `/`. I found this way to work useful. We can change you distro without affecting the `/home` data.
* `/dev/sda8` is the `linux-swap` partition. The ideal case is to put a swap dimension as big as the PC RAM.

#### /dev/sdb
This disk is let to be a storage disk. In order to make it visible both for linux and for windows is better to formatting as `NTFS` as shown in the figure.
![FIG4](/Fig/new_disk_sdb.jpg)

## Final Mint configurations
After partition the disks, reboot the system and plug the `USBM` in the `PC`.
Remember to  the linux-mint installation with a **manual configuration** of the disks!

Now we have to put the correct `mount point` of linux-mint using the partitions created before.
The final result that we want to obtain is shown in the following figure:
![FIG5](/Fig/mint_disk_config.jpg)
Install now and go on and in the end reboot the `PC` unplingging the `USBM`


If everything is ok, Grub will let you decide witch operating system you can play.


In this guide I write the Mint installation on Windows PC step by step.
The strategy used in this guide could not be the clever one, but it worded for me.
Just  to be clear... If you don't like it I 'll' not give a shit.

Have fun.
