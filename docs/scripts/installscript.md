**Installscript** script di installazione **btrfs** diviso in due parti.
- [GitHub :fontawesome-solid-code-branch:](https://github.com/ArchItalia/installscript) 

<br>

![image](https://github.com/ArchItalia/site/assets/117321045/ce7160f3-d429-4ec0-9c4a-f2d726ca67e2)
<br>

La prima parte **(1-parte.sh)** e' composta dalla preparazione del disco e delle partizioni.
Prima di usare questo script diviso in due che si trova in `~/Installscript` su **architalia-live ISO**, 
e' necessario preparare il disco con ad esempio **cfdisk** seguendo le istruzioni sulla guida al paragrafo [**UEFI btrfs**](https://architalia.github.io/site/Archlinux-Guida/arch-guida/#uefi-btrfs), oppure semplicemente **gparted** integrato nella live, le partizioni necessarie per esempio sono:

<br>

![image](https://github.com/ArchItalia/site/assets/117321045/510a8f64-950c-4fc3-a8a4-164479bd9a63)


<br>

```
# 512MiB Type EFI System
# 20GiB  Type Linux Filesystem
# 30GiB  Type Linux Filesystem
```
<br>

!!! note "Nota"
    
    La partizione di swap viene sostituita con l'installazione di ZRAM, che lo script 2-parte.sh 
     configurera per noi, sara' sufficente indicare la misura della swap in GiB

<br>

A questo punto sara' lo script a prendere in mano la formattazione, la creazione dei sottovolumi e il montaggio.
Prima pero' vediamo come cambiare il valore delle variabili a seconda delle proprie impostazioni in entrambi le parti **1** e **2**:

<br>

```
# settings script 1

country="us" # Seleziona il paese piu vicino per configurare il mirrorlist
age="6" # Specifica il numero massimo di mirror da includere nella lista dei mirror sincronizzati.
editor="vim" # Installa vim come editor
kernel="linux" # Scegli il kernel da installare 
FM="linux-firmware" # Installa i firmware
tools="intel-ucode btrfs-progs" # Installa utility per il filesystem BTRFS e altri
base="base base-devel" # gruppi di pacchetti fondamentali per Arch Linux

# --- scegli la nomemclatura, decommenta le tre partizioni necessarie. 

#nvme - M2
#p1="/dev/nvme0n1p1"
#p2="/dev/nvme0n1p2"
#p3="/dev/nvme0n1p3"

#vda - Virtual Machine
#p1="/dev/vda1"
#p2="/dev/vda2"
#p3="/dev/vda3"

#sda - SSD
#p1="/dev/sda1"
#p2="/dev/sda2"
#p3="/dev/sda3" 
```

<br>


```
# settings script 2

localhost="localhost" # nome machina - hostname
user="username"    # nome utente [solo minuscolo] -- username [only lowercase]
realname="realname" # nome reale [minuscolo/maiuscolo] - real name [uppercase/lowercase]
rootpw="password" # password per root -- root password
userpw="password" # password per utente -- user password
localegen="en_US.UTF-8 UTF-8" # locale encoding
localeconf="LANG=en_US.UTF-8"  # lingua locale -- local language
km="us" # lingua della tastiera -- keyboard layout
localtime="Europe/Italy" # localtime
ZS="16G" # dimensione swap zram - size swap zram
groups="wheel" # aggiungi gruppi all'utente - add groups for user
Ntools="wpa_supplicant wireless_tools netctl net-tools iw networkmanager" # set network tools
Audio="alsa-utils pipewire-pulse" # Audio packages
Utils="mtools dosfstools exfatprogs fuse" # tools 
PKGS="firewalld acpi cronie git reflector bluez bluez-utils" #general packages
DE="xorg gnome-shell nautilus gnome-console gvfs gnome-control-center xdg-user-dirs-gtk  gnome-text-editor gnome-keyring gnome-system-monitor" #GNOME [Minimal installation]
DM="gdm" # Display Manager
Service="gdm NetworkManager firewalld bluetooth cronie reflector" # Service

# [root=/dev/XXX] decommenta la nomenclatura in uso per systemd-boot IMPORTANTE! -- uncomment the nomenclature in use for systemd-boot IMPORTANT!

#p="sda2" 
#p="vda2"
#p="nvme0n1p2"

# end setting ----------------------------------------------

```
<br>
<br>


Ecco la parte che si occupera di eseguire la prima parte dell'installazione:

```

#Formattazione delle partizioni - EFI, ROOT, HOME
mkfs.fat -F32 $p1
mkfs.btrfs -f $p2 
mkfs.btrfs -f $p3

#Montaggio e sottovolumi - partizione /home separata
mount $p2 /mnt           
btrfs su cr /mnt/@  
umount /mnt 
mount $p3 /mnt
btrfs su cr /mnt/@home      
umount /mnt                             
mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ $p2 /mnt 
mkdir -p /mnt/{boot,home}
mount $p1 /mnt/boot 
mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home $p3 /mnt/home 

# reflector
reflector --verbose -c $country -a $age --sort rate --save /etc/pacman.d/mirrorlist

#pacstrap
pacstrap -K /mnt $base $FM $kernel $tools $editor

#fstab
genfstab -Up /mnt > /mnt/etc/fstab


cp 2-parte.sh /mnt/home/
arch-chroot /mnt 
  
```
<br>
<br>


Alla fine del primo script ci ritroveremo direttamente in **chroot**, a questo punto dobbiamo spostarci e avviare il secondo script:

- `# cd /home`
- `# ./2-parte.sh`


