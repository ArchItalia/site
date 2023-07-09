# Installation d'Arch Linux


## 1. Vérification de signature

Il est recommandé de vérifier la signature de l'image avant de l'utiliser, surtout si vous la téléchargez depuis un miroir HTTP, où les téléchargements sont sujets à interception pour fournir des images malveillantes.

Sur un système avec *GnuPG* installé, téléchargez la [signature PGP de l'ISO](https://archlinux.org/download/#checksums) dans le répertoire ISO et vérifiez-la avec:

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

Alternativement, à partir d'une installation existante d'Arch Linux, exécutez:

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2. Configuration initiale

Pour commencer, nous devons définir la langue du clavier, la langue par défaut sans entrer la commande est `US`. Les dispositions disponibles peuvent être répertoriées avec:

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

définir la langue de notre clavier avec la commande:

`# loadkeys it`

Les caractères de la console se trouvent dans **/usr/share/kbd/consolefonts/** et peuvent également être définis avec setfont. Par exemple, pour utiliser l'un des plus grands caractères adaptés aux affichages HiDPI, exécutez:

`# setfont ter-132b`


<br><br><br><br>

## 3. Connexion Internet

Si vous avez connecté la machine à Internet via un câble ou une machine virtuelle, vous pouvez vérifier votre adresse IP acquise à l'aide de cette commande:

`# ip a`

La connexion peut être testée avec une commande de test ping:

`# ping -c 3 archlinux.org`

Connectez-vous au réseau Wi-Fi en utilisant l'outil iwctl:

- `# iwctl` Démarrer iwctl
- `# device list` Trouver le nom de votre appareil, par exemple wlan0
- `# station wlan0 scan` Rechercher les réseaux sans fil disponibles
- `# station wlan0 get-networks` Obtenez la liste des réseaux
- `# station wlan0` connect yournetworkname  Connection à votre réseau
- `# exit`

Si dans le cas où nos appareils sont désactivés et que nous ne pouvons pas exécuter ** iwctl **:

- `# rfkill list` Vérification du statut bloqué ou débloqué des appareils
- `# rfkill unblock all` Débloquez tous nos appareils bloqués
- `# systemctl restart iwd` Redémarrez le service iwd

Réessayez `# iwctl` et procédez comme ci-dessus.


<br><br><br><br>

## 4. Préparation de disque

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm EXT4](#uefi-lvm-ext4)


<br><br><br><br>

### Bios-MBR

#### Partitionnement
Identifiez votre disque pour connaître la convention de nommage à utiliser. Par exemple, dans le cas d'un **SSD / dev / sda** ou dans le cas d'un **M.2 /dev/nvme0n1** et, enfin, le **disque virtuel /dev/vda**.

`# lsblk -l`

Une fois le nom de notre disque identifié, utilisez **cfdisk**, ici nous supposerons avoir **/dev/sda**. On peut vous demander le type de table de partitionnement si le disque est brut. Dans ce cas, sélectionnez **DOS** :

`# cfdisk /dev/sda`

Créez les partitions nécessaires pour l'installation de base, en supposant que nous avons un **SSD 128 GiB** :

- `# 4 GiB` Créez une partition pour le swap et sélectionnez le type de partition swap
- `# 124 GiB` Créez la partition Root
- `# write (yes)` et `quit` Écrire les modifications et quitter


#### Formatage des partitions

- `# mkswap /dev/sda1` Partition swap
- `# mkfs.ext4 /dev/sda2` Partition Root en EXT4

#### Montage des partitions

- `# mount /dev/sda2 /mnt` Monter la partition root
- `# swapon /dev/sda1` Monter la partition swap


<br><br><br><br>

### UEFI ext4

#### Partitionnement de disque
Identifiez votre disque pour connaître la convention de nommage à utiliser. Par exemple, dans le cas d'un **SSD /dev/sda** ou dans le cas d'un **M.2 /dev/nvme0n1** et, enfin, le **Disque virtuel /dev/vda**.

`# lsblk -l`

En supposant que nous avons un **SSD 128 GiB** et que nous utiliserons un partitionnement GPT pour l'installation UEFI :

`# cfdisk /dev/sda`

- `# 512 Mib` Créez une partition EFI et sélectionnez le type de partition système EFI
- `# 4 GiB` Créez une partition pour le swap et sélectionnez le type de partition swap
- `# 23,5 GiB`  Créez la partition Root
- `# 100 GiB`  Créez la partition Home
- `# write (yes)` et `quit` Écrire les modifications et quitter


#### Formatage des partitions

- `# mkfs.vfat -F32 /dev/sda1` Partition système EFI en FAT32 pour le boot
- `# mkswap /dev/sda2` Partition swap
- `# mkfs.ext4 /dev/sda3` Partition root en EXT4
- `# mkfs.ext4 /dev/sda4` Partition home en EXT4


#### Montage des partitions

- `# mount /dev/sda3 /mnt` Monter la partition root
- `# mkdir -p /mnt/{home,boot}` Créer des répertoires /home et /boot
- `# mount /dev/sda4 /mnt/home` Monter la partition home
- `# mount /dev/sda1 /mnt/boot` Monter la partition boot
- `# swapon /dev/sda2` Monter la partition swap


<br><br><br><br>

### UEFI btrfs

#### Partitionnement de disque
Identifiez votre disque pour connaître la convention de nommage à utiliser. Par exemple, dans le cas d'un **SSD /dev/sda** ou dans le cas d'un **M.2 /dev/nvme0n1** et, enfin, le **Disque virtuel /dev/vda**.

`# lsblk -l`

En supposant que nous avons un **SSD 128 GiB** et que nous utiliserons un partitionnement GPT pour l'installation UEFI :

`# cfdisk /dev/sda`

- `# 512 Mib` Créez une partition EFI et sélectionnez le type de partition système EFI
- `# 27,5 GiB`  Créez la partition Root
- `# 100 GiB`  Créez la partition Home
- `# write (yes)` et `quit` Écrire les modifications et quitter


#### Formatage des partitions

- `# mkfs.vfat -F32 /dev/sda1` Partition système EFI en FAT32 pour le boot
- `# mkfs.btrfs /dev/sda2` Partition root en BTRFS
- `# mkfs.btrfs /dev/sda3` Partition home en BTRFS


#### Montage des partitions

Créer des sous-volumes **@** et **@home**:

- `# mount /dev/sda2 /mnt`

- `# btrfs su cr /mnt/@`

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`

- `# umount /mnt`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt`

- `# mkdir -p /mnt/{home,boot}` Créer des répertoires /home et /boot
- `# mount /dev/sda1 /mnt/boot`
- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### Partitionnement de disque
Identifiez votre disque pour connaître la convention de nommage à utiliser. Par exemple, dans le cas d'un **SSD /dev/sda** ou dans le cas d'un **M.2 /dev/nvme0n1** et, enfin, le **Disque virtuel /dev/vda**.


`# lsblk -l`

En supposant que nous avons **3 disques de 128 GiB** pour LVM: **sda sdb sdc** utilisez **cfdisk** un par un :

`# cfdifk /dev/sda`

- `# 512 Mib` Créez une partition EFI et sélectionnez le type de partition système EFI
- `# 127,5 GiB`  Créez une partition et sélectionnez le type LVM
- `# write (yes)` et `quit` Écrire les modifications et quitter

`# cfdifk /dev/sdb`

- `# 128 GiB`  Créez une partition et sélectionnez le type LVM
- `# write (yes)` et `quit` Écrire les modifications et quitter

`# cfdifk /dev/sdc`

- `# 128 GiB`  Créez une partition et sélectionnez le type LVM
- `# write (yes)` et `quit` Écrire les modifications et quitter

Pour créer des partitions sous LVM, il nous faut d'abord créer un volume physique :

#### Créer un volume physique

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### Créer un groupe de volumes
Créez et étendez votre groupe de volumes ; vous devez créer un groupe de volumes sur un ou plusieurs volumes physiques `# vgcreate group_volume physical_volume` par exemple :

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

Cette commande configura tout d'abord les trois partitions comme volumes physiques (si nécessaire), puis crée le groupe de volumes avec les trois volumes. La commande vous alertera si elle détecte un système de fichiers existant sur l'un ou l'autre des périphériques.

#### Créer des volumes logiques

Créez des volumes logiques, pour une configuration de base, il nous faudrait chacun pour root, swap et home.

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### Formatage des partitions

- `# mkfs.vfat -F32 /dev/sda1` Partition système EFI en FAT32 pour le boot
- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 


#### Montage des partitions

- `# mount /dev/lvm/root /mnt` Monter la partition root
- `# mkdir -p /mnt/{home,boot}` Créer des répertoires /home et /boot
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot` 
- `# swapon /dev/lvm/swap`

#### Étendre un groupe LVM

Si à l'avenir vous souhaitez ajouter un nouveau volume physique au groupe, consultez quelle commande utiliser, en supposant un quatrième disque sdd et l'avoir partitionné comme précédemment, nous étendons l'espace par exemple à `/dev/lvm/home` :

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`

## 5. Liste de Miroirs

Enregistrez la liste de miroirs pour les dépôts dans **/etc/pacman.d/mirrorlist** en utilisant l'outil **reflector**, en spécifiant le pays pour synchroniser les serveurs, par exemple **it**. Plusieurs pays peuvent être ajoutés en utilisant une virgule, par exemple **it,us**:

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6. Pacstrap

Installer le noyau **linux** et les paquets de base pour créer notre système Arch, ajouter également un éditeur tel que **vim**. Si vous suivez l'installation pour **lvm**, ajoutez le paquet `lvm2` à la commande suivante :

`# pacstrap -K /mnt base base-devel linux linux-firmware vim` 

<br><br><br><br>



## 7. Générer Fstab

Le fichier /etc/fstab vous permet de contrôler les systèmes de fichiers qui sont montés sur votre système Linux lors du démarrage, y compris les partitions Windows et les partages réseau :

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8. Chroot
 
Entrez dans le chroot et configurez les étapes suivantes : Configuration de l'heure locale, de l'horloge système, de la langue, des mappages de clavier, de localhost, du mot de passe Root, de la création de l'utilisateur et de son mot de passe.

Entrez dans le chroot :

`# arch-chroot /mnt`

<br><br><br><br>


### Fuseau horaire

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>

### Localisation

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=it" >> /etc/vconsole.conf`

<br><br><br><br>


### Nom d'hôte et Hosts

- `# echo "NOMDEVOTREAMACHINE" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

<br><br><br><br>


### Utilisateur et Root

Configurez le mot de passe Root, faites attention !

`# passwd`

Configurez un nouvel utilisateur en minuscules, en créant le répertoire `/home/USERNAME` avec `-m`, le groupe `wheel` avec `-G` et le shell avec `-s` :

`# useradd -mG wheel -s /bin/bash USERNAME`

Configurez le nom réel (qui apparaît en graphique avec une majuscule en début de nom par exemple **"Alessio"**)

`# usermod -c 'NOMRÉEL' USERNAME`

Configurez un mot de passe pour le nouvel utilisateur ajouté, faites attention !

`# passwd USERNAME`

Configurez le fichier sudoers pour le groupe wheel :

`# echo "USERNAME ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/USERNAME`

<br><br><br><br>

### mkinitcpio pour LVM

ajouter **lvm2** aux hooks dans **/etc/mkinitcpio.conf**

`HOOKS="base udev ... block lvm2 filesystems"`

puis utilisez la commande :

`# mkinitcpio -p linux`

<br><br><br><br>


## 9. Chargeur de démarrage

### GRUB (BIOS-MBR)

- `# pacman -S grub`
- `# grub-install --target=i386-pc /dev/sda`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

<br><br><br><br>


### GRUB (UEFI)

- `# pacman -S grub`
- `# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

GRUB prend en charge entièrement le démarrage sécurisé en utilisant des clés CA ou shim, cependant, la commande d'installation diffère selon celle que vous avez l'intention d'utiliser.

Pour utiliser des clés CA, la commande est :

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`


Pour utiliser shim-lock, la commande est :

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

<br><br><br><br>

### Systemd-boot (EXT4)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

Maintenant, créez la configuration du fichier **arch.conf** ouvert avec **vim**, il est important d'écrire la partition boot racine correcte telle que `root=/dev/sdax` où `x` est le numéro de la partition racine.

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

Maintenant, créez la configuration du fichier **arch.conf** ouvert avec **vim**, il est important d'écrire la partition boot racine correcte telle que `root=/dev/sdax` où `x` est le numéro de la partition root, ajoutez le drapeau pour le sous-volume **@**.

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

Maintenant, créez la configuration du fichier **arch.conf** ouvert avec **vim**, il est important d'écrire la partition boot racine correcte telle que pour **lvm** `root=/dev/mapper/lvm-root`

- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

## 10. Paquets de base 

`# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`

<br><br><br><br>


## 11. Environnement de bureau

Choisissez parmi les environnements de bureau populaires suggérés :

### Gnome

Gnome complet avec le gestionnaire d'affichage GDM

- `# pacman -S gnome gnome-extra gdm` 
- `# systemctl enable gdm`

### Xfce4

xfce4 avec le gestionnaire d'affichage Lightdm

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

### Lxde

lxde avec le gestionnaire d'affichage Lightdm

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

### Mate

mate avec le gestionnaire d'affichage Lightdm

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

### Plasma

plasma kde avec le gestionnaire d'affichage SDDM

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

### Cinnamon

cinnamon avec le gestionnaire d'affichage Lightdm

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`


<br><br><br><br>


## 12. Services

Si vous avez activé le service pour le gestionnaire d'affichage, vous pouvez passer à l'activation des autres services nécessaires.

- `# systemctl enable NetworkManager` Soyez prudent, il est sensible à la casse.
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

<br><br><br><br>

## 13. Zram

L'exemple suivant décrit comment configurer le swapping automatique vers *zram* au démarrage en utilisant une seule règle *udev*. Aucun paquet supplémentaire ne devrait être nécessaire pour que cela fonctionne.

Chargez explicitement le module au démarrage :

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

Créez la règle *udev* suivante en ajustant l'attribut disksize selon la taille de la swap, dans cet exemple, c'est *16G*:

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k", TAG+="systemd"`

Ajoutez **/dev/zram** à votre fstab avec une priorité supérieure à la valeur par défaut :

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0 `

<br><br><br><br>
