# Base System Installation
There are two ways to prepare live environment: EndeavourOS Live ISO or SSH into Arch Live ISO

## Preparation (EndeavourOS Live ISO)
Recommended if you only have one device available. If you have two or more devices and you know how to SSH, use Arch ISO instead.
### Getting EndeavourOS Live ISO
Yup, we will use that one. Why? It provides graphical working live environment so you can copy paste from browser. And during pacstrap it won't bring CachyOS stuff here (you do not want that).
### Ranking Arch Mirror
When EndeavourOS helper windows pops up, click to update Arch mirrors. Ignore EndeavourOS mirror, we don't care.
### Get to work
Launch Konsole then type `sudo su`. Reading Arch Wiki alongside this guide is recommended for clear comparison here. We can see differences clearly this way.

## Preparation (Arch Live ISO)
You ssh into live ISO. If you know, good. If you don't, look it up or use above method instead.

## Partitioning
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

## Formatting the partitions
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

## Mount filesystems
Mount root partition first
```
mount /dev/root_partition /mnt
```
Mount ESP
```
mount --mkdir /dev/ESP /mnt/boot
```

## Pacstrap for bare minimum stuff
```
pacstrap -K /mnt base xfsprogs micro
```
Note:
- `xfsprogs` can be skipped if you prefer ext4
- If you prefer "less bloated" editor than `micro` then `vim` `nano` `nvim` whatever works. Just get at least one.
- We only need these for now

## Fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```

## Chroot
```
arch-chroot /mnt
```

## Timezones
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

## Hostname
```
micro /etc/hostname
```
Whatever you want here

## Enabling Internet
```
systemctl enable NetworkManager
```

But we are not done yet. We are only doing bare minimum here....

## Configurating Pacman
This is where the fun begins :)

### Spice Up Pacman
Edit `pacman.conf`
```
micro /etc/pacman.conf
```
Change `# Misc options` section to be like this:
```
# Misc options
#UseSyslog
Color
#NoProgressBar
#PrettyProgressBar
ILoveCandy
CheckSpace
#VerbosePkgLists
ParallelDownloads = 20
DownloadUser = alpm
#DisableSandbox
#DisableSandboxFilesystem
#DisableSandboxSyscalls
```
If you think 20 is too much, 15 or 10 is fine.

### Enabling CachyOS repos
Please read CachyOS wiki here while following steps from now on : https://wiki.cachyos.org/features/optimized_repos/#adding-our-repositories-to-an-existing-arch-linux-install
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
**If your CPU is incompatible or you are using Intel CPU then skip to the next step.** 
If the output is znver4 or znver5, then proceed:

Edit `pacman.conf`
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
Still on `pacman.conf`, uncomment multilib lines here:
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```
Then `pacman -Syu` to sync mirrorlist

### Enabling Chaotic AUR Repo
Read here for more info: https://aur.chaotic.cx/

Retriving keys
```
pacman-key --recv-key 3056513887B78AEB --keyserver keyserver.ubuntu.com
pacman-key --lsign-key 3056513887B78AEB
```

Installing Chaotic Keyrings and Chaotic Mirrorlist
```
pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst'
pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst'
```

Put this at **the end of /etc/pacman.conf**
```
[chaotic-aur]
Include = /etc/pacman.d/chaotic-mirrorlist
```
Then `pacman -Syu` to sync mirrorlist

## The "REAL pacstrap"
```
pacman -S linux-cachyos linux-cachyos-lts linux-firmware sof-firmware base-devel booster sudo networkmanager efibootmgr systemd-boot-manager cachyos-settings chwd downgrade amd-ucode ffmpegthumbnailer
```
- Skip `linux-cachyos-lts` if you don't care about fallback kernel. Skip `linux-cachyos` if you prefer LTS kernel for some reasons
- If you don't know whether you need `sof-firmware` or not, keep it
- `systemd-boot-manager` is a nice tool to automate systemd-boot. We could do it manually but who have time for that?
- `cachyos-settings` will automatically handle zram and other nice improvement for us
- `downgrade` is nice to have to rollback specific packages to previous version and pin it until upstream fixes
- Pick `amd-ucode` if you have AMD CPU. Pick `intel-ucode` if you have Intel CPU.
- `ffmpegthumbnailer` also pulls `ffpmeg` which provide basic needed codecs  

## Generating Initramfs
Run this
```
/usr/lib/booster/regenerate_images
```
This will create appropriate booster images

## Bootloader Configuration
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

Simple right? We could do these manually but we have shit to do.

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

## Installing Drivers
```
chwd -a
```
This will handles every necessary drivers for us

## Enabling XFS services
This is why XFS is superior choice of filesystem
```
systemctl enable xfs_scrub_all.timer
systemctl enable xfs_healer.service
systemctl enable fstrim.timer
```
Ofc if you prefer EXT4 instead, you don't get to do these. At least your documentation is better, right? Right????

## Reboot
Base installation is done! If you want desktop that just works, refer to [easy mode](https://github.com/tungnon/mycustomarch/blob/main/Installation/easy.md). Otherwise, follow desktop setup section.
