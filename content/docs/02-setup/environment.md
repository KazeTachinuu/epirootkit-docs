---
title: "Initial Setup & Installation"
description: "Prepare your host system from scratch for the EpiRootkit project."
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-12T00:00:00+01:00"
draft: false
toc: true
weight: 201
---

# Host Environment Setup (from a Blank System)

> **Assumption:** You are starting from a fresh Ubuntu 24.10 (amd64) install with no extra packages.

## 1. Update System
```bash
sudo apt update && sudo apt upgrade -y
```

## 2. Install Essential Packages
Install all required tools for virtualization, development, and Node.js:
```bash
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils build-essential linux-headers-$(uname -r) curl git nodejs npm
```

> **Tip:** If you get an error about missing kernel headers, ensure your kernel version matches the installed headers. Use `uname -r` and `apt search linux-headers` to check.

## 3. (Optional but Recommended) Install pnpm
`pnpm` is used for the Web UI. Install it globally:
```bash
sudo npm install -g pnpm
```

## 4. Enable and Start libvirt
```bash
sudo systemctl enable --now libvirtd
```

## 5. Add Your User to the `libvirt` Group
```bash
sudo usermod -aG libvirt $USER
# Log out and log back in, or run:
newgrp libvirt
```

## 6. Verify Virtualization Support
Check that KVM modules are loaded:
```bash
lsmod | grep kvm
```
You should see `kvm` and `kvm_intel` (or `kvm_amd`).

If not, check if virtualization is enabled in your BIOS:
```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
# output == 0 -> not activated
# output >= 1 -> activated
```
{{< alert context="warning" text="If virtualization is not enabled, reboot and enable it in your BIOS/UEFI settings." />}}

## 7. Verify Node.js, npm, and pnpm
```bash
node -v
npm -v
pnpm -v # (if installed)
```
All should print a version number. If not, check your installation steps above.

## 8. Review Helper Scripts
- `scripts/check_vms.sh`: Checks for VM disks and ISO, provides download URLs for missing items.
- `scripts/run_vms.sh`: Sets up a NAT network (`static200`) and launches both VMs.

## 9. Next Steps
- Proceed to [VM Installation & Verification](./vm-installation.md) to set up your virtual machines.

---

### How it Works
- **libvirt** (`virsh`) manages virtual networks and invokes QEMU.
- The `static200` network (see `scripts/static200.xml`) creates a NAT bridge `virbr200` and DHCP server with fixed MAC-to-IP entries.
- `scripts/run_vms.sh` uses `qemu-system-x86_64` with `-netdev bridge,br=virbr200` to attach VMs to the NAT network.
- Static IPs are assigned automatically by DHCP based on the VM's MAC address.
- QEMU windows use virtio drivers for disk and network for high performance.
