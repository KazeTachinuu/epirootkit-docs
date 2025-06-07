---
title: "Host to VM File Transfer"
description: "Methods for transferring files from host to QEMU VMs"
icon: "folder"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 205
---



## Transfer Methods

{{< tabs tabTotal="4" >}}

{{% tab tabName="Automated (Recommended)" %}}

### One-Command Transfer

For attacker VM setup:

```bash
# From host project directory
./scripts/deploy_project.sh

# ✅ Transfers entire project via SSH/SCP
# ✅ Runs setup script remotely
# ✅ Installs dependencies
# 🎉 Ready to build and start
```

**Requirements:**
- VM running with SSH enabled
- Network connectivity
- VM credentials: `attacker@192.168.200.11` (password: `jules`)

{{% /tab %}}

{{% tab tabName="SSH/SCP" %}}

### Enable SSH
```bash
# Inside VM
sudo apt update && sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```

### Transfer Files
```bash
# Project transfer
scp -r /path/to/epirootkit attacker@192.168.200.11:~/

# Specific files
scp deploy_rootkit.sh epirootkit.ko victim@192.168.200.10:~/
```

{{% /tab %}}

{{% tab tabName="HTTP Server" %}}

### Host Server
```bash
# From project directory
cd /path/to/epirootkit
python3 -m http.server 8080
```

### VM Download
```bash
# Single file
wget http://192.168.200.1:8080/filename

# Multiple files
wget http://192.168.200.1:8080/{file1,file2,file3}
```

{{% /tab %}}

{{% tab tabName="Shared Folders" %}}

### QEMU Setup
```bash
# Add to VM launch
-virtfs local,path=/host/folder,mount_tag=shared,security_model=passthrough
```

### VM Mount
```bash
sudo mkdir -p /mnt/shared
sudo mount -t 9p -o trans=virtio shared /mnt/shared
cp -r /mnt/shared/epirootkit ~/
```

{{% /tab %}}

{{< /tabs >}}

