---
layout: post
title: "Arch Linux installation / January 2104"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Detailed installation of an Arch Linux installation with the following components:
	ZFS and Docker



### Create bootable USB stick 


	$ sudo dd if=/path_to_arch_.iso of=/dev/sdX 
	$ lsblk cgdisk /dev/sda


Boot Partition BIOS-GPT requires BIOS Boot Partition at the beginning of the disk. The Free Space is already selected and then


	Hit New -> Enter First Sector -> Enter Size in Sector -> 1007KiB -> Enter Hex Code of GUID (L to show codes, Enter = 8300) -> ef02 ->Enter Enter partition name – > Enter


You will notice a 1007.0 KiB BIOS boot partition has been created.

Create root Use keyboard to select the free space

    
    Hit New -> Enter First Sector -> Enter Now it will ask you how much space you want to allocate to that partition. In my case I will give root over 20GB Size in Sector -> 20GB -> Enter Hex Code of GUID (L to show codes, Enter = 8300) -> Enter Enter partition name – > Enter
    

Now you will see a 20GB partition has been created.

Creating Swap I have SSD on my desktop with 8GB of RAM and I really don’t need suspension or hibernation so I don’t bother to create swap. Depending on your need, you can create swap. Use keyboard and select Free Space


Hit New -> Enter First Sector -> Enter Now it will ask you how much space you want to allocate to that partition. I would give 2GB for swap (check what’s recommended) Size in Sector -> 2GB -> Enter Hex Code of GUID (L to show codes, Enter = 8300) -> Enter Enter partition name – > swap


Swap has been created.

Creating home Use keyboard and select Free Space


	Hit New -> Enter First Sector -> Enter Now it will ask you how much space you want to allocate to that partition.
	Here I am giving the remaining space to home. Size in Sector -> 50GB -> Enter Hex Code of GUID (L to show codes, Enter = 8300) -> Enter Enter partition name – > home -> Enter


If everything looks good select ‘Write‘, which will ask you to confirm if you want to write the changes. Type ‘yes‘ if you are sure. Once done select ‘Quit‘.

It’s now time to format these partitions and we are going to use ext4 file system. Run the following command for root and home (note choose the appropriate partition instead of sda2 and sda4).


	# mkfs.ext4 /dev/sda2 	



	# mkfs.ext4 /dev/sda4


Now let’s format SWAP


	# mkswap /dev/sda3 	



	# swapon /dev/sda3


[make sure to select the appropriate partition instead of sda3]

Installing the base system

If everything looks fine, it’s time to install Arch. First we need to mount root partition and then create home directory.


	# mount /dev/sda2 /mnt


Then create the home directory:


	# mkdir /mnt/home


Now mount home

	mount /dev/sda4 /mnt/home

Step #4 Choose mirror

Before we initiate the install process let’s select the closest mirror so that you get the best speed while downloading packages. To edit the mirror list run this command:


	# nano /etc/pacman.d/mirrorlist


Which will open the long list of mirrors. You can select the one closest to you. If you want to search the name of the location hit Ctrl+W and type the location you are looking for, once found go to the url of the mirror and hit Alt+6 to copy the line. Now use ‘Page Up‘ key to go on top and then hit Ctrl+U to paste that line on top. Hit Ctrl+x to exit and then type Y to save the changes you made.

Step #5  Install base packages

Now we are about to install base and base-devel packages (which will be needed later). Run this command:


	# pacstrap -i /mnt base base-devel


Step #6 Configure fstab

Once all these packages are installed you need to configure your fstab. Run:


	# genfstab -U -p /mnt >> /mnt/etc/fstab


(NOTE: Run the above command only once even if there are any issues. If there are problems, edit fstab manually, don’t re-run the command).

You must always check if fstab entry is correct or you won’t be able to boot into your system. To check fstab entry, run:


	# nano /mnt/etc/fstab


If everything is OK you should see root and home mounted.

Chroot into your newly installed system to configure it.


	# arch-chroot /mnt


Step #7 Language and location settings

We are going to configure the language of the new system. Since I am using English I am choosing “en_US.UTF-8“. You can choose the language that you use. To set the language, run the following command:


	# nano /etc/locale.gen


It will open a huge list of locales, go and un-comment the one you need. In my case I un-commented:

en_US.UTF-8 UTF-8

Now run


	# locale-gen 	
	# echo LANG=en_US.UTF-8 > /etc/locale.conf 	
	# export LANG=en_US.UTF-8


    Step #8 Time zone

It’s time now to configure the time zone for your system. If you don’t know the exact name of your sub-time zone (for example in my case it Zone is America and sub-zone is New_York), run following command to find the time zone.


	# ls /usr/share/zoneinfo/


Now you can configure the zone


	# ln -s /usr/share/zoneinfo/Zone/SubZone /etc/localtime


In my case it was


	# ln -s /usr/share/zoneinfo/America/New_York> /etc/localtime


