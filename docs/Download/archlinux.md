# Download live iso


## Archiso

![iso](../images/live/iso.png)

Sempre aggiornata, dal server Italiano archmirror.it
 
[Download](https://archmirror.it/repos/iso/latest/archlinux-x86_64.iso)

Verifica della firma

E' raccomandata la verifica della firma dell'immagine prima dell'utilizzo, specialmente se è stata scaricata da un mirror HTTP, dove i download possono essere soggetti ad intercettazioni per fornire immagini malevoli. Su un sistema con GnuPG installato, eseguire il comando seguente per scaricare la firma PGP ISO > nella directory col file ISO, e verificarla con:

`$ gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

In alternativa, da un'installazione esistente di Arch Linux eseguire:

`$ pacman-key -v archlinux-version-x86_64.iso.sig`

<br><br><br><br>

## VM image

![iso](../images/live/vm.png)

Immagini ufficiali per macchina virtuale, l'immagine di base è pensata per l'uso locale ed è preconfigurata con (utente: arch - password: arch) e sshd in esecuzione.

[Download](https://gitlab.archlinux.org/archlinux/arch-boxes/-/jobs/artifacts/master/browse/output?job=build:secure)

<br><br><br><br>

## Docker

![iso](../images/live/dck.png)

Immagine ufficiale Docker

`docker pull archlinux`

[Download](https://hub.docker.com/_/archlinux)

<br><br><br><br>

## ARM

![iso](../images/live/arm.png)

Arch Linux ARM è una distribuzione di Linux per computer ARM

[Download](https://archlinuxarm.org/about/downloads)


<br><br><br><br>
