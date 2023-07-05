### Installazione

1 - Installa il pacchetto rsync.

`# pacman -S rsync`

**rsync** deve essere installato sia sul computer di origine che su quello di destinazione.

<br>

### Usare rsync

**Il comando rsync**

rsync opzioni sorgente destinazione ad esempio:

`# rsync -zvrah ~/Desktop/devdev /Volumes/DiscoEsterno/Backup`

Trasferiamo tutti i file contenuti nella directory sorgente ~/Desktop/devdev alla directory di destinazione /Volumes/DiscoEsterno/Backup. Alla prima esecuzione, rsync copierà l’intero contenuto, dato che la directory destinazione è vuota. Nei casi successivi, ogni file che avrà subito modifiche nella cartella sorgente verrà sovrascritto in quella di destinazione. In questo caso abbiamo usato le opzioni -vrah, vediamo cosa significano.

Opzioni comuni

**-v** significa verbose, avremo più informazioni a schermo

**-r** significa recursive, con questa opzione rsync leggerà nelle cartelle

**-h** trasforma in byte in un formato più leggibile (es. Mb, Gb)

**-a** significa archive. Questa modalità corrisponde a scrivere -rlptgoD e riporta tutte le condizioni originali del file dalla destinazione alla sorgente come timestamp, link simbolici, permessi, proprietario e gruppo

**-z** dice a rsync di comprimere i dati trasmessi

**--delete** dice a rsync di cancellare sulla destinazione i file non presenti nella sorgente

**--exclude** dice a rsync quali file o directory ignorare

**--progress** mostra a schermo il trasferimento dei file

**-u** imposta che i file presenti in destinazione più nuovi di quelli contenuti in sorgente vengano ignorati

<br>

### Sincronizzazione tra due directory locali

`# rsync -vrah ~/Desktop/Grafica /Volumes/DiscoEsterno/backup`

In questo esempio sincronizziamo la directory **~/Desktop/Grafica** con **/Volume/DiscoEsterno/backup**, con le opzioni verbose, recursive e archive.

```
# rsync -vrah ~/Desktop/Grafica /Volumes/DiscoEsterno/backup
building file list ... done
created directory /Volumes/DiscoEsterno/backup
backup/
backup/.DS_Store
backup/2222.png
backup/6y3pg5x7.png
backup/bacdd5f1e202772aa0bca470500c8701.jpg
backup/bb.jpg
backup/logo.jpg
backup/nasa1.png
backup/nasa4.png

sent 4.11M bytes  received 268 bytes  8.22M bytes/sec
total size is 4.11M  speedup is 1.00
```


La cartella di destinazione non esisteva, rsync l’ha creata automaticamente.

<br>

### Visualizzare l’avanzamento del trasferimento

Se vogliamo visualizzare l’avanzamento durante il trasferimento, è sufficiente usare l’opzione `--progress`

```
rsync -vrah --progress ~/Desktop/Grafica /Volumes/DiscoEsterno/backup
building file list ... 
6 files to consider
created directory /Volumes/DiscoEsterno/backup
backup/
backup/APOLLO_11_EVA_Charlesworth-Lunney-Kranz-S69-44137-Science_Center_cr.jpg
     403.45K 100%   29.60MB/s    0:00:00 (xfer#0, to-check=6/6)
backup/APOLLO_11_EVA_Charlesworth-Lunney-Kranz-S69-44137-Science_Center_cr2.jpg
     143.82K 100%    9.14MB/s    0:00:00 (xfer#1, to-check=5/6)
backup/bacdd5f1e202772aa0bca470500c8701.jpg
       1.66M 100%   33.60MB/s    0:00:00 (xfer#2, to-check=4/6)
backup/bb.jpg
     654.59K 100%   12.01MB/s    0:00:00 (xfer#3, to-check=3/6)
backup/logo.jpg
       5.36K 100%  100.64kB/s    0:00:00 (xfer#4, to-check=2/6)
backup/nasa1.png
      61.47K 100%    1.09MB/s    0:00:00 (xfer#5, to-check=1/6)
backup/nasa4.png
      54.68K 100%  970.88kB/s    0:00:00 (xfer#6, to-check=0/6)

sent 4.11M bytes  received 268 bytes  8.22M bytes/sec
total size is 4.11M  speedup is 1.00
```
<br>

### Esclusione di file o directory

Con l’opzione `--exclude`, possiamo specificare quali file, tipi di file o directory escludere dalla sincronizzazione. In quest’esempio abbiamo scelto di ignorare i file “.DS_Store” creati dal sistema macOS usando l’opzione --exclude=.DS_Store.

`rsync -vrah --exclude=.DS_Store ~/Desktop/Grafica /Volumes/DiscoEsterno/backup`

<br>

### L’opzione –delete

Se un file o directory non esiste nella sorgente, ma esiste già nella destinazione, con l’opzione `--delete` possiamo cancellarlo da quest’ultima.

```
rsync -vrah --delete ~/Desktop/Grafica /Volumes/DiscoEsterno/backup
building file list ... done
deleting test.txt
./

sent 423 bytes  received 26 bytes  898.00 bytes/sec
total size is 4.11M  speedup is 9151.07
```
<br>

### Effettuare una simulazione (dry run)

Se è la prima volta che usiamo rsync, oppure vogliamo avere un’anteprima di cosa succederà una volta eseguito, è possibile simulare un comando rsync per assicurarci di non esguire operazioni errate. Aggiungiamo l’opzione `--dry-run`

`rsync -vrah --dry-run ~/Desktop/Grafica /Volumes/DiscoEsterno/backup`

<br>

### Sincronizzare una directory remota via SSH

Con rsync, possiamo usare SSH (secure shell) come modalità di trasferimento: usare il protocollo SSH ci garantisce che i nostri dati viaggino su un canale sicuro e criptato, in modo che nessuno possa leggere i dati mentre vengono trasmessi. Per specificare che si tratta di una connessione SSH, dobbiamo includere l’opzione `-e ssh` ed indicare destinazione o sorgente in modo appropriato.

`rsync -vrahe ssh ~/Desktop/Grafica root@192.168.0.100:/var/backup`

Inoltre il prompt dei comandi ci chiederà la password dell’utente (in esempio root) quando effettuiamo il trasferimento, che avverrà sempre in modo sicuro.

<br>


### Cancellare il contenuto una volta trasferito

In qualche caso potremmo decidere di cancellare automaticamente il contenuto di sorgente una volta che il trasferimento in destinazione sia avvenuto con successo. Per farlo, specifichiamo `--remove-source-files` tra le opzioni

`rsync -vrah --remove-source-files ~/Desktop/Grafica /Volumes/DiscoEsterno/backup`

<br>

### Impostare un limite di banda (bandwidth limit)

Quando effettuiamo sincronizzazioni tra directory remote, a volte è necessario impostare un limite di velocità. Pensiamo ad esempio ad un backup remoto in esecuzione mentre stiamo lavorando: limitiamo la banda in upload con l’opzione `--bwlimit=KBPS`, esempio:

`rsync --bwlimit=100 -vrahe ssh  ~/Desktop/Grafica root@192.168.0.100:/var/backup`

In questo modo limiteremo la banda a 100Kb/s.
