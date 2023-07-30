# Architalia Repository [ai-repo](https://gitlab.com/architalialinux/ai-repo) for Core Linux

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
- 17 Packages
- 30-07-2023 09:02:28 **architalia-fonts** 1.0 version already updated
- 30-07-2023 09:02:28 **ckbcomp** 1.221 version already updated
- 30-07-2023 09:02:28 **clean** 2.2 version already updated
- 30-07-2023 09:02:28 **core-base** 1.0 version already updated
- 30-07-2023 09:02:28 **core-extensions-base** 1.2 version already updated
- 30-07-2023 09:02:28 **core-terminal-nord-theme** 1.2 version already updated
- 30-07-2023 09:02:28 **core-gnome-backgrounds** 1.7 version already updated
- 30-07-2023 09:02:28 **core-gtk-theme** 1.8 version already updated
- 30-07-2023 09:02:28 **core-icons-theme** 1.0 version already updated
- 30-07-2023 09:02:28 **core-keyring** 1.2 version already updated
- 30-07-2023 09:02:28 **gnome-shell-extension-desktop-icons-ng** 47.0.3 version already updated
- 30-07-2023 09:02:29 **extension-manager** 0.4.2 version already updated
- 30-07-2023 09:02:29 **libbacktrace-git** 75 version already updated
- 30-07-2023 09:02:30 **mkinitcpio-openswap** 0.1.0 version already updated
- 30-07-2023 09:02:30 **text-engine** 0.1.0 > 0.1.1 just updated 🔹
- 30-07-2023 09:02:36 **timeshift** 23.07.1 version already updated
- 30-07-2023 09:02:37 **yay** 12.1.0 version already updated
 - [Generate by the 🤖 ai-brain script](https://gitlab.com/architalialinux/ai-repo/-/blob/main/ai-brain)
