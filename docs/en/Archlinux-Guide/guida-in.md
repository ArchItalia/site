# Arch Linux Installation


## 1. हस्ताक्षर सत्यापन

इस्तेमाल से पहले, खासतौर से HTTP मिरर से डाउनलोड करते समय हमें इमेज के हस्ताक्षर की सत्यापन करना चाहिए, जहां डाउनलोड हानिकारक इमेज प्रदान करने के लिए हस्तक्षरों के लिए आवेदन किया जा सकता है।

* GnuPG * संबंधित एक सिस्टम के साथ, * PGP ISO हस्ताक्षर * को ISO निर्देशिका में डाउनलोड करें और इसकी सत्यापन करें:

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

अन्यथा, Arch Linux के मौजूदा स्थापना से निम्न चलाएँ:

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## 2. प्रारंभिक कॉन्फ़िगरेशन

शुरू करने के लिए, हमें कीबोर्ड भाषा, मूल भाषा को परिभाषित करना होगा, बिना अभ्यस्त किए डिफ़ॉल्ट भाषा `US` है। उपलब्ध लेआउट सूची निम्नलिखित तरीके से प्रदर्शित की जा सकती है:

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

अपने कीबोर्ड की भाषा को निम्न आदेश के साथ सेट करें:

`# loadkeys it`

कंसोल अक्षरों को **/usr/share/kbd/consolefonts/** में पाया जा सकता है और इसे सेट भी setfont के साथ किया जा सकता है। उदाहरण के लिए, HiDPI डिस्प्ले के लिए उपयुक्त बड़े अक्षर वाले एक का उपयोग करने के लिए, निम्न आदेश चलाएँ: 

`# setfont ter-132b`


<br><br><br><br>

## 3. इंटरनेट कनेक्शन
यदि आपने केबल या वर्चुअल मशीन के माध्यम से मशीन को इंटरनेट से कनेक्ट किया है, तो आप इस आदेश का उपयोग करके अपना प्राप्त आईपी पता सत्यापित कर सकते हैं:

`# ip a`

* Ping * टेस्ट आदेश के माध्यम से कनेक्शन की जांच की जा सकती है:

`# ping -c 3 archlinux.org`

* iwctl * उपकरण का उपयोग करके Wi-Fi नेटवर्क से कनेक्ट करें:

- `# iwctl` iwctl स्थार्ट करें
- `# device list` अपनी डिवाइस का नाम ढूंढें, उदाहरण वालन 0 हो सकता है
- `# station wlan0 scan` उपलब्ध वायरलेस नेटवर्कों के लिए स्कैन करें
- `# station wlan0 get-networks` नेटवर्कों की सूची प्राप्त करें
- `# station wlan0` yournetworkname से अपने नेटवर्क से कनेक्ट करें
- `# exit`

यदि हमारी डिवाइस अक्षम हो गयी है और हम **iwctl** नहीं चला सकते हैं:

- `# rfkill list` डिवाइसों की ब्लॉक है या नहीं है, जांचें
- `# rfkill unblock all` सभी अक्षम डिवाइस से अक्षम डिवाइसों के ब्लॉक हटाएं
- `# systemctl restart iwd` iwd सेवा को रीस्टार्ट करें

**iwctl** को पुन: प्रयास करें और उपरोक्त के रूप में आगे बढ़ें।


<br><br><br><br>

## 4. डिस्क की तैयारी

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm EXT4](#uefi-lvm-ext4)


<br><br><br><br>

### Bios-MBR

#### Partitioning
नाम निर्देशन का उपयोग करने के लिए अपना डिस्क पहचानें। उदाहरण के लिए, **SSD / dev / sda** या **एम। 2 /dev/nvme0n1** के मामले में और अंततः, एक **virtual disk /dev/vda** के मामले में।

`# lsblk -l`

अपनी डिस्क के नाम के नाम के नाम के नाम का उपयोग करके, यहां हम **/dev/sda** होने के अधिकार में मान लेंगे। एक अज्ञात स्थान पर, यदि डिस्क स्वचालित है, आपसे इसका प्रकार पूछा जाएगा। इस मामले में, **DOS** का चयन करें: 

`# cfdisk /dev/sda`

बेस स्थापना के लिए आवश्यक विभाजन बनाएं, मान लेते हैं कि हमारे पास एक **128GiB SSD** है:

