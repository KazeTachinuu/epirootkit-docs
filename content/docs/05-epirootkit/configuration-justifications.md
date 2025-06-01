---
title: "Configuration Justifications"
description: "Why Ubuntu 20.04 LTS with kernel 5.4.0-26-generic"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-07T00:44:31+01:00"
draft: false
toc: true
weight: 500
---


## Target Environment

- **OS**: Ubuntu 20.04 LTS (Focal Fossa)
- **Kernel**: 5.4.0-26-generic



### Why Ubuntu 20.04 LTS?

**Primary Reason**: Ubuntu 20.04 ships with kernel 5.4.0-26-generic pre-compiled and ready to use.

**Additional Benefits**:
- Widespread deployment
- 5-year LTS support lifecycle (until 31 May 2025... I'm so sad)

### Why Kernel 5.4.0 Specifically?

Kernel 5.4.0 sits in a **unique security window** that makes rootkit development straightforward:

#### The Security Timeline
```
5.3.x  ←  No lockdown mode
5.4.0  ←  Lockdown introduced but disabled by default  ← WE ARE HERE
5.7.x  ←  kallsyms_lookup_name() unexported
5.8.x  ←  UEFI Secure Boot enables lockdown automatically
```

**Result**: 5.4.0 provides modern kernel features without the security restrictions that complicate rootkit development.

## Technical Implementation

### Our kretprobe Approach
```c
static struct kretprobe getdents_probe = {
    .kp.symbol_name = "ksys_getdents64",
    .handler = hide_files_ret_handler,
    .maxactive = 20,
};

register_kretprobe(&getdents_probe);  // Works on 5.4.0
```

**Why this works well on 5.4.0**:
- Lockdown mode inactive by default
- kretprobe infrastructure mature and stable
- `ksys_getdents64` symbol readily available
- No module signature enforcement required

## What Changes in Newer Kernels?

### Kernel 5.7+ Restrictions
```c
// This symbol becomes unexported in 5.7+
kallsyms_lookup_name("symbol_name");  // No longer available
```

### Kernel 5.8+ Lockdown
```bash
# Automatic lockdown with UEFI Secure Boot
cat /sys/kernel/security/lockdown
none [integrity] confidentiality

# Blocks kretprobe registration
register_kretprobe(&probe);  // Returns -EPERM
```

### Why Not Higher Kernel Versions?

While our implementation is technically compatible with kernel 5.6+, it would require:

```bash
# Boot parameter modification required
module.sig_enforce=0
```

**Our Philosophy**: We chose Ubuntu 20.04 LTS with kernel 5.4.0 because it provides a practical deployment environment without requiring boot parameter modifications or security bypasses.

{{< alert context="info" text="And to be honest I was happy that we could do a rootkit on a LTS version of Ubuntu. So I stick to that. (Even though starting May 2025, Ubuntu 20.04 LTS is no longer supported...)" />}}
