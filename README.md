# Embedded Linux Overlay & Cross-Compilation Guide

This README explains how to:

1. Install required packages
2. Change **hostname**, **banner**, and **shell prompt** using a **root filesystem overlay**
3. Set Static IP
4. Cross-compile a simple **C counter application**
5. Integrate everything into an Embedded Linux build system (Buildroot / Yocto-style workflow)

---

## 1.  Package Installation
1️⃣ Menuconfig Method

Install ```nano, htop, dhcp, bash, ssh, ethtool, python, screen```

1. ```make menuconfig```
2. ```Target Package --->```
3. Select and Enable your package
4. Save and Exit from menuconfig
5. ```make -jx```
6. Flash and boot your board.
    ```sudo dd if=output/images/sdcard.img of=/dev/sdx bs=1024 status=progress```
---

## 2. Root Filesystem Overlay Structure

Create an overlay directory:

```bash
mkdir -p rootfs_overlay/etc
mkdir -p rootfs_overlay/etc/profile
mkdir -p rootfs_overlay/usr/bin
```

This overlay will be merged into the final root filesystem during build.

---

## 3. Change Hostname (Overlay)

Create the hostname file:

```bash
nano rootfs_overlay/etc/hostname
```

Content:

```text
Licheepi-zero-dock
```

---

## 4. Change Login Banner

### 4.1 `/etc/issue` (Login banner)

```bash
nano rootfs_overlay/etc/issue
```

```text
    ===========================================
    LICHEEPI ZERO dock Embedded Linux System
    Built with Buildroot
    ===========================================
```

### 4.2 `/etc/motd` (After login)

```bash
nano rootfs_overlay/etc/motd
```

```text
System ready.
Authorized users only.
```

---

## 5. Change Shell Prompt (PS1)

Create a profile script:

```bash
nano rootfs_overlay/etc/profile
```

```bash
    #!/bin/sh
    echo "Welcome to LICHEEPI ZERO DOCK Embedded Linux"
    export PS1='\[\e[32m\]\u@\h\[\e[0m\]:\[\e[34m\]\w\[\e[0m\]\$ '
```
1. Save profile section
2. Enable overlay
    ```make menuconfig```
    ```System configuration  --->```
    ```Root filesystem overlay directories```
    ```board/myboard/rootfs-overlay```
3. Rebuild and Test
    ```make -jx```
4. Flash and boot your board.
    ```sudo dd if=output/images/sdcard.img of=/dev/sdx bs=1024 status=progress```
---

## 6. Cross-Compile Counter C Application

### 6.1 Counter Source Code

Create `counter.c`:

```c
#include <stdio.h>
#include <unistd.h>

int main(void)
{
    int counter = 0;
    while (1)
    {
        printf("Counter: %d\n", counter++);
        fflush(stdout);
        sleep(1);
    }
    return 0;
}
```

---

### 6.2 Cross Compilation

Using ARM toolchain example:
find gcc path in buildroot
   ``` buildrrot/output/host/bin/arm-buildroot-linux-gnueabihf-gcc```
```bash
arm-buildroot-linux-gnueabihf-gcc counter.c -o counter
```

Verify:

```bash
file counter
```

Expected output:

```text
ELF 32-bit LSB executable, ARM
```
we can proceed using the following method.

1. Use the scp command to transfer files to the Embedded Linux system.
2. Use the destination directory as the target directory in Buildroot.
3. Use the overlay method in the same way as done for the hostname, banner, and profile sections.

---

### 6.3 Install Counter via Overlay

Copy binary into overlay:

```bash
cp counter rootfs_overlay/usr/bin/
```

Make sure it is executable:

```bash
chmod +x rootfs_overlay/usr/bin/counter
```

---

## 7. Set Static IP
1. Configure and install the network in Buildroot using menuconfig.
2. Create the network configuration directory:
```
   mkdir -p board/myboard/rootfs-overlay/etc/network
```

4. Create the interfaces file inside ```board/myboard/rootfs-overlay/etc/network``` to define network settings.
    Add the following content to the ```interfaces``` file (replace with your network details if needed):
   
    ```
        auto lo
        iface lo inet loopback

        auto eth0
        iface eth0 inet static
            address 192.168.x.x
            netmask 255.255.255.0
            gateway 192.168.x.x
    ```

6. Rebuild Linux using the following command (replace x with the number of CPU cores for parallel compilation):
     ```make -jx```
    
7. Flash Linux onto the SD card and verify the network:

     ```sudo dd if=output/images/sdcard.img of=/dev/sdb bs=4M status=progress```

8. After booting the board, check the IP address using:

     ```ifconfig```

## 7. Integrating Overlay

### Buildroot

In `make menuconfig`:

```
System configuration  --->
    Root filesystem overlay directories
```

Set path:

```text
/path/to/rootfs_overlay
```

---

## 8. Runtime Verification

On target device:

```bash
hostname
counter
```

Expected:
- Custom hostname
- Custom banner
- Custom prompt
- Static IP
- Counter increments every second

---

## 9. Summary

✔ Hostname customization via overlay

✔ Banner & MOTD customization

✔ Shell prompt modification

✔ Set Static IP

✔ Cross-compiled user application

✔ Clean integration using overlay (no rootfs hacking)

---

**Author:** Sajad Mosayebi
**Use case:** Licheepi Zero/ STM32MP1 / Raspberry Pi / ARM-based systems

