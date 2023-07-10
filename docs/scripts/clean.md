🇮🇹 Questo script Bash verifica la presenza di pacchetti e dipendenze obsoleti su un sistema e li rimuove se ne trova. Calcola anche lo spazio totale occupato dalla cache, la cache dei pacchetti e il cestino da diverse directory e chiede all'utente di rimuovere tutti i file dalla cache e dai repository non utilizzati. Se l'utente accetta, rimuove tutti i file dalla directory della cache, dalla cartella del cestino e cancella la cache dei pacchetti utilizzando il comando 'pacman -Scc'. Lo script mostra anche lo spazio totale occupato attualmente da queste directory prima e dopo la pulizia.

🇬🇧 This Bash script checks for obsolete packages and dependencies on a system and removes them if found. It also calculates the total space taken up by cache, package cache, and trash from different directories and prompts the user to remove all files from cache and unused repositories. If the user agrees, it removes all files from the cache directory, trash folder, and clears the package cache using 'pacman -Scc' command. The script also displays the current total space taken up by these directories before and after cleanup.

![image](https://github.com/ArchItalia/site/assets/117321045/9cbf24c3-54e6-421b-a756-baab1e0bb90f)


## install from AUR
* git clone https://aur.archlinux.org/clean.git
* cd clean
* makepkg -si

## install from GitHub
* git clone https://github.com/architalia/clean.git
* cd clean
* makepkg -si

<br>

`$ clean` easy command for arch linux



