---
title: "VM Installation & Verification"
description: "Set up and verify the attacker and victim VMs"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-12T00:00:00+01:00"
draft: false
toc: true
weight: 202
---

> Complete [Host Environment Setup](./environment.md) before proceeding.

Throughout this documentation, you will see two options:

1. **Pre-built Disks**: Ready-to-use VM images with everything pre-configured and pre-installed.
2. **Fresh Installation**: Manual setup process to create your own VM images.

The pre-built disks were created using the exact steps described in the manual installation sections.

So while we recommend using the pre-built disks, you can also create your own VMs by following the manual installation steps.

## Choose Setup Method

{{< tabs tabTotal="2" >}}

{{% tab tabName="Pre-built Disks (Recommended)" %}}

### Download Images
Download both QCOW2 images and place them in `/var/lib/libvirt/images/`:

| VM Disk          | Path               | Download URL                                           | Size      |
|------------------|--------------------|--------------------------------------------------------|-----------|
| Attacker VM      | `vm/attacker.qcow2`| https://drive.proton.me/urls/J20W6CD998#rB7b5oM6idQC   | 5.6 GB    |
| Victim VM        | `vm/victim.qcow2`  | https://drive.proton.me/urls/EGVVVF6YXW#THevlby2e62E   | 5.6 GB    |

```bash
# After downloading
cd ~/Downloads
sudo mv {attacker.qcow2,victim.qcow2} /var/lib/libvirt/images/
```

**What You Get:**
- **Attacker VM**: Ubuntu + project files + dependencies installed (tbh it's the same Ubuntu 20.04 LTS with kernel 5.4.0 from the victim because I'm lazy)
- **Victim VM**: Ubuntu 20.04 LTS with kernel 5.4.0 (Fresh install with only autologin user `victim` and password `jules`)

### Check VMs

```bash
./scripts/check_vms.sh
```


### Launch VMs
```bash
# run this command before running the vm
newgrp libvirt
# then this command to launch the vm
sudo scripts/run_vms.sh
```

{{% /tab %}}

{{% tab tabName="Build Disks Yourself" %}}
If you prefer to build the disks yourself because you don't trust me (which is fair tbh), follow these steps:

### Download Ubuntu ISO
- Proton Drive: https://drive.proton.me/urls/N5ZQN96C30#77rPStc8Fx2i   (2.5 GB)
- Ubuntu archive: https://releases.ubuntu.com/20.04/ubuntu-20.04-desktop-amd64.iso  (2.5 GB)

### Create Disks
```bash
sudo qemu-img create -f qcow2 /var/lib/libvirt/images/attacker.qcow2 10G
sudo qemu-img create -f qcow2 /var/lib/libvirt/images/victim.qcow2 10G
```

### Install Ubuntu (Attacker)
```bash
sudo qemu-system-x86_64 \
  -enable-kvm -machine accel=kvm -cpu host \
  -m 4096 -smp 4 \
  -name attacker-install \
  -drive file=/var/lib/libvirt/images/attacker.qcow2,format=qcow2,if=virtio,cache=writeback \
  -cdrom vm/ubuntu-20.04-desktop-amd64.iso -boot once=d \
  -netdev user,id=net0 -device virtio-net-pci,netdev=net0 \
  -graphics vnc
```

### Install Ubuntu (Victim)
```bash
sudo qemu-system-x86_64 \
  -enable-kvm -machine accel=kvm -cpu host \
  -m 4096 -smp 4 \
  -name victim-install \
  -drive file=/var/lib/libvirt/images/victim.qcow2,format=qcow2,if=virtio,cache=writeback \
  -cdrom vm/ubuntu-20.04-desktop-amd64.iso -boot once=d \
  -netdev user,id=net0 -device virtio-net-pci,netdev=net0 \
  -graphics vnc
```


> **Setup**: Create user `attacker`/`victim` with password `jules` for consistency.

## Verification

```bash
./scripts/check_vms.sh  # Verify disks and files
```

### Launch VMs
```bash
sudo scripts/run_vms.sh
```

{{% /tab %}}

{{< /tabs >}}



## Next Steps

Configure individual VMs:
- [Attacker VM Setup](./attacking-vm-setup.md)
- [Victim VM Setup](./victim-vm-setup.md)
