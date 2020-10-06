# `0-usb-key`

## Burn Arch Linux's `.iso` to a USB drive using `dd`

<ins>SOURCE</ins>: [Arch Linux Wiki](https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_dd)

<ins>WARNING</ins>: **This will irrevocably destroy all data on /dev/sdx.**

<ins>TIP</ins>: To restore the USB drive as an empty, usable storage device after using the Arch ISO image, the ISO 9660 filesystem signature needs to be removed by **running `wipefs --all /dev/sdx` as root, before repartitioning and reformatting the USB drive.**

<ins>TIP</ins>: Find out the name of your USB drive with `lsblk`. **Make sure that it is not mounted.**

Run the following command, replacing `/dev/sdx` with your drive, e.g. `/dev/sdb`. **Do not append a partition number, so do not use something like `/dev/sdb1`.**

```
# dd bs=4M if=path/to/archlinux.iso of=/dev/sdx status=progress oflag=sync
```