- `# 4Gib`    स्वैप के लिए एक विभाजन बनाएं और स्वैप के प्रकार का चयन करें
- 124Gib # रूट विभाजन बनाएं
- `# write (yes)` और `quit` बदलाव लिखें और बाहर निकलें

#### Formatting Partitions

- `# mkswap /dev/sda1` स्वैप विभाजन
- `# mkfs.ext4 /dev/sda2` रूट विभाजन EXT4 में

#### Mounting Partitions

- `# mount /dev/sda2 /mnt` रूट विभाजन माउंट करें
- `# swapon /dev/sda1` स्वैप विभाजन माउंट करें


<br><br><br><br>

### UEFI ext4

#### Disk Partitioning
नाम निर्देशन का उपयोग करने के लिए अपना डिस्क पहचानें। उदाहरण के लिए, **SSD /dev/sda** या **एम। 2 /dev/nvme0n1** के मामले में और अंततः, एक **virtual disk /dev/vda** के मामले में।

`# lsblk -l`

मान लेते हैं कि हमारे पास **128GiB SSD** हैं और हम UEFI स्थापना के लिए GPT विभाजन का उपयोग करेंगे:

`# cfdisk /dev/sda`

- `# 512Mib` EFI विभाजन बनाएं और EFI सिस्टम विभाजन प्रकार का चयन करें
- `# 4Gib` स्वैप के लिए एक विभाजन बनाएं और स्वैप प्रकार का चयन करें
- `# 23.5Gib`  रूट विभाजन बनाएं
- `# 100Gib` होम विभाजन बनाएं
- `# write (yes)` और `quit` बदलाव लिखें और बाहर निकलें

#### Formatting Partitions

- `# mkfs.vfat -F32 /dev/sda1` बूट के लिए FAT32 में EFI सिस्टम विभाजन
- `# mkswap /dev/sda2` स्वैप विभाजन 
- `# mkfs.ext4 /dev/sda3` रूट विभाजन EXT4 में
- `# mkfs.ext4 /dev/sda4` होम विभाजन EXT4 में


#### Mounting Partitions

- `# mount /dev/sda3 /mnt` रूट विभाजन माउंट करें
- `# mkdir -p /mnt/{home,boot}` /home और /boot निर्मित करें
- `# mount /dev/sda4 /mnt/home` होम विभाजन माउंट करें
- `# mount /dev/sda1 /mnt/boot` बूट विभाजन माउंट करें
- `# swapon /dev/sda2` स्वैप विभाजन माउंट करें


<br><br><br><br>

### UEFI btrfs

#### Disk Partitioning
नाम निर्देशन का उपयोग करने के लिए अपना डिस्क पहचानें। उदाहरण के लिए, **SSD /dev/sda** या **एम। 2 /dev/nvme0n1** के मामले में और अंततः, एक वर्चुअल डिस्क /dev/vda के मामले में।

`# lsblk -l`

मान लेते हैं कि हमारे पास **128GiB SSD** हैं और हम UEFI स्थापना के लिए GPT विभाजन का उपयोग करेंगे:

`# cfdisk /dev/sda`

- `# 512Mib` EFI विभाजन बनाएं और EFI सिस्टम विभाजन प्रकार का चयन करें
- `# 27.5Gib`  रूट विभाजन बनाएं
- `# 100Gib` होम विभाजन बनाएं
- `# write (yes)` और `quit` बदलाव लिखें और बाहर निकलें

#### Formatting Partitions

- `# mkfs.vfat -F32 /dev/sda1` बूट के लिए FAT32 में EFI सिस्टम विभाजन
- `# mkfs.btrfs /dev/sda2` रूट विभाजन BTRFS में
- `# mkfs.btrfs /dev/sda3` होम विभाजन BTRFS में


#### Mounting Partitions 

**@** और **@home** सबवोल्यूम बनाएं:

- `# mount /dev/sda2 /mnt`
- `# btrfs su cr /mnt/@`
- `# umount /mnt`
- `# mount /dev/sda3 /mnt`
- `# btrfs su cr /mnt/@home`
- `# umount /mnt`
- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt`
- `# mkdir -p /mnt/{home,boot}` /home और /boot निर्मित करें
- `# mount /dev/sda1 /mnt/boot`
- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm-ext4

#### Disk Partitioning
नाम निर्देशन का उपयोग करने के लिए अपना डिस्क पहचानें। उदाहरण के लिए, **SSD /dev/sda** या **एम। 2 /dev/nvme0n1** के मामले में और अंततः, एक **virtual disk /dev/vda** के मामले में।


