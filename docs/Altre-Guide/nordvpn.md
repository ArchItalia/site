Installazione  e Configurazione

1 - Installiamo nordvpn-bin:

```
$ git clone https://aur.archlinux.org/nordvpn-bin.git
$ cd nordvpn-bin
$ makepkg -si
```


2 - Aggiungiamo l'utente al gruppo nordvpn, e abilitiamo il servizio:

```
$ sudo usermod -aG nordvpn $USER
$ sudo systemctl enable --now nordvpnd
$ reboot
```

3 - Effettuamo il login al nostro account Apriamo il terminale e lanciate il comando:

`$ nordvpn login`


4 - Seguiamo le indicazioni da terminale facendo CTRL + click sul link nel terminale che si aprira' nel browser, quindi effettuamo l'accesso.

![Screenshot from 2023-07-05 16-54-44](https://github.com/ArchItalia/site/assets/117321045/042a2509-9ee6-4aa1-ad32-2de5bcded5e5)

5 - Una volta effettuato l'accesso al nostro account, possiamo collegarci e scollegarci con questi comandi :

Connessione rapida, solitamente sullo stesso paese da dove siete connessi

`$ nordvpn connect oppure $ nordvpn c`

Connessione specifica per un paese

`$ nordvpn connect italy oppure  $ nordvpn c it`
 
Disconnessione di Nordvpn

`$ nordvpn disconnect oppure  $ nordvpn d`

Verifichiamo lo stato di NordVPN

`$ nordvpn status`



