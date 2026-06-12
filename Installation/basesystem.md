# Base System Installation

## Preparation
### Getting EndeavourOS Live ISO
Yup, we will use that one. Why? It provides graphical working live environment so you can copy paste from browser. And during pacstrap it won't bring CachyOS stuff here (you do not want that).
### Ranking Arch Mirror
When EndeavourOS helper windows pops up, click to update Arch mirrors. Ignore EndeavourOS mirror, we don't care.
### Get to work
Launch Konsole then type `sudo su`. Reading Arch Wiki alongside this guide is recommended for clear comparison here. We can see differences clearly this way.

## Installing Base System
Partition scheme slightly differs depending on what you want:
### Partition the disks (XFS Root Partition)
Use `cfdisk`. You would want partitioning to be like this:
- ESP: 1 GB 
- root_partition: the rest 

ESP with booster only needs 100 MB. But we are reserving 1 GB in case you want to go back to mkinitcpio as XFS can't be shrunk.

### Partition the disks (EXT4 Root Partition)
Use `cfdisk`. You would want partitioning to be like this:
- ESP: 100-250 MB 
- root_partition: the rest 

Depends on available disk space, you may leave ESP to be more comfortable to 250 MB if you want to. But honestly 100 MB should be enough.

### Formatting the partitions
Format root partition for XFS Partition
```
mkfs.xfs /dev/root_partition
```
Or format root partition for EXT4 Partition
```
mkfs.ext4 /dev/root_partition
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
pacstrap -K /mnt base linux-firmware sof-firmware booster base-devel sudo micro networkmanager efibootmgr amd-ucode xfsprogs
```
Note:
- We do not need `linux` here. We will install CachyOS kernel later.
- You may need `sof-firmware` if you are using laptops or handhelds. If you are not whether you need it or not, keep it.
- If you prefer "less bloated" editor than `micro` then `vim` `nano` `nvim` whatever works. Just get at least one.
- Pick `amd-ucode` if you have AMD CPU. Pick `intel-ucode` if you have Intel CPU.
- `xfsprogs` can be skipped if you prefer ext4

### Fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot
```
arch-chroot /mnt
```

### Timezones
```
ln -sf /usr/share/zoneinfo/Area/Location /etc/localtime
```
Put appropriate timezone here. See arch wiki to know how.

```
hwclock --systohc
```

### Locale
```
micro /etc/locale.gen
```
uncomment any locale you want to generate then...
```
locale-gen
```
Edit locales config here
```
micro /etc/locale.conf
```
a single line of `LANG=en_US.UTF-8` works if you are lazy

### Hostname
```
micro /etc/hostname
```
Whatever you want here

### Enabling Internet
```
systemctl enable NetworkManager
```

But we are not done yet. We are only doing bare minimum here....

## Installing Kernel, Repos, Initramfs, and Bootloader
Please read CachyOS wiki here while following steps from now on : https://wiki.cachyos.org/features/optimized_repos/#adding-our-repositories-to-an-existing-arch-linux-install
### Enabling CachyOS repos
```
curl https://mirror.cachyos.org/cachyos-repo.tar.xz -o cachyos-repo.tar.xz
tar xvf cachyos-repo.tar.xz && cd cachyos-repo
./cachyos-repo.sh
```
After the process is over, appropriate CachyOS repos will be enabled and your base system components will be replaced by CachyOS optimized binaries.

Very important, if you are using AMD CPU, please do this here to check:
```
gcc -march=native -Q --help=target 2>&1 | grep -Po "^\s+-march=\s+\K(\w+)\$"
```
If your CPU is incompatible or you are using Intel CPU then skip to the next step. If the output is znver4 or znver5, then proceed:

Edit `/etc/pacman.conf`
```
micro /etc/pacman.conf
```
Replace V3/V4 repos with: 
```
[cachyos-znver4]
Include = /etc/pacman.d/cachyos-v4-mirrorlist

[cachyos-core-znver4]
Include = /etc/pacman.d/cachyos-v4-mirrorlist

[cachyos-extra-znver4]
Include = /etc/pacman.d/cachyos-v4-mirrorlist
```
Keep [cachyos], [core], and [extra] unchanged.

Then do:
```
pacman -Scc   # Confirm with 'y' twice
pacman -Sy
pacman -Qqn | pacman -S -
```
**Continue from here if your CPU is not znver4/znver5 compatible.**

### Enabling Multilib Repo
Still on `etc/pacman.conf`, uncomment multilib lines here:
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```
Save and exit. We are done here. Feel free to do `pacman -Syyu` to ensure multilib repo is loaded.

Also I know this isn't CachyOS stuff but if we are going to edit pacman.conf anyway, why not do it in one go?

### Injecting CachyOS Components
This is where we will have our fun:
```
pacman -S linux-cachyos linux-cachyos-lts systemd-boot-manager cachyos-settings
```
- Skip `linux-cachyos-lts` if you don't care about fallback kernel. Skip `linux-cachyos` if you prefer LTS kernel for some reasons.
- CachyOS settings will automatically handle zram and other nice stuff for us. These are nice improvements. Skip if you prefer vanilla Linux behavior.

### Initramfs
Run this
```
/usr/lib/booster/regenerate_images
```
This will create appropriate booster images

### Bootloader Configuration
See here for more info: https://wiki.cachyos.org/configuration/boot_manager_configuration/

Edit `/etc/sdboot-manage.conf`
```
micro /etc/sdboot-manage.conf
```
Focus on two last lines of configs. Change them here:
```
# Use this to change the default initramfs prefix (e.g. when using booster)
INITRD_PREFIX="booster"

# Additional entities to initrd can be added here, such as microcode if your initramfs does not include it.
INITRD_ENTRIES=("/amd-ucode.img")
```
use `intel` instead of `amd` if you have Intel CPU.

Then generate systemd boot entries via
```
sdboot-manage setup
```
That will setup systemd-boot automatically for you.

If you want to come back to change stuff on config above, do
```
sdboot-manage gen
```
instead

## User Management
### Root password
```
passwd
```
### Add new user
Do this
```
useradd -m -G wheel tungnon
```
Replaces `tungnon` with your username

Set password
```
passwd tungnon
```
Again, replace `tungnon` with your username

### Making Sudo work
Do
```
EDITOR=micro visudo
```

uncomment this line
```
%wheel ALL=(ALL:ALL) ALL
```

### Reboot
Base installation is done! You are done here if you don't care about desktop sides of thing.
