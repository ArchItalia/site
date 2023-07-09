把 Arch Linux 安装指南翻译成中文：

# Arch Linux安装指南


## 1. 签名验证

建议在使用镜像之前验证镜像的签名，尤其是从HTTP镜像下载时，下载可能会被拦截以提供有害镜像。

在已安装GnuPG的系统上，下载 [PGP ISO signature](https://archlinux.org/download/#checksums) 到ISO目录，并用以下命令验证：

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

或者在已有Arch Linux安装中运行：

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2. 初始配置

首先，我们需要定义键盘语言，默认语言是 `US`。可以使用以下命令列出可用的布局：

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

使用以下命令设置键盘语言：

`# loadkeys it`

可以在 **/usr/share/kbd/consolefonts/** 中找到控制台字符，并可以使用setfont设置字符。例如，要使用适用于HiDPI显示器的较大字符之一，请运行：

`# setfont ter-132b`


<br><br><br><br>

## 3. 网络连接

如果您已通过电缆或虚拟机将机器连接到互联网，可以使用以下命令验证已获取的IP地址：

`# ip a`

可以使用 ping 测试命令测试连接：

`# ping -c 3 archlinux.org`

使用 iwctl 工具连接到WiFi网络：

- `# iwctl` 启动iwctl工具
- `# device list` 找到您的设备名称，例如 wlan0
- `# station wlan0 scan` 扫描可用的无线网络
- `# station wlan0 get-networks` 获取网络列表
- `# station wlan0` connect yournetworkname 连接网络
- `# exit`

如果我们的设备已禁用并且无法运行 ** iwctl **：

- `# rfkill list` 检查设备的阻止或解除阻止状态
- `# rfkill unblock all` 解除所有被阻止的设备
- `# systemctl restart iwd` 重新启动iwd服务

重新尝试 `# iwctl` 命令并执行上述操作。


<br><br><br><br>

## 4. 磁盘准备

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm EXT4](#uefi-lvm-ext4)


<br><br><br><br>

### Bios-MBR

#### 分区
确定您的磁盘以了解要使用的命名约定，例如，在 **SSD /dev/sda** 或在 **M.2 /dev/nvme0n1** 的情况下，最终是 **virtual disk /dev/vda**。

`# lsblk -l`

确定我们的磁盘名称后，使用 **cfdisk**，这里我们假设拥有 **128GiB SSD**。如果磁盘是原始的，则可能会询问分区表类型。在这种情况下，选择 **DOS**：

`# cfdisk /dev/sda`

为基础安装创建所需的分区，假设我们有一个 **128GiB SSD**：

- `# 4Gib` 为交换分区创建一个分区并选择交换类型
- `# 124Gib` 创建根目录分区
- `# 写入（是）` 和 `# 退出` 写入更改并退出


#### 格式化分区

- `# mkswap /dev/sda1` 交换分区
- `# mkfs.ext4 /dev/sda2` 根分区在EXT4中

#### 挂载分区

- `# mount /dev/sda2 /mnt` 挂载根分区
- `# swapon /dev/sda1` 挂载交换分区


<br><br><br><br>

### UEFI ext4

#### 磁盘分区
确定您的磁盘以了解要使用的命名约定，例如，在 **SSD /dev/sda** 或在 **M.2 /dev/nvme0n1** 的情况下，最终是 **Virtual Disk /dev/vda**。

`# lsblk -l`

假设我们有 **128GiB SSD** 并将使用 GPT 分区进行 UEFI 安装：

`# cfdisk /dev/sda`

- `# 512Mib` 创建 EFI 分区并选择 EFI 系统分区类型
- `# 4Gib` 为交换分区创建一个分区并选择交换类型
- `# 23.5Gib` 创建根分区
- `# 100Gib` 创建主目录分区
- `# 写入（是）`和 `# 退出` 写入更改并退出


#### 格式化分区

- `# mkfs.vfat -F32 /dev/sda1` 用于引导的FAT32文件系统上的EFI系统分区
- `# mkswap /dev/sda2` 交换分区
- `# mkfs.ext4 /dev/sda3` 根分区在EXT4中
- `# mkfs.ext4 /dev/sda4` 主目录在EXT4中


#### 挂载分区

- `# mount /dev/sda3 /mnt` 挂载根分区
- `# mkdir -p /mnt/{home,boot}` 创建 /home 和 /boot 目录
- `# mount /dev/sda4 /mnt/home` 挂载主目录分区
- `# mount /dev/sda1 /mnt/boot` 挂载引导分区
- `# swapon /dev/sda2` 挂载交换分区


<br><br><br><br>

### UEFI btrfs

#### 磁盘分区
确定您的磁盘以了解要使用的命名约定，例如，在 **SSD /dev/sda** 或在 **M.2 /dev/nvme0n1** 的情况下，最终是 **Virtual Disk /dev/vda**。

`# lsblk -l`

假设我们有 **128GiB SSD** 并将使用 GPT 分区进行 UEFI 安装：

`# cfdisk /dev/sda`

- `# 512Mib` 创建 EFI 分区并选择 EFI 系统分区类型
- `# 27.5Gib` 创建根分区
- `# 100Gib` 创建主目录分区
- `# 写入（是）`和 `# 退出` 写入更改并退出


#### 格式化分区

- `# mkfs.vfat -F32 /dev/sda1` 用于引导的FAT32文件系统上的EFI系统分区
- `# mkfs.btrfs /dev/sda2` 根分区在BTRFS中
- `# mkfs.btrfs /dev/sda3` 主目录在BTRFS中


#### 挂载分区

创建 **@** 和 **@home** 子卷：

- `# mount /dev/sda2 /mnt`

- `# btrfs su cr /mnt/@`

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`

- `# umount /mnt`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt`

- `# mkdir -p /mnt/{home,boot}` 创建 /home 和 /boot 目录

- `# mount /dev/sda1 /mnt/boot`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### 磁盘分区
确定您的磁盘以了解要使用的命名约定，例如，在 **SSD /dev/sda** 或在 **M.2 /dev/nvme0n1** 的情况下，最终是 **Virtual Disk /dev/vda**。


`# lsblk -l`

假设我们有3个**128GiB的磁盘**用于LVM：**sda sdb sdc**，则需要逐个磁盘使用 **cfdisk** ：

`# cfdifk /dev/sda`

- `# 512Mib` 创建 EFI 分区并选择 EFI 系统分区类型
- `# 127.5GiB` 创建一个分区并选择LVM类型
- `# 写入（是）`和 `# 退出` 写入更改并退出

`# cfdifk /dev/sdb`

- `# 128GiB` 创建一个分区并选择LVM类型
- `# 写入（是）`和 `# 退出` 写入更改并退出

`# cfdifk /dev/sdc`

- `# 128GiB` 创建一个分区并选择LVM类型
- `# 写入（是）`和 `# 退出` 写入更改并退出

为了在 LVM 中创建分区，我们需要首先创建一个物理卷：

#### 创建物理卷

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### 创建卷组
创建和扩展您的卷组；您需要在一个或多个物理卷上创建一个卷组 `# vgcreate volume_group physical_volume`，例如：

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

此命令将首先将三个分区设置为物理卷（如果需要），然后使用三个卷创建卷组。如果检测到任何设备上存在文件系统，该命令将向您发出警报。

#### 创建逻辑卷

为了基本的配置，我们需要为根、交换和主目录各创建一个逻辑卷。

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### 格式化分区

- `# mkfs.vfat -F32 /dev/sda1` 用于引导的FAT32文件系统上的EFI系统分区
- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 


#### 挂载分区 

- `# mount /dev/lvm/root /mnt`
- `# mkdir -p /mnt/{home,boot}` 创建 /home 和 /boot 目录
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot` 
- `# swapon /dev/lvm/swap`

#### 扩展LVM组

如果将来要将新的物理卷添加到组中，则可以看一下要使用哪个命令，假设有第四个磁盘sdd，并已按先前说明的方式对其进行了分区，则我们将扩展空间，例如扩展到 `/dev/lvm/home`：

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`

## 5. 镜像列表

使用工具 **reflector** 将存储库的镜像列表保存在 **/etc/pacman.d/mirrorlist** 中，指定要同步服务器的国家，例如 **it**。使用逗号可以添加多个国家，例如 **it,us**：

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6. Pacstrap

安装 **Linux kernel** 和基本软件包以创建我们的Arch系统，还添加编辑器例如 **vim**。如果按照使用 **lvm** 的安装，则将 `lvm2` 软件包添加到以下命令：

`# pacstrap -K /mnt base base-devel linux linux-firmware vim` 

<br><br><br><br>



## 7. 生成Fstab

/etc/fstab 文件允许您在Linux系统启动时控制哪些文件系统被挂载，包括Windows分区和网络共享：

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8. Chroot
 
进入 chroot 并配置以下步骤：本地时间配置、系统时钟、语言、Keyboard 映射、localhost、Root密码、用户创建和密码。

进入 chroot：

`# arch-chroot /mnt`

<br><br><br><br>


###  时间区

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>

### 区域设置

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=it" >> /etc/vconsole.conf`

<br><br><br><br>


### 主机名和Hosts

- `# echo "YOURMACHINENAME" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

<br><br><br><br>


### 用户和Root

为 root 配置密码，请小心！

`# passwd`

配置一个小写的新用户，使用 `-m` 创建 `/home/USERNAME` 目录，使用 `-G` 创建 `wheel` 组和 `-s` 创建 shell：

`# useradd -mG wheel -s /bin/bash USERNAME`

配置真实姓名（在图形中显示带有大写字母的初始字母，例如“Alessio”）：

`# usermod -c 'REALNAME' USERNAME`

为新添加的用户配置密码，请小心！

`# passwd USERNAME`

为 wheel 组配置 sudoers 文件：

`# echo "USERNAME ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/USERNAME`

<br><br><br><br>

### mkinitcpio 对于 LVM

在 **/etc/mkinitcpio.conf** 文件中的 hooks 中添加 **lvm2**

`HOOKS="base udev ... block lvm2 filesystems"`

然后使用以下命令：

`# mkinitcpio -p linux`


<br><br><br><br>


## 9. 引导程序

### GRUB (Bios-MBR)

- `# pacman -S grub`
- `# grub-install --target=i386-pc /dev/sda`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

<br><br><br><br>


### GRUB (UEFI)

- `# pacman -S grub`
- `# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

GRUB 完全支持使用 CA 密钥或 shim 的安全引导，但是安装命令取决于您打算使用哪个。

要使用 CA 密钥，命令是：

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`


要使用 shim-lock，命令是：

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

<br><br><br><br>

### Systemd-boot (EXT4)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

现在创建打开的 **vim** 中的 **arch.conf** 文件的配置，重要的是要写入正确的 root 引导分区，例如 `root=/dev/sdax`，其中 `x` 是根分区的数字。

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

现在创建打开的 **vim** 中的 **arch.conf** 文件的配置，重要的是要写入正确的 root 引导分区，例如 `root=/dev/sdax`，其中 `x` 是根分区的数字，添加 **@** 子卷的标志。

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

现在创建打开的 **vim** 中的 **arch.conf** 文件的配置，重要的是要写入正确的 root 引导分区，例如对于 **lvm** `root=/dev/mapper/lvm-root`

- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

## 10. 基础包

`# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`

<br><br><br><br>


## 11. 桌面环境

从一些推荐的流行桌面环境中选择：

### Gnome
完整的 Gnome 和 GDM 显示管理器

- `# pacman -S gnome gnome-extra gdm` 
- `# systemctl enable gdm`

### Xfce4
带有 Lightdm 显示管理器的 xfce4

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

### Lxde
带有 Lightdm 显示管理器的 lxde

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

### Mate
带有 Lightdm 显示管理器的 mate

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

### Plasma
带有 SDDM 显示管理器的 plasma kde

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

### Cinnamon
带有 Lightdm 显示管理器的 cinnamon

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`


<br><br><br><br>


## 12. 服务

如果您已启用显示管理器的服务，则可以继续启用其他必要的服务。

- `# systemctl enable NetworkManager` 注意，它区分大小写。
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

<br><br><br><br>

## 13. Zram

以下示例描述如何使用单个 *udev* 规则在启动时自动将交换空间切换到 *zram* 中。不需要任何其他包即可使其工作。

在引导时显式加载模块：

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

创建以下 *udev* 规则，根据需要调整 disksize 属性以获得此示例中所需的交换大小，这里是 *16G*：

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k", TAG+="systemd"`

将 **/dev/zram** 添加到 fstab，优先级高于默认值：

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0 `
