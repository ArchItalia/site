Creare un comando semplificato per personalizzare la pulizia del sistema.

`$ sudo vim /usr/bin/clean`

<br>

```
echo ""
echo "Verifica dei pacchetti e delle dipendenze non piu necessarie.."
echo ""
pacman -R $(pacman -Qdtd)

echo ""
echo "Verifica  pacchetti non più disponibili nei repositories dalla cache di pacman"
echo ""
pacman -Sc

echo ""
# Verifica lo spazio occupato dalla directory ~/.cache
cache_size=$(du -sh ~/.cache | awk '{ print $1 }')

echo "Lo spazio occupato dalla directory ~/.cache è di $cache_size."

# Verifica lo spazio occupato dal cestino
trash_size=$(du -sh ~/.local/share/Trash/files | awk '{ print $1 }')

echo "Lo spazio occupato dal cestino è di $trash_size."

# Chiede all'utente quale azione intraprendere
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

![image](https://github.com/ArchItalia/site/assets/117321045/83b6ec50-4dfb-433c-913f-e985d5030e4c)
