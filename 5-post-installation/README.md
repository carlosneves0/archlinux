# `5-post-installation`

<ins>NOTE</ins>: the bellow commands are in **`fish`** not `bash`.

## Network

### Wireless only

### Wired + Wireless

## `/etc/pacman.d/mirrorlist`

```
reflector --country Brazil --sort rate --threads (nproc) | sudo tee /etc/pacman.d/mirrorlist
```

## datetime

TODO: check if NTP service is active with `timedatectl`. In case it isn't:

```
# timedatectl set-ntp true
# timedatectl
```
