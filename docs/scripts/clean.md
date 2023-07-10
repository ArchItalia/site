🇬🇧 [English](#eng) - 🇮🇹 [Italian](#ita)


# ita

🇮🇹 Creare un comando semplificato `$ clean`  per personalizzare la pulizia del sistema.



![Screenshot from 2023-07-10 11-42-57](https://github.com/ArchItalia/site/assets/117321045/f3a9969d-47de-4000-a3f9-1f0d677adcba)



- `sudo pacman -S bc`
- `$ sudo vim /usr/bin/clean`

<br>

```
#!/bin/bash
#
# Author : Jonathan Sanfilippo
# Date: Jul 2023
# Version 1.0.0: Clean
#

cache=$(du -sh /var/cache/pacman/pkg/ | awk '{ print $1 }')
lib=$(du -sh /var/lib/pacman/ | awk '{ print $1 }')
home_cache=$(du -sh ~/.cache/ | awk '{ print $1 }')
total=$(echo "$cache + $lib + $home_cache" | bc)

if (( $(echo "$total < 1024" | bc -l) )); then
        unit="M"
        total=$(echo "$total" | awk '{printf "%.2f\n", $1}')
     else
        unit="G"
        total=$(echo "scale=2; $total/1024" | bc -l)
fi

echo -e "\e[33mChecking for obsolete packages and dependencies..\e[0m"

if pacman -Qdt &> /dev/null; then
    echo  "Removing obsolete packages and dependencies.."
    pacman -Qdt | awk '{print $1}' | sudo pacman -Rs -
else
    echo  "No packages to remove."
fi
echo ""

printf "\e[33mCurrent space between cache, package cache, and trash: \e[0m\e[91m$total$unit\e[0m\e[33m, Clean? Respond with 'y' or 'n': \e[0m" && read answer

if [ $answer = "y" ]; then
     rm -rf ~/.cache/*
     rm -rf ~/.local/share/Trash/files/*
     sudo pacman -Scc

    echo -e "\e[32mClean up completed!\e[0m"
else
    echo  "No action taken."
fi
echo ""

```
<br>

`$ sudo chmod +x /usr/bin/clean`

<br><br>

# eng

🇬🇧 Create a simplified command `$ clean` to customize the system cleanup.



![Screenshot from 2023-07-10 11-42-57](https://github.com/ArchItalia/site/assets/117321045/a2daa925-9e08-4f94-b919-ddb88d2087e8)


- `sudo pacman -S bc`
- `$ sudo vim /usr/bin/clean`

<br>

```
#!/bin/bash
#
# Author : Jonathan Sanfilippo
# Date: Jul 2023
# Version 1.0.0: Clean
#

cache=$(du -sh /var/cache/pacman/pkg/ | awk '{ print $1 }')
lib=$(du -sh /var/lib/pacman/ | awk '{ print $1 }')
home_cache=$(du -sh ~/.cache/ | awk '{ print $1 }')
total=$(echo "$cache + $lib + $home_cache" | bc)

if (( $(echo "$total < 1024" | bc -l) )); then
        unit="M"
        total=$(echo "$total" | awk '{printf "%.2f\n", $1}')
     else
        unit="G"
        total=$(echo "scale=2; $total/1024" | bc -l)
fi

echo -e "\e[33mChecking for obsolete packages and dependencies..\e[0m"

if pacman -Qdt &> /dev/null; then
    echo  "Removing obsolete packages and dependencies.."
    pacman -Qdt | awk '{print $1}' | sudo pacman -Rs -
else
    echo  "No packages to remove."
fi
echo ""

printf "\e[33mCurrent space between cache, package cache, and trash: \e[0m\e[91m$total$unit\e[0m\e[33m, Clean? Respond with 'y' or 'n': \e[0m" && read answer

if [ $answer = "y" ]; then
     rm -rf ~/.cache/*
     rm -rf ~/.local/share/Trash/files/*
     sudo pacman -Scc

    echo -e "\e[32mClean up completed!\e[0m"
else
    echo  "No action taken."
fi
echo ""

```
<br>

`$ sudo chmod +x /usr/bin/clean`
