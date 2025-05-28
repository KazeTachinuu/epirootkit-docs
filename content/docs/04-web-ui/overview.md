---
title: "Overview"
description: "Modern web interface for EpiRootkit C2 server"
icon: "monitor"
date: "2025-05-25T16:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 401
---

# Web UI Overview

Modern React-based web interface for EpiRootkit C2 server management.

{{< figure src="/images/webui/dashboard.png" alt="C2 Dashboard Main" class="img-fluid" >}}

## Quick Start

```bash
# Start C2 server with Web UI
./deploy_c2.sh

# Access Web UI at http://localhost:3000
# Default password is secret123
```

## Architecture

Technology stack and integration.

- **Frontend**: React 18 + Vite
- **Backend**: Express.js + Socket.IO
- **Authentication**: JWT with session management
- **Real-time**: WebSocket connections 