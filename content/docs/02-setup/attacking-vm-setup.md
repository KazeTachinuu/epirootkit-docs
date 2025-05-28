---
title: "Attacking VM Setup"
description: "Configure and use the attacking VM"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-12T00:00:00+01:00"
draft: false
toc: true
weight: 203
---

Follow these steps once the host environment is ready and the VMs have been created or downloaded.


## Usage

1. Ensure the `attacker.qcow2` disk is in the `vm/` directory (verify via `./scripts/check_vms.sh` or see [VM Installation & Verification]({{< relref "vm-installation.md" >}})).
2. Launch the attacking VM with:
   ```bash
   sudo ./scripts/run_vms.sh attacker
   ```
   Or both VMs with:
   ```bash
   sudo ./scripts/run_vms.sh
   ```
3. On the attacking VM:
   - Auto-login is enabled for user `attacker` (no password prompt).
   - The static IP is **192.168.200.11**.
   - The password is `jules`.

For more details on the network configuration and helper scripts, see [Initial Setup & Installation]({{< relref "environment.md" >}}#how-it-works). 