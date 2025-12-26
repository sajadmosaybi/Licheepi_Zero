# Buildroot 2023.05 for Lichee Pi Zero (Allwinner V3s)

This repository contains **Buildroot 2023.05** configured for the **Lichee Pi Zero** development board, based on the **Allwinner V3s (ARM Cortex-A7)** SoC.

## Project Structure
```
my-buildroot-project/
├── buildroot/              # Buildroot 2023.05 source
├── configs/                # (optional) Custom configs will go here
└── board/licheepi-zero/    # (optional) Board-specific files
```

## Requirements

| Tool | Version |
|------|----------|
| Host OS | Linux (Ubuntu recommended) |
| GCC Toolchain | Installed by Buildroot |
| Dependencies | `git build-essential ncurses-dev bison flex python3` |
| SD Card | 8GB or more |

### Install dependencies (Ubuntu)
```bash
sudo apt update
sudo apt install git build-essential bc bison flex libssl-dev ncurses-dev python3 wget cpio unzip
```

## Build Instructions

```bash
cd buildroot
make distclean
make licheepi_zero_defconfig
make -j$(nproc)
```

## Output Files
Located in:
```
buildroot/output/images/
```

| File | Description |
|------|--------------|
| `rootfs.ext4` | Root filesystem |
| `u-boot-sunxi-with-spl.bin` | Bootloader |
| `zImage` | Kernel |
| `sun8i-v3s-licheepi-zero.dtb` | Device Tree |

## Flash to SD Card

```bash
cd buildroot/output/images/
sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1024 seek=8
```

Partition layout:
| Partition | Format | Purpose |
|-----------|---------|----------|
| p1 | FAT32 | Kernel + DTB |
| p2 | EXT4 | Rootfs |

## Serial Console
- Baud: **115200**
- Command:
```bash
picocom -b 115200 /dev/ttyUSB0
```

Login:
```
root
```

## Resources
- Buildroot Manual: https://buildroot.org/downloads/manual/manual.html
- Lichee Pi Zero Docs: https://licheepizero.readthedocs.io
- Linux-Sunxi: https://linux-sunxi.org

---
