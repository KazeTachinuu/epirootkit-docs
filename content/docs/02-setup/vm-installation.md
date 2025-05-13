---
title: "VM Installation & Verification"
description: "Quickly set up the attacker and victim VM disks"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-12T00:00:00+01:00"
draft: false
toc: true
weight: 22
---

{{< alert context="info" text="Run `./scripts/check_vms.sh` at any time to verify VM disks and ISO, and see download URLs for missing items." />}}

## VM Installation

{{< tabs tabTotal="2" >}}

{{% tab tabName="Recommended: Pre-built Disks" %}}

Download and place both QCOW2 images into the `vm/` directory at the project root:


| VM Disk          | Path               | Download URL                                           | Size      |
|------------------|--------------------|--------------------------------------------------------|-----------|
| Attacker VM      | `vm/attacker.qcow2`| https://drive.proton.me/urls/J20W6CD998#rB7b5oM6idQC   | 5.6 Go    |
| Victim VM        | `vm/victim.qcow2`  | https://drive.proton.me/urls/EGVVVF6YXW#THevlby2e62E   | 5.6 Go    |

{{< alert context="info" text="To be honest, these qcow2 images are almost identical. The only difference is that the rootkit files are pre-loaded on the 'attacker' disk. If you prefer, you can simply download the 'attacker' image and copy it into `vm/victim.qcow2` if needed." />}}

Once downloaded, launch both VMs:
```bash
sudo scripts/run_vms.sh
```
{{% /tab %}}

{{% tab tabName="Build Disks Yourself" %}}
If you prefer to build the disks yourself because you don't trust me (which is fair tbh), follow these steps:

1. Download the Ubuntu 20.04 Desktop ISO from Proton Drive or the Ubuntu archive:
   - Proton Drive: https://drive.proton.me/urls/N5ZQN96C30#77rPStc8Fx2i   **2.5 Go**
   - Ubuntu archive: https://releases.ubuntu.com/20.04/ubuntu-20.04-desktop-amd64.iso  **2.5 Go**

2. Create two blank QCOW2 disks (10 GB each):
   ```bash
   qemu-img create -f qcow2 vm/attacker.qcow2 10G
   qemu-img create -f qcow2 vm/victim.qcow2   10G
   ```
3. Install Ubuntu Desktop into each disk:

   **Attacker VM**
   ```bash
   qemu-system-x86_64 \
     -enable-kvm -machine accel=kvm -cpu host \
     -m 4096 -smp 4 \
     -name attacker-install \
     -drive file=vm/attacker.qcow2,format=qcow2,if=virtio,cache=writeback \
     -cdrom vm/ubuntu-20.04-desktop-amd64.iso -boot once=d \
     -netdev user,id=net0 -device virtio-net-pci,netdev=net0 \
     -vga virtio -display gtk
   ```

   **Victim VM**
   ```bash
   qemu-system-x86_64 \
     -enable-kvm -machine accel=kvm -cpu host \
     -m 4096 -smp 4 \
     -name victim-install \
     -drive file=vm/victim.qcow2,format=qcow2,if=virtio,cache=writeback \
     -cdrom vm/ubuntu-20.04-desktop-amd64.iso -boot once=d \
     -netdev user,id=net0 -device virtio-net-pci,netdev=net0 \
     -vga virtio -display gtk
   ```

After installation completes and the VMs shut down, your disks are ready. Launch them with:
```bash
sudo scripts/run_vms.sh
```
{{% /tab %}}

{{< /tabs >}}

## Understanding the `run_vms.sh` Script

The `run_vms.sh` helper automates setting up a private NAT network and launching two QEMU VMs (attacker & victim) on it. Here’s what happens and why:

1. **Libvirt network setup**  
   - Defines a network (`static200`) with a preconfigured XML (`static200.xml`).  
   - Destroys any existing definition, then recreates and starts it, enabling DHCP with static leases.

2. **VM image verification**  
   - Checks that `vm/attacker.qcow2` and `vm/victim.qcow2` exist before proceeding.

3. **Launching VMs**  
   - `launch_vm` function configures each VM with:
     • KVM acceleration and host CPU model for performance  
     • Memory and CPU count from `MEMORY`/`CPUS` env vars (defaults: 2048 MB, 2 CPUs)  
     • A bridged network interface on `virbr200` with a fixed MAC and IP  
     • GTK display.
   - By default, both VMs start; you can specify `attacker` or `victim` as arguments to launch individually.
