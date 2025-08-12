---
title: "Project README"
description: "Concise overview of EpiRootkit and its documentation"
icon: "info"
date: "2025-05-25T16:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: false
weight: 100
---

## EpiRootkit in short

EpiRootkit is a Linux kernel rootkit targeting Ubuntu 20.04 (kernel 5.4) with a Command-and-Control (C2) backend and a Web UI. This documentation site explains how to set up, use, and understand the system.

- **Kernel Module (EpiRootkit)**: remote command execution, file transfer, authentication, XOR-encrypted C2 traffic, DNS resolution, stealth features (module and file hiding), and persistence.
- **C2 Backend**: manages connected clients and command routing.
- **Web UI**: graphical interface for monitoring clients and performing actions.

## Start here

- **Environment setup**: {{< relref "../02-setup" >}}
- **C2 backend**: {{< relref "../03-attacking-program" >}}
- **Web UI**: {{< relref "../04-web-ui" >}}
- **Rootkit**: {{< relref "../05-epirootkit" >}}

## Key sections

- **Rootkit features**: {{< relref "../05-epirootkit/features" >}}
- **Web UI features**: {{< relref "../04-web-ui/features" >}}
- **Web UI panels**: {{< relref "../04-web-ui/features/panels" >}}

## Repository

Documentation source: [GitHub](https://github.com/kazetachinuu/epirootkit-docs)

Team: Tux Fan Club 🐧