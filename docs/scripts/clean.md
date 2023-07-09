Creare un comando semplificato `$ clean`  per personalizzare la pulizia del sistema.

`$ sudo vim /usr/bin/clean`

<br>

```
#!/bin/bash

echo "Verifica dei pacchetti e delle dipendenze non più necessarie.."

if pacman -Qdt &> /dev/null; then
    echo "Eliminazione dei pacchetti e delle dipendenze non più necessarie.."
    pacman -Qdt | awk '{print $1}' | sudo pacman -Rs -
else
    echo "Non ci sono pacchetti da eliminare."
fi

echo "Verifica  pacchetti non più disponibili nei repositories dalla cache di pacman"
sudo pacman -Scc 
echo ""
echo "Verifica lo spazio occupato dalla directory ~/.cache"
cache_size=$(du -sh ~/.cache | awk '{ print $1 }')
echo "Lo spazio occupato dalla directory ~/.cache è di $cache_size."
echo ""
echo "Verifica lo spazio occupato dal cestino"
trash_size=$(du -sh ~/.local/share/Trash/files | awk '{ print $1 }')
echo "Lo spazio occupato dal cestino è di $trash_size."
echo ""
read -p "Vuoi svuotare il cestino e cancellare la directory ~/.cache? Rispondi con 'y' o 'n': " answer

if [ $answer = "y" ]; then
    # Svuota il cestino
    rm -rf ~/.local/share/Trash/files/*

    # Cancella la directory ~/.cache
    rm -rf ~/.cache/*

    echo "Il cestino e la directory ~/.cache sono stati cancellati."
else
    echo "Nessuna azione intrapresa."
fi
```
<br>

`$ sudo chmod +x /usr/bin/clean`

![image](https://github.com/ArchItalia/site/assets/117321045/335ac329-9b9c-44c7-8817-7e55cd092a3c)

![image](https://github.com/ArchItalia/site/assets/117321045/3ddfa62c-696d-48c0-bbdf-2a8b2c7b9d92)
