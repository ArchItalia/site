Instalación de Arch Linux


## 1. Verificación de firma

Se recomienda verificar la firma de la imagen antes de usarla, especialmente al descargar desde un espejo HTTP, donde las descargas están sujetas a interceptación para proporcionar imágenes dañinas.

En un sistema con *GnuPG* instalado, descargue la [firma de ISO PGP](https://archlinux.org/download/#checksums) en el directorio de ISO y verifíquela con:

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

Alternativamente, desde una instalación existente de Arch Linux ejecute:

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2. Configuración inicial

Para empezar, necesitamos definir el idioma del teclado, el idioma predeterminado sin introducir el comando es `US`. Las distribuciones disponibles se pueden listar con:

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

establezca el idioma de nuestro teclado con el comando:

`# loadkeys it`

Los caracteres de la consola se pueden encontrar en **/usr/share/kbd/consolefonts/** y también se pueden establecer con setfont. Por ejemplo, para usar uno de los caracteres más grandes adecuados para pantallas HiDPI, ejecute:

`# setfont ter-132b`


<br><br><br><br>

## 3. Conexión a internet

Si ha conectado la máquina a internet mediante cable o máquina virtual, puede verificar la dirección IP adquirida con este comando:

`# ip a`

La conexión se puede probar con un comando de prueba de ping:

`# ping -c 3 archlinux.org`

Conéctese a la red Wi-Fi utilizando la herramienta iwctl:

- `# iwctl` Iniciar iwctl
- `# device list` Busque el nombre de su dispositivo, por ejemplo, wlan0
- `# station wlan0 scan` Buscar redes inalámbricas disponibles
- `# station wlan0 get-networks` Obtener la lista de redes
- `# station wlan0` connect yournetworkname  Conexión con su red
- `# exit`

Si en caso de que nuestros dispositivos estén deshabilitados y no podamos ejecutar **iwctl **:

- `# rfkill list` Verifique el estado bloqueado o desbloqueado de los dispositivos
- `# rfkill unblock all` Desbloquee todos nuestros dispositivos bloqueados
- `# systemctl restart iwd` Reiniciar el servicio iwd

Intente `# iwctl` de nuevo y proceda como se indica arriba.


<br><br><br><br>

## 4. Preparación del disco

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm EXT4](#uefi-lvm-ext4)


<br><br><br><br>

### Bios-MBR

#### Particionado

Identifique su disco para conocer la convención de nomenclatura que se utilizará. Por ejemplo, en el caso de una **SSD /dev/sda** o en el caso de **M.2 /dev/nvme0n1** y, finalmente, el **disco virtual /dev/vda**.

`# lsblk -l`

Una vez identificada la nomenclatura de nuestro disco, use **cfdisk**, aquí asumiremos tener **/dev/sda**. Se puede solicitar el tipo de tabla de particiones si el disco es crudo. En este caso, seleccione **DOS**:

`# cfdisk /dev/sda`

Cree las particiones necesarias para la instalación básica, suponiendo que tenemos un **SSD de 128 GiB**:

- `# 4Gib`   Crear una partición para swap y seleccione el tipo de swap
- `# 124Gib`  Crear la partición de Root
- `# write (yes)` y `quit`  Escriba los cambios y salga


#### Formateo de particiones

- `# mkswap /dev/sda1` Partición de swap
- `# mkfs.ext4 /dev/sda2` Partición de Root en EXT4

#### Montaje de particiones

- `# mount /dev/sda2 /mnt` Montar la partición de Root
- `# swapon /dev/sda1` Montar la partición de swap


<br><br><br><br>

### UEFI ext4

#### Particionado del disco
Identifique su disco para conocer la convención de nomenclatura que se utilizará. Por ejemplo, en el caso de un **SSD /dev/sda** o en el caso de **M.2 /dev/nvme0n1** y, finalmente, el **disco virtual /dev/vda**.

`# lsblk -l`

Suponiendo que tenemos una **SSD de 128 GiB** y usaremos la partición GPT para la instalación UEFI:

`# cfdisk /dev/sda`

- `# 512Mib` Crear una partición EFI y seleccione el tipo de partición de sistema EFI
- `# 4Gib`   Crear una partición para swap y seleccione el tipo de swap
- `# 23.5Gib`  Cree la partición Root
- `# 100Gib`  Cree la partición de Home
- `# write (yes)` y `quit` Escriba los cambios y salga


#### Formateo de particiones

- `# mkfs.vfat -F32 /dev/sda1` Partición del sistema EFI en FAT32 para el arranque
- `# mkswap /dev/sda2` Partición de swap
- `# mkfs.ext4 /dev/sda3` Partición de Root en EXT4
- `# mkfs.ext4 /dev/sda4` Partición de Home en EXT4


#### Montaje de particiones

- `# mount /dev/sda3 /mnt` Montar la partición de Root
- `# mkdir -p /mnt/{home,boot}` Cree los directorios /home y /boot
- `# mount /dev/sda4 /mnt/home` Montar la partición de Home
- `# mount /dev/sda1 /mnt/boot` Montar partición de arranque
- `# swapon /dev/sda2` Montar la partición de swap


<br><br><br><br>

### UEFI btrfs

#### Particionado del disco
Identifique su disco para conocer la convención de nomenclatura que se utilizará. Por ejemplo, en el caso de un **SSD /dev/sda** o en el caso de **M.2 /dev/nvme0n1** y, finalmente, el **disco virtual /dev/vda**.

`# lsblk -l`

Suponiendo que tenemos una **SSD de 128 GiB** y usaremos la partición GPT para la instalación UEFI:

`# cfdisk /dev/sda`

- `# 512Mib` Crear una partición EFI y seleccione el tipo de partición de sistema EFI
- `# 27.5Gib`  Cree la partición Root
- `# 100Gib`  Cree la partición de Home
- `# write (yes)` y `quit` Escriba los cambios y salga


#### Formateo de particiones

- `# mkfs.vfat -F32 /dev/sda1` Partición del sistema EFI en FAT32 para el arranque
- `# mkfs.btrfs /dev/sda2` Partición de Root en BTRFS
- `# mkfs.btrfs /dev/sda3` Partición de Home en BTRFS


#### Montaje de particiones 

Cree subvolúmenes **@** y **@home**:

- `# mount /dev/sda2 /mnt`

- `# btrfs su cr /mnt/@`

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`

- `# umount /mnt`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt`

- `# mkdir -p /mnt/{home,boot}` Cree los directorios /home y /boot
- `# mount /dev/sda1 /mnt/boot`
- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### Partición de disco
Identifique su disco para conocer la convención de nomenclatura que se utilizará. Por ejemplo, en el caso de una **SSD /dev/sda** o en el caso de **M.2 /dev/nvme0n1** y, finalmente, el **disco virtual /dev/vda**.


`# lsblk -l`

Suponiendo que tenemos **3 discos de 128 GiB** para LVM: **sda sdb sdc** use **cfdisk** para un disco a la vez:

`# cfdifk /dev/sda`

- `# 512Mib` Crear una partición EFI y seleccione el tipo de partición de sistema EFI
- `# 127.5GiB` Cree una partición y seleccione el tipo de LVM
- `# write (yes)` y `quit` Escriba los cambios y salga

`# cfdifk /dev/sdb`

- `# 128GiB` Cree una partición y seleccione el tipo de LVM
- `# write (yes)` y `quit` Escriba los cambios y salga

`# cfdifk /dev/sdc`

- `# 128GiB`  Cree una partición y seleccione el tipo de LVM
- `# write (yes)` y `quit` Escriba los cambios y salga

Para crear particiones en LVM, primero necesitamos crear un volumen físico:

#### Crear volumen físico

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### Crear grupo de volúmenes
Cree y extienda su grupo de volúmenes; debe crear un grupo de volúmenes en uno o más volúmenes físicos `# vgcreate volume_group physical_volume` por ejemplo:

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

Este comando primero configurará las tres particiones como volúmenes físicos (si es necesario) y luego creará el grupo de volúmenes con los tres volúmenes. El comando le alertará si detecta un sistema de archivos existente en cualquier dispositivo.

#### Crear volúmenes lógicos

Cree volúmenes lógicos, para una configuración básica, necesitaríamos uno para Root, swap y home.

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### Formateo de particiones

- `# mkfs.vfat -F32 /dev/sda1` Partición del sistema EFI en FAT32 para el arranque
- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 


#### Montaje de particiones 

- `# mount /dev/lvm/root /mnt` Montar la partición de Root
- `# mkdir -p /mnt/{home,boot}` Cree los directorios /home y /boot
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot` 
- `# swapon /dev/lvm/swap`

#### Extender un grupo LVM

Si en el futuro desea agregar un nuevo volumen físico al grupo, vea qué comando debe usar, asumiendo un cuarto disco sdd y habiéndolo particionado como antes, ampliamos el espacio por ejemplo a `/dev/lvm/home`:

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`

## 5. Lista de espejos

Guarde la lista de espejos para los repositorios en **/etc/pacman.d/mirrorlist** utilizando la herramienta **reflector**, especificando el país para sincronizar los servidores, por ejemplo **it**. Se pueden agregar varios países separándolos con una coma, por ejemplo, **it,us**:

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6. Pacstrap

Instale el kernel **linux** y los paquetes base para crear nuestro sistema Arch, también agregue un editor como **vim**. Si sigue la instalación para **lvm**, agregue el paquete `lvm2` al siguiente comando:

`# pacstrap -K /mnt base base-devel linux linux-firmware vim` 

<br><br><br><br>



## 7. Generar Fstab

El archivo /etc/fstab le permite controlar qué sistemas de archivos se montan en su sistema Linux durante el inicio, incluidas las particiones de Windows y las compartidas en la red:

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8. Chroot
 
Ingrese al chroot y configure los siguientes pasos: Configuración de la hora local, systemclock, idioma, asignaciones de teclado, localhost, contraseña de root, creación de usuario y contraseña.

Ingrese al chroot:

`# arch-chroot /mnt`

<br><br><br><br>


### Zona horaria

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>

### Configuración regional

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=it" >> /etc/vconsole.conf`

<br><br><br><br>


### Hostname y Hosts

- `# echo "YOURMACHINENAME" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

<br><br><br><br>


### Usuario y Root

Configure la contraseña de Root, ¡tenga cuidado!

`# passwd`

Configure un nuevo usuario en minúsculas, creando el directorio `/home/USERNAME` con `-m`, el grupo `wheel` con `-G` y la shell con `-s`:

`# useradd -mG wheel -s /bin/bash USERNAME`

Configure el nombre real (que aparece en gráficos con letra inicial en mayúscula, por ejemplo **"Alessio"**) 

`# usermod -c 'NOMBREREAL' USERNAME`

Configure una contraseña para el usuario recién agregado, ¡tenga cuidado!

`# passwd USERNAME`

Configure el archivo sudoers para el grupo wheel:

`# echo "USERNAME ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/USERNAME`

<br><br><br><br>

### mkinitcpio para LVM

Agregar **lvm2** en los hooks de **/etc/mkinitcpio.conf**

`HOOKS="base udev ... block lvm2 filesystems"`

luego usar el comando:

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

GRUB admite completamente el arranque seguro usando claves CA o shim, sin embargo, el comando de instalación es diferente dependiendo de cual se pretenda utilizar.

Para usar claves CA, el comando es:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`


Para usar shim-lock, el comando es:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

<br><br><br><br>

### Systemd-boot (EXT4)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

Ahora cree la configuración del archivo **arch.conf** abierto con **vim**, es importante escribir la partición de arranque raíz correctamente, como `root=/dev/sdax` donde `x` es el número de la partición raíz.

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

Ahora cree la configuración del archivo **arch.conf** abierto con **vim**, es importante escribir la partición de arranque raíz correctamente, como `root=/dev/sdax` donde `x` es el número de la partición raíz, agregar la bandera para el subvolumen **@**.

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

Ahora cree la configuración del archivo **arch.conf** abierto con **vim**, es importante escribir la partición de arranque raíz correctamente, como para **lvm** `root=/dev/mapper/lvm-root`

- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

## 10. Paquetes Base

`# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`

<br><br><br><br>


## 11. Entorno de Escritorio

Elija entre algunos de los entornos de escritorio populares sugeridos:

### Gnome
Gnome completo con el gestor de pantallas GDM

- `# pacman -S gnome gnome-extra gdm` 
- `# systemctl enable gdm`

### Xfce4
Xfce4 con el gestor de pantallas Lightdm

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

### Lxde
Lxde con el gestor de pantallas Lightdm

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

### Mate
Mate con el gestor de pantallas Lightdm

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

### Plasma
Plasma kde con el gestor de pantallas SDDM

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

### Cinnamon
Cinnamon con el gestor de pantallas Lightdm

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`


<br><br><br><br>


## 12. Servicios

Si ha habilitado el servicio para el gestor de pantalla, puede pasar a habilitar los demás servicios necesarios.

- `# systemctl enable NetworkManager` Tenga cuidado, es sensible a mayúsculas.
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

<br><br><br><br>

## 13. Zram

El siguiente ejemplo describe cómo configurar el intercambio automático a *zram* en el arranque mediante una única regla *udev*. No deberían ser necesarios paquetes adicionales para que esto funcione.

Cargue explícitamente el módulo en el arranque:

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

Cree la siguiente regla *udev* ajustando el atributo disksize según se requiera para el tamaño del swap, en este ejemplo es *16G*:

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k", TAG+="systemd"`

Agregue **/dev/zram** a fstab con una prioridad más alta que la predeterminada:

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0 `

<br><br><br><br>
