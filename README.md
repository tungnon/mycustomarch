# This is still WIP

# How I install Arch Linux

## Intro
I suck at grammar and I am not asking AI to help. So yeah, deal with imperfect writing. Anyway, this is how I setup my Arch Linux. it is very far from "cultural Arch" but it works for me. Feel free to follow or use this as a baseline if you wish. This assumes you already know how to install Arch manually. If you are wonder exact step details: go read Arch Wiki.

## Goal
- Distro: Arch Linux
- Kernel: CachyOS kernel (CachyOS LTS is also optional choice)
- Repos: Core + Extra + Multilib + CachyOS repos (So we can enjoy optimized binaries)
- Initramfs: Booster (for my setup Booster is fast simple minimal and bootable)
- Bootloader: systemd-boot (minimal bootloader for UEFI system)
- Filesystem: XFS (Simple, fast, and reliable)
- Swap: zram
- AUR Helper: paru
- WM: niri (We are not going to do full DE here)
- Shell: Noctalia V5 (this also handles wallpaper polkit notif and more)
- Display Manager: ly
- File Manager: Thunar
- Desktop Portal: gtk and gnome (Nautilus won't be on our system)
- Text Editors: nano and mousepad
- Archive Managers: XArchiver
- Terminal Emulators: Kitty (Alacritty is also great here)
- Theming: nwg-look qt5ct qt6ct darkly adw-gtk-theme

## Choose Live ISO
We need one of these:
- Arch Linux (ssh is recommended if you use arch iso)
- CachyOS or EndeavourOS either works. We are installing manually anyway, those iso just provide nice graphical environment for us to open this page and Arch Wiki.
 
## Installing Base System
We are not using archinstall or any GUI installer. It's manual time.

### Connect to internet
Follow arch wiki I cba to explain here

### Making live iso to recognize xfs
Since we are using XFS as root partition, we need to make sure THIS live iso recognizes it
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


