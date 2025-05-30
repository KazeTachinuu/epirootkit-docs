---
title: "UI Panels"
description: "Individual Web UI panel components"
icon: "dashboard"
date: "2025-05-25T16:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 420
---

# UI Panels

Interactive panel components for EpiRootkit Web UI operations.

## Available Panels

Each panel provides specific functionality for managing rootkit clients:

- **[Authentication Panel](./authentication-panel.md)**: Secure client authentication
- **[Terminal Panel](./terminal-panel.md)**: Command execution interface  
- **[Upload/Download Panel](./upload-download-panel.md)**: File transfer operations
- **[Configuration Panel](./configuration-panel.md)**: Settings and toggles
- **[Persistence Panel](./persistence-panel.md)**: Boot survival configuration
- **[Event Log Panel](./event-log-panel.md)**: Real-time activity monitoring
- **[Overview Panel](./overview-panel.md)**: Client status and information

## Panel System

All panels feature:
- **Real-time Updates**: Live synchronization via WebSocket
- **Authentication Required**: Secure access control
- **Responsive Design**: Works on desktop and mobile
- **Error Handling**: Graceful failure management