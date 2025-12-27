# Buildroot: Changing Kernel and Toolchain Version

This README explains how to change the **Linux kernel version** and **toolchain version** in Buildroot using `menuconfig`.

---

## Prerequisites

- Buildroot installed
- Basic knowledge of Buildroot configuration
- Terminal access

---

## 1. Start Buildroot Menuconfig

Open a terminal in your Buildroot root directory:

```bash
make menuconfig
```

This will open the Buildroot configuration menu.

---

## 2. Change Linux Kernel Version

1. Navigate to:

```
Target packages → Linux → Linux Kernel
```

2. Enable the kernel (if not already):

```
[*] Linux Kernel
```

3. Select the desired kernel version:

```
(5.15.24) Kernel version  ---> [choose your version]
```

4. Optional configurations:
   - Kernel defconfig
   - Custom patches
   - Kernel build options

---

## 3. Change Toolchain Version

1. Navigate to:

```
Toolchain → Toolchain type
```

2. Choose your toolchain type (Buildroot internal or external).

3. Select the desired GCC version:

```
Toolchain version ---> [choose your version]
```

4. Optional configurations:
   - C library (glibc, uClibc, musl)
   - Kernel headers version
   - Compiler options

---

## 4. Save Configuration and Build

1. Save your configuration and exit `menuconfig`.
2. Rebuild the system:

```bash
make clean   # optional but recommended
make
```

> Note: Changing kernel or toolchain usually requires a **full rebuild**.

---

## 5. Additional Tips

- Ensure the kernel version is compatible with your toolchain and board.
- Configure kernel headers for the toolchain under:

```
Toolchain → Kernel headers
```

- For external toolchains (e.g., Linaro), specify the path:

```
Toolchain → External toolchain → Toolchain path
```

---

**References:**

- [Buildroot Manual](https://buildroot.org/downloads/manual/manual.html)

