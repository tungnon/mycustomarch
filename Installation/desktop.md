# Desktop Installation

## Optional: Nvidia
If you are using Nvidia GPU, do:
```
sudo pacman -S linux-cachyos-nvidia-open linux-cachyos-lts-nvidia-open nvidia-utils nvidia-settings
```
- Ignore `linux-cachyos-lts-nvidia-open` if you only use main kernel
- Ignore `linux-cachyos-nvidia-open` if you only use LTS kernel

If you don't use Nvidia GPU, skip.

## Easy Mode
If you just want stuff to work after these steps, login on TTY then do:
```
sudo pacman -S plasma-meta helium-browser-bin shelly cachyos-hello
```
- Replace Helium with browser of choice
- Replace `shelly` with `octopi` or don't include it if you don't care about GUI package manager
- Don't include `cachyos-hello` if you don't care about CachyOS tools

Then
```
sudo systemctl enable --now plasmalogin
```
Enjoy actually using system instead being stuck on config hell!

## What I am actually using: Niri + Noctalia V5
(WIP)