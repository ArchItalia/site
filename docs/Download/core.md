
# Core Linux


![image](https://github.com/ArchItalia/site/assets/117321045/3dcdd1a1-e9d2-4dde-bd99-8404541a643b)


!!! info "Informazioni"

    
    * Core-2023.08.01-x86_64.iso
    * Dimensione: 1.75 GB
    * Versione: 2023.8 beta
    * kernel: 6.4.7-arch1-1
    * sha256sum: c5a6c40bad3af7daf046e080504ea7a2e159d4b168bd853238e0aa1397c556ee

- [Download iso :fontawesome-regular-circle-down:](#) non disponibile 
- [Github :fontawesome-brands-github:](https://github.com/ArchItalia/core-linux)
- [Repo :fontawesome-brands-gitlab:](https://gitlab.com/architalialinux/ai-repo)

<br>

* base: arch linux 
* kernel: linux
* Installer: **installcore** solo con connessione attiva  
* filesystem btrfs @ @home partizione separata
* zram incluso
* pacchetti inclusi: clean firefox yay timeshift extension-manager evince eog gparted gsmartcontrol mpv gnome-calculator gnome-clocks gnome-calendar htop gnome-system-monitor vim
* DE: gnome

<br>

## Cos'è Core Linux 🇮🇹

**Core Linux** è una distribuzione Linux leggera e minimale basata su Arch Linux. Si concentra sulla semplicità e sulla velocità, fornendo solo i pacchetti essenziali per il funzionamento del sistema operativo. Core Linux è disponibile solo per architetture a 64 bit ed è progettato per essere personalizzato e configurato dall'utente secondo le proprie esigenze. Inoltre, Core Linux è anche una distribuzione Linux ufficiale della comunita' Architalia.

## Installazione di Core Linux

**Core Linux** offre la possibilita' di eseguire un installazione cruda di Arch Linux puro usando direttamente la console in un ambiente comodo e organizzato di strumenti utili, e' possibile importare delle proprie guide o script da eseguire attraverso la live basata su Arch Linux. Per installare la versione di Core Linux con il suo ambiente minimale ma organizzato e' possibile usare dal menu applicazioni **installcore**, o semplicemente digitare in console `installcore` e avviare lo strumeto di installazione.

![Screenshot from 2023-08-01 16-12-57](https://github.com/ArchItalia/site/assets/117321045/10df28fb-d8a4-42d9-b2f6-915f66b7f335)


## Configurazione di Core Linux 

**Core Linux** offre una configurazione personalizzata che include gia' la presenza di alcuni pacchetti utili: come **yay**, **timeshift**, e altri pacchetti mantenuti dal proprio repository [**ai-repo**](https://architalia.github.io/site/Download/ai-repo/), l'ambiente grafico e' una versione minimizzata di **Gnome**, la quale predispone delle estensioni base che permettono un uso piu comodo come **Arch Linux updates indicator** che e' gia configurato per la gestione dei pacchetti ufficiali e di AUR attraverso lo script **updates** di Core Linux, **AppIndicator** per avere la funzione del vassoio di sistema sulla topbar, **Night Theme Switcher** per cambiare automaticamente dal tema chiaro al tema scuro, **desktop-icons-ng** per le icone sul desktop, molte altre funzioni e pacchetti saranno aggiunti con l'evoluzione della distribuzione.  


Il filesystem usato e' **btrfs** con la creazione dei sottovolumi **@** e **@home**, per la gestione della swap viene installato **zram generator**.
<br>

![Screenshot from 2023-07-27 20-54-10](https://github.com/ArchItalia/site/assets/117321045/19125ab4-201e-4af2-a07b-3a4ea1117f84)


Per la manutenzione del sistema e' installato **clean** disponibile usando il comando `clean`

<br>

## What is Core Linux 🇬🇧

**Core Linux** is a lightweight and minimal Linux distribution based on Arch Linux. It focuses on simplicity and speed, providing only the essential packages for the proper functioning of the operating system. Core Linux is only available for 64-bit architectures and is designed to be customizable and configured by the user according to their needs. Additionally, Core Linux is also an official Linux distribution of the Architalia community.

## Installing Core Linux

**Core Linux** offers the possibility to run a raw installation of pure Arch Linux using the console in a comfortable and organized environment of useful tools, it is possible to import your own guides or scripts to be run through the live based on Arch Linux. To install the Core Linux version with its minimal but organized environment, you can use the **installcore** application from the menu, or simply type `installcore` in the console and start the installation process.

![Screenshot from 2023-08-01 16-12-57](https://github.com/ArchItalia/site/assets/117321045/10df28fb-d8a4-42d9-b2f6-915f66b7f335)


## Configuring Core Linux

**Core Linux** offers a customized configuration that already includes the presence of some useful packages such as **yay**, **timeshift**, and other packages maintained by its own repository [**ai-repo**](https://architalia.github.io/site/Download/ai-repo/), the graphical environment is a minimized version of **Gnome**, which provides basic extensions that allow for more convenient use, such as **Arch Linux updates indicator** which is already set up for managing official packages and AUR through the Core Linux **updates** script, **AppIndicator** to have the system tray function on the top bar, **Night Theme Switcher** to automatically switch from light mode to dark mode, **desktop-icons-ng** for desktop icons, many other functions and packages will be added with the evolution of the distribution.

The filesystem used is **btrfs** with the creation of the subvolumes **@** and **@home**, for the management of swap, **zram generator** is installed.
<br>

![Screenshot from 2023-07-27 20-54-10](https://github.com/ArchItalia/site/assets/117321045/19125ab4-201e-4af2-a07b-3a4ea1117f84)


For system maintenance, **clean** is installed and available using the `clean` command.
