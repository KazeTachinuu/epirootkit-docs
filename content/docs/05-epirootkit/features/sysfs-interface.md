---
title: "Sysfs Interface"
description: "Simple permission-based feature control"
icon: "settings"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 513
---

# Sysfs Interface

Simple two-step interface using file permissions to control rootkit features.

## How It Works

Like git, changes require two steps:
1. **Stage change** (`chmod`) - like `git commit`
2. **Apply change** (`cat`/`echo`) - like `git push`

```bash
chmod 607 /sys/kernel/epirootkit/control  # Stage features (1+2+4=7)
cat /sys/kernel/epirootkit/control        # Apply changes
```

## Permission Mapping

Last 3 permission bits control features:
- **Bit 1** (execute): Module hiding
- **Bit 2** (write): File hiding  
- **Bit 4** (read): Line hiding

## Usage Examples

```bash
# Enable all features
chmod 607 /sys/kernel/epirootkit/control  # Stage: all (7)
cat /sys/kernel/epirootkit/control        # Apply

# Enable only module hiding  
chmod 601 /sys/kernel/epirootkit/control  # Stage: module (1)
echo "apply" > /sys/kernel/epirootkit/control  # Apply

# Disable everything
chmod 600 /sys/kernel/epirootkit/control  # Stage: none (0)
cat /sys/kernel/epirootkit/control        # Apply
```

## Feature Combinations

| chmod | Bits | Lines | Files | Module | Description |
|:-----:|:----:|:-----:|:-----:|:------:|:------------|
| ---   | 0    | ❌    | ❌    | ❌     | All disabled |
| --x   | 1    | ❌    | ❌    | ✅     | Module only |
| -w-   | 2    | ❌    | ✅    | ❌     | Files only |
| -wx   | 3    | ❌    | ✅    | ✅     | Module + files |
| r--   | 4    | ✅    | ❌    | ❌     | Lines only |
| r-x   | 5    | ✅    | ❌    | ✅     | Module + lines |
| rw-   | 6    | ✅    | ✅    | ❌     | Files + lines |
| rwx   | 7    | ✅    | ✅    | ✅     | All enabled |

## Status Output

```bash
cat /sys/kernel/epirootkit/control
```

**Example:**
```
module_hidden=yes
file_hiding=yes  
line_hiding=yes
```

## File Location

```
/sys/kernel/epirootkit/control
```

Two-step design ensures explicit control over when rootkit features activate. 