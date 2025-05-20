---
title: "Fileless Dropper & Rootkit Loader"
description: "Stages 1 & 2: in-memory payload staging and LKM insertion"
icon: "cloud_download"
date: "2025-05-11T18:15:00+01:00"
lastmod: "2025-05-11T18:15:00+01:00"
draft: false
toc: true
weight: 55
---

## TODO: Implement Fileless Dropper & Loader

- [ ] Implement in-memory ELF staging using `memfd_create`
- [ ] Develop loader to extract and decompress kernel image
- [ ] Implement symbol resolution and LKM insertion
- [ ] Add secure cleanup of temporary files and processes
- [ ] Integrate dropper/loader with C2 workflow
- [ ] Test on supported kernels and document limitations

_This section will be updated with documentation once features are implemented and tested._

