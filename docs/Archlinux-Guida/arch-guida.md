# Installazione di Arch Linux

Guide all'installazione di Arch Linux: 

1.  [Verifica Firma](#1-verifica-firma)
2.  [Configurazione iniziale](#2-configurazione-iniziale)
3.  [Connessione Internet](#3-connessione-internet)
4.  [Preparazione del disco](#4-preparazione-del-disco)
5.  [Mirrorlist](#5-mirrorlist)
6.  [Pacstrap](#6-pacstrap)
7.  [Generare Fstab](#7-generare-fstab)
8.  [Chroot](#8-chroot)
9.  [Bootloader](#9-bootloader)
10. [Pacchetti Base](#10-pacchetti-base)
11. [Ambiente Grafico](#11-ambiente-grafico)
12. [Servizi](#12-servizi)
13. [zram](#13-zram)

<br><br><br><br>

## 1. Verifica Firma

Si consiglia di verificare la firma dell'immagine prima dell'uso, in particolare durante il download da un mirror HTTP, dove i download sono generalmente soggetti a essere intercettati per fornire immagini dannose.

Su un sistema con *GnuPG* installato, scarica la firma [ISO PGP](https://archlinux.org/download/#checksums)  nella directory ISO e verificala con:

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

In alternativa, da un'installazione esistente di Arch Linux eseguire:

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2. Configurazione iniziale

Per iniziare dobbiamo definire la lingua della tastiera, la lingua predefinita senza immettere il comando e' `US`. I layout disponibili possono essere elencati con: 

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

impostiamo la lingua della nostra tastiera con il comando:

`# loadkeys it`

I caratteri della console si trovano in **/usr/share/kbd/consolefonts/** e possono anche essere impostati con setfont. Ad esempio, per utilizzare uno dei caratteri più grandi adatti agli schermi HiDPI, eseguire:

`# setfont ter-132b`


<br><br><br><br>

## 3. Connessione Internet
Se avete connesso la machina a internet mediante cavo o macchina virtuale, possiamo verificare il nostro indirizzo ip acquisito attraverso questo comando :

`# ip a`

La connessione può essere verificata con un test tramite il comando ping:

`# ping -c 3 archlinux.org`

Collegarsi alla rete wifi utilizzando lo strumento iwctl:

- `# iwctl` Avviare iwctl
- `# device list`  Trova il nome del tuo dispositivo esempio wlan0
- `# station wlan0 scan` Scansiona le reti wireless disponibili
- `# station wlan0 get-networks` Acquisiamo la lista delle rete
- `# station wlan0` connect nometuarete  Connessione alla rete
- `# exit` 

Se nel caso in cui i nostri dispositivi siano disabilitati e non riusciamo a eseguire **iwctl**:

- `# rfkill list`  verifichiamo lo stato dei dispositivi blocked o unblocked
- `# rfkill unblock all` sblocchiamo tutti i nostri dispositivi bloccati blocked
- `# systemctl restart iwd`  riavviamo il servizio iwd

Riprovare `# iwctl` e procedere come sopra.


<br><br><br><br>

## 4. Preparazione del disco

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm EXT4](#uefi-lvm-ext4)


<br><br><br><br>

### Bios-MBR

#### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.

`# lsblk -l`

Una volta individuata la nomenclatura del nostro disco usiamo **cfdisk**, qui ipotizzeremo di avere **/dev/sda**. Potrebbe essere richiesto il tipo di tabella di partizionamento se il disco e' vergine, in questo caso andiamo a selezionare **DOS**:

`# cfdisk /dev/sda`

Creiamo le partizioni necessarie all'installazione base, ipotizzando di avere un disco **SSD** da **128GiB**:

- `# 4Gib`   Creiamo una partizione per la swap e selezioniamo tipo swap
- `# 124Gib`  Creiamo la partizione Root
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo


#### Formattare le Partizioni

- `# mkswap /dev/sda1` La partizione per la swap
- `# mkfs.ext4 /dev/sda2` La partizione Root in EXT4

#### Montaggio delle Partizioni

- `# mount /dev/sda2 /mnt` Montiamo la partizione root
- `# swapon /dev/sda1` Montiamo la partizione di swap


<br><br><br><br>

### UEFI ext4

#### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.

`# lsblk -l`

Una volta individuata la nomenclatura del nostro disco usiamo **cfdisk**, qui ipotizzeremo di avere **/dev/sda**. Potrebbe essere richiesto il tipo di tabella di partizionamento se il disco e' vergine, in questo caso andiamo a selezionare **GUID Partition Table (GPT)**:

`# cfdisk /dev/sda`

Creiamo le partizioni necessarie all'installazione base, ipotizzando di avere un disco **SSD** da **128GiB**:

- `# 512Mib`  Creiamo la partizione EFI e scegliamo di tipo EFI system
- `# 4Gib`   Creiamo una partizione per la swap e selezioniamo tipo swap
- `# 23.5Gib`  Creiamo la partizione Root
- `# 100Gib`  Creiamo la partizione Home
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

#### Formattare le Partizioni

- `# mkfs.vfat -F32 /dev/sda1` La partizione EFI system in FAT32 per il boot
- `# mkswap /dev/sda2` La partizione per la swap
- `# mkfs.ext4 /dev/sda3` La partizione Root in EXT4
- `# mkfs.ext4 /dev/sda4` La partizione Home in EXT4


#### Montaggio delle Partizioni

- `# mount /dev/sda3 /mnt` Montiamo la partizione root
- `# mkdir -p /mnt/{home,boot}` creiamo la directory /home e la directory /boot
- `# mount /dev/sda4 /mnt/home` Montiamo la partizione home
- `# mount /dev/sda1 /mnt/boot` Montiamo la partizione di boot
- `# swapon /dev/sda2` Montiamo la partizione di swap



<br><br><br><br>

### UEFI btrfs

#### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.

`# lsblk -l`

Una volta individuata la nomenclatura del nostro disco usiamo **cfdisk**, qui ipotizzeremo di avere **/dev/sda**. Potrebbe essere richiesto il tipo di tabella di partizionamento se il disco e' vergine, in questo caso andiamo a selezionare **GUID Partition Table (GPT)**:

`# cfdisk /dev/sda`

Creiamo le partizioni necessarie all'installazione base, ipotizzando di avere un disco **SSD** da **128GiB**:

- `# 512Mib`  Creiamo la partizione EFI e scegliamo di tipo EFI system
- `# 27.5Gib`  Creiamo la partizione Root
- `# 100Gib`  Creiamo la partizione Home
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

#### Formattare le Partizioni

- `# mkfs.vfat -F32 /dev/sda1` La partizione EFI system in FAT32 per il boot
- `# mkfs.btrfs /dev/sda2` La partizione Root in BTRFS
- `# mkfs.btrfs /dev/sda3` La partizione Home in BTRFS

#### Montaggio delle Partizioni 

Creiamo i sottovolumi **@** e **@home**

- `# mount /dev/sda2 /mnt`           

- `# btrfs su cr /mnt/@`  

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`      

- `# umount /mnt`                             

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt` 

- `# mkdir -p /mnt/{home,boot} creiamo la directory /home e la directory /boot`

- `# mount /dev/sda1 /mnt/boot` 

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.


`# lsblk -l`

Ipotizzando di avere **3 dischi da 128GiB**  da usare con **LVM** **sda sdb sdc** useremo lo strumento **cfdisk** un operazione alla volta per disco:


`# cfdifk /dev/sda`

- `# 512Mib` Creiamo la partizione EFI e scegliamo di tipo EFI system
- `# 127.5GiB` Creiamo la partizione e scegliamo di tipo LVM
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

- `# cfdifk /dev/sdb`
- `# 128GiB` Creiamo la partizione e scegliamo di tipo LVM
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

- `# cfdifk /dev/sdc`
- `# 128GiB` Creiamo la partizione e scegliamo di tipo LVM
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

Per creare partizioni sotto LVM dobbiamo prima creare un volume fisico:

#### Creazione volume fisico

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### Creazione gruppo volume
Crea e estendi il tuo gruppo volume, è necessario creare un gruppo di volumi su uno o piu' volumi fisici `# vgcreate volume_group physical_volume` per esempio:

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

Questo comando imposterà prima le tre partizioni come volumi fisici (se necessario) e quindi creerà il gruppo di volumi con i tre volumi. Il comando ti avviserà se rileva un filesystem esistente su qualsiasi dispositivo.

#### Creazione Volume Logici

Crea volumi logici, per una configurazione di base ne avremmo bisogno per root, swap e home.

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### Formattare le Partizioni

`# mkfs.vfat -F32 /dev/sda1` La partizione EFI system in FAT32 per il boot

- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 

#### Montaggio delle Partizioni 

- `# mount /dev/lvm/root /mnt`
- `# mkdir -p /mnt/{home,boot}` creiamo la directory /home e la directory /boot
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot` 
- `# swapon /dev/lvm/swap`

#### Estendere un gruppo lvm

Se in futuro vorrete aggiungere un nuovo volume fisico al gruppo vediamo quale comando usare, ipotizzando un quarto disco sdd e di averlo partizionato come prima di tipo lvm, estendiamo lo spazio per esempio a `/dev/lvm/home`:

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`


<br><br><br><br>

## 5. Mirrorlist

Salviamo il mirrorlist per i repositoy in **/etc/pacman.d/mirrorlist** con lo strumento **reflector**, specificare il paese dove sincronizzare i server ad esempio **it**, e' possibile aggiungere piu paesi usando la virgola ad esempio **it,us**:

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6. Pacstrap

Installiamo il **kernel linux** e i pacchetti base per creare il nostro arch, aggiungiamo anche un editor ad esempio **vim**, se stai seguendo l'installazione per **lvm** aggiungi al comando che segue il pacchetto `lvm2`:

`# pacstrap -K /mnt base base-devel linux linux-firmware vim` 

<br><br><br><br>



## 7. Generare Fstab

Il file /etc/fstab vi permette di controllare quali filesystem sono montati in fase di avvio sul vostro sistema Linux, comprese le partizioni di Windows e le condivisioni di rete:

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8. Chroot
 
Passiamo in chroot e configuriamo i seguenti passaggi: Configurazione del localtime, del systemclock, lingua, keyboard mappings, localhost, Password Root, Creazione User e password.

entriamo in chroot:

`# arch-chroot /mnt`

<br><br><br><br>


### Time zone

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>

### Localizzazione

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=it" >> /etc/vconsole.conf`

<br><br><br><br>


### Hostname e Hosts

- `# echo "NOMETUAMACCHINA" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

<br><br><br><br>


### Utente e Root

Configuriamo la password di Root, fai attenzione!

`# passwd`

Configuriamo un nuovo utente in minuscolo creando con `-m` la directory `/home/NOMEUTENTE`, con `-G` il gruppo `wheel` e infine con `-s` la shell:

`# useradd -mG wheel -s /bin/bash NOMEUTENTE`


Configuriamo il nome reale (quello che appare in grafica con l'iniziale maiuscola ad esempio **"Alessio"**) 

`# usermod -c 'NOMEREALE' NOMEUTENTE`

Configuriamo una password per l'utente appena aggiunto, fai attenzione!

`# passwd NOMEUTENTE`

Configuriamo il file sudoers per il gruppo wheel

`# echo "NOMEUTENTE ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/NOMEUTENTE`


<br><br><br><br>


### mkinitcpio per lvm

aggiungere **lvm2** a hooks in **/etc/mkinitcpio.conf**

`HOOKS="base udev ... block lvm2 filesystems"`

quindi usare il comando:

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

GRUB supporta completamente l'avvio protetto utilizzando chiavi CA o shim, tuttavia il comando di installazione è diverso a seconda di quale si intende utilizzare.

Per utilizzare le chiavi CA il comando è:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`


Per utilizzare shim-lock il comando è:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

<br><br><br><br>

### Systemd-boot (EXT4)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "deafult arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

adesso creiamo la configurazione del file **arch.conf** aperto con **vim**, e' importante scrivere la partizione di avvio della root corretta ad esempio `root=/dev/sdax` dove `x` sta per il numero di partizione della root.

- `title   Arch Linux`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/sdax rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>


### Systemd-boot (BTRFS)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "deafult arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

adesso creiamo la configurazione del file **arch.conf** aperto con **vim**, e' importante scrivere la partizione di avvio della root corretta ad esempio `root=/dev/sdax` dove `x` sta per il numero di partizione della root, aggiungiamo il flag per il sottovolume **@**.

- `title   Arch Linux`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/sdax rootflags=subvol=@ rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

### Systemd-boot (LVM)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "deafult arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

adesso creiamo la configurazione del file **arch.conf** aperto con **vim**, e' importante scrivere la partizione di avvio della root corretta ad esempio per **lvm** `root=/dev/mapper/lvm-root`.

- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

## 10. Pacchetti Base

`# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`

<br><br><br><br>


## 11. Ambiente Grafico

Scegli tra alcuni dei piu famosi ambienti desktop suggeriti:

### Gnome
Gnome completo con display manager GDM

- `# pacman -S gnome gnome-extra gdm` 
- `# systemctl enable gdm`

### Xfce4
xfce4 con display manager Lightdm

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

### lxde
lxde con display manger lightdm

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

### Mate
mate con display manger lightdm

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

### Plasma
plasma kde con display manager SDDM

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

### Cinnamon
cinnamon con display manager Lightdm

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`


<br><br><br><br>


## 12. Servizi

Se hai abilitato il servizio per il display manager puoi passare ad abilitare gli altri servizi necessari.

- `# systemctl enable NetworkManager` Fai attenzione e' case sensitive.
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

<br><br><br><br>

## 13. Zram

L'esempio seguente descrive come configurare lo scambio su *zram* automaticamente all'avvio con una singola regola *udev*. Non dovrebbe essere necessario alcun pacchetto aggiuntivo per farlo funzionare.

Carica esplicitamente il modulo all'avvio:

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

Crea la seguente regola *udev* regolando l'attributo disksize come necessario per la misura della swap in questo esempio e' *16G*:

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k" , TAG+="systemd"`

Aggiungi **/dev/zram** al tuo fstab con una priorità superiore a quella predefinita:

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0 `

<br><br><br><br>










