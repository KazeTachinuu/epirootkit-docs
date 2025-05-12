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

{{< tabs tabTotal="2" >}}

{{% tab tabName="Recommended: Pre-built Disks" %}}

Download and place both QCOW2 images into the `vm/` directory at the project root:


| VM Disk          | Path               | Download URL                                           |
|------------------|--------------------|--------------------------------------------------------|
| Attacker VM      | `vm/attacker.qcow2`| https://drive.proton.me/urls/J20W6CD998#rB7b5oM6idQC    |
| Victim VM        | `vm/victim.qcow2`  | https://drive.proton.me/urls/EGVVVF6YXW#THevlby2e62E    |


Once downloaded, launch both VMs:
```bash
sudo scripts/run_vms.sh
```
{{% /tab %}}

{{% tab tabName="Build Disks Yourself" %}}
If you prefer to build the disks yourself because you don't trust me (which is fair tbh), follow these steps:

1. Download the Ubuntu 20.04 Desktop ISO from Proton Drive or the Ubuntu archive:
   - Proton Drive: https://drive.proton.me/urls/N5ZQN96C30#77rPStc8Fx2i
   - Ubuntu archive: https://releases.ubuntu.com/20.04/ubuntu-20.04-desktop-amd64.iso

2. Create two blank QCOW2 disks (20 GB each):
   ```bash
   qemu-img create -f qcow2 vm/attacker.qcow2 20G
   qemu-img create -f qcow2 vm/victim.qcow2   20G
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