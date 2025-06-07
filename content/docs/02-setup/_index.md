---
title: "Setup"
description: "Environment and Virtual Machine setup instructions"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-12T00:00:00+01:00"
draft: false
toc: true
weight: 200
---

## Overview

Set up your host environment and configure attacker/victim VMs for EpiRootkit deployment.

### Setup Guide

1. **[Host Environment]({{< relref "environment.md" >}})** - Install QEMU/KVM and libvirt
2. **[VM Installation]({{< relref "vm-installation.md" >}})** - Download or build VM disks  
3. **[Attacker VM]({{< relref "attacking-vm-setup.md" >}})** - Configure C2 server and web interface
4. **[Victim VM]({{< relref "victim-vm-setup.md" >}})** - Deploy and test rootkit
5. **[File Transfer]({{< relref "file-transfer.md" >}})** - Methods for host-to-VM file transfer

