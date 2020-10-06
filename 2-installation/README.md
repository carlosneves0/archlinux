# `2-installation`

<ins>SOURCE</ins>: [Arch Linux Wiki Installation Guide](https://wiki.archlinux.org/index.php/installation_guide#Installation)

## Select the mirrors

```
# pacman -Sy --noconfirm reflector
# reflector --threads $(nproc) --country Brazil --sort rate > /etc/pacman.d/mirrorlist
```

## Install essential packages

If you don't need wifi, there's no need for iwd.

```
# pacstrap /mnt base linux linux-firmware vim fish sudo iwd reflector
```
