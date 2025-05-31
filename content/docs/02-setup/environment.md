---
title: "Host Environment Setup"
description: "Prepare your host system for running EpiRootkit VMs"
icon: "computer"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 201
---

# Host Environment Setup

> **Purpose:** Configure Ubuntu 24.10 host to run attacker and victim VMs. The actual EpiRootkit components run INSIDE the VMs.

## Setup Method

{{< tabs tabTotal="2" >}}

{{% tab tabName="Automated Setup (Recommended)" %}}

### One-Command Installation

Run the automated setup script to install all dependencies:

```bash
./scripts/install_dependencies.sh
```

**What it installs:**
- **QEMU/KVM**: Virtual machine hypervisor
- **libvirt**: VM management daemon and tools
- **Bridge utils**: Network bridge configuration
- **User permissions**: Adds you to libvirt group

{{% /tab %}}

{{% tab tabName="Manual Step-by-Step" %}}

### 1. Update System
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install VM Management Tools
```bash
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
```

### 3. Configure Libvirt
```bash
# Enable and start libvirt daemon
sudo systemctl enable --now libvirtd

# Add current user to libvirt group
sudo usermod -aG libvirt $USER

# Activate group membership (required before using VMs)
newgrp libvirt
```

### 4. Optional: Documentation Tools
```bash
# Only if you want to build docs locally
sudo apt install -y hugo
```

{{% /tab %}}

{{< /tabs >}}

## Verification

After completing either setup method, verify your installation:

```bash
# Check VM management tools
virsh list --all
qemu-system-x86_64 --version

# Verify KVM support
lsmod | grep kvm
# Should show: kvm_intel (or kvm_amd) and kvm

# Check hardware virtualization
egrep -c '(vmx|svm)' /proc/cpuinfo
# Should be >= 1 (if 0, enable in BIOS)

# Verify KVM device access
ls -la /dev/kvm
# Should be accessible by libvirt group
```

## Next Steps

1. **VM Setup**: [VM Installation & Verification](./vm-installation.md)
2. **Launch VMs**: Use `sudo ./scripts/run_vms.sh` 
3. **Inside VMs**: Install project components

## Architecture Overview

```
HOST (Ubuntu 24.10)
├── QEMU/KVM: VM hypervisor
├── libvirt: VM management
└── Bridge network: VM communication

ATTACKER VM (192.168.200.11)
├── Node.js: C2 server runtime  
├── attacking_program: C2 server
└── webui: React interface

VICTIM VM (192.168.200.10)
├── Development tools: gcc, headers
├── rootkit: Kernel module
└── Ubuntu 20.04: Target system
```

## Helper Scripts

- **`scripts/check_vms.sh`**: Verify VM disks and download links
- **`scripts/run_vms.sh`**: Launch VMs with networking
- **`scripts/install_dependencies.sh`**: Automated host setup
