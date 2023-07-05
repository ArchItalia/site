# manjaro-mesa-codecs 🇮🇹

- Autore: bucch
- Repository: [GitLab : fontawesome-brands-gitlab: ](https://gitlab.com/th3bucch/manjaro-mesa-codecs)


# Manjaro Mesa Codecs

Uno script semplice per ricompilare il pacchetto `mesa` al fine di abilitare l'accelerazione hardware per i codec proprietari (ovvero H.264 e H.265).
**Non è necessario eseguire questo script se non si dispone di una scheda grafica AMD installata nel proprio sistema!**

## Background info

Dall'anno 2022, Manjaro Linux Team ha seguito la decisione di Fedora e openSUSE di disattivare i codec proprietari nel pacchetto mesa ridistribuito nel loro repository:
> **Mesa è ora alla versione 22.2.4**
>  Includa una modifica importante che disattiva l'accelerazione hardware per i codec video proprietari (in genere H.264 e H.265) quando si utilizza lo stack di driver Mesa.
>  I codec video aperti (VP8, VP9, AV1 - in base alle capacità hardware) non sono influenzati e possono essere ancora accelerati hardware "out of the box".
>  Questa modifica riguarda principalmente le schede grafiche AMD. (Le GPU Intel non utilizzano Mesa per l'accelerazione video, le schede Nvidia utilizzano il driver proprietario e l'accelerazione video Mesa non funziona correttamente con il driver Nouveau di opensource). E' possibile leggere di più sull'accelerazione video hardware [qui](https://wiki.archlinux.org/title/Hardware_video_acceleration) e in generale sull'argomento [qui](https://lists.fedoraproject.org/archives/list/devel@lists.fedoraproject.org/thread/PYUYUCM3RGTTN4Q3QZIB4VUQFI77GE5X/)

[(fonte)](https://forum.manjaro.org/t/stable-update-2022-12-06-kernels-mesa-plasma-cinnamon-nvidia-libreoffice-pipewire-virtualbox/128453)

## Requisiti
Installare i pacchetti richiesti prima di eseguire lo script con:

```
sudo pacman -S --needed base-devel git 
```

## Utilizzo

Clonare questo repository quindi eseguire lo script:

```
git clone https://gitlab.com/th3bucch/manjaro-mesa-codecs.git
cd manjaro-mesa-codecs
./manjaro-mesa-codecs.sh
```

## Suggerimenti

Il modo più semplice per eseguire questo script ovunque nel sistema è di creare un link simbolico ad una delle directory incluse nella variabile globale `$PATH` (ad es. `~/.local/bin`).
Verificare le directory disponibili con `echo $PATH`.

Eseguire il comando:
```
cd manjaro-mesa-codecs
ln -s $(pwd)/manjaro-mesa-codecs.sh /home/$USER/.local/bin/manjaro-mesa-codecs
```

lo script sarà disponibile a livello di sistema con il comando:
```
manjaro-mesa-codecs
```

## Pacman HOOK

*Da fare*

## Risoluzione dei problemi

Se `pacman` non riesce a verificare il pacchetto di codice sorgente restituendo il messaggio "unknown public key *key-id*", copiare l'ID della chiave fornita e importarla con:
```
gpg --recv-keys <key-id>
```
<br><br>


## manjaro-mesa-codecs 🇬🇧

- Autore: bucch
- Repository: [GitLab :fontawesome-brands-gitlab:](https://gitlab.com/th3bucch/manjaro-mesa-codecs)


# Manjaro Mesa Codecs

A simple script to re-compile the `mesa` package in order to enable hardware acceleration for proprietary codecs (i.e H.264 and H.265).  
**You shouldn't need to run this script if you don't have installed an AMD graphic card on your system!**

## Background info

Since december 2022 the Manjaro Linux Team has followed Fedora and openSUSE decision to disable proprietary codecs in the mesa package redistributed in their repository:
>  **Mesa is now at 22.2.4**
>    includes a notable change which disables hardware acceleration for proprietary video codecs (most commonly H.264 and H.265) when using the Mesa drivers stack.
>    Open video codecs (VP8, VP9, AV1 - based on your hardware capabilities) are unaffected and can still be hardware-accelerated out of the box.
>    This change mainly affects AMD graphics cards. (Intel GPUs don’t use Mesa for video acceleration, Nvidia cards use the proprietary driver, and Mesa video acceleration mostly doesn’t work properly with the opensource Nouveau driver) you can read more about hardware video acceleration [here](https://wiki.archlinux.org/title/Hardware_video_acceleration) and about that topic in general [here](https://lists.fedoraproject.org/archives/list/devel@lists.fedoraproject.org/thread/PYUYUCM3RGTTN4Q3QZIB4VUQFI77GE5X/)

[(source)](https://forum.manjaro.org/t/stable-update-2022-12-06-kernels-mesa-plasma-cinnamon-nvidia-libreoffice-pipewire-virtualbox/128453)

## Requirements
Install required packages before running the script with:

```
sudo pacman -S --needed base-devel git 
```

## Usage

Clone this repository then run the script:

```
git clone https://gitlab.com/th3bucch/manjaro-mesa-codecs.git
cd manjaro-mesa-codecs
./manjaro-mesa-codecs.sh
```

## Tips

Easiest way to run this script from everywhere on the system is to symlink it to one of the directories included in the `$PATH` global variable (i.e. `~/.local/bin`).
Check which directories are inclued using `echo $PATH`.

`cd` inside the cloned repository then run:
```
ln -s $(pwd)/manjaro-mesa-codecs.sh /home/$USER/.local/bin/manjaro-mesa-codecs
```

the script will be available systemwide for your user with the command:
```
manjaro-mesa-codecs
```

## Pacman HOOK

*TBD*

## Troubleshooting

If `pacman` cannot verify the source code package returning `unknown public key *key-id*` message, just copy the provided *key-id* and import that with:
```
gpg --recv-keys <key-id>
```
