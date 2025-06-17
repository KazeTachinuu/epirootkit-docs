---
title: "Overview"
description: "Web interface for EpiRootkit C2"
icon: "monitor"
weight: 401
---


{{< figure src="/images/webui/dashboard.png" alt="C2 Dashboard Main" class="img-fluid" >}}

## Quick Start

```bash
./deploy_c2.sh --c2     # Start C2 + Web UI
```

**Access**: `http://localhost:3000`  
**Default Password**: `secret123` (hash configured in `config.env`)

## Features

- **[Authentication]({{< relref "features/authentication.md" >}})**: Secure login for web interface  
- **[Dashboard]({{< relref "features/dashboard.md" >}})**: Real-time client monitoring with stats
- **[Client Management]({{< relref "features/client-management.md" >}})**: Individual client control

### Client Panels

When managing individual clients (`/clients/:id`), the following panels are available:

- **[Overview Panel]({{< relref "features/panels/overview-panel.md" >}})**: Client status and information
- **[Authentication Panel]({{< relref "features/panels/authentication-panel.md" >}})**: Client authentication management  
- **[Terminal Panel]({{< relref "features/panels/terminal-panel.md" >}})**: Execute commands on clients
- **[Upload/Download Panel]({{< relref "features/panels/upload-download-panel.md" >}})**: File transfer with drag-drop
- **[Persistence Panel]({{< relref "features/panels/persistence-panel.md" >}})**: Manage rootkit persistence
- **[Configuration Panel]({{< relref "features/panels/configuration-panel.md" >}})**: Toggle rootkit settings
- **[Event Log Panel]({{< relref "features/panels/event-log-panel.md" >}})**: Real-time activity monitoring
