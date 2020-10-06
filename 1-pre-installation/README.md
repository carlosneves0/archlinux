# `1-pre-installation`

<ins>SOURCE</ins>: [Arch Linux Wiki Installation Guide](https://wiki.archlinux.org/index.php/installation_guide#Pre-installation)

## Boot the live environment

### HDPI diplay

Boot with the following additional kernel parameter:

```
video=1920x1080@60
```

### Add kernel parameters when booting the usb key

<ins>SOURCE</ins>: [Arch Linux Reddit](https://www.reddit.com/r/archlinux/comments/9bv2vj/how_to_add_kernel_parameters_to_boot_from/)

When the boot menu appears leave the focus on the first item of the list, then press `e`.

An input field with a string will appear, append additional kernel parameters to the end.

Press Enter to boot with these parameters.

## Increase virtual console font size

```
# setfont latarcyrheb-sun32
```

## Adjust display brightness

```
# cat /sys/class/backlight/intel_backlight/max_brightness
# cat /sys/class/backlight/intel_backlight/brightness
# echo x > /sys/class/backlight/intel_backlight/brightness
```

## Connect to the internet

<ins>SOURCE</ins>: [Insanity Industries Blog](https://insanity.industries/post/simple-networking/)

Wired network setup: systemd-networkd + systemd-resolved.

Wireless network setup: iwd + systemd-resolved.

### DNS

```
# systemctl enable --now systemd-resolved
# ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
```

### Wired network

List wired network interfaces:

```
# ls /sys/class/net | grep -E 'en.+'
```

`vim /etc/systemd/network/wired.network`:

```
[Match]
Name=<name of the wired network device> <second one if applicable> [<more ...>]

[Network]
DHCP=yes
IPv6PrivacyExtensions=true

[DHCP]
Anonymize=true
```

```
# systemctl enable --now systemd-networkd
```

### Wireless network

`mkdir /etc/iwd; vim /etc/iwd/main.conf`:

```
[General]
EnableNetworkConfiguration=true
AddressRandomization=once
```

```
# systemctl enable --now iwd
# iwctl station list
# iwctl station <wlan> scan
# iwctl station <wlan> get-networks
# iwctl station <wlan> connect 'foo wifi ssid'
```

## Update the system clock

```
# timedatectl set-ntp true
# timedatectl
```

## Verify the boot mode

Make sure UEFI mode is enabled on our motherboard:

```
# ls /sys/firmware/efi/efivars
```

If the directory does not exist, the system may be booted in BIOS or CSM mode. Refer to your motherboard's manual for details.

## Partition the disks

My partitions:

```
$ lsblk -o NAME,TYPE,MOUNTPOINT,PARTTYPENAME
NAME   TYPE MOUNTPOINT PARTTYPENAME
sda    disk
├─sda1 part /boot       EFI System
├─sda2 part /          Linux root (x86-64)
└─sda3 part [SWAP]     Linux swap
```

I'm gonna use `systemd-boot` later as a boot loader, so I'm mounting the ESP to `/boot` to make updating the kernel easier (ESP == EFI Sytem Partition).

```
# fdisk /dev/sdx
```

## Format the partitions

<ins>SOURCE</ins>: [Arch Linux Wiki](https://wiki.archlinux.org/index.php/File_systems)

> an EFI system partition must contain a **FAT32 file system**, and the file system containing the /boot directory must be supported by the boot loader.

[`systemd-boot` supports VFAT](https://wiki.archlinux.org/index.php/Arch_boot_process#Boot_loader) and the `/boot` directory will be the contents of the ESP. So format the EFI system partition with:

```
# mkfs.fat -F 32 /dev/sdxX
```

Format the root partition with:

```
# mkfs.ext4 /dev/sdxY
```

Initialize the swap partition with:

```
# mkswap /dev/sdxZ
# swapon /dev/sdxZ
```

## Mount the file systems

```
# mount /dev/sdxY /mnt
# mkdir /mnt/boot
# mount /dev/sdxX /mnt/boot
```
