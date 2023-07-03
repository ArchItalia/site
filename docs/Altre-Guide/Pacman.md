
I pacchetti di software vengono scaricati e gestiti attraverso i repositories, che sono cataloghi online dove sono archiviati i pacchetti. Pacman permette di installare, aggiornare e rimuovere pacchetti tramite una semplice interfaccia da linea di comando, con la possibilità di personalizzarla tramite opzioni aggiuntive.

Pacman è in grado di gestire le dipendenze tra i pacchetti, garantendo la compatibilità tra di essi e la corretta installazione di tutti i pacchetti necessari per il funzionamento di un'applicazione.

### Configurazione

La configurazione di Pacman è in **/etc/pacman.conf**. Ulteriori informazioni riguardo il file di configurazione possono essere trovate usando il comando:

`$ man pacman.conf`

Evitare l'aggiornamento di un pacchetto

Per evitare di aggiornare la versione di un pacchetto attraverso il file di configurazione **/etc/pacman.conf**, aggiungere la linea:

`$ IgnorePkg=nomepacchetto`

Evitare l'aggiornamento di un gruppo di pacchetti

Per evitare di aggiornare la versione di un gruppo di  pacchetti esempio gnome attraverso il file di configurazione **/etc/pacman.conf**, aggiungere la linea:

`$ IgnoreGroup=gnome`

### Repositories

In questa sezione puoi definire che repositories usare, come specificato in **pacman.conf**. Possono essere definite direttamente qui o puoi aggiungerle da un altro file. Tutti i repositories ufficiali utilizzano lo stesso file **/etc/pacman.d/mirrorlist** dov'è contenuta una variabile `$repo` in modo da mantenere una sola lista:

```
[core]
include = /etc/pacman.d/mirrorlist

[extra]
include = /etc/pacman.d/mirrorlist

[multilab]
include = /etc/pacman.d/mirrorlist
```

### Comandi 

Ecco una lista esaustiva dei comandi di pacman:

- `# pacman -S`          Installa un pacchetto, scaricandolo dai repositories.
- `# pacman -R`          Rimuove un pacchetto dal sistema, insieme ai suoi dipendenze non necessarii.
- `# pacman -U`          Installa o Aggiorna un pacchetto locale. 
- `# pacman -Sy`         Aggiorna l'elenco dei pacchetti scaricabili dai repositories.
- `# pacman -Syu`        Aggiorna il sistema, scaricando i pacchetti più recenti dai repositories.
- `# pacman -Syy`        Aggiorna completamente l'elenco dei pacchetti scaricabili dai repositories.
- `# pacman -Su`         Aggiorna tutti i pacchetti del sistema.
- `# pacman -Sw`         Scarica un pacchetto senza installarlo.
- `# pacman -Sg`        Mostra i gruppi di pacchetti disponibili.
- `# pacman -Sgg`       Mostra i pacchetti presenti in tutti i gruppi di pacchetti disponibili.
- `# pacman -Si`         Mostra le informazioni descrittive di un pacchetto disponibile nei repositories.
- `# pacman -Ss`         Cerca un pacchetto nei repositories.
- `# pacman -Scc`       Rimuove tutti i pacchetti in cache dal database di pacman.
- `# pacman -Sc`        Rimuove pacchetti non più disponibili nei repositories dalla cache di pacman.
- `# pacman -Qu`        Mostra i pacchetti che necessitano di aggiornamento disponibili nei repositories.
- `# pacman -Q`          Mostra i pacchetti installati nel sistema.
- `# pacman -Qc`        Mostra i file di configurazione dei pacchetti che non fanno parte del sistema base.
- `# pacman -Qi`         Mostra le informazioni dettagliate di un pacchetto installato.
- `# pacman -Qk`        Verifica integrità dei file di un pacchetto con md5sums.
- `# pacman -Qu`        Mostra i pacchetti che necessitano di aggiornamento disponibili nei repositories.
- `# pacman -Ql`         Mostra tutti i file installati da un pacchetto.
- `# pacman -Qm`      Mostra i pacchetti esplicitamente installati dall'utente, non presenti nei repositories ufficiali.
- `# pacman -Qo`        Mostra il pacchetto proprietario di un file specifico.
- `# pacman -Qp`        Mostra le informazioni descrittive di un pacchetto locale.
- `# pacman -Qs`         Cerca un pacchetto tra quelli già installati nel sistema.
- `# pacman -Qu`        Mostra i pacchetti che necessitano di aggiornamento disponibili nei repositories.
- `# pacman -Uu`      Scarica e installa in modo automatico le dipendenze di un pacchetto locale.
- `# pacman -Qdt`       Mostra i pacchetti orfani, non più necessari a dipendenze già rimosse.
- `# pacman -Rs`         Rimuove un pacchetto, insieme ai pacchetti dipendenti da esso e non più utilizzati da nessun altro pacchetto.
- `# pacman -Rdd`       Forza la rimozione di un pacchetto e delle dipendenze.
- `# pacman -Rn`        Rimuove un pacchetto, senza rimuovere le dipendenze non necessarie.
- `# pacman -Rns`       Rimuove un pacchetto, insieme alle dipendenze non necessarie.
- `# pacman -Syu --ignore <pacchetto>`   Aggiorna il sistema, ignorando un pacchetto specifico.
- `# pacman -Sw <pacchetto>`            Scarica un pacchetto specifico, senza installarlo.
- `# pacman -Qu --color auto`          Mostra i pacchetti che necessitano di aggiornamento, evidenziando le informazioni più importanti con i colori.
- `# pacman -Qk <pacchetto>`           Verifica integrità dei file di un pacchetto specifico con md5sums.
- `# pacman -U <pacchetto.tar.xz>`     Installa/aggiorna un pacchetto da un file locale.
- `# pacman -Sdd <pacchetto>`          Forza il tentativo di installazione di un pacchetto senza dipendenze requiste, ognuna di esse viene ignorata.




































