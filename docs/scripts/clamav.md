Semplice script per la scansione antivirus settimanale.
scegliere nella variabile **weekday** il giorno della settimana in cui verificare il controllo.
lo script ad ogni avvio di sistema controllera' se e' il giorno impostato per la scansione, se lo e' verifica se non sia gia stata eseguita una scansione, verra salvato alla fine della scansione un log nella directory **av**.


### Prerequisiti

Installare clamav: 

- `$ sudo pacman -S clamav`

effettuare l'aggiornamento: 

- `$ sudo freshclam`

abilitare il servizio automatico degli aggiornamenti: 

- `$ sudo systemctl enable --now clamav-freshclam.service`

Per assicurarti che ClamAV e le definizioni siano installate correttamente, scansiona il file di test **EICAR** *(una firma innocua senza codice virus)* con `clamscan`.

- `$ curl https://secure.eicar.org/eicar.com.txt | clamscan -`

Creare la directory con i file per lo script: 

- `$ mkdir ~/clamav/av`
- `$ mkdir ~/clam/av/img`
- `$ echo "0" > ~/clamav/av/last`

aggiungiamo due icone per i messaggi di notifica: **~/clam/av/img/{icon1.svg,icon2.svg}**
adesso possiamo creare il nostro script **~/clamav/clamav.sh** che avvieremo con un file.desktop in **~/.config/autostart**:

```
#!/bin/bash
#
# Author : Jonathan Sanfilippo
# Date: Jun 2023
# Version 1.0.0: clamav weekly script
# Tested with www.eicar.org

# Set Scan
weekday="Tue" # see date +'%d'
dirscan="/" # directory of scan
terminal="kgx -e" # terminal for log

#variable
data=$(date +'%d')
target="$weekday"
current=$(date +'%a')
log="$HOME/.scripts/av/log" # log file
url="$HOME/.scripts/av/last" # date of the last scan
last=$(cat $url)
icon1="$HOME/.scripts/av/img/icon1.svg"
icon2="$HOME/.scripts/av/img/icon2.svg"



clamav(){

if [ "$target" = "$current" ]; then # check if it is the day of the week to scan
    if ! [ "$data" -eq "$last" ]; then # Compare today's date with the last scan
         
         echo "scan!"
         nice -n19  clamscan -ri  --remove=yes $dirscan > "$log";
         echo "$data" > $url
         
         MALWARE=$(tail "$log" | grep 'Infected' | cut -d " " -f3);
         if [ "$MALWARE" -eq "0" ];then
         
             ACTION=$(notify-send -i "$icon1" --action="Read Log" --action="Close"  "ClamAV"  "System Protect"   -u normal  -u critical )
                case "$ACTION" in
                      "0")
                         $terminal vim $log
                         ;;
                      "1")
                         exit
                         ;;
                esac
         else
             ACTION=$(notify-send -i "$icon2" --action="Read log" --action="Close"  "ClamAV"  "$MALWARE Malware Removed"  -u critical )
                case "$ACTION" in
                      "0")
                         $terminal vim $log
                         ;;
                      "1")
                         exit
                         ;;
                esac
         fi    
    else
        echo "already scan!"
    fi
else    
    echo "no scan for today!"
fi

}

clamav
```

