# Jetson Nano — Ubuntu 22.04 Server Image

A ready-to-run Ubuntu 22.04 (Jammy) server image for the Jetson Nano including CUDA, TensorRT, OpenCV (no CUDA), Python 3.10.12 and automatic fan control.

This image is based on NVIDIA JetPack 4.6.6 / L4T r32.x and upgrades the userspace to Ubuntu 22.04.

---

## ⚠️ License & Legal Notice

This image contains proprietary NVIDIA software components from Linux for Tegra (L4T), including CUDA, cuDNN, TensorRT and related drivers.

These components are redistributed **unmodified** under the original NVIDIA Tegra Software License Agreement.

* No NVIDIA binaries have been modified.
* Some sample and demo files were removed to reduce image size.
* NVIDIA components remain property of NVIDIA Corporation.
* This project is not affiliated with or endorsed by NVIDIA.

The full NVIDIA license text is included in this repository as:

* [NVIDIA-L4T-License.txt](NVIDIA-L4T-License.txt)

Or on the official NVIDA website:

* [Tegra_Software_License_Agreement-Tegra-Linux](https://developer.download.nvidia.com/embedded/L4T/r32_Release_v4.3/t186ref_release_aarch64/Tegra_Software_License_Agreement-Tegra-Linux.txt)

Your use of this image must comply with the NVIDIA Tegra Software License Agreement.

---

## What's Included

* Ubuntu 22.04 LTS (Jammy) userspace
* NVIDIA L4T (JetPack 4.6.x base)
* CUDA 10.2
* TensorRT
* cuDNN
* OpenCV (CUDA enabled)
* [jtop](https://github.com/rbonghi/jetson_stats) — Jetson system monitor
* [JetFanC](https://github.com/mischa-robots/JetFanC) — automatic fan control service (MIT licensed)
* Partition auto-expand on first boot
* Pre-installed SDK validation script: `/home/jetson/test-sdk.sh` (run with `sudo ./test-sdk.sh`)

---

## Requirements

* Jetson Nano (4GB or 2GB)
* SD card — **64GB recommended**, 16GB minimum
* Monitor + keyboard **or** ethernet connection for headless setup via ssh

---

## 1. Flash the SD Card

### With Balena Etcher (recommended)

Download [Balena Etcher](https://etcher.balena.io), select `jetson-nano-ubuntu22-server.img.xz` and flash to your SD card. Etcher handles decompression automatically.

### With dd

```bash
xz -dc jetson-nano-ubuntu22-server.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
sync
```

> ⚠️ Replace `/dev/sdX` with your actual SD card device. Double check with `lsblk` — writing to the wrong device will destroy your data.

---

## 2. First Boot

Insert the SD card into your Jetson Nano and power it on.

On first boot the partition will **automatically expand** to fill your SD card — this takes about 30 seconds. The board will then be ready to use.

Login credentials:

| User     | Password |
| -------- | -------- |
| `jetson` | `jetson` |

> 🔒 Change your password after first login with `passwd`

---

## 3. Connect to Network

### Ethernet

Just plug in a cable, it connects automatically.

### WiFi

```bash
# list available networks
nmcli device wifi list

# connect to your network (will prompt for password)
sudo nmcli device wifi connect "YourSSID" --ask
```

### Headless via SSH

```bash
ssh jetson@nano1.local
```

Or use the IP address shown on the monitor.

---

## 4. Verify System

```bash
# Jetson system monitor
sudo jtop

# Fan control service status
sudo systemctl status jetfanc

# Check CUDA
nvcc --version
```

---

## Included Services

| Service          | Description                                     |
| ---------------- | ----------------------------------------------- |
| `jetfanc`        | Automatic PWM fan control                       |
| `jtop`           | Jetson stats monitor                            |
| `ssh`            | Remote access                                   |
| `NetworkManager` | WiFi and ethernet management                    |
| `expand-rootfs`  | Auto-expand partition on first boot (runs once) |

## NOT Included Services

- Desktop environment
- Docker

---

## Tested On

* Jetson Nano 4GB (B01)

---

## Credits

This image builds upon work from the community:

* xronos-inc — Jetson Nano Ubuntu 22.04 upgrade method
  [https://github.com/xronos-inc/jetson-nano-ubuntu-22.04](https://github.com/xronos-inc/jetson-nano-ubuntu-22.04)

* Qengineering — Jetson Ubuntu & CUDA guidance
  [https://qengineering.eu/install-ubuntu-20.04-on-jetson-nano.html](https://qengineering.eu/install-ubuntu-20.04-on-jetson-nano.html)


This image includes:

* jtop (jetson_stats) is licensed under AGPL-3.0.
Source code is available at: https://github.com/rbonghi/jetson_stats
AGPL-3.0 license is avaiable at: https://github.com/rbonghi/jetson_stats/blob/master/LICENSE

---

## Author

Michael "Mischa" Schaefer

Additional software included:

* JetFanC (MIT License)
  [https://github.com/mischa-robots/JetFanC](https://github.com/mischa-robots/JetFanC)
