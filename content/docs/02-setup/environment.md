---
title: "Initial Setup & Installation"
description: "Prerequisites on the EPITA laptop"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-12T00:00:00+01:00"
draft: false
toc: true
weight: 21
---

To develop and test the rootkit, you need to prepare your host (EPITA laptop) with the following:

1. Operating System
   - Ubuntu 24.10 (amd64)

2. Install virtualization and helper tools
   ```bash
   sudo apt update
   sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
   ```

3. Enable and start the libvirt service
   ```bash
   sudo systemctl enable --now libvirtd
   ```

4. Add your user to the `libvirt` group (re-login or `newgrp libvirt` afterward)
   ```bash
   sudo usermod -aG libvirt $USER
   ```

5. Verify that KVM modules are loaded
   ```bash
   lsmod | grep kvm
   ```
   You should see `kvm` and `kvm_intel` (or `kvm_amd`) in the output.


6. Review the helper scripts:
   - `scripts/check_vms.sh`: checks if the VM disks and ISO are present and provides download URLs for missing items.
   - `scripts/run_vms.sh`: defines and starts a NAT network (static200) with static DHCP leases, then launches both VMs attached to that network.

With these steps complete, your host is ready to build, launch, and manage the attacking and victim VMs.

### How it works
- libvirt (`virsh`) manages virtual networks and invokes QEMU under the hood.
- The `static200` network (defined in `scripts/static200.xml`) creates a NAT bridge `virbr200` and DHCP server with fixed MAC-to-IP entries.
- `scripts/run_vms.sh` uses `qemu-system-x86_64` with `-netdev bridge,br=virbr200` to attach VMs to the NAT network.
- Static IPs are assigned automatically by DHCP based on the VM's MAC address.
- QEMU windows use virtio drivers for disk and network for high performance. 