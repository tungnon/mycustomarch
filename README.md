# This is still WIP

# How I install Arch Linux

## Intro
I suck at grammar and I am not asking AI to help. So yeah, deal with imperfect writing. And if you actually read this page for 5 hot seconds, it is far from what you would expect from usual Arch installs. Do not blindly follow these steps if you don't know what these mean.

## What we will get here
- Distro: Arch Linux
- Kernel: CachyOS kernel and CachyOS LTS kernel
- CLI text editor: micro
- Repos: Core + Extra + Multilib + CachyOS + CachyOS Core + CachyOS Extra + Chaotic AUR
- Initramfs: Booster 
- Bootloader: systemd-boot 
- Filesystem: XFS or EXT4
- zram enabled 

## Goal Explanation
### systemd-boot + XFS/EXT4 + Booster
This trio are very great together for "fast, simple, minimal, and get out of my way" components.
- These are stupidly simple trio. Gonna love that simplicity
- XFS or EXT4 works as both are simple file systems. But honestly, if you are not a coward and deal with unshrinkable root, XFS is just faster and more reliable file system. [See here to know why](https://github.com/tungnon/mycustomarch/blob/main/Installation/basesystem.md#enabling-xfs-services). But hey, EXT4 users still demand more respect than people who "play safe" with btrfs snapshots. After all, when they are forced to chroot, they are scratching their heads figuring how to actually chroot their root
- Booster is a simple, fast, and minimal initramfs generator. Well, not only images size is riduculously small, boot time is stupidly fast as well.
- systemd-boot is fast
### CachyOS stuff
I have premium CPU. I don't want my paid CPU to go into waste just because I have to use generic binaries from upstream
### Chaotic AUR
I trust them so I outsourced my work to Chaotic AUR team. Simple as that.

## Installation Steps
### Base Install (Do this first)
https://github.com/tungnon/mycustomarch/blob/main/Installation/basesystem.md
### Desktop Setup: KDE Plasma
https://github.com/tungnon/mycustomarch/blob/main/Installation/easy.md
### Desktop Setup: niri + Noctalia V5
WIP
### My dots
(insert link here)
