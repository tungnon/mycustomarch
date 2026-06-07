# This is still WIP

# How I install Arch Linux

## Intro
I suck at grammar and I am not asking AI to help. So yeah, deal with imperfect writing. Anyway, this is how I setup my Arch Linux. it is very far from "cultural Arch" but it works for me. Feel free to follow or use this as a baseline if you wish. This assumes you already know how to install Arch manually. **If you do not know why you are doing any of these, this page isn't for you and install CachyOS or vanilla Arch via archinstall instead.**

## What we will get here
- Distro: Arch Linux
- Kernel: CachyOS kernel (CachyOS LTS is also optional choice)
- Repos: Core + Extra + Multilib + CachyOS repos 
- Initramfs: Booster 
- Bootloader: systemd-boot 
- Filesystem: XFS 
- Swap: zram
- AUR Helper: paru
- WM: niri 
- Desktop Shell: Noctalia V5 
- Terminal Shell: Fish
- Display Manager: ly
- File Manager: Thunar
- Text Editors: nano and mousepad
- Archive Managers: XArchiver
- Terminal Emulators: Kitty (Alacritty is also great here)
- Theming: nwg-look qt5ct qt6ct darkly adw-gtk-theme

## Goal Explanation
### CachyOS stuff
I believe we should fully utilize our premium CPU that we paid. So optimized binaries are nice to have even if it means only +10% performance max. Also, custom kernel that targets desktop instead of global system is really nice to have. Overall it's just free upgrade over stock Arch binaries and Arch kernel.
### systemd-boot + XFS + Booster
This trio are very great together for "fast, simple, minimal, and get out of my way" components.
- The trio emphasize on keeping it simple stupid philosophy, this means there are only few stuff you have to worry about in long run, which is good if you prefer direct manual intervention.
- EXT4 or XFS works as both are simple file systems with no features which we may not want in the first place. EXT4 is technically more documented and can be shrunk, but I play games and huge filesize transactions appeals me more. Thus, XFS is my choice. 
- Booster is a simple, fast, and minimal initramfs generator. Initramfs images it generates are stupidly small compared to mkinitcpio. Not only that, it is also fast to boot and capable of auto generating initrd images as well just like mkinitcpio (except it is much faster). To give you an idea, **stock booster saves us 3 seconds boot time**.
- Booster only supports systemd-boot and rEFInd, both of which are not known for btrfs snapshots integration (I know systemd boot snapshots exist on OpenSUSE but this is arch we are using).
- I pick systemd-boot instead of rEFInd because I am more familiar with it, it's more simple as well. if I set timeout to be 0, then it wastes less than a second on the bootloader.
- This combo is definitely not viable for btrfs snapshots users at all (in case it isn't obvious already). If you value btrfs snapshots, I suggest you to install CachyOS or using archinstall script to save your sanity.
### Zram instead of swap partition
It works so why not?
### ly
I believe login manager should only does one thing, login into session. it is fast, minimal, and simple.
### Niri + Noctalia + ly + Thunar
I believe tools should be simple and effective while get out of users way. These are great modular tools that have clear jobs.

## Choose Method
### Arch Live ISO + SSH
Believe me, we are doing lots of manual configuration here. So I strongly recommend to SSH if you are using Arch live iso.
### CachyOS live ISO
CachyOS live iso already have graphical environment for you to browse this page alongside easy way for you to copy paste stuff. This make SSH much less appealing but the "downside" is that you are not using Arch official iso. 
 
## Installing Base System
We are not using archinstall or any GUI installer. It's manual time.

### Connect to internet
Follow arch wiki I cba to explain here

### Update system clock
```
timedatectl
```
### Optional: Install XFS tools in live ISO for SSH method
Since we are using XFS as root partition, install xfsprogs to live Arch iso. This is not needed for CachyOS ive ISO however.
```
pacman -S xfsprogs
```

### Optional: Terminal info for SSH method
If you are doing this via SSH, you should make live iso to recognize terminal you are using while you SSH into live environment. For my case I was using kitty.
```
pacman -S kitty-terminfo
```

### Partition the disks
Use `cfdisk`. You would want partitioning to be like this:
- ESP: 1-2 GB 
- root_partition: the rest 
Feel free to have swap here but I intend to use zram instead of swap partition

### Formatting the partitions
First use `cfdisk` to manage partition scheme
```cfdisk```
Follow what it says

Then format root partition
```
mkfs.xfs /dev/root_partition
```
Format ESP
```
mkfs.fat -F 32 /dev/ESP
```

### Mount filesystems
Mount root partition first
```
mount /dev/root_partition /mnt
```
Mount ESP
```
mount --mkdir /dev/ESP /mnt/boot
```

### Pacstrap
This is very important. Do not miss any of these packages (notes below)
```
pacstrap -K /mnt base linux linux-firmware sof-firmware booster base-devel sudo nano networkmanager efibootmgr fish amd-ucode xfsprogs
```
Note:
- You may need `sof-firmware` if you are using laptops or handhelds. If you are not whether you need it or not, keep it.
- I also include `fish` because it's my preferred user shell. You may use `zsh` instead if you want or none if you prefer good ol' `bash`.
- Pick `amd-ucode` if you have AMD CPU. Pick `intel-ucode` if you have Intel CPU.
- We will handle CachyOS stuff later once we boot into our system. For now vanilla arch kernel would do.

### Fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot
```
arch-chroot /mnt
```

### Time
```
ln -sf /usr/share/zoneinfo/Area/Location /etc/localtime
```
Put appropriate timezone here. See arch wiki to know how.

```
hwclock --systohc
```

### Locale
```
nano /etc/locale.gen
```
uncomment any locale you want to generate then...
```
locale-gen
```
Edit locales config here
```
nano /etc/locale.conf
```
a single line of `LANG=en_US.UTF-8` works if you are lazy

### Hostname
```
nano /etc/hostname
```
Whatever you want here

### Initramfs
Here's where we deviate from Arch Wiki. Run this
```
/usr/lib/booster/regenerate_images
```
This will create appropriate booster images

### Bootloader
Install systemd boot first
```
bootctl install
```

Check root partition UUID
```
blkid
```
then copy paste it somewhere

Make an entry manually for now. We will have tool to automate this later
```
nano /boot/loader/entries/arch.conf
```
Then paste this:
```
title Arch Linux
linux /vmlinuz-linux
initrd /amd-ucode.img
initrd /booster-linux.img
options root=UUID=INSERT ROOT UUID HERE rw
```
Again, use `intel-ucode.img` instead if you have intel CPU
then 
```
nano /boot/loader/loader.conf
```
put
```
default arch.conf
```
We will optimize these later. We just want system that boots.

### User Management
Make sudo work.
```
EDITOR=nano visudo
```
uncomment this line
```
%wheel ALL=(ALL:ALL) ALL
```

Add your user, `tungnon` is user I am using
```
useradd -m -G wheel -s /bin/fish tungnon
```

Set root password
```
passwd
```

Set user password
```
passwd tungnon
```

### Enabling Internet
```
systemctl enable NetworkManager
```

Then reboot