`# lsblk -l`

मान लेते हैं कि हमारे पास LVM के लिए **3 128GiB डिस्क** हैं: **sda sdb sdc** एक के लिए एक **cfdisk** उपयोग करें:

`# cfdifk /dev/sda`

- `# 512Mib` EFI विभाजन बनाएं और EFI सिस्टम विभाजन प्रकार का चयन करें
- `# 127.5GiB` पारगमन के लिए एक विभाजन बनाएं और LVM प्रकार का चयन करें
- `# write (yes)` और `quit` लिखें बदलाव लिखें और बाहर निकलें

`# cfdifk /dev/sdb`

- `# 128GiB` LVM प्रकार के लिए एक विभाजन बनाएं
- `# write (yes)` और `quit` बदलाव लिखें और बाहर निकलें

`# cfdifk /dev/sdc`

- `# 128GiB` LVM प्रकार के लिए एक विभाजन बनाएं
- `# write (yes)` और `quit` बदलाव लिखें और बाहर निकलें

LVM के तहत विभाजन बनाने के लिए, हमें सबसे पहले Physical volume बनाना पड़ेगा:

#### Physical Volume बनाएं
`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### वॉल्यूम ग्रुप बनाएं
अपनी वॉल्यूम ग्रुप बनाएं और विस्तार दें; आपको एक या एक से अधिक फिजिकल वॉल्यूम पर एक वॉल्यूम ग्रुप बनाने की आवश्यकता होगी `# vgcreate volume_group physical_volume`, उदाहरण के लिए:

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

यह आदेश पहले तीन विभाजनों को फिजिकल वॉल्यूम में सेट करेगा (जरूरत पड़ने पर), और फिर तीन वॉल्यूमों के साथ वॉल्यूम ग्रुप बनाएगा। आपको इस आदेश में बताया जाएगा यदि इसे किसी भी डिवाइस पर पहले से ही मौजूद होने वाले सूची का पता लगाता है।

#### लॉजिकल वॉल्यूम बनाएं

लॉजिकल वॉल्यूम बनाएं, एक बेसिक विन्यास के लिए, हमें एक-एक करके रूट, स्वैप और होम पर एक लॉजिकल वॉल्यूम चाहिए।

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### Formatting Partitions


- `#mkfs.vfat -F32 /dev/sda1` EFI सिस्टम पार्टीशन को बूट के लिए FAT32 में बनाएं।
- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap`

#### पार्टीशन माउंट करना

- `# mount /dev/lvm/root /mnt`
- `# mkdir -p /mnt/{home,boot}` /home और /boot निर्मित करें
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot`
- `# swapon /dev/lvm/swap`

#### एलवीएम समूह बढ़ाएं

भविष्य में जब आप समूह में एक नया भौतिक खंड जोड़ना चाहेंगे, तो मैंने पहले जितने डिस्क दिए थे, उनमें से एक चौथा डिस्क sdd है और मैंने उसे पहले जितने पार्टिशन किए थे, उदाहरण के लिए हम त्वरित निम्नलिखित प्रयोग करेंगे, हम विवरण में जगह बढ़ाते हैं `/dev/lvm/home`:

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`

## 5. Mirrorlist

कंटेनर को सिंक्रनाइज़ करने के लिए देश को निर्दिष्ट करते हुए **reflector** का उपयोग करें, रिपॉजिट्री के लिए मिररलिस्ट को **/etc/pacman.d/mirrorlist** में सहेजें, उदाहरण के लिए **it**, एक या एक से अधिक देश जोड़ें, उदाहरण के लिए **it,us**:

`# reflector --verbose -c it -a 12 --sort rate --save /etc/pacman.d/mirrorlist`

<br><br><br><br>

## 6. Pacstrap

अपनी Arch सिस्टम बनाने के लिए **Linux kernel** और बेस पैकेज को इंस्टॉल करें, इसके अलावा **vim** जैसा एक एडिटर भी जोड़ें। यदि आप **lvm** के लिए स्थापना का पालन कर रहे हैं, तो निम्नलिखित कमांड में `lvm2` पैकेज को शामिल करें:

`# pacstrap -K /mnt base base-devel linux linux-firmware vim`

<br><br><br><br>



## 7. Fstab बनाएं

