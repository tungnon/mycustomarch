# Easy Version: KDE Plasma
If any of these packages are not found, read base system page first

## Installing base KDE Plasma
```
sudo pacman -S plasma-meta dolphin ark kio-extras plasma-login-manager
```
These are essential KDE Plasma applications

## Installing additional applications
```
sudo pacman -S konsole gwenview haruna kate helium-browser-bin 
```
- Terminal Emulator: `konsole` - Feel free to replace it with `foot` `alacritty` `kitty` or other terminal of choice if you wish
- Internet Browser: `helium-browser-bin`: replace it with any browser of your choice

## Get Plasma Working
```
sudo systemctl enable --now plasmalogin
```

## Optional Applications
- Image Viewer: `gwenview` - `qimgv` is viable lightweight alternative
- Media Viewer: `haruna` - `mpv` maybe enough if you prefer minimal media viewer
- GUI text editor: `kate` - Feel free to replace it with any of your choice
- PDF Viewer: `okular` - `mupdf` is viable lightweight alternative
- Music Player: `elisa` - `audacious` is viable lightweight alternative
- GUI package manager: `octopi` - `shelly` is viable alternative that does not use pacman
- Flatpak store: `bazaar` - `discover` is also viable alternative that also handle flatpak alongside KDE widgets and theme updates
- Update Notifier: `arch-update` - If `arch-update` isn't what you wanted, you may use built-in notifiers from `octopi`, `shelly` or KDE Widget `Apdatifier` or none if you wish
- Others: `cachyos-hello` `cachyos-kernel-manager` can be easily skipped
- AUR helper - Any works but I prefer `paru` but it is far from necessary. `shelly` can be used as AUR manager as well.

