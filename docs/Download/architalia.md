
![Screenshot from 2023-07-17 02-20-16](https://github.com/ArchItalia/site/assets/117321045/7f68a893-0ba2-4de9-a3d0-b058cf471ce1)


## 🇮🇹 Core (Architalia-live) 
> Pensata per tutti i tipi di utenti, obbiettivo avere un ambiente utile senza perdere la liberta' di costruzione del proprio sistema. 

Introduzione

**Core** è una distribuzione live che consente l'installazione di Arch Linux in modo completamente manuale grazie alla presenza della console e degli script. Allo stesso tempo, include una shell minimale di Gnome con pacchetti utili per una facile gestione dell'installazione. La connessione a Internet avviene tramite NetworkManager, integrato nella shell di Gnome, e ogni utente può accedere alle guide in formato PDF della community Architalia, salvate nella cartella `~/Guide`. Architettura avanzata e opzioni di personalizzazione sono accessibili anche attraverso l'utilizzo dello script di Architalia posizionato in `~/Installscript`. 

Poiché **Core** è solo una live, i pacchetti aggiuntivi come Firefox, GParted, Git, Evince e Nautilus sono presenti solo temporarily e non sono parte dell'installazione di Arch Linux. Questa distribuzione live rappresenta una soluzione ideale per coloro che vogliono sperimentare l'esperienza di Arch Linux, con le opportunità di personalizzazione e di gestione manuale della console, senza dover effettuare un'installazione completa su disco rigido. La presenza di una shell minimale di Gnome con un'interfaccia utente intuitiva garantisce un accesso facile e veloce alle funzionalità principali, mentre la possibilità di connettersi a internet attraverso NetworkManager offre una configurazione senza problemi per la rete. In poche parole, con Core, gli utenti possono sperimentare l'efficienza di Arch Linux in una modalità versatile e personalizzabile.

<br><br>

## 🇬🇧 Core (Architalia-live)

> Designed for all types of users, the goal is to have a useful environment without losing the freedom to build your own system.

Introduction

**Core** is a live distribution that allows for the manual installation of Arch Linux thanks to the presence of the console and scripts. At the same time, it includes a minimal Gnome shell with useful packages for easy installation management. Internet connection is established via NetworkManager, integrated into the Gnome shell, and each user can access community Architalia PDF guides saved in the `~/Guide` folder. Advanced architecture and customization options are also accessible through the use of Architalia script located in `~/Installscript`.

Since **Core** is only a live distribution, additional packages such as Firefox, GParted, Git, Evince, and Nautilus are only temporarily present and are not part of the Arch Linux installation. This live distribution represents an ideal solution for those who want to experience Arch Linux's customization and manual console management without having to perform a full installation on a hard disk. The presence of a minimal Gnome shell with an intuitive user interface guarantees easy and quick access to main functions, while the ability to connect to the internet via NetworkManager offers a hassle-free network configuration. In short, with Core, users can experience the efficiency of Arch Linux in a versatile and customizable mode.

<br><br>

![Screenshot from 2023-07-17 02-21-27](https://github.com/ArchItalia/site/assets/117321045/77e3bc33-7284-46ef-bcc5-467b48b0298d)
![Screenshot from 2023-07-17 02-22-02](https://github.com/ArchItalia/site/assets/117321045/742fb29c-15fc-4058-adec-df73527b3087)
![Screenshot from 2023-07-17 02-24-25](https://github.com/ArchItalia/site/assets/117321045/b118aa94-83bf-41fa-af56-93edae5f4a9e)



### Download

!!! info "Informazioni"
    
    * Core-2023.07.17-x86_64.iso
    * Dimensione 1.58 GB
    * Versione 3.1 Stable
    * kernel linux-6.4.3.arch1-2
    * sha256sum: b065c7314f8d9af09f17d0edf99e4061eb16b7e816690c11c1a2fb678f88b622

- [Download iso :fontawesome-regular-circle-down:](https://drive.google.com/file/d/1Q6I9b8dqY5OM_UFQ02k9htyXoeesSwLX/view?usp=sharing)
- [Github :fontawesome-solid-code-branch:](https://github.com/ArchItalia/architalia-live.git)


<br><br>

### Uso di Installscript su Core

Usare **Installscript** su core e' facile gli script sono gia presenti in `~/Installscript` .

Procedere con la preparazione del disco attraverso **cfdisk** o **gparted**, le partizioni necessarie sono tre ad esempio:

![250954264-510a8f64-950c-4fc3-a8a4-164479bd9a63](https://github.com/ArchItalia/site/assets/117321045/52235cdb-6add-4cec-a774-0a9dde59c06e)


1. `512MiB Type EFI System `   per /boot
2. `xxGiB  Type Linux Filesystem` per /root
3. `xxGiB  Type Linux Filesystem` per /home

per vedere come usare **cfdisk** leggi il [qui](https://architalia.github.io/site/Archlinux-Guida/arch-guida/#uefi-btrfs) dove dopo la creazione non occorre formattare ci pensera' lo script.

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

Avviare il primo script  `./1-parte.sh` 

Alla fine del primo script ci ritroveremo direttamente in **chroot**, a questo punto dobbiamo spostarci  `cd /home` e avviare il secondo script `./2-parte.sh`:

```
# [arch-chroot]
# cd /home
# ./2-parte.sh
```
