
# BusyBox Service Program

This project demonstrates how to create and manage a custom C program as a service on an embedded Linux system using BusyBox `init` and `rcS` scripts.

---

## 1. Build the Service Program

Example service program (`myservice.c`):

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    while (1) {
        printf("My service is running...\n");
        fflush(stdout);
        sleep(5);
    }
    return 0;
}
```

### Compile for your target
```bash
# Native build
gcc myservice.c -o myservice

# Cross-compile (example for ARM)
arm-linux-gnueabihf-gcc myservice.c -o myservice
```

Copy the binary into your root filesystem:
```bash
cp myservice /usr/bin/
```

---

## 2. Create Init Script

Create `/etc/init.d/S99myservice`:

```sh
#!/bin/sh

PIDFILE=/var/run/myservice.pid
SERVICE=/usr/bin/myservice

case "$1" in
  start)
    echo "[BOOT] Starting myservice..." > /dev/console
    $SERVICE &
    echo $! > $PIDFILE
    ;;
  stop)
    echo "[BOOT] Stopping myservice..." > /dev/console
    kill `cat $PIDFILE` 2>/dev/null
    rm -f $PIDFILE
    ;;
  restart)
    echo "[BOOT] Restarting myservice..." > /dev/console
    $0 stop
    $0 start
    ;;
  status)
    if [ -f $PIDFILE ] && kill -0 `cat $PIDFILE` 2>/dev/null; then
      echo "myservice is running with PID `cat $PIDFILE`" > /dev/console
    else
      echo "myservice is not running" > /dev/console
    fi
    ;;
  *)
    echo "Usage: $0 {start|stop|restart|status}" > /dev/console
    exit 1
esac

exit 0
```

Make it executable:
```bash
chmod +x /etc/init.d/S99myservice
```

---

## 3. rcS Configuration

The main `rcS` script (`/etc/init.d/rcS`) should look like this:

```sh
#!/bin/sh
for i in /etc/init.d/S??* ; do
    [ -x $i ] && $i start
done
```

This ensures all `SXX*` scripts run at boot in numeric order.

- `S99myservice` → runs at the end of boot  
- If you want your service earlier, rename it (e.g., `S80myservice`)  

---

## 4. Testing

### Start service manually
```bash
/etc/init.d/S99myservice start
```

### Stop service
```bash
/etc/init.d/S99myservice stop
```

### Check status
```bash
/etc/init.d/S99myservice status
```

### Reboot to test auto-start
```bash
reboot
```

At boot, you should see:
```
[BOOT] Starting myservice...
myservice is running with PID 123
```
on your **serial console**.

---

## 5. Notes
- `S` prefix = start scripts at boot  
- `K` prefix = kill scripts at shutdown (optional)  
- Numbers (`01–99`) define execution order  

---

✅ With this setup, your C program runs as a proper BusyBox-managed service.  
