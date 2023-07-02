# Installazione di Arch Linux

Guide all'installazione di Arch Linux: 

* [Configurazione iniziale](#configurazione-iniziale)
* [Connessione Internet](#connessione-internet)
* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm](#uefi-lvm)

<br><br><br><br>

## Configurazione iniziale

Per iniziare dobbiamo definire la lingua della tastiera, la lingua predefinita senza immettere il comando e' `US`. I layout disponibili possono essere elencati con: 

`# ls /usr/share/kbd/keymaps/**/*.map.gz`

impostiamo la lingua della nostra tastiera con il comando:

`# loadkeys it`

I caratteri della console si trovano in **/usr/share/kbd/consolefonts/** e possono anche essere impostati con setfont. Ad esempio, per utilizzare uno dei caratteri più grandi adatti agli schermi HiDPI, eseguire:

`# setfont ter-132b`


<br><br><br><br>

## Connessione Internet
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

Se nel caso in cui i nostri dispositivi siano disabilitati e non riusciamo a eseguire iwctl:

- `# rfkill list`  verifichiamo lo stato dei dispositivi blocked o unblocked
- `# rfkill unblock all` sblocchiamo tutti i nostri dispositivi bloccati blocked
- `# systemctl restart iwd`  riavviamo il servizio iwd

Riprovare `# iwctl` e procedere come sopra.


<br><br><br><br>

## Bios-MBR




<br><br><br><br>

## UEFI ext4




<br><br><br><br>

## UEFI btrfs




<br><br><br><br>

## UEFI lvm




<br><br><br><br>





