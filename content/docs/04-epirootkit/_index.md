---
title: "EpiRootkit"
description: "Linux kernel rootkit implementation for kernel 5.4.0"
icon: "shield"
date: "2025-05-25T16:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 40
---

# EpiRootkit Documentation

Linux kernel rootkit for Ubuntu 20.04 LTS (kernel 5.4.0).

## Quick Navigation

### Getting Started
- **[Overview](./overview.md)**: What we built and how it works
- **[Build & Deployment](./deployment.md)**: How to compile and load the rootkit
- **[Connection & Authentication](./connection-authentication.md)**: Network setup and authentication

### Core Features
- **[Command Execution](./features/command-execution.md)**: Remote shell command execution
- **[File Transfer](./features/file-transfer.md)**: Upload/download files
- **[Stealth & Hiding](./features/hiding.md)**: Module and file hiding
- **[Persistence](./features/persistence.md)**: Boot survival mechanisms

### Additional Information
- **[Configuration Justifications](./configuration-justifications.md)**: Why Ubuntu 20.04 and kernel 5.4.0

## What We Actually Implemented

✅ **C2 Communication**: TCP connection with automatic reconnection  
✅ **Authentication**: SHA-512 password verification with rate limiting  
✅ **Command Execution**: Remote shell commands with output capture  
✅ **File Transfer**: Upload/download with 10MB limit  
✅ **Module Hiding**: Hide from `lsmod` and `/proc/modules`  
✅ **File Hiding**: Hide files by prefix from directory listings  
✅ **Persistence**: Three mechanisms (systemd, cron, shell profiles)  
✅ **Keepalive System**: 60-second ping/pong with reconnection  

❌ **Encryption**: Currently disabled (XOR cipher implemented but not active)

---

Educational kernel rootkit demonstrating modern techniques on Linux 5.4.0.
