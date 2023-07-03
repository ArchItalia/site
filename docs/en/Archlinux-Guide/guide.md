# Arch Linux Installation

Installation guides for Arch Linux: 

1.  [Signature Verification](#1-signature-verification)
2.  [Initial Configuration](#2-initial-configuration)
3.  [Internet Connection](#3-internet-connection)
4.  [Disk Preparation](#4-disk-preparation)
5.  [Mirrorlist](#5-mirrorlist)
6.  [Pacstrap](#6-pacstrap)
7.  [Generate Fstab](#7-generate-fstab)
8.  [Chroot](#8-chroot)
9.  [Bootloader](#9-bootloader)
10. [Base Packages](#10-base-packages)
11. [Graphical Environment](#11-graphical-environment)
12. [Services](#12-services)
13. [zram](#13-zram)

<br><br><br><br>

## 1. Signature Verification

It is recommended to verify the signature of the image before use, especially when downloading from an HTTP mirror, where downloads are subject to interception to provide harmful images.

On a system with *GnuPG* installed, download the [PGP ISO signature](https://archlinux.org/download/#checksums) to the ISO directory and verify it with:

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

Alternatively, from an existing installation of Arch Linux run:

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2. Initial Configuration

To begin, we need to define the keyboard language, the default language without entering the command is `US`. The available layouts can be listed with:

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

set the language of our keyboard with the command:

`# loadkeys it`

The console characters can be found in **/usr/share/kbd/consolefonts/** and can also be set with setfont. For example, to use one of the larger characters suitable for HiDPI displays, run:

`# setfont ter-132b`


<br><br><br><br>

## 3. Internet Connection
If you have connected the machine to the internet via cable or virtual machine, you can verify your acquired IP address using this command:

`# ip a`

The connection can be tested with a ping test command:

`# ping -c 3 archlinux.org`

Connect to the Wi-Fi network using the iwctl tool:

- `# iwctl` Start iwctl
- `# device list` Find the name of your device, example wlan0
- `# station wlan0 scan` Scan for available wireless networks
- `# station wlan0 get-networks` Get the list of networks
- `# station wlan0` connect yournetworkname  Connection to your network
- `# exit`

If in case our devices are disabled and we are unable to run ** iwctl **:

- `# rfkill list` Check the blocked or unblocked status of the devices
- `# rfkill unblock all` Unblock all our blocked devices
- `# systemctl restart iwd` Restart the iwd service

Retry `# iwctl` and proceed as above.


<br><br><br><br>

## 4. Disk Preparation

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm EXT4](#uefi-lvm-ext4)


<br><br><br><br>

### Bios-MBR

#### Partitioning
Identify your disk to know the naming convention to use. For example, in the case of an **SSD / dev / sda** or in the case of **M.2 /dev/nvme0n1** and, finally, the **virtual disk /dev/vda**.

`# lsblk -l`

Once the naming of our disk is identified, use **cfdisk**, here we will assume to have **/dev/sda**. You may be asked the type of partitioning table if the disk is raw. In this case, select **DOS**:

`# cfdisk /dev/sda`

Create the necessary partitions for the base installation, assuming that we have a **128GiB SSD**:

- `# 4Gib`   Create a partition for swap and select swap type
- `# 124Gib`  Create the Root partition
- `# write (yes)` and `quit`  Write the changes and exit


#### Formatting Partitions

- `# mkswap /dev/sda1` Swap partition
- `# mkfs.ext4 /dev/sda2` Root partition in EXT4

#### Mounting Partitions

- `# mount /dev/sda2 /mnt` Mount the root partition
- `# swapon /dev/sda1` Mount the swap partition


<br><br><br><br>

### UEFI ext4

#### Disk Partitioning
Identify your disk to know the naming convention to use. For example, in the case of an **SSD /dev/sda** or in the case of **M.2 /dev/nvme0n1** and, finally, the **Virtual Disk /dev/vda**.

`# lsblk -l`

Assuming that we have **128GiB SSD** and will use GPT partitioning for UEFI install:

`# cfdisk /dev/sda`

- `# 512Mib` Create an EFI partition and select EFI system partition type
- `# 4Gib`   Create a partition for swap and select swap type
- `# 23.5Gib`  Create the Root partition
- `# 100Gib`  Create the Home partition
- `# write (yes)` and `quit`  Write changes and exit


#### Formatting Partitions

- `# mkfs.vfat -F32 /dev/sda1` EFI system partition in FAT32 for boot
- `# mkswap /dev/sda2` Swap partition
- `# mkfs.ext4 /dev/sda3` Root partition in EXT4
- `# mkfs.ext4 /dev/sda4` Home partition in EXT4


#### Mounting Partitions

- `# mount /dev/sda3 /mnt` Mount root partition
- `# mkdir -p /mnt/{home,boot}` Create /home and /boot directories
- `# mount /dev/sda4 /mnt/home` Mount home partition
- `# mount /dev/sda1 /mnt/boot` Mount boot partition
- `# swapon /dev/sda2` Mount swap partition


<br><br><br><br>

### UEFI btrfs

#### Disk Partitioning
Identify your disk to know the naming convention to use. For example, in the case of an **SSD /dev/sda** or in the case of **M.2 /dev/nvme0n1** and, finally, the **Virtual Disk /dev/vda**.

`# lsblk -l`

Assuming that we have **128GiB SSD** and will use GPT partitioning for UEFI install:

`# cfdisk /dev/sda`

- `# 512Mib` Create an EFI partition and select EFI system partition type
- `# 27.5Gib`  Create the Root partition
- `# 100Gib`  Create the Home partition
- `# write (yes)` and `quit`  Write changes and exit


#### Formatting Partitions

- `# mkfs.vfat -F32 /dev/sda1` EFI system partition in FAT32 for boot
- `# mkfs.btrfs /dev/sda2` Root partition in BTRFS
- `# mkfs.btrfs /dev/sda3` Home partition in BTRFS


#### Mounting Partitions 

Create **@** and **@home** subvolumes:

- `# mount /dev/sda2 /mnt`

- `# btrfs su cr /mnt/@`

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`

- `# umount /mnt`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt`

- `# mkdir -p /mnt/{home,boot}` Create /home and /boot directories

- `# mount /dev/sda1 /mnt/boot`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### Disk Partitioning
Identify your disk to know the naming convention to use. For example, in the case of an **SSD /dev/sda** or in the case of **M.2 /dev/nvme0n1** and, finally, the **Virtual Disk /dev/vda**.


`# lsblk -l`

Assuming that we have **3 128GiB disks** for LVM: **sda sdb sdc** use **cfdisk** for one disk at a time:

`# cfdifk /dev/sda`

- `# 512Mib` Create an EFI partition and select EFI system partition type
- `# 127.5GiB` Create a partition and select LVM type
- `# write (yes)` and `quit`  Write changes and exit

`# cfdifk /dev/sdb`

- `# 128GiB` Create a partition and select LVM type
- `# write (yes)` and `quit`  Write changes and exit

`# cfdifk /dev/sdc`

- `# 128GiB`  Create a partition and select LVM type
- `# write (yes)` and `quit`  Write changes and exit

To create partitions under LVM, we need to first create a physical volume:

#### Create Physical Volume

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### Create Volume Group
Create and extend your volume group; you need to create a volume group on one or more physical volumes `# vgcreate volume_group physical_volume` for example:

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

This command will first set up the three partitions as physical volumes (if needed), and then create the volume group with the three volumes. The command will alert you if it detects an existing filesystem on any device.

#### Create Logical Volumes

Create logical volumes, for a basic configuration, we'd need one each for root, swap, and home.

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### Formatting Partitions

- `# mkfs.vfat -F32 /dev/sda1` EFI system partition in FAT32 for boot
- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 


#### Mounting Partitions 

- `# mount /dev/lvm/root /mnt`
- `# mkdir -p /mnt/{home,boot}` Create /home and /boot directories
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot` 
- `# swapon /dev/lvm/swap`

#### Extend an LVM group

If in the future you want to add a new physical volume to the group, see which command to use, assuming a fourth disk sdd and having partitioned it as before, we extend space for example to `/dev/lvm/home`:

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`

## 5. Mirrorlist

Save the mirrorlist for the repositories in **/etc/pacman.d/mirrorlist** using the tool **reflector**, specifying the country to synchronize the servers, for example **it**. Multiple countries can be added using a comma, for example **it,us**:

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6. Pacstrap

Install the **linux kernel** and base packages to create our Arch system, also add an editor such as **vim**. If following the installation for **lvm**, add the `lvm2` package to the following command:

`# pacstrap -K /mnt base base-devel linux linux-firmware vim` 

<br><br><br><br>



## 7. Generate Fstab

The /etc/fstab file allows you to control which filesystems are mounted on your Linux system during boot, including Windows partitions and network shares:

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8. Chroot
 
Enter the chroot and configure the following steps: Configuration of localtime, systemclock, language, keyboard mappings, localhost, Root Password, User Creation and Password.

Enter the chroot:

`# arch-chroot /mnt`

<br><br><br><br>


### Time zone

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>

### Localization

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=it" >> /etc/vconsole.conf`

<br><br><br><br>


### Hostname and Hosts

- `# echo "YOURMACHINENAME" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

<br><br><br><br>


### User and Root

Configure Root password, be careful!

`# passwd`

Configure a new lowercase user, creating the directory `/home/USERNAME` with `-m`, the group `wheel` with `-G`, and the shell with `-s`:

`# useradd -mG wheel -s /bin/bash USERNAME`

Configure the real name (which appears in graphics with uppercase initial letter for example **"Alessio"**) 

`# usermod -c 'REALNAME' USERNAME`

Configure a password for the newly added user, be careful!

`# passwd USERNAME`

Configure the sudoers file for the wheel group:

`# echo "USERNAME ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/USERNAME`


<br><br><br><br>


### mkinitcpio for lvm

add **lvm2** to hooks in **/etc/mkinitcpio.conf**

`HOOKS="base udev ... block lvm2 filesystems"`

then use the command:

`# mkinitcpio -p linux`


<br><br><br><br>


## 9. Bootloader

### GRUB (Bios-MBR)

- `# pacman -S grub`
- `# grub-install --target=i386-pc /dev/sda`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

<br><br><br><br>


### GRUB (UEFI)

- `# pacman -S grub`
- `# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

GRUB fully supports secure boot using CA keys or shim, however, the installation command is different depending on which one you intend to use.

To use CA keys, the command is:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`


To use shim-lock, the command is:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

<br><br><br><br>

### Systemd-boot (EXT4)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

Now create the configuration of the **arch.conf** file opened with **vim**, it is important to write the correct root boot partition such as `root=/dev/sdax` where `x` is the number of the root partition.

- `title   Arch Linux`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/sdax rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>


### Systemd-boot (BTRFS)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

Now create the configuration of the **arch.conf** file opened with **vim**, it is important to write the correct root boot partition such as `root=/dev/sdax` where `x` is the number of the root partition, add the flag for the **@** subvolume.

- `title   Arch Linux`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/sdax rootflags=subvol=@ rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

### Systemd-boot (LVM)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

Now create the configuration of the **arch.conf** file opened with **vim**, it is important to write the correct root boot partition such as for **lvm** `root=/dev/mapper/lvm-root`

- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

## 10. Base Packages

`# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`

<br><br><br><br>


## 11. Desktop Environment

Choose from some of the suggested popular desktop environments:

### Gnome
Complete Gnome with GDM display manager

- `# pacman -S gnome gnome-extra gdm` 
- `# systemctl enable gdm`

### Xfce4
xfce4 with Lightdm display manager

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

### Lxde
lxde with Lightdm display manager

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

### Mate
mate with Lightdm display manager

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

### Plasma
plasma kde with SDDM display manager

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

### Cinnamon
cinnamon with Lightdm display manager

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`


<br><br><br><br>


## 12. Services

If you have enabled the service for the display manager, you can move on to enabling the other necessary services.

- `# systemctl enable NetworkManager` Be careful, it is case sensitive.
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

<br><br><br><br>

## 13. Zram

The following example describes how to configure automatic swapping to *zram* at boot using a single *udev* rule. No additional packages should be needed to get this working.

Explicitly load the module at boot:

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

Create the following *udev* rule adjusting the disksize attribute as required for the swap size in this example it is *16G*:

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k", TAG+="systemd"`

Add **/dev/zram** to your fstab with a higher priority than the default:

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0 `

<br><br><br><br>







