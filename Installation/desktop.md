# Desktop Installation

## Easy Mode
If you just want stuff to work after these steps, login on TTY then do:
```
sudo pacman -S plasma-meta dolphin ark kio-extras helium-browser-bin octopi bazaar cachyos-hello konsole gwenview haruna okular kate arch-update
```
- Replace Helium with browser of choice
- Replace `konsole` with `foot` `alacritty` `kitty` or other terminal of choice if you wish but then `xdg-terminal-exec-git` is recommended if you want to use `arch-update`
- Don't add `octopi` and/or `bazaar` if you do not want GUI app manager
- Don't include `cachyos-hello` if you don't care about CachyOS tools
- Replace `gwenview` `haruna` `okular` with other apps if needed
- Skip `arch-update` if you prefer update manually for some reasons
- AUR helper like `paru` maybe needed if something you want is not found on Chaotic AUR at all

Then
```
sudo systemctl enable --now plasmalogin
```
Enjoy actually using system instead being stuck on config hell!

## What I am actually using: Niri + Noctalia V5
(WIP)
