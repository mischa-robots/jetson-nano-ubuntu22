# Jetson Nano — Ubuntu 22.04 Server Image

A ready-to-run Ubuntu 22.04 (Jammy) server image for the Jetson Nano with CUDA, TensorRT, OpenCV, Python 3.10.12 and automatic fan control pre-installed.

This image is based on NVIDIA JetPack 4.6.6 / L4T R32.7.6 and upgrades the userspace to Ubuntu 22.04.

---

## What's Included

| Component        | Version       | Status |
| ---------------- | ------------- | ------ |
| Ubuntu           | 22.04 LTS (Jammy) | ✅ |
| L4T              | R32.7.6       | ✅ |
| Kernel           | 4.9.337-tegra | ✅ |
| CUDA             | 10.2.300      | ✅ |
| cuDNN            | 8.2.1         | ✅ |
| TensorRT         | 8.2.0.1       | ✅ |
| OpenCV           | 4.5.4 (CPU only) | ⚠️ CUDA build in progress |
| VPI              | 1.2.3         | ✅ |
| VisionWorks      | 1.6.0.501     | ✅ |
| DeepStream       | 6.0.1         | ✅ |
| GStreamer        | with nvvidconv, nvv4l2decoder, nvv4l2h264enc | ✅ |
| MIPI CSI cameras | ?             | ✅ |
| Python           | 3.10.12       | ✅ |
| [jtop](https://github.com/rbonghi/jetson_stats) | latest | ✅ |
| [JetFanC](https://github.com/mischa-robots/JetFanC) | latest | ✅ |

## NOT Included

* Desktop environment
* Docker

---

## Requirements

* Jetson Nano (4GB or 2GB)
* SD card — **64GB recommended**, 16GB minimum
* Monitor + keyboard **or** ethernet connection for headless setup via SSH

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
# Jetson system monitor (CPU, GPU, RAM, temperature, fan)
sudo jtop

# Fan control service status
sudo systemctl status jetfanc

# Check CUDA
nvcc --version

# Run full SDK validation
sudo ~/test-sdk.sh
```

> Note: if `nvcc` is not found interactively, add CUDA to your PATH permanently:
> ```bash
> sudo tee /etc/profile.d/cuda.sh >/dev/null <<'EOF'
> export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
> export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
> EOF
> source /etc/profile.d/cuda.sh
> ```

---

## Included Services

| Service          | Description                                     |
| ---------------- | ----------------------------------------------- |
| `jetfanc`        | Automatic PWM fan control                       |
| `jtop`           | Jetson stats monitor                            |
| `ssh`            | Remote access                                   |
| `NetworkManager` | WiFi and ethernet management                    |
| `expand-rootfs`  | Auto-expand partition on first boot (runs once) |

---

## Known Limitations

* CUDA 10.2 only — newer CUDA versions are not supported on Jetson Nano
* OpenCV 4.5.4 is CPU only for now — a CUDA-enabled build is in progress
* JetPack 4.6.6 base — some newer libraries targeting JetPack 5.x will not be compatible
* Python bindings for TensorRT and VPI are not functional — C++ APIs work correctly
* No desktop environment — this is a server image
* Docker not included - work in progress

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

* jtop (jetson_stats) licensed under AGPL-3.0
  Source: [https://github.com/rbonghi/jetson_stats](https://github.com/rbonghi/jetson_stats)
  License: [AGPL-3.0](https://github.com/rbonghi/jetson_stats/blob/master/LICENSE)

* JetFanC licensed under MIT
  [https://github.com/mischa-robots/JetFanC](https://github.com/mischa-robots/JetFanC)

---

## Author

Michael "Mischa" Schaefer

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

Or on the official NVIDIA website:

* [Tegra_Software_License_Agreement-Tegra-Linux](https://developer.download.nvidia.com/embedded/L4T/r32_Release_v4.3/t186ref_release_aarch64/Tegra_Software_License_Agreement-Tegra-Linux.txt)

Your use of this image must comply with the NVIDIA Tegra Software License Agreement.
