---
title: "Host to VM File Transfer"
description: "Best practices for transferring files from host to QEMU VMs"
icon: "folder"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 205
---

# Host to VM File Transfer

Guide for transferring files from the host system to QEMU virtual machines.

## Transfer Methods

{{< tabs tabTotal="4" >}}

{{% tab tabName="Automated Script (Recommended)" %}}

### One-Command Transfer

For attacker VM setup, use the automated deployment script:

```bash
# From host project directory
./scripts/deploy_to_attacker.sh

# ✅ Transfers entire project via SSH/SCP
# ✅ Runs setup script remotely
# ✅ Installs all dependencies
# 🎉 Ready to build and start
```

### What It Does
1. **Checks connectivity**: VM reachable via SSH
2. **Transfers files**: Complete project to `~/epirootkit`
3. **Runs setup**: Executes dependency installation
4. **Verifies**: All components ready

### Requirements
- VM running with SSH enabled
- Network connectivity between host and VM
- VM credentials: `attacker@192.168.200.11` (password: `jules`)

{{% /tab %}}

{{% tab tabName="Manual SCP/SSH" %}}

### Enable SSH in VM
```bash
# Inside the VM
sudo apt update && sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```

### Transfer Files
```bash
# From host to VM
scp -r /path/to/epirootkit attacker@192.168.200.11:~/
scp -r /path/to/epirootkit victim@192.168.200.10:~/

# Transfer specific files
scp deploy_rootkit.sh epirootkit.ko victim@192.168.200.10:~/
```

{{% /tab %}}

{{% tab tabName="HTTP Server" %}}

### Setup Server on Host
```bash
# From project directory
cd /path/to/epirootkit
python3 -m http.server 8080
```

### Download in VM
```bash
# Inside VM
wget http://192.168.200.1:8080/filename

# Download entire project
wget -r -np -nH --cut-dirs=1 http://192.168.200.1:8080/
```


{{% /tab %}}

{{% tab tabName="Other Methods" %}}

### Shared Folders (Development)
Add to QEMU launch:
```bash
-virtfs local,path=/path/to/host/folder,mount_tag=shared,security_model=passthrough
```

Mount in VM:
```bash
sudo mkdir -p /mnt/shared
sudo mount -t 9p -o trans=virtio shared /mnt/shared
cp -r /mnt/shared/epirootkit ~/
```


{{% /tab %}}

{{< /tabs >}}

