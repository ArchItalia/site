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

Se nel caso in cui i nostri dispositivi siano disabilitati e non riusciamo a eseguire **iwctl**:

- `# rfkill list`  verifichiamo lo stato dei dispositivi blocked o unblocked
- `# rfkill unblock all` sblocchiamo tutti i nostri dispositivi bloccati blocked
- `# systemctl restart iwd`  riavviamo il servizio iwd

Riprovare `# iwctl` e procedere come sopra.


<br><br><br><br>

## Bios-MBR

### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.

`# lsblk -l`

Una volta individuata la nomenclatura del nostro disco usiamo **cfdisk**, qui ipotizzeremo di avere **/dev/sda**. Potrebbe essere richiesto il tipo di tabella di partizionamento se il disco e' vergine, in questo caso andiamo a selezionare **DOS**:

`# cfdisk /dev/sda`

Creiamo le partizioni necessarie all'installazione base, ipotizzando di avere un disco **SSD** da **128GiB**:

- `# 4Gib`   Creiamo una partizione per la swap e selezioniamo tipo swap
- `# 124Gib`  Creiamo la partizione Root
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo


### Formattare le Partizioni

- `# mkswap /dev/sda1` La partizione per la swap
- `# mkfs.ext4 /dev/sda2` La partizione Root in EXT4

### Montaggio delle Partizioni

- `# mount /dev/sda2 /mnt Montiamo la partizione root`
- `# swapon /dev/sda1 Montiamo la partizione di swap`


<br><br><br><br>

## UEFI ext4




<br><br><br><br>

## UEFI btrfs




<br><br><br><br>

## UEFI lvm




<br><br><br><br>





