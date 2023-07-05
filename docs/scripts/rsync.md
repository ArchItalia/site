Semplice script da avviare con un file.desktop in **~/.config/autostart**, impostare ogni quanto lo sript effettua il backup e definire le directory.

```
#!/bin/bash
#
# Author : Jonathan Sanfilippo
# Date: Jun 2023
# Version 1.0.0: rsync backup
#

checkSpace=$(df -h /path | awk '{print $4}')
data=$(date +'%H:%M')
dirBak="/path/rsync-backup"
time="10800" #3h

backup(){
rsync -zvrah  /your/path1     $dirBak
rsync -zvrah  /your/path2     $dirBak
rsync -zvrah  /your/path3     $dirBak
rsync -zvrah  /your/path4     $dirBak
notify-send   "rsync" "Backup done. Space $checkSpace" -u normal
}


while true; do
  backup
  sleep $time
done
```
