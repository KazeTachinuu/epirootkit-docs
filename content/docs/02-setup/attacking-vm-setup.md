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



## Prerequisites

1. **Host setup**: [Host Environment Setup]({{< relref "environment.md" >}})
2. **VM disk**: `attacker.qcow2` in `/var/lib/libvirt/images/`
3. **VM launched**: `sudo ./scripts/run_vms.sh`


## VM Access

- **IP**: 192.168.200.11 (static)
- **Credentials**: `attacker` / `jules` (auto-login)

## Setup Method

{{< tabs tabTotal="2" >}}

{{% tab tabName="Pre-built (Recommended)" %}}

### Quick Start

Everything is pre-configured. Build and run immediately:

```bash
# Inside attacker VM (auto-logged in)
cd epirootkit && ./deploy_c2.sh
# ✅ Builds rootkit automatically
# ✅ Builds dropper app
# ✅ C2 server: port 4444
# ✅ Web interface: port 3000
# ✅ Landing page dropper: port 8080
```

### Deploy Script Options

The `deploy_c2.sh` script supports selective component building:

```bash
# Build everything (default)
./deploy_c2.sh

# Build only specific components
./deploy_c2.sh -r                # Build only rootkit (epirootkit.ko)
./deploy_c2.sh --c2              # Build only C2 server and web interface
./deploy_c2.sh -d                # Build only dropper and landing page

# Build without starting servers
./deploy_c2.sh --no-start        # Build all components but don't start servers
./deploy_c2.sh --c2 --no-start   # Build C2 without starting servers

# Combine options
./deploy_c2.sh -r -d             # Build rootkit and dropper only

# Get help
./deploy_c2.sh -h                # Show all available options
```

**Available Options:**
- `-r, --rootkit`: Build only rootkit kernel module
- `--c2`: Build only C2 server and web interface
- `-d, --dropper`: Build only dropper and start landing page
- `--no-start`: Build components but don't start servers
- `-h, --help`: Show help message

{{% /tab %}}

{{% tab tabName="Fresh VM Setup" %}}

### Prerequisites on the VM

The setup requires the `setup_attacker.sh` script. You have two options to obtain it:

**Option 1: Get it from the host with python (recommended)**

```bash
# In the scripts/ directory on the host
python3 -m http.server 8080
```
Then on the attacker VM:

```bash
wget http://192.168.200.1:8080/setup_attacker.sh
chmod +x setup_attacker.sh
```

**Option 2: Download from GitHub Gist**

```bash
wget https://gist.githubusercontent.com/KazeTachinuu/397da3d739384de9e592a2e6f26b7cc0/raw/0895738fbf9dccc16dd6fe1139eb206ec9024076/setup_attacker.sh
chmod +x setup_attacker.sh
```

### Setup

**1. Install Dependencies (inside VM):**
```bash
./setup_attacker.sh
```
This installs:

- Node.js 18.x LTS
- Build tools (gcc, make, linux-headers-$(uname -r), python3)
- SSH server

**2. Deploy Project (from host):**
```bash
./scripts/deploy_project.sh
# ✅ Transfers all project files via SSH
```

**3. Auto Build and Start (inside VM):**
```bash
cd epirootkit && ./deploy_c2.sh
# ✅ Builds rootkit automatically
# ✅ Installs C2 dependencies  
# ✅ Starts C2 server + Web UI
```

**Note:** The `deploy_c2.sh` script supports the same selective building options as shown in the Pre-built tab.


{{% /tab %}}

{{< /tabs >}}

## Access URLs

Once deployed, access the C2 infrastructure at:
- **Web Interface**: `http://192.168.200.11:3000` (from host) or `http://localhost:3000` (from attacker VM)
- **Landing Page**: `http://192.168.200.11:8080` (from host) or `http://localhost:8080` (from attacker VM)

## Configuration

### Attacker VM Role
- **Builds rootkit**: Compiles `epirootkit.ko` for victim
- **C2 server**: Port 4444 (client connections)
- **Web interface**: Port 3000 (operator access)
- **Landing page dropper**: Port 8080 (dropper access)

### Network
- **Attacker IP**: `192.168.200.11`
- **C2 port**: `4444` (default)
- **Web UI**: `http://192.168.200.11:3000`
- **Landing page dropper**: `http://192.168.200.11:8080`

## Next Steps

1. **[Victim Setup]({{< relref "victim-vm-setup.md" >}})**: Deploy rootkit
2. **Monitor**: Watch for client connections
3. **Control**: Use CLI or Web UI
