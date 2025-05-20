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

## TODO: Implement File Transfer Features

- [x] Define protocol for file upload/download (C2 and web UI stubbed)
- [ ] Implement file write/read in kernel (e.g., using `filp_open`, `kernel_write`, `kernel_read`) (WIP)
- [x] Add CLI/Web UI commands for upload/download (UI present, backend partial)
- [ ] Handle errors and edge cases (permissions, disk space, etc.)
- [ ] Test file transfer end-to-end (various file sizes, error cases)

_This section will be updated with documentation once features are implemented and tested._

