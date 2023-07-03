Per effettuare il downgrade di uno o più pacchetti su Arch Linux, segui questi passaggi:

1. Verifica la versione attuale del pacchetto:
- `# pacman -Qi nomepacchetto` Mostra informazioni sul pacchetto, tra cui la versione attuale.

2. Verifica le versioni disponibili del pacchetto:
- `# pacman -Syyu` Aggiorna i database di pacman.
- `# pacman -Sii nomepacchetto | grep Version` Mostra tutte le versioni disponibili del pacchetto.

3. Rimuovi il pacchetto attuale:
- `# pacman -Rs nomepacchetto` Rimuovi il pacchetto e le sue dipendenze.

4. Installa la versione desiderata del pacchetto:
- `# pacman -U /var/cache/pacman/pkg/nomepacchetto-versione.pkg.tar.zst` Installa la versione del pacchetto specificata.

5. Impedisci l'aggiornamento del pacchetto:
Aggiungi il pacchetto alla blacklist di pacman per impedirne l'aggiornamento automatico in futuro.
- `# nano /etc/pacman.conf` Apri il file di configurazione di pacman.
- Aggiungi `IgnorePkg = nomepacchetto` alle sezioni [IgnorePkg] e [IgnoreGroup].
- Salva e chiudi il file.

Ricorda che il downgrade di un pacchetto può causare problemi di dipendenze e creare instabilità nel sistema. Assicurati di comprendere i rischi prima di procedere.

<br><br>

L'utilità `downgrade` presente su AUR semplifica notevolmente il processo di downgrade dei pacchetti su Arch Linux. Ecco come installare e utilizzare `downgrade`:

1. Assicurati di avere AUR abilitato nel tuo sistema. Se non lo hai già fatto, installa il pacchetto `yay` che ti permetterà di accedere ai pacchetti AUR. Puoi farlo seguendo questi comandi:
```
# pacman -Syu
# pacman -S --needed git base-devel
# git clone https://aur.archlinux.org/yay.git
# cd yay
# makepkg -si
```

2. Installa `downgrade` utilizzando `yay`:
```
$ yay -S downgrade
```

3. Esegui `downgrade` per selezionare e installare una versione precedente di un pacchetto. Ad esempio, per eseguire il downgrade del pacchetto `nomepacchetto`, puoi utilizzare il seguente comando:
```
$ sudo downgrade nomepacchetto
```

4. Seleziona la versione desiderata dal menu che viene visualizzato. `downgrade` mostrerà le diverse versioni disponibili e ti chiederà quale installare.

`downgrade` si occuperà automaticamente di scaricare e installare la versione del pacchetto da te selezionata, oltre a gestire eventuali dipendenze. È uno strumento molto utile per effettuare il downgrade sicuro e controllato dei pacchetti su Arch Linux.