/etc/fstab फ़ाइल बूट के समय आपके लिनक्स सिस्टम पर माउंट होने वाली फ़ाइल सिस्टम को नियंत्रित करने की अनुमति देती है, जिसमें विंडोज पार्टीशन और नेटवर्क शेयर शामिल होते हैं:

`# genfstab -U /mnt > /mnt/etc/fstab`

<br><br><br><br>


## 8. Chroot

उस Chroot में जाएं और निम्नलिखित चरणों को कॉन्फ़िगर करें: localtime, systemclock, भाषा, कुंजीपटलमानचित्र नक्शे, localhost, रूट पासवर्ड, उपयोगकर्ता सृजन और पासवर्ड।

chroot में जाएं:

`# arch-chroot /mnt`

<br><br><br><br>


### समय क्षेत्र

- `# ln -sf /usr/share/zoneinfo/Europe/Italy /etc/localtime`
- `# hwclock --systohc`

<br><br><br><br>

### स्थानीयकरण

- `# echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen`
- `# locale-gen`
- `# echo "LANG=it_IT.UTF-8" >> /etc/locale.conf`
- `# echo "KEYMAP=it" >> /etc/vconsole.conf`

<br><br><br><br>


### होस्टनाम और होस्ट

- `# echo "आपका मशीन नाम" > /etc/hostname`
- `# echo "127.0.0.1 localhost" >> /etc/hosts`
- `# echo "::1       localhost" >> /etc/hosts`

<br><br><br><br>


### उपयोगकर्ता और रूट

ध्यान दें, यह सावधानीपूर्वक हो सकता है, रूट पासवर्ड को कॉन्फ़िगर करें।

`# passwd`

नए लोअर केस उपयोगकर्ता को कॉन्फ़िगर करें, `-m` के साथ निर्दिष्ट किए गए डायरेक्टरी `/home/USERNAME` बनाएं, `-G` समूह `wheel` जोड़ें और शेल दर्ज करें ` / -s`:

`# useradd -mG wheel -s /bin/bash USERNAME`

वास्तविक नाम कॉन्फ़िगर करें (जो उच्च आरंभिक अक्षरों के साथ ग्राफिक में दिखता है, उदाहरण के लिए **"Alessio"**):

`# usermod -c 'REALNAME' USERNAME`

नई जोड़ी गई उपयोगकर्ता के लिए पासवर्ड कॉन्फ़िगर करें, सावधान रहें!


`# passwd USERNAME`

स्वीकृत स्वीडविंडोज़ीओर्स के लिए सूडोएर्स फ़ाइल कॉन्फ़िगर करें:

`# echo "USERNAME ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/USERNAME`


<br><br><br><br>


### एलवीएम के लिए mkinitcpio

**lvm** के लिए हुक्स में **lvm2** जोड़ें, **/etc/mkinitcpio.conf** में।

 `HOOKS = "base udev...block lvm2 filesystems"`
 
पुन: लिनक्स के लिए निम्नलिखित आदेश का उपयोग करें:

 `# mkinitcpio -p linux`


<br><br><br><br>


## 9. बूटलोडर

### GRUB (Bios-MBR)

- `# pacman -S grub`
- `# grub-install --target=i386-pc /dev/sda`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

<br><br><br><br>


### GRUB (UEFI)

- `# pacman -S grub`
- `# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`
- `# grub-mkconfig -o /boot/grub/grub.cfg`

GRUB पूरी तरह से CA कुंजी या शिम का उपयोग समर्थित करता है, हालांकि, आप उपयोग करना चाहते हैं उसके आधार पर निम्नलिखित कोमांड अलग-अलग होते हैं।

CA कुंजियों का उपयोग करने के लिए, रणनीति यह है:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`

शिम लॉक का उपयोग करने के लिए, रणनीति यह है:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`

<br><br><br><br>

### Systemd-boot (EXT4)

- `# pacman -S efibootmgr`
- `# bootctl --path=/boot install`
- `# echo "default arch-*" >> /boot/loader/loader.conf`
- `# vim /boot/loader/entries/arch.conf`

अब **vim** के साथ खुली अभिलेख फ़ाइल **arch.conf** का विन्यास बनाएं, इसमें **root** बूट पार्टीशन जैसे `root=/dev/sdax` को सही तरीके से लिखना महत्वपूर्ण है जहां `x` रूट पार्टीशन की संख्या है।

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

