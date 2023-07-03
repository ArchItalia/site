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
  <br>
      
      ```
      sudo grub-mkconfig -o /boot/grub/grub.cfg
      ```

Nota: verificare che il parametro "GRUB_TIMEOUT" sia impostato su un valore superiore a 0 per consentire la selezione del sistema operativo all'avvio del computer.
