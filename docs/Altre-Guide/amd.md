

## manjaro-mesa-codecs

- Autore: bucch
- Repository: [GitLab :fontawesome-brands-gitlab:](https://gitlab.com/th3bucch/manjaro-mesa-codecs)


# Manjaro Mesa Codecs

A simple script to re-compile the `mesa` package in order to enable hardware acceleration for proprietary codecs (i.e H.264 and H.265).  
**You shouldn't need to run this script if you don't have installed an AMD graphic card on your system!**

## Background info

Since december 2022 the Manjaro Linux Team has followed Fedora and openSUSE decision to disable proprietary codecs in the mesa package redistributed in their repository:
>  **Mesa is now at 22.2.4**
>    includes a notable change which disables hardware acceleration for proprietary video codecs (most commonly H.264 and H.265) when using the Mesa drivers stack.
>    Open video codecs (VP8, VP9, AV1 - based on your hardware capabilities) are unaffected and can still be hardware-accelerated out of the box.
>    This change mainly affects AMD graphics cards. (Intel GPUs don’t use Mesa for video acceleration, Nvidia cards use the proprietary driver, and Mesa video acceleration mostly doesn’t work properly with the opensource Nouveau driver) you can read more about hardware video acceleration [here](https://wiki.archlinux.org/title/Hardware_video_acceleration) and about that topic in general [here](https://lists.fedoraproject.org/archives/list/devel@lists.fedoraproject.org/thread/PYUYUCM3RGTTN4Q3QZIB4VUQFI77GE5X/)

[(source)](https://forum.manjaro.org/t/stable-update-2022-12-06-kernels-mesa-plasma-cinnamon-nvidia-libreoffice-pipewire-virtualbox/128453)

## Requirements
Install required packages before running the script with:

```
sudo pacman -S --needed base-devel git 
```

## Usage

Clone this repository then run the script:

```
git clone https://gitlab.com/th3bucch/manjaro-mesa-codecs.git
cd manjaro-mesa-codecs
./manjaro-mesa-codecs.sh
```

## Tips

Easiest way to run this script from everywhere on the system is to symlink it to one of the directories included in the `$PATH` global variable (i.e. `~/.local/bin`).
Check which directories are inclued using `echo $PATH`.

`cd` inside the cloned repository then run:
```
ln -s $(pwd)/manjaro-mesa-codecs.sh /home/$USER/.local/bin/manjaro-mesa-codecs
```

the script will be available systemwide for your user with the command:
```
manjaro-mesa-codecs
```

## Pacman HOOK

*TBD*

## Troubleshooting

If `pacman` cannot verify the source code package returning `unknown public key *key-id*` message, just copy the provided *key-id* and import that with:
```
gpg --recv-keys <key-id>
```
