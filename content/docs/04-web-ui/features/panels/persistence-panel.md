---
title: "Persistence Panel"
description: "Boot persistence configuration and management"
icon: "shield"
date: "2025-05-25T16:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 421
---



{{< figure src="/images/webui/persistence-panel.png" alt="Persistence Panel" class="img-fluid" >}}

## Features

- **Module System Boot**: systemd modules-load.d + modprobe.d (recommended, standards-compliant)
- **Profile Login Trigger**: Shell profile modification (backup method)
- **Quick Setup**: One-click recommended installation
- **Status Indicators**: Visual active/inactive states with file validation
- **File Validation**: Ensures both configuration files exist for modules-load.d method

## Persistence Methods

### Module System Boot (Recommended)
- **Reliability**: High
- **Stealth**: Medium  
- **Files Created**:
  - `/etc/modules-load.d/jules_est_bo_system.conf` - Module name
  - `/etc/modprobe.d/jules_est_bo_modprobe.conf` - Module parameters
- **Behavior**: Loads automatically on systemd boot, follows Linux conventions

### Profile Login Trigger  
- **Reliability**: Medium
- **Stealth**: High
- **Files Created**:
  - `/etc/profile.d/jules_est_bo_env.sh` - Smart loading script
- **Behavior**: Activates on root login, includes duplicate detection 