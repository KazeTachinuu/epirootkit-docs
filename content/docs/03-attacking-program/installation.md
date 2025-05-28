---
title: "Installation"
description: "Step-by-step installation and setup guide"
icon: "download"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 302
---

# Installation Guide

Complete setup instructions for the EpiRootkit C2 server.

## Prerequisites

<<<<<<< HEAD
*   **Node.js & pnpm**: Ensure Node.js (e.g., LTS v18.x+) and pnpm are installed.
    ```bash
    sudo apt update && sudo apt install nodejs npm
    npm install -g pnpm
    ```
=======
>>>>>>> d8205ef9e1984d8455d84e1cada77692ef2857c0

### Verify Prerequisites
```bash
# Check Node.js version
node --version

# Check pnpm installation
pnpm --version

<<<<<<< HEAD
2.  Install Dependencies:
    ```bash
    pnpm install
    ```
=======
# Install pnpm if not available
npm install -g pnpm
```
>>>>>>> d8205ef9e1984d8455d84e1cada77692ef2857c0

## Installation Steps

<<<<<<< HEAD
Required directories (`uploads/`, `downloads/`, `logs/`) are created automatically.

4.  **Start the C2 server and Web UI:**
    ```bash
    pnpm start
    ```
    This will launch both the C2 server and the Web UI. By default, the Web UI is available at [http://localhost:3000](http://localhost:3000).
=======
### 1. Navigate to Project Directory
```bash
cd attacking_program
```

### 2. Install Dependencies
```bash
# Using pnpm (recommended)
pnpm install

# Or using npm
npm install
```


## Configuration

### 1. Review Configuration File
```bash
cat config.env
```

**Default Configuration:**
```bash
# EpiRootkit C2 Server Configuration
C2_PORT=4444
WEB_PORT=3000

# Authentication Configuration
PASSWORD_HASH=b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86

# Encryption Configuration
ENCRYPTION_KEY=0123456789abcdef0123456789abcdef
ENABLE_ENCRYPTION=true
```


## First Run

### 1. Start the C2 Server
```bash
pnpm start
```

**Expected Output:**
```
┌─ C2 Server Interface • Initializing...
└─ Initializing...

┌─ Setup
  • [2025-05-25 16:00:00] Config: C2_PORT=4444, WEB_PORT=3000
└─

┌─ Server Status
  • [2025-05-25 16:00:00] Server started on port 4444
└─

  • Web interface started on port 3000

c2-server$ 
```


### 3. Test CLI Interface
```bash
# In the C2 server prompt
c2-server$ help

# List clients (should be empty initially)
c2-server$ ls
# No clients connected
```


## Next Steps

1. **Connect a Rootkit**: Load the EpiRootkit module on a victim machine
2. **Authenticate**: Use `auth Client-1 password` to establish secure connection
3. **Execute Commands**: Start with `exec Client-1 whoami` to test functionality
4. **Explore Features**: Try file operations, persistence management, and configuration
>>>>>>> d8205ef9e1984d8455d84e1cada77692ef2857c0
