
systemd-boot è un bootloader open source utilizzato su Arch Linux per avviare il sistema operativo. È stato sviluppato da systemd, il sistema di inizializzazione utilizzato da molte distribuzioni Linux moderne.

systemd-boot è noto anche come "bootctl" ed è un bootloader molto veloce e leggero che supporta UEFI e offre un'interfaccia utente semplice e facile da usare. Il suo scopo principale è quello di selezionare il kernel del sistema operativo da avviare e di fornire le opzioni di avvio necessarie.


Installazione di systemd-boot:

```
# pacman -S efibootmgr
# bootctl --path=/boot install
# echo "deafult arch-*" >> /boot/loader/loader.conf
# vim /boot/loader/entries/arch.conf
```


adesso creiamo la configurazione del file arch.conf aperto con vim, e' importante scrivere la partizione di avvio della root corretta ad esempio in questo esempio e' root=/dev/sda2 , altrimenti il sistema non si avviera'.
Se stiamo usando un sottovolume @ dobbiamo aggiungere il flag rootflags=subvol=@
   
```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=/dev/sda2 rootflags=subvol=@ rw quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_level=3
```
