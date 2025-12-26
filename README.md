# Customize Buildroot (Lichee Pi Zero)
## Change Hostname, Login Banner, and Root Password

This guide explains how to customize a Buildroot 2023.05 image for the **Lichee Pi Zero (Allwinner V3s)** by:
- Setting a **custom hostname**
- Editing the **login banner (motd / issue)**
- Setting a **root password**

---

## 📌 1. Change the Hostname

Edit the hostname file:
```
buildroot/board/licheepi-zero/rootfs-overlay/etc/hostname
```

Add your hostname, for example:
```
licheepi-zero-Dock
```

If the file doesn't exist, create it.  
Then enable overlay in Buildroot:

```bash
make menuconfig
# System configuration --->
#   Root filesystem overlay directories ---> (board/licheepi-zero/rootfs-overlay)
```

---

## 📌 2. Change Login Banner (MOTD)

Edit or create this file:
```
buildroot/board/licheepi-zero/rootfs-overlay/etc/motd
```

Example content:
```
===========================================
 Welcome to My Lichee Pi Zero System
 Buildroot 2023.05 | Allwinner V3s Cortex-A7
===========================================
```

### Optional: Change `/etc/issue` (shown before login)
```
buildroot/board/licheepi-zero/rootfs-overlay/etc/issue
```

Example:
```
My Lichee Pi Zero Linux (\n \l)
Login with user: root
```

---

## 📌 3. Change Root Password

Run the Buildroot configuration tool:
```bash
make menuconfig
```

Go to:
```
System configuration --->
    Root password ---> (enter your password)
```

Or leave it empty for no password.

---

## 📌 4. Rebuild the Image

To apply changes:
```bash
make
```

If issues occur, rebuild root filesystem only:
```bash
make fs
```

---

## 📌 5. Flash and Test

Flash the bootloader and filesystem as usual:
```bash
cd buildroot/output/images/
sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1024 seek=8
```

Mount `/dev/sdX2` and verify:
```
etc/hostname
etc/motd
etc/issue
```

Serial console login:
```bash
picocom -b 115200 /dev/ttyUSB0
```

---

## 📌 Summary Table

| Task | File / Setting |
|------|-----------------|
| Hostname | `/etc/hostname` |
| Banner (after login) | `/etc/motd` |
| Banner (before login) | `/etc/issue` |
| Root password | menuconfig → System configuration |

---

## ✔️ Recommended Folder Layout

```
board/
└── licheepi-zero/
    └── rootfs-overlay/
        └── etc/
            ├── hostname
            ├── motd
            └── issue
```

---

## 🎯 Final Notes

- This method is scalable for production systems.
- Overlay keeps your customizations version-controlled.
- No manual changes are needed after boot.

---

🚀 *Your Lichee Pi Zero now has a custom identity!*
