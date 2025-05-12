---
title: "Victim VM Setup"
description: "Configure and use the victim VM to load EpiRootkit"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-12T00:00:00+01:00"
draft: false
toc: true
weight: 24
---

Follow these steps once the host environment is ready and the VMs have been created or downloaded.


1. Place the `victim.qcow2` disk in the `vm/` directory at the project root (verify via `./scripts/check_vms.sh` or see [VM Installation & Verification]({{< relref "vm-installation.md" >}})).

2. Launch the victim VM with:
   ```bash
   sudo ./scripts/run_vms.sh victim
   ```
   Or both VMs with:
   ```bash
   sudo ./scripts/run_vms.sh
   ```

3. On the victim VM:
   - The VM has auto-login enabled for the `victim` user (no password prompt).  
   - Its static IP is **192.168.200.10**.
   - The password is `jules`.



4. When finished, close the QEMU window or use **Ctrl+C** on the host script to shut down both VMs. 