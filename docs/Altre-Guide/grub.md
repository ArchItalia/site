Grub, acronimo di GRand Unified Bootloader, è un gestore di avvio molto utile per selezionare il sistema operativo che si desidera avviare all'avvio del computer.

Di seguito è riportata una guida per l'installazione e la configurazione di Grub su Arch Linux.

1. Installare Grub tramite il gestore di pacchetti
    - Aprire un terminale e digitare il seguente comando:
      <br>
      
      ```
      sudo pacman -S grub
      ```

        <br>
        
2. Creare la configurazione di Grub
    - Digitare il seguente comando per generare la configurazione di Grub:
      <br>
      
      ```
      sudo grub-mkconfig -o /boot/grub/grub.cfg
      ```

      <br>
      
3. Configurare Grub
      - Aprire il file di configurazione di Grub tramite il seguente comando:
  
        <br>
      ```
      sudo nano /etc/default/grub
      ```
    - Navigare per il file di configurazione e modificare i parametri secondo le preferenze personali.
      Esempio:
      ```
      GRUB_DEFAULT=0
      GRUB_TIMEOUT=5
      GRUB_DISTRIBUTOR="Arch"
      GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
      GRUB_CMDLINE_LINUX="loglevel=3"
      ```
    - Salvare le modifiche e chiudere il file di configurazione.

 <br> <br>
    
4. Aggiornare Grub
    - Digitare il seguente comando per aggiornare Grub con le modifiche apportate:

```  
sudo grub-mkconfig -o /boot/grub/grub.cfg
```    
<br> <br><br> <br>

installazione UEFI

1 - Installazione di Grub:

`# pacman -S efibootmgr grub`


`# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`


1a - Supporto per l'avvio protetto:

GRUB supporta completamente l'avvio protetto utilizzando chiavi CA o shim, tuttavia il comando di installazione è diverso a seconda di quale si intende utilizzare.

Per utilizzare le chiavi CA il comando è:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="tpm" --disable-shim-lock`


Per utilizzare shim-lock il comando è:

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB --modules="normal test efi_gop efi_uga search echo linux all_video gfxmenu gfxterm_background gfxterm_menu gfxterm loadenv configfile tpm"`


2 - infine Genera il file di configurazione principale

`# grub-mkconfig -o /boot/grub/grub.cfg`

installazione Bios/MBR

1 - Installazione di Grub:

`# pacman -S grub`

`# grub-install --target=i386-pc /dev/xxx`


2 - infine Genera il file di configurazione principale

`# grub-mkconfig -o /boot/grub/grub.cfg`

Nota: verificare che il parametro "GRUB_TIMEOUT" sia impostato su un valore superiore a 0 per consentire la selezione del sistema operativo all'avvio del computer.
