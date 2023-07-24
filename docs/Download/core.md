
# Core Linux

![image](https://github.com/ArchItalia/site/assets/117321045/c63e1873-8d35-4618-866e-ef5ec97ad7d3)


!!! info "Informazioni"

    
    * Core-2023.07.22-x86_64.iso
    * Dimensione 1.58 GB
    * Versione 3.2 Stable
    * kernel linux-6.4.4.arch1-1
    * sha256sum: cbd16782edcf0c44a5614e159653e0e7b914af1f48a220e35abf1bf2bc749722

- [Download iso :fontawesome-regular-circle-down:](https://drive.google.com/file/d/1mOvo4MeTkN6zBdGvNBZLV3DFvYhCoq2L/view?usp=sharing)
- [Github :fontawesome-brands-github:](https://github.com/ArchItalia/core-linux)
- [Repo :fontawesome-brands-gitlab:](https://gitlab.com/architalialinux/ai-repo)

<br>

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

### Uso di Installscript su Core

#### Video Tutorial

<div>
<iframe width="760" height="415" src="https://www.youtube.com/embed/45GnQpOGyxg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>


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
<br><br>

## Core Theme

Se dopo un installazione di gnome minimal con Core volete customizzare il sistema con il tema di core ecco i passaggi da effettuare per rendere il vostro Archlinux puro un gradevole stile Nord.

![image](https://github.com/ArchItalia/site/assets/117321045/d3a9aeb6-4e27-495c-ae4a-33f54fcad94e)


<br>

### Requisiti per applicare il tema Core:

Aggiungere il repository di [architalia](https://architalia.github.io/site/Download/ai-repo/) 

### Installazione

puoi usare gnome-tweaks per gestire i temi `sudo pacman -S gnome-tweaks`
cercare i pacchetti da installare per il tema completo

```
sudo pacman -Ss core
```

```
ai-repo/architalia-fonts  
ai-repo/core-gnome-backgrounds 
ai-repo/core-gtk-theme 
ai-repo/core-icons-theme 
    
```

installa i pacchetti uno ad uno e imposta il tema con gnome-tweaks, nota: per far funzionare gtk-4.0 su nautilus, text-editor etc e' necessario copiare il tema gtk4.0 installato 

```
sudo cp -rp /etc/gtk-4.0 /home/$USER/.config/
```
![image](https://github.com/ArchItalia/site/assets/117321045/d8f68489-2833-4751-9a80-95b349c9b05e)

<br><br>


per il profilo nord per gnome-terminal scarica lo script che installera il nuovo tema al terminale [nord.sh](https://raw.githubusercontent.com/ArchItalia/site/main/files/nord.sh)


# Core Theme

If you have installed a minimal GNOME with Core and want to customize your Archlinux system with the Core theme, follow these steps to give your pure Archlinux a pleasant Nord style.

![image](https://github.com/ArchItalia/site/assets/117321045/d3a9aeb6-4e27-495c-ae4a-33f54fcad94e)

### Requirements to apply the Core theme:

Add the [architalia](https://architalia.github.io/site/Download/ai-repo/) repository.

### Installation

You can use `gnome-tweaks` to manage themes:

```
sudo pacman -S gnome-tweaks
```

Then search for the packages to install for the complete theme:

```
sudo pacman -Ss core
```

```
ai-repo/architalia-fonts  
ai-repo/core-gnome-backgrounds 
ai-repo/core-gtk-theme 
ai-repo/core-icons-theme 
```

Install the packages one by one and set the theme with `gnome-tweaks`. Note: To get `gtk-4.0` to work on `nautilus`, `text editor`, etc. you need to copy the installed `gtk4.0` theme:

```
sudo cp -rp /etc/gtk-4.0 /home/$USER/.config/
```
![image](https://github.com/ArchItalia/site/assets/117321045/d8f68489-2833-4751-9a80-95b349c9b05e)

For the Nord profile for gnome-terminal, download the script that will install the new theme to the terminal: [nord.sh](https://raw.githubusercontent.com/ArchItalia/site/main/files/nord.sh).





