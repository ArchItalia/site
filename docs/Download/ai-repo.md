# Architalia Repository [ai-repo] for Core Linux

ai-repo is a repository of Arch Linux packages that was created primarily for Core Linux, which is still a work-in-progress and not a real thing yet.

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



## 🟢 Server logs:
- 11 Packages
- 24-07-2023 12:14:24 **architalia-fonts** 1.0 version already updated
- 24-07-2023 12:14:24 **clean** 2.2 version already updated
- 24-07-2023 12:14:25 **core-gnome-backgrounds** 1.2 version already updated
- 24-07-2023 12:14:25 **core-gtk-theme** 1.8 version already updated
- 24-07-2023 12:14:25 **core-icons-theme** 1.0 version already updated
- 24-07-2023 12:14:25 **yay** 12.1.0 version already updated
- 24-07-2023 12:14:25 **text-engine** 0.1.1 version already updated
- 24-07-2023 12:14:25 **extension-manager** 0.4.1 > 0.4.2 just updated 🔹
- 24-07-2023 12:14:34 **libbacktrace-git** 75 version already updated
- 24-07-2023 12:14:35 **timeshift** 23.07.1 version already updated
- 24-07-2023 12:14:35 **calamares** 3.2.62 version already updated
