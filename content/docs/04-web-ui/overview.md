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



{{< figure src="/images/webui/dashboard.png" alt="C2 Dashboard Main" class="img-fluid" >}}

## Quick Start

```bash
# Start system (includes Web UI)
./deploy_c2.sh

# Access Web UI
# URL: http://localhost:3000
# Password: password
```

## Architecture

**Frontend Technology Stack:**

- **Framework**: React 18 + Vite
- **Styling**: Tailwind CSS + shadcn/ui components
- **State**: React Context + hooks
- **Real-time**: WebSocket connections via Socket.IO
- **Authentication**: JWT tokens with session management

## Core Features

- **Dashboard**: Real-time client monitoring and statistics
- **Authentication**: Secure login with JWT tokens
- **Client Management**: Visual client cards with status indicators
- **Interactive Panels**: Terminal, file transfer, configuration, logs
- **Real-time Updates**: Live WebSocket synchronization

## User Interface

### Main Components

- **Login Screen**: JWT authentication
- **Dashboard**: Client overview with search and filtering
- **Client Cards**: Visual representation of connected rootkits
- **Panel System**: Tabbed interface for different operations

### Panel Types

- **Terminal Panel**: Command execution interface
- **Upload/Download Panel**: File transfer with drag-drop
- **Configuration Panel**: Toggle rootkit settings
- **Authentication Panel**: Client authentication management
- **Event Log Panel**: Real-time activity monitoring

## Integration

The Web UI integrates with the [C2 Server](../03-attacking-program/overview.md) via:
- REST API endpoints
- WebSocket for real-time updates
- JSON message protocol 