# Easy Version: KDE Plasma
If any of these packages are not found, read base system page first

## Get Plasma Working
### Installing Base KDE Plasma
```
sudo pacman -S plasma-meta dolphin ark kio-extras plasma-login-manager konsole
```
- These are essential KDE Plasma applications. Replace `konsole` with your preferred terminal emulator if needed.
- `plasma-meta` also pulls `discover`

### Enabling Plasma Login Manager
```
sudo systemctl enable --now plasmalogin
```
Enjoy!

## Additional Applications
### Codecs
```
sudo pacman -S ffmpegthumbnailer
```
`ffmpegthumbnailer` also pulls `ffmpeg`

### KDE Applications
```
sudo pacman -S gwenview haruna kate okular elisa
```
- Image Viewer: `gwenview` 
- Media Viewer: `haruna` - Also automatically pulls `mpv`
- GUI text editor: `kate` - Also includes KWrite
- PDF Viewer: `okular` 
- Music Player: `elisa`
Feel free to replace any of these with any of your choice

### Other Applications
- Internet Browser: I recommend `helium-browser-bin`
- Office Suite: `libreoffice-fresh` - `libreoffice-still` and `onlyoffice` are viable alternatives
- GUI package manager: `octopi` - `shelly` is viable alternative that does not use pacman
- Update Notifier: `arch-update` - If `arch-update` isn't what you wanted, you may use built-in notifiers from `octopi`, `shelly` or KDE Widget `Apdatifier` or none if you wish. 
- Others: `cachyos-hello` `cachyos-kernel-manager` can be easily skipped
- AUR helper - Any works but I prefer `paru` but it is far from necessary. `shelly` can be used as AUR manager as well.
Note: `xdg-terminal-exec-git` is recommended if you use other terminal that isn't `konsole`
