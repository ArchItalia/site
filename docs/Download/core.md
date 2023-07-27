
# Core Linux

![image](https://github.com/ArchItalia/site/assets/117321045/de8b9083-7d18-4ead-a0b4-d4a0c4e7a739)




!!! info "Informazioni"

    
    * Core-2023.07.27-x86_64.iso
    * Dimensione 1.58 GB
    * Versione 2023.7 Stable
    * kernel linux-6.4.6.arch1-1
    * sha256sum: 

- [Download iso :fontawesome-regular-circle-down:](https://drive.google.com/file/d/1mOvo4MeTkN6zBdGvNBZLV3DFvYhCoq2L/view?usp=sharing)
- [Github :fontawesome-brands-github:](https://github.com/ArchItalia/core-linux)
- [Repo :fontawesome-brands-gitlab:](https://gitlab.com/architalialinux/ai-repo)

<br>

# Cos'è Core Linux 🇮🇹

**Core Linux** è una distribuzione Linux leggera e minimale basata su Arch Linux. Si concentra sulla semplicità e sulla velocità, fornendo solo i pacchetti essenziali per il funzionamento del sistema operativo. Core Linux è disponibile solo per architetture a 64 bit ed è progettato per essere personalizzato e configurato dall'utente secondo le proprie esigenze. Inoltre, Core Linux è anche una distribuzione Linux ufficiale della comunita' Architalia.

# Installazione di Core Linux

**Core Linux** offre la possibilita' di eseguire un installazione cruda di Arch Linux puro usando direttamente la console in un ambiente comodo e organizzato di strumenti utili, e' possibile importare delle proprie guide o script da eseguire attraverso la live basata su Arch Linux. Per installare la versione di Core Linux con il suo ambiente minimale ma organizzato e' possibile usare dal menu applicazioni **installcore**, o semplicemente digitare in console `installcore` e avviare lo strumeto di installazione.

![Screenshot from 2023-07-27 16-09-50](https://github.com/ArchItalia/site/assets/117321045/1f56011f-757d-4ca0-9b65-1574fe0b1ce1)

seguire le indicazioni per configurare l'installazione di Core Linux.

![Screenshot from 2023-07-27 16-10-03](https://github.com/ArchItalia/site/assets/117321045/444b36e2-9a8e-4868-a38f-b6d713cf8d9a)

# Configurazione di Core Linux 

![image](https://github.com/ArchItalia/site/assets/117321045/95d8cd5c-1e1d-46da-af41-99462d521c4f)


**Core Linux** offre una configurazione personalizzata che include gia' la presenza di alcuni pacchetti utili: come **yay**, **timeshift**, e altri pacchetti mantenuti dal proprio repository [**ai-repo**](https://architalia.github.io/site/Download/ai-repo/), l'ambiente grafico e' una versione minimizzata di **Gnome**, la quale predispone delle estensioni base che permettono un uso piu comodo come **Arch Linux updates indicator** che e' gia configurato per la gestione dei pacchetti ufficiali e di AUR attraverso lo script **updates** di Core Linux, **AppIndicator** per avere la funzione del vassoio di sistema sulla topbar, **Night Theme Switcher** per cambiare automaticamente dal tema chiaro al tema scuro, **desktop-icons-ng** per le icone sul desktop, molte altre funzioni e pacchetti saranno aggiunti con l'evoluzione della distribuzione.  


Il filesystem usato e' **btrfs** con la creazione dei sottovolumi **@** e **@home**, per la gestione della swap viene installato **zram generator**. 

<br>

# What is Core Linux 🇬🇧

**Core Linux** is a lightweight and minimal Linux distribution based on Arch Linux. It focuses on simplicity and speed, providing only the essential packages for the proper functioning of the operating system. Core Linux is only available for 64-bit architectures and is designed to be customizable and configured by the user according to their needs. Additionally, Core Linux is also an official Linux distribution of the Architalia community.

# Installing Core Linux

**Core Linux** offers the possibility to run a raw installation of pure Arch Linux using the console in a comfortable and organized environment of useful tools, it is possible to import your own guides or scripts to be run through the live based on Arch Linux. To install the Core Linux version with its minimal but organized environment, you can use the **installcore** application from the menu, or simply type `installcore` in the console and start the installation process.

![Screenshot from 2023-07-27 16-09-50](https://github.com/ArchItalia/site/assets/117321045/1f56011f-757d-4ca0-9b65-1574fe0b1ce1)

follow the directions to configure the installation of Core Linux.

![Screenshot from 2023-07-27 16-10-03](https://github.com/ArchItalia/site/assets/117321045/444b36e2-9a8e-4868-a38f-b6d713cf8d9a)

# Configuring Core Linux

![image](https://github.com/ArchItalia/site/assets/117321045/95d8cd5c-1e1d-46da-af41-99462d521c4f)

**Core Linux** offers a customized configuration that already includes the presence of some useful packages such as **yay**, **timeshift**, and other packages maintained by its own repository [**ai-repo**](https://architalia.github.io/site/Download/ai-repo/), the graphical environment is a minimized version of **Gnome**, which provides basic extensions that allow for more convenient use, such as **Arch Linux updates indicator** which is already set up for managing official packages and AUR through the Core Linux **updates** script, **AppIndicator** to have the system tray function on the top bar, **Night Theme Switcher** to automatically switch from light mode to dark mode, **desktop-icons-ng** for desktop icons, many other functions and packages will be added with the evolution of the distribution.

The filesystem used is **btrfs** with the creation of the subvolumes **@** and **@home**, for the management of swap, **zram generator** is installed.