अब **vim** के साथ खुली अभिलेख फ़ाइल **arch.conf** का विन्यास बनाएं, इसमें **root** बूट पार्टीशन जैसे `root=/dev/sdax` को सही तरीके से लिखना महत्वपूर्ण है और **@** उपवर्ग के लिए झंडा जोड़ें।

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

अब **vim** के साथ खुली अभिलेख फ़ाइल **arch.conf** का विन्यास बनाएं, **lvm** के लिए उचित रूट बूट पार्टीशन जैसे `root=/dev/mapper/lvm-root` लिखना महत्वपूर्ण है।


- `title   Arch Linux (LVM)`
- `linux   /vmlinuz-linux`
- `initrd  /initramfs-linux.img`
- `options root=/dev/mapper/lvm-root rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3`

<br><br><br><br>

## 10. बेस पैकेज

 `# pacman -S xorg wpa_supplicant wireless_tools netctl net-tools iw networkmanager alsa-utils pipewire-pulse mtools dosfstools mtools ntfs-3g f2fs-tools dosfstools exfatprogs fuse firewalld acpi cronie git reflector bluez bluez-utils cups reflector`


<br><br><br><br>


## 11. डेस्कटॉप वातावरण

कुछ सुझाए गए लोकप्रिय डेस्कटॉप वातावरणों में से चुनें:

### Gnome

पूर्ण Gnome जीडीएम प्रदर्शन प्रबंधक सहित।

- `# pacman -S gnome gnome-extra gdm ` 
- `# systemctl enable gdm`

### Xfce4

Xfce4 लाइटडीएम प्रदर्शन प्रबंधक के साथ।

- `# pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings`
- `# systemctl enable lightdm`

### Lxde

Lxde लाइटडीएम प्रदर्शन प्रबंधक के साथ।

- `# pacman -S lxde lxde-common lxsession openbox`
- `# systemctl enable lightdm`

### Mate

Mate लाइटडीएम प्रदर्शन प्रबंधक के साथ।

- `# pacman -S mate mate-extra`
- `# systemctl enable lightdm`

### Plasma

SDDM प्रदर्शन प्रबंधक के साथ प्लाज्मा के साथ।

- `# pacman -S plasma kde-applications sddm`
- `# systemctl enable sddm`

### Cinnamon

Cinnamon लाइटडीएम प्रदर्शन प्रबंधक के साथ।

- `# pacman -S cinnamon nemo-fileroller gnome-terminal lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xdg-user-dirs-gtk`
- `# systemctl enable lightdm`


<br><br><br><br>


## 12. सेवाएं

यदि आपने प्रदर्शन प्रबंधक के लिए सेवा सक्षम कर रखी है, तो आप अन्य आवश्यक सेवाएं सक्षम करने के लिए आगे बढ़ सकते हैं।

- `# systemctl enable NetworkManager` ध्यान दें, यह केस संवेदनशील होता है।
- `# systemctl enable bluetooth`
- `# systemctl enable reflector`
- `# systemctl enable cronie`
- `# systemctl enable firewalld` 

<br><br><br><br>

## 13. Zram

निम्नलिखित उदाहरण में, आपको एकल *udev* नियम का उपयोग करके *zram* पर स्वचालित रूप से स्वैपिंग कैसे कॉन्फ़िगर करें इसका वर्णन है। इस काम के लिए कोई अतिरिक्त पैकेज की आवश्यकता नहीं होनी चाहिए।

लोड जल्दी से एक मॉड्यूल:

- `# vim /etc/modules-load.d/zram.conf`

- `zram`

इस प्रकार का निम्नलिखित *udev* नियम बनाएं और सामान्य रूप से इसी प्रकार के लिए आवश्यकतानुसार डिस्कसाइज विवरण में अनुकूलन करें, इस उदाहरण में इसमें स्वैप का आकार *16G* है:

- `# vim /etc/udev/rules.d/99-zram.rules`

- `ACTION=="add", KERNEL=="zram0", ATTR{comp_algorithm}="zstd", ATTR{disksize}="16G", RUN="/usr/bin/mkswap -U clear /dev/%k", TAG+="systemd"`

अपनी fstab में **/dev/zram** को मुख्यतः डिफ़ॉल्ट से अधिक प्राथमिकता दें:

- `# vim /etc/fstab`

- `/dev/zram0 none swap defaults,pri=100 0 0 `

<br><br><br><br>
