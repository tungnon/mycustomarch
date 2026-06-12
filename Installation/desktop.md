# Desktop Installation

## Optional: Nvidia
If you are using Nvidia GPU, do:
```
sudo pacman -S nvidia-open linux-cachyos-nvidia-open nvidia-open-lts linux-cachyos-lts-nvidia-open nvidia-utils nvidia-settings
```
- Ignore LTS stuff if you don't use the LTS kernel
- Use only LTS stuff if you only use the LTS kernel

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