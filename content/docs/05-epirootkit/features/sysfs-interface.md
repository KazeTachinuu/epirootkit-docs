---
title: "Sysfs Interface"
description: "Linux permissions-based feature control"
icon: "settings"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 513
---

# Sysfs Interface

Simple two-step interface using group file permissions to control rootkit features.

## How It Works

Like git, changes require two steps:
1. **Stage change** (`chmod`) - like `git commit`
2. **Apply change** (`cat`/`echo`) - like `git push`

```bash
chmod 670 /sys/kernel/epirootkit/control  # Stage features (group rwx)
cat /sys/kernel/epirootkit/control        # Apply changes
```

## Permission Mapping

Group permission bits control features:
- **Bit 1** (group execute): Module hiding
- **Bit 2** (group write): File hiding  
- **Bit 4** (group read): Line hiding

## Usage Examples

```bash
# Enable all features
chmod 670 /sys/kernel/epirootkit/control  # Stage: group rwx (7)
cat /sys/kernel/epirootkit/control        # Apply

# Enable only module hiding  
chmod 610 /sys/kernel/epirootkit/control  # Stage: group --x (1)
echo "apply" > /sys/kernel/epirootkit/control  # Apply

# Disable everything
chmod 600 /sys/kernel/epirootkit/control  # Stage: group --- (0)
cat /sys/kernel/epirootkit/control        # Apply
```

## Feature Combinations

| chmod | Group | Module | Files | Lines | Description |
|:-----:|:-----:|:------:|:-----:|:-----:|:------------|
| 600   | ---   | ❌     | ❌    | ❌    | All disabled |
| 610   | --x   | ✅     | ❌    | ❌    | Module only |
| 620   | -w-   | ❌     | ✅    | ❌    | Files only |
| 630   | -wx   | ✅     | ✅    | ❌    | Module + files |
| 640   | r--   | ❌     | ❌    | ✅    | Lines only |
| 650   | r-x   | ✅     | ❌    | ✅    | Module + lines |
| 660   | rw-   | ❌     | ✅    | ✅    | Files + lines |
| 670   | rwx   | ✅     | ✅    | ✅    | All enabled |

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