Let’s now configure the hardware clock. It is recommended to use UTC instead of localtime.


	# hwclock --systohc --utc


Get a hostname

If you want a custom hostname for your system, run the following command and choose your desired name instead of muktware


	# echo muktware > /etc/hostname


Step #9 Configure repositories

Now it’s time to configure repositories. Open the pacman.conf file:


	# nano /etc/pacman.conf


If you are using 64 bit system you should go ahead and enable (un-comment) the “multilib” repo:

[multilib] 	Include = /etc/pacman.d/mirrorlist

Then hit Ctrl+X and then type ‘y‘ when asked.

Now it’s time to update the repositories by running this command:


	pacman -Sy


Step #10 Create users

We first need to give a root password so we can perform administrative tasks. But we will also create a user for the system as it’s not recommended to run as root.

First set root password. Run this command and give the password and give the desired password:


	# passwd


Now it’s time to create a user for the system and also add some groups to it. So run the following command and replace ‘muktware‘ with your user-name.


	# useradd -m -g users -G wheel,storage,power -s /bin/bash oskar


Then give the password for this new user (which in my case was oskar). When you run this command it will again ask you to enter new password:


	# passwd oskar


Now we have to allow this use to do administrative jobs as sudo so let’s install sudo.


	# pacman -S sudo 	
	# pacman -Ss sudo


Once that is done, we will now allow the users in wheel group to be able to performance administrative tasks with sudo. Run the following command to edit the sudoers:


	# EDITOR=nano visudo


It will open the sudoers file where you have to uncomment this line:


	%wheel ALL=(ALL) ALL


I will also recommend installing bash-completion so that Arch auto-complete commands of names of packages:


	# pacman -S bash-completion


Step #11 Install boot loader

We are now going to install grub and configure the boot loader. In my case I have a system with BIOS (if you have UFI then check out the appropriate Arch Wiki page).

Let’s first install grub for bios and configure it. Run these commands:


	# pacman -S grub 	



	# grub-install --target=i386-pc --recheck /dev/sda


I have additional operating system installed on the same system and I wanted Arch to show these systems in the grub menu so I can select at the boot. Even if you don’t have other OSes installed I would recommend installing OS Prober:


	pacman -S os-prober openssh


Once it is installed update the grub so Arch knows about other operating systems. Run this command:


	# grub-mkconfig -o /boot/grub/grub.cfg


In order for it to stay connected to the Internet after reboots, run this commands


	# systemctl enable dhcpcd.service 
    # systemctl start dhcpcd.service
    # systemctl enable sshd.service
    # systemctl 	


We are now done with the installation and configuration of Arch Linux. There is still some work left – installing the Display Manager (X server), the desktop environment and appropriate graphics drivers. Since the OS is installed let’s reboot into the new OS. So first exit from the chroot environment:


	# exit


And now unmount the root, home and reboot the system:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    # umount -R /mnt 	
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    # reboot
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

### ZFS installation

For fast and effortless installation and updates, the ["archzfs"][1] signed repository is available to add to your `pacman.conf`:

[1]: <http://demizerone.com/archzfs>


/etc/pacman.conf



[demz-repo-core]
SigLevel = Required
Server = http://demizerone.com/$repo/$arch


The repository and packages are signed with the maintainer's PGP key which is verifiable here: <http://demizerone.com>. This key is not trusted by any of the Arch Linux master keys, so it will need to be locally signed before use. See [pacman-key][1].

[1]: <https://wiki.archlinux.org/index.php/Pacman-key>

Add the maintainer's key,


	# pacman-key -r 0EE7A126


and locally sign to add it to the system's trust database,


	# pacman-key --lsign-key 0EE7A126


Once the key has been signed, it is now possible to update the package database,


	# pacman -Syyu


and install ZFS packages:


	# pacman -S archzfs




Docker Installation
-------------------------

[1]: <http://docs.docker.io/en/latest/installation/archlinux/#installation>

For the normal package a simple


	pacman -S docker


is all that is needed.

For the AUR package execute:


	yaourt -S docker-git


The instructions here assume **yaourt** is installed. See [Arch User Repository][2] for information on building and installing packages from the AUR if you have not done so before.

[2]: <https://wiki.archlinux.org/index.php/Arch_User_Repository#Installing_packages>

Starting Docker
-------------------

[^3]: <http://docs.docker.io/en/latest/installation/archlinux/#starting-docker>

There is a systemd service unit created for docker. To start the docker service:


	sudo systemctl start docker


To start on system boot:


	sudo systemctl enable docker


Network Configuration
-------------------------

<http://docs.docker.io/en/latest/installation/archlinux/#network-configuration>

IPv4 packet forwarding is disabled by default on Arch, so internet access from inside the container may not work.

To enable the forwarding, run as root on the host system:


sysctl net.ipv4.ip_forward=1


And, to make it persistent across reboots, enable it on the host’s **/etc/sysctl.d/docker.conf**:


net.ipv4.ip_forward=1
