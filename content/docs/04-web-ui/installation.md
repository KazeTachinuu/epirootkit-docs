---
title: "Installation & Setup"
description: "How to install and launch the Web UI"
icon: "download"
date: "2025-05-25T16:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 402
---

# Web UI Installation

The Web UI is integrated with the C2 server and launches automatically.

## Quick Setup

```bash
# From project root
./deploy_c2.sh
```

This starts both the C2 server and Web UI simultaneously.

## Access

- **URL**: `http://localhost:3000`
- **Default Password**: `password`
- **Features**: All C2 functionality via web interface

## Manual Installation

If you need to install dependencies manually:

```bash
cd attacking_program
pnpm install
pnpm start
```

For detailed configuration options, see [C2 Server Installation](../03-attacking-program/installation.md).
