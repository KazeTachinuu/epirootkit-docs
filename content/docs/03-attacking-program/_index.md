---
title: "Attacking Program (C2 Server)"
weight: 30
type: "docs"
description: "Professional C2 server for EpiRootkit management with CLI interface, web dashboard, and comprehensive security features."
---

# Attacking Program (C2 Server)

Professional Command & Control server built with Node.js providing comprehensive management capabilities for EpiRootkit instances.

## Overview

The Attacking Program is a sophisticated C2 server that provides:

- **Interactive CLI Interface**: Vorpal.js-based command system
- **Web Dashboard**: REST API with Socket.IO real-time updates
- **Secure Authentication**: SHA-512 hashing with rate limiting
- **File Operations**: Upload/download capabilities with validation
- **Persistence Management**: Multiple installation methods
- **Stealth Configuration**: Interactive rootkit configuration
- **Connection Health**: Automatic monitoring and cleanup

## Quick Navigation

### 📖 [Overview](overview/)
Architecture, features, and technology stack overview.

### 🔧 [Installation](installation/)
Step-by-step setup guide with prerequisites and troubleshooting.

### 🎮 [Usage Guide](usage/)
Comprehensive workflows and operational scenarios.

### ⚙️ [Configuration](configuration/)
Server configuration options and customization.

### 🛡️ [Security](security/)
Security considerations, threat model, and best practices.

### 📋 [Command Reference](commands/)
Complete command documentation with examples.

## Key Features

### Client Management
- Real-time connection monitoring
- Authentication with session management
- Health checks and stale client detection
- User-friendly client aliases

### Remote Operations
- Shell command execution with output capture
- File upload/download with integrity checks
- System status and information gathering
- Interactive configuration management

### Security Features
- SHA-512 password authentication
- XOR encryption for traffic obfuscation
- Rate limiting and session timeouts
- Secure configuration management

### Persistence Management
- Multiple persistence mechanisms
- Interactive installation/removal
- Status monitoring and verification
- Method-specific configuration

---

Complete C2 functionality for professional rootkit management and security research. 