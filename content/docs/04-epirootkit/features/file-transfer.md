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

- [ ] Design and implement secure file upload from C2 to rootkit
    - [ ] Define protocol for file upload (message format, chunking, etc.)
    - [ ] Implement file write in kernel (e.g., using `filp_open`, `kernel_write`)
    - [ ] Handle errors and edge cases (permissions, disk space, etc.)
- [ ] Design and implement secure file download from rootkit to C2
    - [ ] Define protocol for file download (request, chunking, etc.)
    - [ ] Implement file read in kernel (e.g., using `filp_open`, `kernel_read`)
    - [ ] Stream file data securely to C2
- [ ] Integrate file transfer with C2 server and client
    - [ ] Add CLI/Web UI commands for upload/download
    - [ ] Display transfer status and errors in UI
- [ ] Test file transfer end-to-end (various file sizes, error cases)

_This section will be updated with documentation once features are implemented and tested._

