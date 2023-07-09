＃Arch Linuxのインストール


## 1.署名の検証

特にHTTPミラーからのダウンロードの場合、悪意のあるイメージを提供するためにインターセプトされる可能性があるため、使用前にイメージの署名を確認することが推奨されます。* GnuPG *がインストールされたシステムでは、ISOディレクトリに[PGP ISO署名](https://archlinux.org/download/#checksums)をダウンロードして、次のコマンドで検証します。

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

または、Arch Linuxの既存のインストールから実行します。

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2.初期構成

開始するには、キーボードの言語、デフォルトの言語を定義する必要があります。コマンドを入力せずに、利用可能なレイアウトは次のようにリストできます。

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

次のコマンドでキーボードの言語を設定します。

`# loadkeys it`

コンソールの文字は**/usr/share/kbd/consolefonts/** で見つけることができ、setfontでも設定できます。たとえば、HiDPIディスプレイに適した大きな文字のうちの1つを使用する場合は、次のように実行します。

`# setfont ter-132b`

<br><br><br><br>

## 3.インターネット接続

有線または仮想マシンを介してマシンをインターネットに接続した場合、次のコマンドを使用して取得したIPアドレスを確認できます。

`# ip a`

接続はpingテストコマンドでテストできます。

`# ping -c 3 archlinux.org`

ツール「iwctl」を使用してWi-Fiネットワークに接続します。

- `# iwctl` iwctlの起動
- `# device list` デバイス名を見つける（例：wlan0）
- `# station wlan0 scan` 利用可能な無線ネットワークのスキャン
- `# station wlan0 get-networks` ネットワークのリストを取得
- `# station wlan0` あなたのネットワーク名に接続
- `# exit`

デバイスが無効になっていて** iwctl **を実行できない場合は、次のようにします。

- `# rfkill list` デバイスのブロックまたはアンブロックされた状態を確認します。
- `# rfkill unblock all` ブロックされたデバイスをすべて解除します。
- `# systemctl restart iwd` iwdサービスを再起動します。

`＃iwctl`を再試行して、上記のように進めてください。


<br><br><br><br>

## 4.ディスクの準備

* [Bios-MBR ext4]（＃bios-mbr）
* [UEFI ext4]（＃uefi-ext4）
* [UEFI btrfs]（＃uefi-btrfs）
* [UEFI lvm EXT4]（＃uefi-lvm-ext4）


<br><br><br><br>

### Bios-MBR

#### パーティショニング
ディスクの名前付け規則を知るためにディスクを識別します。例えば、** SSD / dev / sda **の場合や、** M.2 / dev / nvme0n1 **の場合、そして最後に**仮想ディスク/ dev / vda**の場合。

`# lsblk -l`

ディスクの名前付けが特定されたら、ここでは** / dev / sda **を持っていると仮定します。 **cfdisk**を使用します。rawディスクの場合はパーティショニングテーブルのタイプを尋ねられる場合があります。この場合は、「DOS」を選択してください。

`# cfdisk /dev/sda`

基本的なインストールのために必要なパーティションを作成します。** 128GiB SSD **を持っていると仮定する場合：

- `# 4Gib` スワップ用のパーティションを作成し、スワップタイプを選択します
- `# 124Gib` ルートパーティションを作成します
- `# write（yes）`および`quit` 変更を書き込んで終了します


#### パーティションのフォーマット

- `# mkswap /dev/sda1` スワップパーティション
- `# mkfs.ext4 /dev/sda2` Rootパーティションin EXT4

#### パーティションのマウント

- `# mount /dev/sda2 /mnt` ルートパーティションをマウントします
- `# swapon /dev/sda1` スワップパーティションをマウントします


<br><br><br><br>

### UEFI ext4

#### ディスクのパーティショニング
ディスクの名前付け規則を知るためにディスクを識別します。例えば、** SSD /dev/sda **の場合や、** M.2 /dev/nvme0n1 **の場合、そして最後に**仮想ディスク/ dev / vda**の場合。

`# lsblk -l`

** 128GiB SSD **を持っているとし、UEFIインストールのためにGPTパーティショニングを使用する場合：

`# cfdisk /dev/sda`

- `# 512Mib` EFIパーティションを作成し、EFIシステムパーティションタイプを選択します
- `# 4Gib` スワップ用のパーティションを作成し、スワップタイプを選択します
- `# 23.5Gib`  ルートパーティションを作成します
- `# 100Gib`  ホームパーティションを作成します
- `# write（yes）`および`quit` 変更を書き込んで終了します


#### パーティションのフォーマット

- `# mkfs.vfat -F32 /dev/sda1` EFIシステムパーティションin FAT32ブート用
- `# mkswap /dev/sda2` スワップパーティション
- `# mkfs.ext4 /dev/sda3` Rootパーティションin EXT4
- `# mkfs.ext4 /dev/sda4` Homeパーティションin EXT4


#### パーティションをマウントする

- `# mount /dev/sda3 /mnt` ルートパーティションをマウントします
- `# mkdir -p /mnt/{home,boot}`  /homeと/bootディレクトリを作成
- `# mount /dev/sda4 /mnt/home` ホームパーティションをマウントします
- `# mount /dev/sda1 /mnt/boot`  ブートパーティションをマウントします
- `# swapon /dev/sda2`  スワップパーティションをマウントします


<br><br><br><br>

### UEFI btrfs

#### ディスクのパーティショニング
ディスクの名前付け規則を知るためにディスクを識別します。例えば、** SSD /dev/sda **の場合や、** M.2 /dev/nvme0n1 **の場合、そして最後に**仮想ディスク/ dev / vda**の場合。

`# lsblk -l`

** 128GiB SSD **を持っているとし、UEFIインストールのためにGPTパーティショニングを使用する場合：

`# cfdisk /dev/sda`

- `# 512Mib` EFIパーティションを作成し、EFIシステムパーティションタイプを選択します
- `# 27.5Gib`  ルートパーティションを作成します
- `# 100Gib`  ホームパーティションを作成します
- `# write（yes）`および`quit` 変更を書き込んで終了します


#### パーティションのフォーマット

- `# mkfs.vfat -F32 /dev/sda1` EFIシステムパーティションin FAT32ブート用
- `# mkfs.btrfs /dev/sda2` ルートパーティションin BTRFS
- `# mkfs.btrfs /dev/sda3` ホームパーティションin BTRFS


#### パーティションをマウントする

 **@**および**@ home**のサブボリュームを作成します。

- `# mount /dev/sda2 /mnt`

- `# btrfs su cr /mnt/@`

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`

- `# umount /mnt`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt`

- `# mkdir -p /mnt/{home,boot}` /homeと/bootディレクトリを作成します

- `# mount /dev/sda1 /mnt/boot`

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### ディスクのパーティショニング
ディスクの名前付け規則を知るためにディスクを識別します。例えば、** SSD /dev/sda **の場合や、** M.2 /dev/nvme0n1 **の場合、そして最後に**仮想ディスク/ dev / vda**の場合。

`# lsblk -l`

** 3 128GiBディスク **を持ち、LVMのために：** sda sdb sdc **を使用する場合、1つのディスクずつ** cfdisk **を使用してください。

`# cfdifk /dev/sda`

- `# 512Mib` EFIパーティションを作成し、EFIシステムパーティションタイプを選択します
- `# 127.5GiB` LVMタイプを選択してパーティションを作成します
- `# write（yes）`および`quit` 変更を書き込んで終了します

`# cfdifk /dev/sdb`

- `# 128GiB` LVMタイプを選択してパーティションを作成します
- `# write（yes）`および`quit` 変更を書き込んで終了します

`# cfdifk /dev/sdc`

- `# 128GiB`  LVMタイプを選択してパーティションを作成します
- `# write（yes）`および`quit` 変更を書き込んで終了します

LVMの下にパーティションを作成するには、まず物理ボリュームを作成する必要があります。

#### 物理ボリュームを作成する

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### ボリュームグループを作成する
ボリュームグループを作成して拡張する。ボリュームグループを1つ以上の物理ボリュームに作成するには、`# vgcreate volume_group physical_volume`などのコマンドが必要です。たとえば：

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

このコマンドは最初に、必要に応じて3つのパーティションを物理ボリュームとして設定し、次に3つのボリュームでボリュームグループを作成します。コマンドは、デバイス上に既存のファイルシステムを検出した場合はアラートを表示します。

#### 論理ボリュームを作成する

ルート、スワップ、およびホーム各々に論理ボリュームを作成します。

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### パーティションのフォーマット

- `# mkfs.vfat -F32 /dev/sda1` EFIシステムパーティションin FAT32ブート用
- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 


#### パーティションをマウントする 

- `# mount /dev/lvm/root /mnt`
- `# mkdir -p /mnt/{home,boot}` /homeと/bootディレクトリを作成します
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot` 
- `# swapon /dev/lvm/swap`

#### LVMグループを拡張する

将来的にグループに新しい物理ボリュームを追加したい場合は、4番目のディスクsddを追加し、以前と同じようにパーティションを作成して、スペースを拡大するためのコマンドを確認します。 例：`/dev/lvm/home`：

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`

## 5.ミラーリスト

国を指定して** reflector **を使用して、リポジトリのミラーリストを** /etc/pacman.d/mirrorlist **に保存します。複数の国を追加することもできます。例えば、** it、us **：

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6.パックストラップ

** Linuxカーネル**と基本パッケージをインストールして、Archシステムを作成し、** vim **などのエディターを追加します。 **lvm**のインストールの場合は、次のコマンドに`lvm2`パッケージを追加してください。

`# pacstrap -K /mnt base base-devel linux linux-firmware vim` 

<br><br><br><br>



## 7.fstabを生成する

** / etc / fstab **ファイルを使用して、Linuxシステムのブート中にマウントされるファイルシステムを制御できます（Windowsパーティションやネットワーク共有を含む）。

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8.Chroot
 
Chrootに入り、以下の手順を構成します：ローカルタイム、システムクロック、言語、キーボードマッピング、localhost、ルートパスワード、ユーザーの作成とパスワード。

chrootに入ります。

`# arch-chroot /mnt`

<br><br><br><br>


### タイムゾーン

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>

### ローカライズ

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=us" >> /etc/vconsole.conf
- 
<br><br><br><br>

ホスト名とホスト

- `# echo "YOURMACHINENAME" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

ユーザーとルート

ルートパスワードの設定に注意してください！

`# passwd`

新しいローワーケースユーザーを構成し、ディレクトリ `/home/USERNAME` を `-m` で作成し、グループ `wheel` を `-G` で作成し、シェルを `-s` で設定します。

`# useradd -mG wheel -s /bin/bash USERNAME`

実名を設定します（例えばグラフィック上では大文字で始まる **"Alessio"** が表示されます）。

`# usermod -c 'REALNAME' USERNAME`

新しく追加したユーザーにパスワードを設定してください。注意してください！

`# passwd USERNAME`

wheelグループのsudoersファイルを設定します。

`# echo "USERNAME ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/USERNAME`

LVM用のmkinitcpio

`/etc/mkinitcpio.conf`内の`hooks`に **lvm2** を追加します。

`HOOKS="base udev ... block lvm2 filesystems"`

その後、次のコマンドを使用します。

`# mkinitcpio -p linux`

ブートローダー

GRUB（Bios-MBR）

- `# pacman -S grub`
- `# grub-install --target=i386-pc /dev/sda`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

GRUBは、CAキーまたはshimを使用したセキュアブートを完全にサポートしています。ただし、使用するものによってインストールコマンドが異なります。

CAキーを使用する場合、コマンドは次のとおりです。

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`

shim-lockを使用する場合、コマンドは次のとおりです。

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

Systemd-boot（EXT4）

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

次に、**vim**で開いた**arch.conf**ファイルの構成を作成します。`root=`には、ルートパーティションの番号である `x` と書き込む必要があります。

- `title   Arch Linux`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/sdax rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

Systemd-boot（Btrfs）

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

次に、**vim**で開いた**arch.conf**ファイルの構成を作成します。`root=`には、ルートパーティションの番号である `x` と書き込む必要があります。そして、**@**サブボリュームのためのフラグを追加します。

- `title   Arch Linux`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/sdax rootflags=subvol=@ rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

Systemd-boot（LVM）

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

次に、**vim**で開いた**arch.conf**ファイルの構成を作成します。ルートパーティションとして **lvm** を使用する場合、 `root=/dev/mapper/lvm-root` と書き込む必要があります。

- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

基本パッケージ

`# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`

デスクトップ環境

いくつかの人気のあるデスクトップ環境から選択できます。

Gnome
完全なGnomeとGDMディスプレイマネージャー

- `# pacman -S gnome gnome-extra gdm` 
- `# systemctl enable gdm`

Xfce4
Lightdmディスプレイマネージャーでxfce4を使う

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

Lxde
Lightdmディスプレイマネージャーでlxdeを使う

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

Mate
Lightdmディスプレイマネージャーでmateを使う

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

Plasma
SDDMディスプレイマネージャーでplasma kdeを使う

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

Cinnamon
Lightdmディスプレイマネージャーでcinnamonを使う

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`

サービス

ディスプレイマネージャーのサービスを有効にした場合、他の必要なサービスを有効にするために進めることができます。

- `# systemctl enable NetworkManager` 注意：大文字と小文字を区別します。
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

Zram

以下の例では、単一の *udev* ルールを使用して起動時に *zram* に自動スワッピングを設定する方法について説明しています。この機能を使うためには、追加のパッケージは必要ありません。

起動時に明示的にモジュールをロードします。

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

次の *udev* ルールを作成してください。この例では、スワップサイズに応じてディスクサイズ属性を調整します。以下の例では、スワップサイズは *16G* になります。

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k", TAG+="systemd"`

優先度をデフォルトよりも高くして、fstabに **/dev/zram** を追加してください。

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0`

