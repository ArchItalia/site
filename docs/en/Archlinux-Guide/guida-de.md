# Arch Linux Installation


## 1. Signaturüberprüfung

Es wird empfohlen, die Signatur des Images vor der Verwendung zu überprüfen, insbesondere beim Herunterladen von einem HTTP-Mirror, wo Downloads abgefangen werden können, um schädliche Bilder bereitzustellen.

Auf einem System mit *GnuPG* installiert, laden Sie die [PGP ISO Signatur](https://archlinux.org/download/#checksums) in das ISO-Verzeichnis herunter und überprüfen Sie sie mit:

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

Alternativ von einer vorhandenen Arch-Linux-Installation ausführen:

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2. Erstkonfiguration

Um zu beginnen, müssen wir die Tastatursprache definieren, die Standardsprache ohne Eingabeaufforderung ist `US`. Die verfügbaren Layouts können mit dem Befehl `ls /usr/share/kbd/keymaps/**/*.map.gz` aufgelistet werden.

Legen Sie die Sprache unserer Tastatur mit dem Befehl fest:

`# loadkeys it`

Die Konsolenzeichen finden Sie unter **/usr/share/kbd/consolefonts/** und können auch mit setfont festgelegt werden. Um beispielsweise Zeichen für HiDPI-Displays zu verwenden, führen Sie folgenden Befehl aus:

`# setfont ter-132b`


<br><br><br><br>

## 3. Internetverbindung
Wenn Sie die Maschine über ein Kabel oder eine virtuelle Maschine mit dem Internet verbunden haben, können Sie Ihre erworbene IP-Adresse mit diesem Befehl überprüfen:

`# ip a`

Die Verbindung kann mit dem Ping-Test-Befehl getestet werden:

`# ping -c 3 archlinux.org`

Verbinden Sie mit dem Wi-Fi-Netzwerk mithilfe des iwctl-Tools:

- `# iwctl` Starte iwctl
- `# device list` Finden Sie den Namen Ihres Geräts. Beispiel wlan0
- `# station wlan0 scan` Scannen Sie nach verfügbaren drahtlosen Netzwerken.
- `# station wlan0 get-networks` Get the list of networks
- `# station wlan0` verbinden Sie Ihr Netzwerkname  Verbindung zu Ihrem Netzwerk
- `# exit`

Wenn unsere Geräte deaktiviert sind und wir ** iwctl ** nicht ausführen können:

- `# rfkill list` Überprüfen Sie den blockierten oder entblockten Status der Geräte.
- `# rfkill unblock all` Entsperren Sie alle unsere blockierten Geräte.
- `# systemctl restart iwd` Starten Sie den iwd-Service neu.

Versuchen Sie es erneut mit `# iwctl` und fahren Sie wie oben fort.

<br><br><br><br>

## 4. Festplattenvorbereitung

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm EXT4](#uefi-lvm-ext4)


<br><br><br><br>

### Bios-MBR

#### Partitionierung
Identifizieren Sie Ihre Festplatte, um die zu verwendende Namenskonvention zu kennen. Im Fall von **SSD /dev/sda** oder im Fall von **M.2 /dev/nvme0n1** und schließlich von **virtueller Festplatte /dev/vda**.

`# lsblk -l`

Sobald der Name unserer Festplatte identifiziert wurde, verwenden Sie **cfdisk**, hier gehen wir davon aus, dass **/dev/sda** vorhanden ist. Möglicherweise wird bei einem Rohlingstyp des Datenträgers nach dem Typ der Partitionierungstabelle gefragt. Wählen Sie in diesem Fall **DOS** aus:

`# cfdisk /dev/sda`

Erstellen Sie die notwendigen Partitionen für die Basiseinrichtung und gehen Sie davon aus, dass wir eine **128GiB SSD** haben:

- `# 4Gib` Erstellen Sie eine Partition für den Swap und wählen Sie den Swap-Typ.
- `# 124Gib` Erstellen Sie die Root-Partition.
- `# write (yes)` und `quit` Schreiben Sie die Änderungen und verlassen Sie den Editor.


#### Formattierung der Partitionen

- `# mkswap /dev/sda1` Swap-Partition
- `# mkfs.ext4 /dev/sda2` Root-Partition in EXT4

#### Einhängen der Partitionen

- `# mount /dev/sda2 /mnt` Mounten Sie die Root-Partition.
- `# swapon /dev/sda1` Mounten Sie die Swap-Partition.


<br><br><br><br>

### UEFI ext4

#### Disk Partitioning
Identifizieren Sie Ihre Festplatte, um die zu verwendende Namenskonvention zu kennen. Im Fall von **SSD /dev/sda** oder im Fall von **M.2 /dev/nvme0n1** und schließlich von **virtueller Festplatte /dev/vda**.

`# lsblk -l`

Angenommen, wir haben eine **128GiB SSD** und verwenden GPT-Partitionierung für die UEFI-Installation:

`# cfdisk /dev/sda`

- `# 512Mib` Erstellen Sie eine EFI-Partition und wählen Sie den EFI-Systempartitions-Typ.
- `# 4Gib` Erstellen Sie eine Partition für die Swap-Datei und wählen Sie den Swap-Typ.
- `# 23,5Gib` Erstellen Sie die Root-Partition.
- `# 100Gib` Erstellen Sie die Home-Partition.
- `# write (yes)` und `quit` Schreiben Sie die Änderungen und verlassen Sie den Editor.

#### Formattierung der Partitionen

- `# mkfs.vfat -F32 /dev/sda1` EFI-Systempartition in FAT32 für den Bootvorgang.
- `# mkswap /dev/sda2` Swap-Partition
- `# mkfs.ext4 /dev/sda3` Root-Partition in EXT4
- `# mkfs.ext4 /dev/sda4` Home-Partition in EXT4


#### Einhängen der Partitionen

- `# mount /dev/sda3 /mnt` Mounten Sie die Root-Partition.
- `# mkdir -p /mnt/{home,boot}` Erstellen Sie die Verzeichnisse /home und /boot
- `# mount /dev/sda4 /mnt/home` Mounten Sie die Home-Partition.
- `# mount /dev/sda1 /mnt/boot` Mounten Sie die Boot-Partition.
- `# swapon /dev/sda2` Aktivieren Sie die Swap-Partition.


<br><br><br><br>

### UEFI btrfs

#### Disk Partitioning
Identifizieren Sie Ihre Festplatte, um die zu verwendende Namenskonvention zu kennen. Im Fall von **SSD /dev/sda** oder im Fall von **M.2 /dev/nvme0n1** und schließlich von **virtueller Festplatte /dev/vda**.

`# lsblk -l`

Angenommen, wir haben eine **128GiB SSD** und verwenden GPT-Partitionierung für die UEFI-Installation:

`# cfdisk /dev/sda`

- `# 512Mib` Erstellen Sie eine EFI-Partition und wählen Sie den EFI-Systempartitions-Typ.
- `# 27.5Gib`  Erstellen Sie die Root-Partition.
- `# 100Gib` Erstellen Sie die Home-Partition.
- `# write (yes)` und `quit` Schreiben Sie die Änderungen und verlassen Sie den Editor.

#### Formattierung der Partitionen

- `# mkfs.vfat -F32 /dev/sda1` EFI-Systempartition in FAT32 für den Bootvorgang.
- `# mkfs.btrfs /dev/sda2` Root-Partition in BTRFS
- `# mkfs.btrfs /dev/sda3` Home-Partition in BTRFS

#### Einhängen der Partitionen 

Erstellen Sie die Subvolumen **@** und **@home**:

- `# mount /dev/sda2 /mnt`

- `# btrfs su cr /mnt/@`

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`

- `# umount /mnt`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt`

- `# mkdir -p /mnt/{home,boot}` Erstellen Sie die Verzeichnisse /home und /boot.
- `# mount /dev/sda1 /mnt/boot`
- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### Disk Partitioning
Identifizieren Sie Ihre Festplatte, um die zu verwendende Namenskonvention zu kennen. Im Fall von **SSD /dev/sda** oder im Fall von **M.2 /dev/nvme0n1** und schließlich von **virtueller Festplatte /dev/vda**.


`# lsblk -l`

Angenommen, wir haben **3 128GiB-Festplatten** für LVM: **sda sdb sdc** verwenden Sie für eine Festplatte nach der anderen **cfdisk**:

`# cfdisk /dev/sda`

- `# 512Mib` Erstellen Sie eine EFI-Partition und wählen Sie den EFI-Systempartitions-Typ.
- `# 127.5GiB` Erstellen Sie eine Partition und wählen Sie den LVM-Typ.
- `# write (yes)` und `quit` Schreiben Sie die Änderungen und verlassen Sie den Editor.

`# cfdisk /dev/sdb`

- `# 128GiB` Erstellen Sie eine Partition und wählen Sie den LVM-Typ.
- `# write (yes)` und `quit` Schreiben Sie die Änderungen und verlassen Sie den Editor.

`# cfdisk /dev/sdc`

- `# 128GiB` Erstellen Sie eine Partition und wählen Sie den LVM-Typ.
- `# write (yes)` und `quit` Schreiben Sie die Änderungen und verlassen Sie den Editor.

Um Partitionen unter LVM zu erstellen, müssen wir zunächst eine physikalische Volume erstellen:

#### Physikalische Volume erstellen

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### Volume Group erstellen
Erstellen Sie Ihre Volume Group; Sie müssen eine Volume Group auf einem oder mehreren physikalischen Volumes erstellen `# vgcreate volume_group physical_volume` zum Beispiel:

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

Dieser Befehl richtet zuerst die drei Partitionen als physische Volumes ein (wenn benötigt) und erstellt dann die Volume Group mit den drei Volumes. Der Befehl warnt Sie, wenn es ein vorhandenes Dateisystem auf einem Gerät erkennt.

#### Logische Volumes erstellen

Erstellen Sie logische Volumes. Für eine grundlegende Konfiguration benötigen wir jeweils eines für Root, Swap und Home.

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### Formattierung der Partitionen

- `# mkfs.vfat -F32 /dev/sda1` EFI-Systempartition in FAT32 für den Bootvorgang.
- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 


#### Einhängen der Partitionen 

- `# mount /dev/lvm/root /mnt` Mounten Sie die Root-Partition.
- `# mkdir -p /mnt/{home,boot}` Erstellen Sie die Verzeichnisse /home und /boot.
- `# mount /dev/lvm/home /mnt/home` Mounten Sie die Home-Partition.
- `# mount /dev/sda1 /mnt/boot` Mounten Sie die Boot-Partition.
- `# swapon /dev/lvm/swap` 


#### Eine LVM-Gruppe erweitern

Wenn Sie in der Zukunft eine neue physische Volume der Gruppe hinzufügen möchten, finden Sie den Befehl, der ausgeführt werden soll, indem Sie davon ausgehen, dass eine vierte Festplatte sdd vorhanden ist und wie zuvor partitioniert wurde, vergrößern Sie z. B. den Platz für `/dev/lvm/home`:

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`

## 5. Spiegelserver

Speichern Sie die Mirrorliste für die Repositories in **/etc/pacman.d/mirrorlist** mit dem Tool **reflector**, das das Land spezifiziert, um die Server zu synchronisieren, zum Beispiel **it**. Mehrere Länder können mit einem Komma getrennt werden, zum Beispiel **it,us**:

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6. Pacstrap

Installieren Sie den **Linux-Kernel** und Basispakete, um unser Arch-System zu erstellen. Fügen Sie auch einen Editor wie **vim** hinzu. Falls Sie die Installation für **lvm** durchführen, fügen Sie dem folgenden Befehl das `lvm2`-Paket hinzu:

`# pacstrap -K /mnt base base-devel linux linux-firmware vim` 

<br><br><br><br>



## 7. Fstab generieren

Die Datei /etc/fstab ermöglicht die Steuerung, welche Dateisysteme beim Start Ihres Linux-Systems eingebunden werden, einschließlich Windows-Partitionen und Netzwerkfreigaben:

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8. Chroot
 
Betreten Sie die chroot-Shell und konfigurieren Sie die folgenden Schritte: Konfiguration der local time, Systemzeit, Sprache, Tastaturzuordnung, localhost, Root-Passwort, Benutzererstellung und Passwort.

Betreten Sie die chroot-Shell:

`# arch-chroot /mnt`

<br><br><br><br>


### Timezone

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>



### Lokalisation

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=it" >> /etc/vconsole.conf`

<br><br><br><br>


### Hostname und Hosts

- `# echo "YOURMACHINENAME" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

<br><br><br><br>


### Benutzer und Root

Konfigurieren Sie das Root-Passwort, seien Sie vorsichtig!

`# passwd`

Konfigurieren Sie einen neuen Benutzernamen in Kleinbuchstaben, indem Sie das Verzeichnis `/home/USERNAME` mit `-m`, die Gruppe `wheel` mit `-G` und die Shell mit `-s` erstellen:

`# useradd -mG wheel -s /bin/bash USERNAME`

Konfigurieren Sie den richtigen Namen (der in der Grafik mit großem Anfangsbuchstaben angezeigt wird, zum Beispiel **"Alessio"**

`# usermod -c 'REALNAME' USERNAME`

Konfigurieren Sie ein Passwort für den neu hinzugefügten Benutzer, seien Sie vorsichtig!

`# passwd USERNAME`

Konfigurieren Sie die Sudoers-Datei für die Wheel-Gruppe:

`# echo "USERNAME ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/USERNAME`

<br><br><br><br>

### mkinitcpio für LVM

Fügen Sie **lvm2** zu den Hooks in **/etc/mkinitcpio.conf** hinzu.

`HOOKS="base udev ... block lvm2 filesystems"`

Verwenden Sie dann den Befehl:

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

GRUB unterstützt Secure Boot vollständig mit CA-Schlüsseln oder Shim. Die Installationsbefehle unterscheiden sich jedoch je nachdem, welchen Sie verwenden möchten.

Um CA-Schlüssel zu verwenden, lautet der Befehl:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`


Um Shimlock zu verwenden, lautet der Befehl:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

<br><br><br><br>

### Systemd-boot (EXT4)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

Erstellen Sie nun die Konfiguration der mit **vim** geöffneten **arch.conf**-Datei. Es ist wichtig, die korrekte Root-Boot-Partition wie `root=/dev/sdax` zu schreiben, wobei `x` die Nummer der Root-Partition ist.

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

Erstellen Sie nun die Konfiguration der mit **vim** geöffneten **arch.conf**-Datei. Es ist wichtig, die korrekte Root-Boot-Partition wie `root=/dev/sdax` zu schreiben, wobei `x` die Nummer der Root-Partition ist. Fügen Sie die Flagge für das **@**-Unterdatenträger hinzu.

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

Erstellen Sie nun die Konfiguration der mit **vim** geöffneten **arch.conf**-Datei. Es ist wichtig, die korrekte Root-Boot-Partition wie für **lvm** `root=/dev/mapper/lvm-root` zu schreiben.

- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

## 10. Basispakete

`# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`


<br><br><br><br>


## 11. Desktop-Umgebung

Wählen Sie aus einigen der vorgeschlagenen populären Desktop-Umgebungen:

### Gnome
Vollständiges Gnome mit GDM Display-Manager

- `# pacman -S gnome gnome-extra gdm` 
- `# systemctl enable gdm`

### Xfce4
Xfce4 mit LightDM Display-Manager

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

### Lxde
LXDE mit LightDM Display-Manager

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

### Mate
Mate mit LightDM Display-Manager

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

### Plasma
Plasma KDE mit SDDM Display-Manager

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

### Cinnamon
Cinnamon mit LightDM Display-Manager

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`


<br><br><br><br>


## 12. Dienste

Wenn Sie den Dienst für den Display-Manager aktiviert haben, können Sie damit beginnen, die anderen erforderlichen Dienste zu aktivieren.

- `# systemctl enable NetworkManager` Beachten Sie, dass es auf Groß- / Kleinschreibung ankommt.
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

<br><br><br><br>

## 13. Zram

Im folgenden Beispiel wird beschrieben, wie Sie das automatische Auslagern auf "Zram" beim Booten mithilfe einer einzigen "udev"-Regel konfigurieren. Es dürften keine zusätzlichen Pakete erforderlich sein, um dies zum Laufen zu bringen.

Laden Sie das Modul bei Boot explizit:

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

Erstellen Sie die folgende "udev"-Regel und passen Sie das Attribut "disksize" entsprechend der gewünschten Swap-Größe an. In diesem Beispiel ist es "16G":

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k", TAG+="systemd"`

Fügen Sie **/dev/zram** zu Ihrem fstab hinzu und setzen Sie die Priorität höher als die Standardpriorität:

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0 `

<br><br><br><br>
