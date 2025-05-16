---
title: "Remote Exec & File Transfer"
description: "Run commands, upload & download files over our encrypted channel"
icon: "cloud_upload"
date: "2025-05-11T18:35:00+01:00"
lastmod: "2025-05-11T18:35:00+01:00"
draft: false
toc: true
weight: 54
---

# 1. Remote Command Execution

**What we did**  
Allow arbitrary program execution via C2.

**How it works**  
- Receive a packet:  
  ```
  EXEC <len>\n<payload>
  ```
- Use `call_usermodehelper(argv, envp, UMH_WAIT_PROC)` to spawn the process.
- Capture its `stdout`/`stderr` via kernel pipes and send back encrypted.

---

# 2. File Upload

**What we did**  
Accept files from the attacker.

**How it works**  
- Receive `UPLOAD <path> <len>\n<data>`.
- `filp_open("/usr/share/epirootkit/<path>", O_CREAT|O_WRONLY, 0600)`.
- `kernel_write()` the data.
- Our `getdents()` hook keeps `/usr/share/epirootkit` hidden.

---

# 3. File Download

**What we did**  
Send files to the attacker.

**How it works**  
- Receive `DOWNLOAD <path>`.
- `filp_open()` + `kernel_read()` in chunks.
- Stream each chunk over the encrypted C2 link.
```