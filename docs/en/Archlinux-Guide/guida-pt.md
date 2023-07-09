# Instalação do Arch Linux


## 1. Verificação de Assinatura

É recomendável verificar a assinatura da imagem antes de usá-la, especialmente ao fazer o download de um espelho HTTP, onde os downloads estão sujeitos à interceptação para fornecer imagens maliciosas.

Em um sistema com *GnuPG* instalado, baixe a [assinatura PGP da ISO](https://archlinux.org/download/#checksums) para o diretório ISO e verifique-a com o seguinte comando:

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

Alternativamente, a partir de uma instalação existente do Arch Linux, execute:

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2. Configuração Inicial

Para começar, precisamos definir o idioma do teclado, o idioma padrão sem digitar o comando é `US`. As disposições disponíveis podem ser listadas com:

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

defina o idioma do teclado com o comando:

`# loadkeys it`

Os caracteres do console podem ser encontrados em **/usr/share/kbd/consolefonts/** e também podem ser definidos com o comando setfont. Por exemplo, para usar caracteres maiores adequados para telas de HiDPI, execute:

`# setfont ter-132b`


<br><br><br><br>

## 3. Conexão com a Internet
Se você conectou a máquina à Internet via cabo ou máquina virtual, pode verificar o endereço IP adquirido usando este comando:

`# ip a`

A conexão pode ser testada com um comando de teste de ping:

`# ping -c 3 archlinux.org`

Conecte-se à rede Wi-Fi usando a ferramenta iwctl:

- `# iwctl` Inicie o iwctl
- `# device list` Encontre o nome do seu dispositivo, por exemplo, wlan0
- `# station wlan0 scan` Verifique as redes sem fio disponíveis
- `# station wlan0 get-networks` Obtenha a lista de redes
- `# station wlan0` connect yournetworkname Conecte-se à sua rede
- `# exit`

Se, no caso, nossos dispositivos estiverem desativados e não conseguirmos executar o **iwctl**:

- `# rfkill list` Verifique o status bloqueado ou desbloqueado dos dispositivos
- `# rfkill unblock all` Desbloqueie todos os nossos dispositivos bloqueados
- `# systemctl restart iwd` Reinicie o serviço iwd

Tente novamente `# iwctl` e continue como acima.


<br><br><br><br>

## 4. Preparação do disco

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm EXT4](#uefi-lvm-ext4)


<br><br><br><br>

### BIOS-MBR

#### Particionamento
Identifique seu disco para saber qual convenção de nomenclatura usar. Por exemplo, no caso de um **SSD /dev/sda** ou no caso de **M.2 /dev/nvme0n1** e, finalmente, o **disco virtual /dev/vda**.

`# lsblk -l`

Assim que a nomenclatura de nosso disco for identificada, use o **cfdisk**, aqui assumiremos ter **/dev/sda**. Você pode ser solicitado o tipo de tabela de partição se o disco for bruto. Nesse caso, selecione **DOS**:

`# cfdisk /dev/sda`

Crie as partições necessárias para a instalação básica, assumindo que temos um **SSD de 128GiB**:

- `# 4Gib`   Crie uma partição para swap e selecione o tipo de swap
- `# 124Gib`  Crie a partição raiz
- `# write (yes)` and `quit`  Escreva as alterações e saia


#### Formatação de partições

- `# mkswap /dev/sda1` Partição de swap
- `# mkfs.ext4 /dev/sda2` Partição raiz em EXT4

#### Montagem de partições

- `# mount /dev/sda2 /mnt` Monte a partição raiz
- `# swapon /dev/sda1` Monte a partição de swap


<br><br><br><br>

### UEFI ext4

#### Particionamento de disco
Identifique seu disco para saber qual convenção de nomenclatura usar. Por exemplo, no caso de um **SSD /dev/sda** ou no caso de **M.2 /dev/nvme0n1** e, finalmente, o **disco virtual /dev/vda**.

`# lsblk -l`

Assumindo que temos um **SSD de 128GiB** e usaremos o particionamento GPT para a instalação UEFI:

`# cfdisk /dev/sda`

- `# 512Mib` Crie uma partição EFI e selecione o tipo de partição do sistema EFI
- `# 4Gib`   Crie uma partição para swap e selecione o tipo de swap
- `# 23.5Gib`  Crie a partição raiz
- `# 100Gib`  Crie a partição Home
- `# write (yes)` e `quit`  Escreva as alterações e saia


#### Formatação de partições

- `# mkfs.vfat -F32 /dev/sda1` Partição do sistema EFI em FAT32 para inicialização
- `# mkswap /dev/sda2` Partição de swap
- `# mkfs.ext4 /dev/sda3` Partição raiz em EXT4
- `# mkfs.ext4 /dev/sda4` Partição Home em EXT4


#### Montagem de partições

- `# mount /dev/sda3 /mnt` Monte a partição raiz
- `# mkdir -p /mnt/{home,boot}` Crie os diretórios /home e /boot
- `# mount /dev/sda4 /mnt/home` Monte a partição Home
- `# mount /dev/sda1 /mnt/boot` Monte a partição de inicialização
- `# swapon /dev/sda2` Monte a partição de swap


<br><br><br><br>

### UEFI btrfs

#### Particionamento de disco
Identifique seu disco para saber qual convenção de nomenclatura usar. Por exemplo, no caso de um **SSD /dev/sda** ou no caso de **M.2 /dev/nvme0n1** e, finalmente, o **disco virtual /dev/vda**.

`# lsblk -l`

Assumindo que temos um **SSD de 128GiB** e usaremos o particionamento GPT para a instalação UEFI:

`# cfdisk /dev/sda`

- `# 512Mib` Crie uma partição EFI e selecione o tipo de partição do sistema EFI
- `# 27.5Gib`  Crie a partição raiz
- `# 100Gib`  Crie a partição Home
- `# write (yes)` e `quit`  Escreva as alterações e saia


#### Formatação de partições

- `# mkfs.vfat -F32 /dev/sda1` Partição do sistema EFI em FAT32 para inicialização
- `# mkfs.btrfs /dev/sda2` Partição raiz em BTRFS
- `# mkfs.btrfs /dev/sda3` Partição Home em BTRFS


#### Montagem de partições 

Crie os subvolumes **@** e **@home**:

- `# mount /dev/sda2 /mnt`

- `# btrfs su cr /mnt/@`

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`

- `# umount /mnt`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt`

- `# mkdir -p /mnt/{home,boot}` Crie os diretórios /home e /boot
 
- `# mount /dev/sda1 /mnt/boot`
 
- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### Particionamento de disco
Identifique seu disco para saber qual convenção de nomenclatura usar. Por exemplo, no caso de um **SSD /dev/sda** ou no caso de **M.2 /dev/nvme0n1** e, finalmente, o **disco virtual /dev/vda**.

`# lsblk -l`

Assumindo que temos **3 discos de 128GiB** para LVM: **sda sdb sdc** use o **cfdisk** para um disco de cada vez:

`# cfdifk /dev/sda`

- `# 512Mib` Crie uma partição EFI e selecione o tipo de partição do sistema EFI
- `# 127.5GiB` Crie uma partição e selecione o tipo LVM
- `# write (yes)` e `quit`  Escreva as alterações e saia

`# cfdifk /dev/sdb`

- `# 128GiB` Crie uma partição e selecione o tipo LVM
- `# write (yes)` e `quit`  Escreva as alterações e saia

`# cfdifk /dev/sdc`

- `# 128GiB`  Crie uma partição e selecione o tipo LVM
- `# write (yes)` e `quit`  Escreva as alterações e saia

Para criar partições em LVM, precisamos primeiro criar um volume físico:

#### Crie o volume físico

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### Crie o grupo de volume



Crie e estenda seu grupo de volume; você precisa criar um grupo de volume em um ou mais volumes físicos `# vgcreate grupo_de_volume volume_físico` por exemplo:

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

Este comando primeiro configurará as três partições como volumes físicos (se necessário) e, em seguida, criará o grupo de volumes com os três volumes. O comando o alertará se detectar um sistema de arquivos existente em qualquer dispositivo.

#### Crie volumes lógicos

Crie volumes lógicos; para uma configuração básica, precisaríamos de um para raiz, swap e home.

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### Formatação de partições

- `# mkfs.vfat -F32 /dev/sda1` Partição do sistema EFI em FAT32 para inicialização
- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 


#### Montagem de partições 

- `# mount /dev/lvm/root /mnt` Monte a partição raiz
- `# mkdir -p /mnt/{home,boot}` Crie os diretórios /home e /boot
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot` 
- `# swapon /dev/lvm/swap`

#### Estender um grupo LVM

Se no futuro você quiser adicionar um novo volume físico ao grupo, veja qual comando usar, supondo um quarto disco sdd e tendo particionado como antes, estendemos o espaço, por exemplo, para `/dev/lvm/home`:

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`

## 5. Lista de servidores espelho

Salve a lista de servidores para os repositórios em **/etc/pacman.d/mirrorlist** usando a ferramenta **reflector**, especificando o país para sincronizar os servidores, por exemplo **it**. Vários países podem ser adicionados usando uma vírgula, por exemplo, **it,us**:

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6. Pacstrap

Instale o **kernel Linux** e pacotes base para criar nosso sistema Arch, adicione também um editor, como **vim**. Se estiver seguindo a instalação do **lvm**, adicione o pacote `lvm2` ao comando a seguir:

`# pacstrap -K /mnt base base-devel linux linux-firmware vim` 

<br><br><br><br>



## 7. Gerar Fstab

O arquivo /etc/fstab permite controlar quais sistemas de arquivos são montados em seu sistema Linux durante a inicialização, incluindo as partições do Windows e os compartilhamentos de rede:

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8. Chroot
 
Entre no chroot e configure as etapas a seguir: configuração de hora local, relógio de sistema, idioma, mapeamentos de teclado, nome do host, senha do root, criação de usuário e senha.

Entre no chroot:

`# arch-chroot /mnt`

<br><br><br><br>


### Fuso horário

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>

### Localização

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=it" >> /etc/vconsole.conf`

<br><br><br><br>


### Nome de host e Hosts

- `# echo "NOMEDAMAQUINA" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

<br><br><br><br>


### Usuário e Root

Configure a senha do Root, tenha cuidado!

`# passwd`

Configure um novo usuário em minúsculas, criando o diretório `/home/USUARIO` com `-m`, o grupo `wheel` com `-G` e o shell com `-s`:

`# useradd -mG wheel -s /bin/bash USUARIO`

Configure o nome real (que aparece em gráficos com letra inicial maiúscula por exemplo **"Alessio"**)

`# usermod -c 'NOMEREAL' USUARIO`

Configure uma senha para o usuário recém-adicionado, tenha cuidado!

`# passwd USUARIO`

Configure o arquivo sudoers para o grupo wheel:

`# echo "USUARIO ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/USUARIO`


<br><br><br><br>

### mkinitcpio para lvm

adicionar o **lvm2** em **hooks** no arquivo **/etc/mkinitcpio.conf**

`HOOKS="base udev ... block lvm2 filesystems"`

em seguida, usar o comando:

`# mkinitcpio -p linux`


<br><br><br><br>


## 9. Carregador de boot

### GRUB (Bios-MBR)

- `# pacman -S grub`
- `# grub-install --target=i386-pc /dev/sda`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

<br><br><br><br>


### GRUB (UEFI)

- `# pacman -S grub`
- `# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

O GRUB suporta totalmente o secure boot usando CA keys ou shim, no entanto, o comando de instalação é diferente dependendo de qual for usar.

Para usar as CA keys, o comando é:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`


Para usar o shim-lock, o comando é:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

<br><br><br><br>

### Systemd-boot (EXT4)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

Agora, crie a configuração do arquivo **arch.conf** que foi aberto com **vim**, é importante escrever a partição de inicialização raiz correta como `root=/dev/sdax` onde `x` é o número da partição raiz.

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

Agora, crie a configuração do arquivo **arch.conf** que foi aberto com **vim**, é importante escrever a partição de inicialização raiz correta como `root=/dev/sdax` onde `x` é o número da partição raiz, adicione a bandeira para o subvolume **@**.

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

Agora, crie a configuração do arquivo **arch.conf** que foi aberto com **vim**, é importante escrever a partição de inicialização raiz correta como para o **lvm** `root=/dev/mapper/lvm-root`

- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

## 10. Pacotes Básicos

`# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`

<br><br><br><br>


## 11. Ambiente de Área de Trabalho

Escolha alguns dos ambientes de área de trabalho populares sugeridos:

### Gnome
Gnome completo com o gerenciador de exibição GDM

- `# pacman -S gnome gnome-extra gdm` 
- `# systemctl enable gdm`

### Xfce4
xfce4 com o gerenciador de exibição Lightdm

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

### Lxde
lxde com o gerenciador de exibição Lightdm

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

### Mate
mate com o gerenciador de exibição Lightdm

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

### Plasma
plasma kde com o gerenciador de exibição SDDM

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

### Cinnamon
cinnamon com o gerenciador de exibição Lightdm

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`


<br><br><br><br>


## 12. Serviços

Se você tiver habilitado o serviço para o gerenciador de exibição, pode passar para a ativação de outros serviços necessários.

- `# systemctl enable NetworkManager` Tenha cuidado, o comando diferencia maiúsculas e minúsculas.
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

<br><br><br><br>

## 13. Zram

O exemplo a seguir descreve como configurar a troca automática para *zram* na inicialização usando uma única regra *udev*. Nenhum pacote adicional é necessário para fazê-lo funcionar.

Carregar explicitamente o módulo na inicialização:

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

Crie a seguinte regra *udev* ajustando o atributo disksize conforme necessário para o tamanho da troca, neste exemplo é *16G*:

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k", TAG+="systemd"`

Adicione **/dev/zram** ao seu **fstab** com uma prioridade mais alta que o padrão:

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0 `

<br><br><br><br>
