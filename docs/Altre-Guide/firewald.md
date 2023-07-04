### Installazione e uso

Installiamo firewalld e abilitiamo il servizio.

- `$ pacman -S firewalld`
  
- `$ sudo systemctl enable firewalld --now`

Verifichiamo lo stato del servizio.

- `$ sudo systemctl status firewalld`

Potete vedere tutte le vostre configurazioni e impostazioni in una volta sola con questo comando:

- `$ sudo firewall-cmd --list-all`

<br><br>

### Gestire le zone

Prima di ogni altra cosa, devo spiegare le zone. Le zone sono una caratteristica che fondamentalmente permette di definire diversi set di regole per diverse situazioni. Le zone sono una parte enorme di firewalld, quindi è utile capire come funzionano.

Se la tua macchina ha più modi per connettersi a reti diverse (ad esempio, Ethernet e WiFi), puoi decidere che una connessione è più affidabile dell'altra. Potresti impostare la tua connessione Ethernet nella zona "trusted" se è collegata solo a una rete locale che hai costruito, e mettere il WiFi (che potrebbe essere collegato a Internet) nella zona "public" con restrizioni più severe.

    !!! note "nota"

    Una zona può solo essere in uno stato attivo se ha una di queste due condizioni:
    La zona è assegnata a un'interfaccia di rete.
    Alla zona vengono assegnati IP sorgente o intervalli di rete.

<br><br>

Le zone predefinite includono le seguenti 

**drop**: Il livello più basso di fiducia. Tutte le connessioni in entrata sono abbandonate senza risposta e solo le connessioni in uscita sono possibili.

**block**: Simile al precedente, ma invece di abbandonare semplicemente le connessioni, le richieste in entrata sono rifiutate con un messaggio icmp-host-prohibited o icmp6-adm-prohibited.

**public**: Rappresenta le reti pubbliche, non fidate. Non ti fidi degli altri computer, ma puoi permettere connessioni in entrata selezionate caso per caso.

**internal**: L'altro lato della zona esterna, usata per la parte interna di un gateway. I computer sono abbastanza affidabili e sono disponibili alcuni servizi aggiuntivi.

**dmz**: utilizzato per i computer situati in una DMZ (computer isolati che non avranno accesso al resto della vostra rete). Solo certe connessioni in entrata sono permesse.

**work**: Usato per le macchine da lavoro. Fidatevi della maggior parte dei computer della rete.

**home**: Un ambiente domestico. Generalmente implica che vi fidate della maggior parte degli altri computer e che qualche servizio in più sarà accettato. Qualche altro servizio potrebbe essere permesso.

**trusted**: Fidati di tutte le macchine della rete. La più aperta delle opzioni disponibili e dovrebbe essere usata con parsimonia.
<br><br>

### Comandi di gestione zone

Per vedere la vostra zona predefinita, esegui:

- `$ firewall-cmd --get-default-zone`

Per vedere quali zone sono attive e fanno cosa, esegui:

- `$ firewall-cmd --get-active-zones`


Per cambiare la zona predefinita:

- `$ firewall-cmd --set-default-zone [tua-zona]`

Per aggiungere un'interfaccia di rete a una zona:

- `$ firewall-cmd --zone=[tua-zona] --add-interface=[tua-intefaccia-di-rete]`


Per cambiare la zona di un'interfaccia di rete:

- `$ firewall-cmd --zone=[tua-zona] --change-interface=[tua-interfaccia-di-rete]`

Per rimuovere completamente un'interfaccia da una zona:

- `$ firewall-cmd --zone=[tua-zona] --remove-interface=[tua-intefaccia-di-rete]`


Per creare una zona nuova con un set di regole personalizzate, e per controllare che sia stata aggiunta correttamente:

- `$ firewall-cmd --new-zone=[tua-nuova-zona]`
- `$ firewall-cmd --get-zones`
  
<br><br>

### Gestione delle porte

Per vedere tutte le porte aperte:

- `$ firewall-cmd --list-ports`

Per aggiungere una porta alla vostra zona firewall:

- `$ firewall-cmd --zone=public --add-port=9001/tcp`


Per rimuovere una porta, basta usare il comando:

- `$ firewall-cmd --zone=public --remove-port=9001/tcp`

### Gestione dei servizi

Questo è il modo preferito per aprire le porte per questi servizi comuni, e molto di più:

   - **HTTP** e **HTTPS**: per i server web
   - **FTP**: per spostare i file 
   - **SSH**: per controllare macchine remote e spostare file avanti e indietro nel nuovo modo
   - **Samba**: Per la condivisione di file con macchine Windows

<br><br>

Per vedere un elenco di tutti i servizi disponibili:

- `$ firewall-cmd --get-services`

Per vedere quali servizi hai attualmente attivi sul tuo firewall:

- `$ firewall-cmd --list-services`


Per aprire un servizio nel tuo firewall (ad esempio HTTP nella zona pubblica):

- `$ firewall-cmd --zone=public --add-service=http`


Per rimuovere/chiudere un servizio sul vostro firewall:

- `$ firewall-cmd --zone=public --remove-service=http`


<br><br>

### Limitare l'accesso

Diciamo che avete un server e non volete renderlo pubblico. se volete definire solo chi è autorizzato ad accedere via SSH, o visualizzare alcune pagine web/app private, potete farlo.

Ci sono un paio di metodi per farlo. Per prima cosa, per un server più chiuso, puoi scegliere una delle zone più restrittive, assegnare il tuo dispositivo di rete ad essa, aggiungere il servizio SSH come mostrato sopra, e poi mettere in whitelist il tuo indirizzo IP pubblico in questo modo:

<br><br>

    !!! warning "Attenzione"
    Non rimuovere mai il servizio SSH dal firewall di un server remoto!
    Ricordate, SSH è quello che usate per accedere al vostro server. A meno che non abbiate un altro modo per accedere al server fisico, o alla sua shell (cioè tramite. un pannello di         controllo fornito dall'host), la rimozione del servizio SSH vi bloccherà permanentemente.
    Dovrete contattare il supporto per riavere il vostro accesso, o reinstallare completamente il sistema operativo.

<br><br>

- `$ firewall-cmd --permanent --zone=trusted --add-source=192.168.1.0 [< insert your IP here]`

Potete renderlo un intervallo di indirizzi IP aggiungendo un numero più alto alla fine, in questo modo:

- `$ firewall-cmd --permanent --zone=trusted --add-source=192.168.1.0/24 [< insert your IP here]`

Di nuovo, basta cambiare **--add-source** in **--remove-source** per invertire il processo
