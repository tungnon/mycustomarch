# This is still WIP

# How I install Arch Linux

## Intro
I suck at grammar and I am not asking AI to help. So yeah, deal with imperfect writing. Anyway, this is how I setup my Arch Linux. It is very far from what you would expect from usual Arch installs. Feel free to follow or use this as a baseline if you wish. If you are going to complain, I don't care. Close this page and touch grass. It works for me.

Obviously this is not for a new user and I assume you already know how to install Arch manually. **If you do not know why you are doing any of these, close this page. Install CachyOS or vanilla Arch via archinstall instead.**

## What we will get here
### System
This assumes if you have UEFI. If you have only BIOS, close this page.
- Distro: Arch Linux
- Kernel: CachyOS kernel and CachyOS LTS kernel
- CLI text editor: micro
- Repos: Core + Extra + Multilib + CachyOS + CachyOS Core + CachyOS Extra + Chaotic AUR
- Initramfs: Booster 
- Bootloader: systemd-boot 
- Filesystem: XFS or EXT4
- zram enabled 

### Userspace/Desktop
- WM: niri 
- Desktop Shell: Noctalia V5 
- Login Manager: Noctalia Greeter
- File Manager: Thunar
- GUI Text Editor: Mousepad
- Archive Managers: XArchiver
- Music Player: Audacious
- Image + Video Viewer: qimgv
- Terminal Emulator: foot
- Terminal Shell: fish
- AUR Helper: paru

## Goal Explanation
### systemd-boot + XFS + Booster
This trio are very great together for "fast, simple, minimal, and get out of my way" components.
- The trio emphasize on keeping it simple stupid philosophy, this means there are only few stuff you have to worry about in long run, which is good if you prefer direct manual intervention.
- EXT4 or XFS works as both are simple file systems with no features which we may not want in the first place. EXT4 seems better if your available disk space is limited as we are installing small ESP for EXT4 users. Otherwise XFS is generally better if you are fine with root partition cannot be shrunk. 
- Booster is a simple, fast, and minimal initramfs generator. Initramfs images it generates are stupidly small compared to mkinitcpio. Not only that, it is also fast to boot and capable of auto generating initrd images as well just like mkinitcpio (except it is much faster). To give you an idea, **stock booster saves us 3 seconds boot time**.
- I pick systemd-boot because bootloader should be simple and fast.
- This combo is definitely not viable for btrfs snapshots users at all (in case it isn't obvious already). If you value btrfs snapshots, I suggest you to install CachyOS or using archinstall script to save your sanity.
### CachyOS stuff
I believe we should fully utilize our premium CPU that we paid. So optimized binaries are nice to have even if it means only +10% performance max. Also, custom kernel that targets desktop instead of global system is really nice to have. It's just free upgrade over stock Arch binaries and Arch kernel.
### Chaotic AUR
I trust them so I outsourced my work to Chaotic AUR team. Simple as that.
### The rest
The rest is only a matter of preferences which you may use base installation for something else. Maybe even KDE Plasma, GNOME, XFCE, hyprland. Whatever works.

## Installation Steps
### Base Install
https://github.com/tungnon/mycustomarch/blob/main/Installation/basesystem.md
### Desktop Setup: KDE Plasma
https://github.com/tungnon/mycustomarch/blob/main/Installation/easy.md
### Desktop Setup: niri + Noctalia V5
WIP
### My dots
(insert link here)
