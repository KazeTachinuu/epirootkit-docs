---
title: "Installation"
description: "C2 server installation in attacker VM environment"
icon: "download"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 302
---

# C2 Server Installation

The C2 server runs inside the **attacker VM** (192.168.200.11), not on the host system.

## Quick Setup

**Complete attacker VM setup with C2 server:**
👉 **[Attacker VM Setup Guide](../../02-setup/attacking-vm-setup.md)**

This covers everything: Node.js installation, project transfer, dependencies, and C2 server launch.

## VM-Only Installation

If you already have the attacker VM configured and just need to install the C2 server:

### Prerequisites
- **Attacker VM running**: Ubuntu with Node.js 12+
- **Project files**: Available in VM (via git, scp, or shared folder)
- **Network**: VM can reach port 4444 (victim connections)

### Installation Steps

```bash
# Inside attacker VM (192.168.200.11)
cd attacking_program

# Install dependencies
pnpm install
# ✓ Dependencies installed

# Verify configuration
cat config.env
# C2_PORT=4444
# WEB_PORT=3000
# PASSWORD_HASH=b109f3bbbc...

# Start server
pnpm start
# ✓ C2 server started on port 4444
# ✓ Web interface started on port 3000
```

## Configuration Options

### Environment Settings
```bash
# Edit config.env in attacker VM
C2_PORT=4444               # Port for victim connections
WEB_PORT=3000              # Web interface port
ENCRYPTION_KEY=secret32hex  # XOR encryption key (32 hex chars)
ENABLE_ENCRYPTION=true     # Enable XOR encryption
PASSWORD_HASH=hash         # SHA-512 authentication hash
```

### Change Default Password
```bash
# Generate new SHA-512 hash
echo -n "newpassword" | sha512sum
# Copy hash to config.env as PASSWORD_HASH
```


## Access Points

### CLI Interface
```bash
# Direct access in attacker VM
pnpm start
# c2-server$ help
# c2-server$ ls
```

### Web Interface
**From any device on the network:**
- **URL**: `http://192.168.200.11:3000`
- **Login**: `password` (or your custom password)
- **Features**: Full CLI functionality via web

### Remote Access
```bash
# From host or other VMs
ssh attacker@192.168.200.11
cd attacking_program && pnpm start
```

## Verification

### Test CLI
```bash
c2-server$ help
# Shows available commands

c2-server$ ls  
# No clients connected (initially)
```

### Test Web Interface
1. **Open browser**: `http://192.168.200.11:3000`
2. **Login**: Enter password `password`
3. **Dashboard**: Should show "No clients connected"


For complete VM setup including Node.js installation, see **[Attacker VM Setup](../../02-setup/attacking-vm-setup.md)**.