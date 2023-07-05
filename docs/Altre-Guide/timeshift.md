Installazione

1 - Per usare Timeshift sul nostro arch Linux per creare snapshots, abbiamo bisogno di un sistema che sia creato con File System BTRFS e con minimo i sottovolumi @, @/home. Se non sapete come installare un sistema con questi requisiti seguite la guida>.
Dando per scontato che il vostro sistema sia configurato come richiesto, installiamo Timeshift:

`$ git clone https://aur.archlinux.org/timeshift.git`

`$ cd timeshift`

`$ makepkg -Si`

2 - Una volta installato Timeshift, necessitiamo di avere installato e attivato il servizio di cronie per automatizzare gli snapshots, se non lo avete ancora fatto procediamo cosi:

`$ sudo pacman -S cronie`

`$ sudo systemctl enable cronie --now`

3 -  A questo punto apriamo Timeshift e impostiamo la partizione btrfs del sistema quindi scegliamo la configurazione per fare gli snapshots che piu ci sembra utile per il nostro sistema, ad esempio ogni ora :

![Screenshot from 2023-07-05 14-52-12](https://github.com/ArchItalia/site/assets/117321045/a31ced93-27d5-47e2-b13d-56c6562c274e)


4 -  E' possibile ripristinare il sistema dall'interfaccia grafica di Timeshift, ma vediamo quali sono i comandi da terminale con un esempio, per vedere la lista completa fare riferimento a man:

```
$ timeshift --list

$ timeshift --list --snapshot-device /dev/sda2

$ timeshift --create --comments "after update" --tags D

$ timeshift --restore

$ timeshift --restore --snapshot '2023-10-12_16-29-08' --target /dev/sda2
```
