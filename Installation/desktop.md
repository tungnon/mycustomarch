# Desktop Installation

## Easy Mode
If you just want stuff to work without thinking. After you boot to your working system. Login on TTY then do:
```
sudo pacman -S plasma-meta helium-browser-bin shelly cachyos-hello
```
Replace Helium with browser of choice
Replace `shelly` with `octopi` or don't include it if you don't care about GUI package manager
Don't include `cachyos-hello` if you don't care about CachyOS tools

Then
```
sudo systemctl enable --now plasmalogin
```
Enjoy

## What I am actually using: Niri + Noctalia V5
(WIP)