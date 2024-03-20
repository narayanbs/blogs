---
title: "sway wm"
date: 2023-01-09T10:37:54+05:30
publishdate: 2023-01-09
lastmod: 2023-01-09
draft: true
---
##### Installation
```bash
$ sudo apt install sway -y
```
Install wofi application launcher
```bash
$ sudo apt install wofi -y
```
Install waybar 
```bash
$ sudo apt install waybar -y
```
copy configuration settings from dotfiles
```bash
$ cp -r dotfiles/.config/sway ~/.config
$ cp -r dotfiles/.config/waybar ~/.config
```
##### Points

Sway doesn't source ~/.profile, so copy the following  pyenv entries .bashrc
```bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH=$PATH:"$PYENV_ROOT"/bin
```
Handling bluetooth 
```bash
$ sudo systemctl enable bluetooth.service
$ sudo systemctl start bluetooth.service
# for stopping
$ sudo systemctl stop bluetooth.service

# enabling airdrop wireless bluetooth

$ bluetoothctl
[bluetooth]# devices
Device 17:4E:95:32:C3:6D Airdopes 141
[bluetooth]# connect 17:4E:95:32:C3:6D 
```

Install lxappearance (sudo apt install lxappearance) for enabling dark mode, in case the apps don't start in dark mode.
