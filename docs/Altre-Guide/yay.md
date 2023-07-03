Yay è un helper per il AUR di Archlinux che semplifica notevolmente la gestione dei pacchetti. Il AUR (Arch User Repository) è una collezione di pacchetti mantenuti dagli utenti di Archlinux, che non sono ufficialmente supportati dal team di sviluppo di Arch.

Cos'è Yay AUR helper?

Yay è una versione avanzata di Yaourt, un'altra utility per la gestione di pacchetti AUR. Tuttavia, Yay ha alcune funzionalità aggiuntive che fanno la differenza, come:

- Installazione automatica delle dipendenze
- Compatibilità con pacman, il gestore di pacchetti ufficiale di Archlinux
- Interfaccia completa per la gestione dei pacchetti (installazione, aggiornamento, rimozione e ricerca)
- Configurazione delle impostazioni predefinite attraverso il file yay.conf

Come installare Yay AUR helper?

```
pacman -S --needed git base-devel
clone di git https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```
<br><br>

Ecco la lista completa di tutti i comandi esistenti per Yay:

- `$ yay`  *nessuna opzione* - Aggiorna il database dei pacchetti e cerca nuovi aggiornamenti per i pacchetti installati.
- `$ yay -h` - Mostra la guida degli aiuti per Yay.
- `$ yay -S nome_pacchetto` - Installa un pacchetto dal repo dei pacchetti.
- `$ yay -Su` - Aggiorna solo i pacchetti installati che hanno aggiornamenti disponibili.
- `$ yay -Syyu` - Aggiorna il database dei pacchetti e tutti i pacchetti installati nel sistema.
- `$ yay -R nome_pacchetto` - Rimuove un pacchetto dal sistema.
- `$ yay -Rs nome_pacchetto` - Rimuove il pacchetto e tutte le sue dipendenze che non sono utilizzate da altri pacchetti.
- `$ yay -Syu nome_pacchetto` - Aggiorna solo il pacchetto specificato.
- `$ yay -Ss nome_pacchetto` - Cerca un pacchetto tramite il nome.
- `$ yay -Si nome_pacchetto` - Mostra informazioni dettagliate sul pacchetto specificato, come descritto e dipendenze.
- `$ yay -Q` - Mostra la lista dei pacchetti installati nel sistema.
- `$ yay -Qe` - Mostra solo i pacchetti esplicitamente installati dall'utente (escludendo quelli installati come dipendenze).
- `$ yay -Ql nome_pacchetto` - Mostra i file di un pacchetto specificato.
- `$ yay -Qu` - Verifica se ci sono aggiornamenti disponibili per i pacchetti installati.
- `$ yay -Qdt` - Mostra le dipendenze non utilizzate nella cache dei pacchetti.
- `$ yay -Y` - Scarica e visualizza il PKGBUILD di un pacchetto specificato senza installarlo.
- `$ yay -Yc` - Rimuove i pacchetti archiviati nella cache che non sono installati. 
- `$ yay -G nome_pacchetto` - Scarica il pacchetto senza installarlo. 
- `$ yay -P nome_pacchetto` - Crea un pacchetto a partire dai sorgenti.
- `$ yay -Sc` - Scansione alla ricerca di fonti di dati più vecchie rispetto a quelle attualmente installate.
- `$ yay -Sl` - Mostra la lista dei repo dei pacchetti.
- `$ yay -Syy` - Aggiorna il database dei pacchetti.
- `$ yay -U ` `nome_file_pacchetto` - Aggiorna un pacchetto installato o installa un nuovo pacchetto dal file di pacchetto locale.
- `$ yay -F ` `nome_pacchetto` - Ricarica i pacchetti disabilitati in modo sicuro.
- `$ yay -Qm` - Mostra l'elenco dei pacchetti AUR installati nel sistema.
- `$ yay -Rns` - Rimuove un pacchetto e tutte le sue dipendenze, incluse quelle che vengono utilizzate da altri pacchetti in modo sicuro.
- `$ yay -Sdd` - Installa un pacchetto e le sue dipendenze necessarie in modo sicuro.
- `$ yay -Yc --aur` - Rimuove i pacchetti della cache AUR.
- `$ yay -Fyy` - Forza la ri-sincronizzazione della cache.
- `$ yay -Fy` - Aggiorna i pacchetti che si trovano nella cache della repo.
- `$ yay -Scu` - Effettua una scansione per verificare che i pacchetti installati e le dipendenze siano aggiornati in modo sicuro.
- `$ yay -Qkk` - Aggiorna la lista dei pacchetti archiviati orfani.
- `$ yay -Scc` - Cancella tutti i dati della cache dei pacchetti.
