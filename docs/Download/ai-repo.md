# Architalia Repository [ai-repo] 🇮🇹

* Come aggiungere [**ai-repo**](https://gitlab.com/architalialinux/ai-repo/-/tree/main/) al tuo Arch Linux

È possibile aggiungere ai-repo a qualsiasi distribuzione Linux basata su Arch. Aggiungi semplicemente le seguenti righe alla fine di **/etc/pacman.conf**:

```
[ai-repo]
SigLevel = Required DatabaseOptional
Server = https://gitlab.com/architalialinux/$repo/-/raw/main/$arch 
```

Quindi, sincronizza i repository e aggiorna il sistema con:

```
sudo pacman -Syyu
```

E, quindi:

```
sudo pacman -S nome-pacchetto
```

NOTA: pacman segnalerà l'importazione di una chiave PGP che è invalida o corrotta. Il problema può essere risolto firmando localmente la chiave importata:

```
sudo pacman-key --lsign-key AEA0A2E06D592805
```
<br><br>

# Architalia Repository [ai-repo] 🇬🇧

* How to add **ai-repo** to your Arch Linux

You can add ai-repo to any Arch-based Linux distribution.  Just add the following lines to the end of **/etc/pacman.conf**:

```
[ai-repo]
SigLevel = Required DatabaseOptional
Server = https://gitlab.com/architalialinux/$repo/-/raw/main/$arch 
```

Then, sync the repositories and update your system with:

```
sudo pacman -Syyu
```

And, then:

```
sudo pacman -S name-of-package
```

NOTE: Pacman will complain about importing a PGP key that is either invalid or corrupted.  The problem can be fixed by locally signing the imported key:

```
sudo pacman-key --lsign-key AEA0A2E06D592805
```
