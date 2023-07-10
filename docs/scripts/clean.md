🇬🇧 [English](#eng) - 🇮🇹 [Italian](#ita)


# ita

🇮🇹 Creare un comando semplificato `$ clean`  per personalizzare la pulizia del sistema.

![image](https://github.com/ArchItalia/site/assets/117321045/bf39f1e0-33b7-47d4-b984-fae2cdbe56f4)





`$ sudo vim /usr/bin/clean`

<br>

```
#!/bin/bash

echo -e "\e[33mVerifica dei pacchetti e delle dipendenze non più necessarie..\e[0m"

if pacman -Qdt &> /dev/null; then
    echo  "Eliminazione dei pacchetti e delle dipendenze non più necessarie.."
    pacman -Qdt | awk '{print $1}' | sudo pacman -Rs -
else
    echo  "Non ci sono pacchetti da eliminare."
fi
echo ""
echo -e "\e[33mVerifica pacchetti non più disponibili nei repositories dalla cache di pacman\e[0m"
sudo pacman -Sc
echo ""
echo -e "\e[33mVerifica lo spazio occupato dalla directory ~/.cache\e[0m"
cache_size=$(du -sh ~/.cache | awk '{ print $1 }')
echo  "Lo spazio occupato dalla directory ~/.cache è di $cache_size."
echo ""
echo -e "\e[33mVerifica lo spazio occupato dal cestino\e[0m"
trash_size=$(du -sh ~/.local/share/Trash/files | awk '{ print $1 }')
echo "Lo spazio occupato dal cestino è di $trash_size."
echo ""
read -p "Vuoi svuotare il cestino e cancellare la directory ~/.cache? Rispondi con 'y' o 'n': " answer

if [ $answer = "y" ]; then
    # Svuota il cestino
    rm -rf ~/.local/share/Trash/files/*

    # Cancella la directory ~/.cache
    rm -rf ~/.cache/*

    echo  "Il cestino e la directory ~/.cache sono stati cancellati."
else
    echo  "Nessuna azione intrapresa."
fi
echo ""
echo -e "\e[32mPulizia terminata!\e[0m"
```
<br>

`$ sudo chmod +x /usr/bin/clean`

<br><br>

# eng

🇬🇧 Create a simplified command `$ clean` to customize the system cleanup.

![image](https://github.com/ArchItalia/site/assets/117321045/bf07d1c6-4ddc-49fa-911b-4c2d6911749a)



`$ sudo vim /usr/bin/clean`

<br>

```
#!/bin/bash

echo -e "\e[33mChecking for obsolete packages and dependencies..\e[0m"

if pacman -Qdt &> /dev/null; then
    echo  "Removing obsolete packages and dependencies.."
    pacman -Qdt | awk '{print $1}' | sudo pacman -Rs -
else
    echo  "No packages to remove."
fi
echo ""
echo -e "\e[33mChecking for unavailable packages from pacman's cache\e[0m"
sudo pacman -Scc 
echo ""
echo -e "\e[33mChecking size of ~/.cache directory\e[0m"
cache_size=$(du -sh ~/.cache | awk '{ print $1 }')
echo  "The size of ~/.cache directory is $cache_size."
echo ""
echo -e "\e[33mChecking size of Trash directory\e[0m"
trash_size=$(du -sh ~/.local/share/Trash/files | awk '{ print $1 }')
echo "The size of Trash directory is $trash_size."
echo ""
read -p "Do you want to empty the Trash directory and  ~/.cache directory? Respond with 'y' or 'n': " answer

if [ $answer = "y" ]; then
    # Empty the Trash directory
    rm -rf ~/.local/share/Trash/files/*

    #  ~/.cache directory
    rm -rf ~/.cache/*

    echo  "The Trash directory and ~/.cache directory have been d."
else
    echo  "No action taken."
fi
echo ""
echo -e "\e[32mClean up completed!\e[0m"
```
<br>

`$ sudo chmod +x /usr/bin/clean`
