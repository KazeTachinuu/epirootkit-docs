---
title: "VM Installation & Verification"
description: "Set up and verify the attacker and victim VMs from scratch."
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-12T00:00:00+01:00"
draft: false
toc: true
weight: 22
---

# Virtual Machine Installation & Verification

> **You must have completed [Initial Setup & Installation](./environment.md) before proceeding.**

## 1. Choose Your VM Setup Method

{{< tabs tabTotal="2" >}}

{{% tab tabName="Recommended: Pre-built Disks" %}}

### Download Pre-built Images
Download both QCOW2 images and place them in the `vm/` directory at the project root:

| VM Disk          | Path               | Download URL                                           | Size      |
|------------------|--------------------|--------------------------------------------------------|-----------|
| Attacker VM      | `vm/attacker.qcow2`| https://drive.proton.me/urls/J20W6CD998#rB7b5oM6idQC   | 5.6 GB    |
| Victim VM        | `vm/victim.qcow2`  | https://drive.proton.me/urls/EGVVVF6YXW#THevlby2e62E   | 5.6 GB    |

> **Tip:** The images are nearly identical. You can copy `attacker.qcow2` to `victim.qcow2` if that's more convenient.



### Launch the VMs
```bash
sudo scripts/run_vms.sh
```

{{% /tab %}}

{{% tab tabName="Build Disks Yourself" %}}
If you prefer to build the disks yourself because you don't trust me (which is fair tbh), follow these steps:

### Download Ubuntu ISO
- Proton Drive: https://drive.proton.me/urls/N5ZQN96C30#77rPStc8Fx2i   (2.5 GB)
- Ubuntu archive: https://releases.ubuntu.com/20.04/ubuntu-20.04-desktop-amd64.iso  (2.5 GB)

### Create Blank QCOW2 Disks
```bash
qemu-img create -f qcow2 vm/attacker.qcow2 10G
qemu-img create -f qcow2 vm/victim.qcow2   10G
```

### Install Ubuntu Desktop into Each Disk
#### Attacker VM
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
#### Victim VM
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

> **Tip:** During installation, set up a user named `attacker` (for the attacker VM) and `victim` (for the victim VM) with password `jules` for consistency with the rest of the docs.

### After Installation
- Shut down the VM when done.
- The disks are now ready for use.

### Launch the VMs
```bash
sudo scripts/run_vms.sh
```

{{% /tab %}}

{{< /tabs >}}

## 2. Verifying Your VMs
- Run `./scripts/check_vms.sh` to verify the presence of disks and ISO files.
- If you see errors, check file paths and permissions.

## 3. Copying Files Into VMs
To transfer files (e.g., your project) into a VM, use one of:
- `scp` (requires SSH server in the VM)
- Shared folders (with QEMU/virtio)
- Cloud storage (e.g., upload to Google Drive, download in VM)


## 4. Troubleshooting
- If VMs do not start, check virtualization is enabled and your user is in the `libvirt` and `kvm` groups.
- If you see network errors, ensure the `static200` network is up (`virsh net-list --all`).
- For display issues, try `-display sdl` or `-display gtk`.

## 5. Next Steps
- Continue to [Attacking VM Setup](./attacking-vm-setup.md) and [Victim VM Setup](./victim-vm-setup.md) for per-VM configuration and usage.
