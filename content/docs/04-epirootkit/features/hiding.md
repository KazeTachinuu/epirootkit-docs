---
title: "Stealth Hooks & daniel.* Commands"
description: "Hiding files, modules, PIDs, plus our rmdir() command channel"
icon: "visibility_off"
date: "2025-05-11T18:25:00+01:00"
lastmod: "2025-05-11T18:25:00+01:00"
draft: false
toc: true
weight: 30
---

# 1. File & Directory Hiding

**What we did**  
We hook `getdents()`/`getdents64()`.

**How it works**  
1. Call the original syscall to fill a `dirent` buffer.
2. Traverse entries; if `d_name` starts with any prefix in  
   ```c
   hide_prefixes[] = { "jules_est_bo_", "memfd:" };
   ```
   we drop that entry by `memmove()`.
3. Return the new byte count.

---

# 2. Module Hiding

**What we did**  
We hide our LKM from `/proc/modules` and `lsmod`.

**How it works**  
We hook the procfs `iterate` method for `/proc/modules` and `/sys/modules`, filtering out `"epirootkit"` entries.

---

# 3. PID Hiding

**What we did**  
Allow dynamic hiding of arbitrary PIDs.

**How it works**  
- Maintain a `hidden_pids` list in the kernel.
- In our `getdents()` hook on `/proc`, skip entries matching that list.
- Add to the list via `rmdir("daniel.k.<pid>")`.

---

# 4. C2 & Commands via `rmdir()`

**What we did**  
Use `rmdir()` as our stealth control channel.

<table>
  <tr><th>Command</th><th>Action</th></tr>
  <tr><td>`daniel.0`</td><td>Privilege escalation</td></tr>
  <tr><td>`daniel.c`</td><td>Dump in-kernel config</td></tr>
  <tr><td>`daniel.v`</td><td>Report version</td></tr>
  <tr><td>`daniel.k.<pid>`</td><td>Hide PID `<pid>`</td></tr>
</table>

**How it works**  
- Hook `rmdir(const char __user *path,…)`.
- Copy `path` into kernel space.
- If it matches `^daniel\.` we run the command and return `-ENOENT`.
```
