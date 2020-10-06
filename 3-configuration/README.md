# `3-configuration`

<ins>SOURCE</ins>: [Arch Linux Wiki Installation Guide](https://wiki.archlinux.org/index.php/installation_guide#Configure_the_system)

## Fstab

```
# genfstab -U /mnt >> /mnt/etc/fstab
```

## Chroot

```
# arch-chroot /mnt
```

## Time zone

```
# ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
# hwclock --systohc
```

## Localization

`vim /etc/locale.gen` and uncomment `en_US.UTF-8 UTF-8` and other needed locales.

Generate the locales by running:

```
# locale-gen
```

`vim /etc/locale.conf`:

```
LANG=zh_TW.UTF-8
```

## Virtual console

`vim /etc/vconsole.conf`:

```
FONT=latarcyrheb-sun32
```

## Network configuration

`vim /etc/hostname`:

```
myhostname
```

`vim /etc/hosts`:

```
127.0.0.1  localhost
::1        localhost
```

## Initramfs

Add **`resume`** to `HOOKS` in `mkinitcpio.conf`. `vim /etc/mkinitcpio.conf`:

```
HOOKS=(base udev autodetect keyboard modconf block filesystems resume fsck)
```

Regenerate the initramfs:

```
# mkinitcpio -p linux
```

## Users and permissions

### [Specify acceptable login combinations with access.conf](https://wiki.archlinux.org/index.php/Security#Specify_acceptable_login_combinations_with_access.conf)

Only allow local logins for the `root` user and users belonging to the `wheel` group:

```
# vim /etc/security/access.conf
```

```
+:root:LOCAL
+:(wheel):LOCAL
-:ALL:ALL
```

### [File access permissions](https://wiki.archlinux.org/index.php/Security#File_access_permissions)

```
# chmod -R go-rwx /boot /etc/iptables /root
```

---

```
# vim /etc/profile
```

```
umask 077
```

---

```
# vim /etc/fish/config.fish
```

```
umask 077
```

### Root shell and password

```
# chsh -s /bin/fish
# passwd
```

### Create my user

```
# useradd -m -g wheel -G video -s /bin/fish myuser
# chmod go-rwx /home/myuser/bash*
# passwd myuser
```

### Configure `sudo`

```
# EDITOR=vim visudo
```

```
%wheel ALL=(ALL) ALL
```

## Boot loader

### `systemd-boot`

Again, be sure UEFI boot mode is enabled.

```
# ls /sys/firmware/efi/efivars
```

```
# pacman -S --noconfirm intel-ucode
# bootctl --path=/boot install
```

`vim /boot/loader/loader.conf`:

```
default achlinux.conf
editor  no
```

`vim /boot/loader/entries/archlinux.conf`:

```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /intel-ucode.img
initrd  /initramfs-linux.img
options root=UUID=%ROOT_UUID% resume=UUID=%SWAP_UUID% rw
```

Bellow, `/dev/sdxY` is my Linux root partition and `/dev/sdxZ` is my swap partion. Replace `%ROOT_UUID%` and `%SWAP_UUID%` with their actual UUIDs:

```
sed -i "s/%ROOT_UUID%/$(lsblk -o UUID -n /dev/sdxY)/" /boot/loader/entries/archlinux.conf
sed -i "s/%SWAP_UUID%/$(lsblk -o UUID -n /dev/sdxZ)/" /boot/loader/entries/archlinux.conf
```

### Useful kernel parameters

Add them after `rw` in the "`options`" line in `/boot/loader/entries/archlinux.conf`.

#### `video=1920x1080@60`

Make the HDPI display Linux user experience more user-friendly.

#### `pci=nomsi`

Suppress "`PCIe Bus error severity=Corrected`" error messages; prevent them from disrupting the linux console.
