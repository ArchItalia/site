Creare un comando semplificato `$ clean`  per personalizzare la pulizia del sistema.

![Screenshot from 2023-07-09 16-34-29](https://github.com/ArchItalia/site/assets/117321045/569c06a6-ead7-447b-9b50-3cf6a4bcf034)



`$ sudo vim /usr/bin/clean`

<br>

```
#!/bin/bash

echo -e "\e[33mVerifica dei pacchetti e delle dipendenze non più necessarie..\e[0m"

if pacman -Qdt &> /dev/null; then
    echo -e "\e[33mEliminazione dei pacchetti e delle dipendenze non più necessarie..\e[0m"
    pacman -Qdt | awk '{print $1}' | sudo pacman -Rs -
else
    echo -e "\e[33mNon ci sono pacchetti da eliminare.\e[0m"
fi

echo -e "\e[33mVerifica pacchetti non più disponibili nei repositories dalla cache di pacman\e[0m"
sudo pacman -Scc 
echo ""
echo -e "\e[33mVerifica lo spazio occupato dalla directory ~/.cache\e[0m"
cache_size=$(du -sh ~/.cache | awk '{ print $1 }')
echo -e "\e[33mLo spazio occupato dalla directory ~/.cache è di $cache_size.\e[0m"
echo ""
echo -e "\e[33mVerifica lo spazio occupato dal cestino\e[0m"
trash_size=$(du -sh ~/.local/share/Trash/files | awk '{ print $1 }')
echo -e "\e[33mLo spazio occupato dal cestino è di $trash_size.\e[0m"
echo ""
read -p "Vuoi svuotare il cestino e cancellare la directory ~/.cache? Rispondi con 'y' o 'n': " answer

if [ $answer = "y" ]; then
    # Svuota il cestino
    rm -rf ~/.local/share/Trash/files/*

    # Cancella la directory ~/.cache
    rm -rf ~/.cache/*

    echo -e "\e[33mIl cestino e la directory ~/.cache sono stati cancellati.\e[0m"
else
    echo -e "\e[33mNessuna azione intrapresa.\e[0m"
fi

echo -e "\e[32mPulizia terminata!\e[0m"
```
<br>

`$ sudo chmod +x /usr/bin/clean`


