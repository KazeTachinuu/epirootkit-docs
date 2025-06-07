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



## Setup Method

{{< tabs tabTotal="2" >}}

{{% tab tabName="Automated (Recommended)" %}}

### One-Command Installation

```bash
./scripts/install_dependencies.sh
```

**Installs:**
- QEMU/KVM: Virtual machine hypervisor
- libvirt: VM management daemon and tools  
- Bridge utils: Network bridge configuration
- User permissions: Adds you to libvirt group

{{% /tab %}}

{{% tab tabName="Manual Step-by-Step" %}}

### Install Dependencies
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
```

### Configure Libvirt
```bash
sudo systemctl enable --now libvirtd
sudo usermod -aG libvirt $USER
newgrp libvirt
```

### Optional: Documentation Tools
```bash
# Only if you want to build docs locally
sudo apt install -y hugo
```

{{% /tab %}}

{{< /tabs >}}

## Verification

```bash
# Check VM tools
virsh list --all
qemu-system-x86_64 --version

# Verify KVM support
lsmod | grep kvm
egrep -c '(vmx|svm)' /proc/cpuinfo  # Should be >= 1
ls -la /dev/kvm                     # Should be accessible
```

## Next Steps

1. **VM Setup**: [VM Installation](./vm-installation.md)
2. **Launch VMs**: `sudo ./scripts/run_vms.sh`
3. **Configure**: Follow VM-specific setup guides

## Architecture

```
HOST (Ubuntu 24.10)
├── QEMU/KVM → VM hypervisor
├── libvirt → VM management
└── Bridge network → VM communication

ATTACKER VM (192.168.200.11)
├── C2 server → attacking_program
└── Web UI → React interface

VICTIM VM (192.168.200.10)
├── Target system → Ubuntu 20.04
└── Rootkit → epirootkit.ko
```

## Helper Scripts

- **`scripts/check_vms.sh`**: Verify VM disks and download links
- **`scripts/run_vms.sh`**: Launch VMs with networking
- **`scripts/install_dependencies.sh`**: Automated host setup
