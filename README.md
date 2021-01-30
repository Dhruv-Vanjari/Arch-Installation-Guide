# Arch-Installation-Guide
This document contains a step-by-step guide to installing `Arch linux` and a little bit of set-up after install
The Disk will be partitioned to have a `swap` partition, a `root file system` partition and a `home` partition
It is necessary to know basic `vi` shortcuts to follow along with this guide as it is needed in one step during the installation

Please read the licence before continuing

Check whether network is working
--------------------------------
```
ping 8.8.8.8
```
If networking is not working then use usb tethering from phone or `iwctl`

Make sure system clock is accurate
----------------------------------
```
timedatectl set-ntp true
# verify using following command
timedatectl status
```

Partition the disk
------------------
```
fdisk /dev/sdX
```
* sdX1 for swap partition, you have to change partion type manually
* sdX2 for file system
* sdX3 for home partition

Make file System
----------------
```
mkswap /dev/sdX1           # make the swap file system
swapon /dev/sdX1           # turn on the swap partition

mkfs.ext4 /dev/sdX2
mkfs.ext4 /dev/sdX3
```

Mount sdX2 and install system
-----------------------------
```
mount /dev/sdX2 /mnt
pacstrap /mnt base base-devel linux linux-firmware
mount /dev/sdX3 /mnt/home
genfstab -U /mnt >> /mnt/etc/fstab
```

Set up the system
-----------------
```
arch-chroot /mnt   # change root directory to newly installed arch
```

Set the timezone info
```
ln -sf /usr/share/zoneinfo/REGION/CITY/ /etc/localtime
```

Set the hardware clock
```
hwclock --systohc
```

Edit `/etc/locale.gen` and uncomment the following line
```
en_US.UTF-8 UTF-8
```
Then run `locale-gen`

Give the computer a name 'the hostname'
```
echo 'hostname' > /etc/hostname
```

Create the hosts file
Add the following three lines in `/etc/hosts`
```
127.0.0.1        localhost
::1              localhost
127.0.1.1        hostname.localdomain        hostname
```
here the `hostname` is the hostname which is in `/etc/hostname` that you created earlier

Now give the root user a password with the following command
```
passwd
```

Type the following command to edit the sudoers file so members of wheel group have sudo privilages
```
visudo
```
uncomment `%wheel ALL=(ALL) ALL`

Create non root users
---------------------
```
useradd -m username

# give the user a password
passwd username

# add the user to some groups
usermod -aG wheel,audio,video,power,optical,storage username
```
Add user to the `wheel` group for giving him root provilages with `sudo` command

Install grub
------------
```
pacman -S grub
grub-install /dev/sdX
grub-mkconfig -o /boot/grub/grub.cfg
```
`X` in `sdX` has to be replaced by whatever partition you want to install grub on

Install few other programs
--------------------
* xorg -> display server
* xorg-xinit -> for `startx` command
* i3 -> Window manager
* dmenu -> run launcher
* picom -> compositor
* firefox -> web browser
* git
* networkmanager -> to setup wifi
* nm-connection-editor -> visual program
* alsa
* mesa-demos -> for checking opengl version (This is optional)
* xwallpapers -> for setting wallpapers
* pcmanfm -> filemanager
* vifm -> terminal based file manager
* nemo -> nicer file manager
* vim -> text editor
* alsa-utils -> for `alsamixer`

Command to install these programs is
```
pacman -S program-name
```
Use program names (these are actually called package names) listed above

Type the following command to automatically start `i3` by typing `startx`
```
echo exec=/usr/bin/i3 > /home/your-username/.xinitrc
```
Now you can type `startx` to start the graphical environment
> Install `xf86-video-intel` if startx fails

Alternative to i3 would be `lxde`, simply type the following commands to install lxde
```
pacman -S lxde
echo exec=/usr/bin/startlxde > /home/your-username/.xinitrc
```

Umount and reboot
------------------
```
umount /mnt/home
umount /mnt
reboot
```

After reboot
------------

* Enable networking
```
systemctl enable NetworkManager
```

* Start graphical environment and run `nm-connection-editor` to connect to wifi
