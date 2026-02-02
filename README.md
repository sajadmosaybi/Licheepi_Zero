# Embedded CLI Shell for Allwinner V3S (Buildroot + BusyBox)

This project provides a lightweight **Command Line Interface (CLI) shell** written in **C** for the **Licheepi Zero Dock** development board running **Linux built with Buildroot**.  
It replaces the standard Linux shell (bash/sh) with a custom user-defined shell that allows only specific commands and restricts system access.

---

## 🧩 Features

- Runs automatically on boot via serial console (`ttyS0`)
- Provides a restricted, user-friendly CLI
- Supports common Linux commands:
  - `ls`, `cat <file>`, `ps`, `reboot`
- Includes user-defined commands:
  - `led_on`, `led_off`
- Built-in commands:
  - `help`, `clear`, `exit`
- Prevents user access to bash or other shells
- Simple, clean C implementation (no external libraries)

---

## 🛠️ Requirements

- Licheepi Zero Dock board  
- Buildroot (tested with 2023.x)  
- BusyBox init system (default in Buildroot)  
- UART serial console (e.g., `/dev/ttyS0` at 115200 baud)

---

## 📁 Project Structure

```
embedded_shell/
├── Config.in
├── embedded_shell.c
└── embedded_shell.mk
```

---

## ⚙️ Build Instructions

### 1️⃣ Add to Buildroot

Copy this folder into your Buildroot tree:
```
buildroot/package/embedded_shell/
```

Then add it to Buildroot’s configuration:

```bash
make menuconfig
```

Go to:
```
Target packages → Misc → [*] embedded_shell
```

Save and exit.

---

### 2️⃣ Build the System

Run:
```bash
make
```

After the build completes, the binary will be installed at:
```
/usr/bin/myshell
```

---

### 3️⃣ Auto-start on Boot

Edit your Buildroot overlay or root filesystem file `/etc/inittab`:

Find this line:
```
ttyS0::respawn:/sbin/getty -L ttySTM0 115200 vt100
```

Replace it with:
```
ttyS0::respawn:/usr/bin/myshell
```

This ensures that when the board boots and the serial console opens, your CLI runs directly — without login or access to the standard Linux shell.

---

## ▶️ Usage Example

When you power up and connect via serial (115200 baud), you’ll see:

```
=====================================
  Embedded CLI Shell - Allwinner V3S
=====================================
Type 'help' to see available commands.

shell>
```

### Available Commands

| Command | Description |
|----------|-------------|
| `help` | Show list of available commands |
| `clear` | Clear the terminal screen |
| `ls` | List files in current directory |
| `cat <file>` | Display file contents |
| `ps` | Show running processes |
| `reboot` | Reboot the system |
| `led_on` | Turn on LED (example using sysfs) |
| `led_off` | Turn off LED |
| `exit` | Exit shell (if `respawn:` in inittab, it restarts) |

---

## 🔒 Security

To limit access to the standard shell:
- Disable `login` and `bash` in Buildroot (`make menuconfig`)
- Ensure `/bin/sh` is not accessible
- Configure `/etc/inittab` to run only this CLI

This ensures that users interacting over UART can **only** use your restricted CLI.

---

## 🧰 Future Enhancements

- Command history and arrow key navigation (via `linenoise`)
- Tab auto-completion
- Custom hardware control commands (GPIO, I2C, etc.)
- Ethernet or UART remote CLI interface

---

## 🧑‍💻 Author

**Sajad Mosayebi**  
Embedded Systems Engineer  
📧 Smosaybi@gmail.com 

---

## 📄 License

This project is licensed under the **MIT License** — you are free to use, modify, and distribute it with attribution.
