# USB Stick Swap & Storage Setup

This guide explains how to set up an USB stick as a swapfile and additional storage on your Jetson Nano.

This is recommended to reduce SD card wear and to extend the available swap space.

## Why this might be important

When compiling programs like OpenCV, usually more RAM is required than the 4GB the Jetson Nano has.

Or when running some AI model on the Jetson Nano, that occupies most of the RAM, a swap might prevent system crashes.

Creating a swapfile on the sd card will make the system very slow and shorten the life of the card due to high IO.

---

## Requirements

- USB stick, **USB 3.0 or higher recommended** (USB 3.2 for best performance)
- **Minimum 16 GB** total size (8 GB for swapfile + space for storage)
- The stick must be formatted as **ext4** (see step below)

---

## Overview

What this setup does:

- Mounts the USB stick at `/mnt/usb`
- Creates an **8 GB swapfile** on the USB stick (supplements the built-in zram swap)
- Bind-mounts `/mnt/usb/nano-usb` to `/home/jetson/nano-usb` for easy access as a working/download directory

---

## Step 1 — Format the USB Stick as ext4

> ⚠️ This will erase all data on the stick. Back up anything important first.

Find your USB stick device name:

```bash
lsblk
```

It will typically show as `/dev/sda` or `/dev/sdb`. Your USB partition will be something like `/dev/sda1`.

Unmount it if it is already mounted:

```bash
sudo umount /dev/sda1
```

Format as ext4 with a label:

```bash
sudo mkfs.ext4 -L "NanoUSB" /dev/sda1
```

---

## Step 2 — Mount the USB Stick

Create the mount point and mount the stick temporarily:

```bash
sudo mkdir -p /mnt/usb
sudo mount /dev/sda1 /mnt/usb

# Verify
df -h | grep sda1
```

---

## Step 3 — Create the Swapfile

```bash
sudo fallocate -l 8G /mnt/usb/swapfile
sudo chmod 600 /mnt/usb/swapfile
sudo mkswap /mnt/usb/swapfile
```

---

## Step 4 — Create the nano-usb Storage Directory

```bash
# Create the folder on the USB stick
sudo mkdir -p /mnt/usb/nano-usb

# Set ownership to the jetson user
sudo chown jetson:jetson /mnt/usb/nano-usb

# Create the mount point in the home directory
mkdir -p /home/jetson/nano-usb
```

---

## Step 5 — Find the USB Stick UUID

```bash
sudo blkid /dev/sda1
```

Example output:
```
/dev/sda1: LABEL="NanoUSB" UUID="02102ffb-6966-478e-9195-5e563fe0a652" TYPE="ext4"
```

Copy the UUID value — you will need it in the next step.

---

## Step 6 — Edit /etc/fstab

Open fstab:

```bash
sudo nano /etc/fstab
```

The file already contains the three lines below as comments. **Uncomment them** and replace `YOUR-UUID-HERE` with the UUID you copied in Step 5:

```
# USB Stick — replace YOUR-UUID-HERE with your actual UUID from: sudo blkid /dev/sda1
UUID=YOUR-UUID-HERE  /mnt/usb               ext4  defaults,nofail,x-systemd.automount  0  0
/mnt/usb/swapfile    none                   swap  sw,nofail                            0  0
/mnt/usb/nano-usb    /home/jetson/nano-usb  none  bind,nofail                          0  0
```

> **Note on options:**
> - `nofail` — the system boots normally even if the USB stick is not present
> - `x-systemd.automount` — prevents boot errors by waiting for the USB device to be ready before mounting
> - `bind` — makes `/mnt/usb/nano-usb` accessible at `/home/jetson/nano-usb` without copying data

Save and exit: `Ctrl+O`, `Enter`, `Ctrl+X`

---

## Step 7 — Apply and Verify

Reload systemd and mount everything:

```bash
sudo systemctl daemon-reload
sudo mount -a
```

Activate the swapfile:

```bash
sudo swapon /mnt/usb/swapfile
```

Verify everything is working:

```bash
# Check swap — should show zram entries + your swapfile
swapon --show

# Check mounts
df -h | grep usb

# Check bind mount
ls /home/jetson/nano-usb

# Check total swap (should show ~10GB: 2GB zram + 8GB swapfile)
free -m
```

---

## Step 8 — Reboot and Confirm

```bash
sudo reboot
```

After reboot, verify again:

```bash
swapon --show
df -h | grep usb
ls /home/jetson/nano-usb
```

If everything looks good, your setup is complete.

---

## Reverting / Disabling the Setup

To disable the USB swap and storage (e.g. when preparing a clean image for distribution):

```bash
sudo swapoff /mnt/usb/swapfile
sudo umount /home/jetson/nano-usb
sudo umount /mnt/usb
```

Then comment out the three lines in `/etc/fstab` again and reload:

```bash
sudo systemctl daemon-reload
```

---

## Summary

| What | Where |
|---|---|
| USB stick mount | `/mnt/usb` |
| Swapfile | `/mnt/usb/swapfile` (8 GB) |
| Storage directory (on stick) | `/mnt/usb/nano-usb` |
| Storage directory (home) | `/home/jetson/nano-usb` |
| Total swap after setup | ~10 GB (2 GB zram + 8 GB USB) |

---

## MIT License

Copyright 2026 Mischa (Michael Schaefer)

This document is part of this Repo: [mischa-robots/jetson-nano-ubuntu22](https://github.com/mischa-robots/jetson-nano-ubuntu22)
