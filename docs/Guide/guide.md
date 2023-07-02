# Installazione di Arch Linux

Guide all'installazione di Arch Linux: 

* [Configurazione iniziale](#configurazione-iniziale)
* [Connessione Internet](#connessione-internet)
* [Preparazione del disco](#preparazione-del-disco)

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

## Preparazione del disco

* [Bios-MBR ext4](#bios-mbr)
* [UEFI ext4](#uefi-ext4)
* [UEFI btrfs](#uefi-btrfs)
* [UEFI lvm](#uefi-lvm)

### Bios-MBR

#### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.

`# lsblk -l`

Una volta individuata la nomenclatura del nostro disco usiamo **cfdisk**, qui ipotizzeremo di avere **/dev/sda**. Potrebbe essere richiesto il tipo di tabella di partizionamento se il disco e' vergine, in questo caso andiamo a selezionare **DOS**:

`# cfdisk /dev/sda`

Creiamo le partizioni necessarie all'installazione base, ipotizzando di avere un disco **SSD** da **128GiB**:

- `# 4Gib`   Creiamo una partizione per la swap e selezioniamo tipo swap
- `# 124Gib`  Creiamo la partizione Root
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo


#### Formattare le Partizioni

- `# mkswap /dev/sda1` La partizione per la swap
- `# mkfs.ext4 /dev/sda2` La partizione Root in EXT4

#### Montaggio delle Partizioni

- `# mount /dev/sda2 /mnt` Montiamo la partizione root
- `# swapon /dev/sda1` Montiamo la partizione di swap


<br><br><br><br>

### UEFI ext4

#### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.

`# lsblk -l`

Una volta individuata la nomenclatura del nostro disco usiamo **cfdisk**, qui ipotizzeremo di avere **/dev/sda**. Potrebbe essere richiesto il tipo di tabella di partizionamento se il disco e' vergine, in questo caso andiamo a selezionare **GUID Partition Table (GPT)**:

`# cfdisk /dev/sda`

Creiamo le partizioni necessarie all'installazione base, ipotizzando di avere un disco **SSD** da **128GiB**:

- `# 512Mib`  Creiamo la partizione EFI e scegliamo di tipo EFI system
- `# 4Gib`   Creiamo una partizione per la swap e selezioniamo tipo swap
- `# 23.5Gib`  Creiamo la partizione Root
- `# 100Gib`  Creiamo la partizione Home
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

#### Formattare le Partizioni

- `# mkfs.vfat -F32 /dev/sda1` La partizione EFI system in FAT32 per il boot
- `# mkswap /dev/sda2` La partizione per la swap
- `# mkfs.ext4 /dev/sda3` La partizione Root in EXT4
- `# mkfs.ext4 /dev/sda4` La partizione Home in EXT4


#### Montaggio delle Partizioni

- `# mount /dev/sda3 /mnt` Montiamo la partizione root
- `# mkdir -p /mnt/{home,boot}` creiamo la directory /home e la directory /boot
- `# mount /dev/sda4 /mnt/home` Montiamo la partizione home
- `# mount /dev/sda1 /mnt/boot` Montiamo la partizione di boot
- `# swapon /dev/sda2` Montiamo la partizione di swap



<br><br><br><br>

### UEFI btrfs

#### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.

`# lsblk -l`

Una volta individuata la nomenclatura del nostro disco usiamo **cfdisk**, qui ipotizzeremo di avere **/dev/sda**. Potrebbe essere richiesto il tipo di tabella di partizionamento se il disco e' vergine, in questo caso andiamo a selezionare **GUID Partition Table (GPT)**:

`# cfdisk /dev/sda`

Creiamo le partizioni necessarie all'installazione base, ipotizzando di avere un disco **SSD** da **128GiB**:

- `# 512Mib`  Creiamo la partizione EFI e scegliamo di tipo EFI system
- `# 27.5Gib`  Creiamo la partizione Root
- `# 100Gib`  Creiamo la partizione Home
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

#### Formattare le Partizioni

- `# mkfs.vfat -F32 /dev/sda1` La partizione EFI system in FAT32 per il boot
- `# mkfs.btrfs /dev/sda2` La partizione Root in BTRFS
- `# mkfs.btrfs /dev/sda3` La partizione Home in BTRFS

#### Montaggio delle Partizioni 

Creiamo i sottovolumi **@** e **@home**

- `# mount /dev/sda2 /mnt`           

- `# btrfs su cr /mnt/@`  

- `# umount /mnt`

- `# mount /dev/sda3 /mnt`

- `# btrfs su cr /mnt/@home`      

- `# umount /mnt`                             

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@ /dev/sda2 /mnt` 

- `# mkdir -p /mnt/{home,boot} creiamo la directory /home e la directory /boot`

- `# mount /dev/sda1 /mnt/boot` 

- `# mount -o noatime,ssd,space_cache=v2,compress=zstd,discard=async,subvol=@home /dev/sda3 /mnt/home`


<br><br><br><br>

### UEFI lvm

#### Partizionamento
Individuamo il nostro disco per conoscere la nomenclatura da usare ad Esempio: in caso di **SSD /dev/sda** oppure nel caso di **M.2 /dev/nvme0n1** infine il **Disco Virtuale /dev/vda**.


`# lsblk -l`

Ipotizzando di avere **3 dischi da 128GiB**  da usare con **LVM** **sda sdb sdc** useremo lo strumento **cfdisk** un operazione alla volta per disco:


`# cfdifk /dev/sda`

- `# 512Mib` Creiamo la partizione EFI e scegliamo di tipo EFI system
- `# 127.5GiB` Creiamo la partizione e scegliamo di tipo LVM
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

- `# cfdifk /dev/sdb`
- `# 128GiB` Creiamo la partizione e scegliamo di tipo LVM
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

- `# cfdifk /dev/sdc`
- `# 128GiB` Creiamo la partizione e scegliamo di tipo LVM
- `# write (yes)` e `quit`  Scriviamo le modifiche e usciamo

Per creare partizioni sotto LVM dobbiamo prima creare un volume fisico:

#### Creazione volume fisico

`# pvcreate /dev/sda2 /dev/sdb1 /dev/sdc1`

#### Creazione gruppo volume
Crea e estendi il tuo gruppo volume, è necessario creare un gruppo di volumi su uno o piu' volumi fisici `# vgcreate volume_group physical_volume` per esempio:

`# vgcreate lvm /dev/sda2 /dev/sdb1 /dev/sdc1`

Questo comando imposterà prima le tre partizioni come volumi fisici (se necessario) e quindi creerà il gruppo di volumi con i tre volumi. Il comando ti avviserà se rileva un filesystem esistente su qualsiasi dispositivo.

#### Creazione Volume Logici

Crea volumi logici, per una configurazione di base ne avremmo bisogno per root, swap e home.

- `# lvcreate -L 120G lvm -n root`
- `# lvcreate -L 8G lvm -n swap`
- `# lvcreate -l 100%FREE lvm -n home`

#### Formattare le Partizioni

`# mkfs.vfat -F32 /dev/sda1` La partizione EFI system in FAT32 per il boot

- `# mkfs.ext4 /dev/lvm/root`
- `# mkfs.ext4 /dev/lvm/home`
- `# mkswap /dev/lvm/swap` 

#### Montaggio delle Partizioni 

- `# mount /dev/lvm/root /mnt`
- `# mkdir -p /mnt/{home,boot}` creiamo la directory /home e la directory /boot
- `# mount /dev/lvm/home /mnt/home`
- `# mount /dev/sda1 /mnt/boot` 
- `# swapon /dev/lvm/swap`

#### Estendere un gruppo lvm

Se in futuro vorrete aggiungere un nuovo volume fisico al gruppo vediamo quale comando usare, ipotizzando un quarto disco sdd e di averlo partizionato come prima di tipo lvm, estendiamo lo spazio per esempio a `/dev/lvm/home`:

- `# pvcreate /dev/sdd1`
- `# vgextend lvm /dev/sdd1`
- `# lvextend -l +100%FREE /dev/lvm/home`



<br><br><br><br>





