---
title: "Deployment"
description: "How to build, load, and persist the EpiRootkit kernel module."
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-07T00:44:31+01:00"
draft: false
toc: true
weight: 43
---

# EpiRootkit Deployment

> **Assumption:** You are starting from a fresh Ubuntu 20.04 VM. Complete [VM Installation](../02-setup/vm-installation.md) first.

## 1. Install Build Tools and Kernel Headers
```bash
sudo apt update
sudo apt install -y build-essential linux-headers-$(uname -r) git
```

> {{< alert context="info" text="Check your kernel version with `uname -r`. The headers must match exactly!" />}}

## 2. Clone or Copy the Project
If not already present, copy the `rootkit/` directory into your VM, or clone your repository.

## 3. Build the Kernel Module
```bash
cd rootkit
make
```

You should see `epirootkit.ko` in the directory after a successful build.

> {{< alert context="danger" text="If you see errors about missing headers or symbols, check your kernel version and header installation." />}}

## 4. Load the Module
```bash
sudo insmod epirootkit.ko
# Or, if installed to /lib/modules:
sudo modprobe epirootkit
```

Check that the module is loaded:
```bash
lsmod | grep epirootkit
```

Check kernel logs for messages:
```bash
dmesg | tail -n 20
```

## 5. Unload the Module
```bash
sudo rmmod epirootkit
```

## 6. Persistence (Autoload on Boot)
To load the module automatically on boot:
```bash
sudo cp epirootkit.ko /lib/modules/$(uname -r)/extra/
sudo depmod -a
echo epirootkit | sudo tee /etc/modules-load.d/epirootkit.conf
```

> {{< alert context="success" text="On next reboot, epirootkit will be loaded automatically." />}}

## 7. Troubleshooting
- If you see `Invalid module format`, your kernel headers may not match your running kernel.
- If you see `Unknown symbol`, check for missing dependencies or kernel version mismatches.
- For permission errors, ensure you are using `sudo`.
- Check `dmesg` for detailed kernel error messages.

## 8. Next Steps
- See [Features](./features/_index.md) for usage and advanced configuration. 