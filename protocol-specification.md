# EpiRootkit C2 Protocol Specification

## Overview

This document defines the structured communication protocol between the EpiRootkit kernel module and the C2 web interface. The protocol uses JSON for structured data exchange to ensure reliable parsing and extensibility.

## Protocol Version

- **Version**: 1.0
- **Format**: JSON
- **Encoding**: UTF-8

## Message Types

### 1. Status Response (`status`)

**Command**: `status`

**Response Format**:
```json
{
  "type": "status",
  "timestamp": 1234567890,
  "rootkit": {
    "version": "2.42.0",
    "authenticated": true,
    "module_hidden": true,
    "encryption_enabled": false
  },
  "connection": {
    "status": "connected",
    "server": "192.168.64.1:4444",
    "socket_active": true
  },
  "persistence": {
    "enabled": true,
    "methods": {
      "modules_load": {
        "active": true,
        "path": "/etc/modules-load.d/epirootkit.conf",
        "description": "systemd boot persistence"
      },
      "cron": {
        "active": false,
        "path": "/etc/cron.d/system-update",
        "description": "periodic check every 5 minutes"
      },
      "shell_profile": {
        "active": true,
        "path": "/etc/profile.d/system-env.sh",
        "description": "triggers on root login"
      }
    }
  },
  "config": {
    "persistence_enabled": true,
    "encryption_enabled": false,
    "file_hiding_enabled": true
  }
}
```

### 2. Command Result (`result`)

**Format**:
```json
{
  "type": "result",
  "timestamp": 1234567890,
  "command": "persist_install",
  "success": true,
  "message": "Persistence installed successfully",
  "details": {
    "methods_installed": ["modules_load", "cron"],
    "methods_failed": ["shell_profile"],
    "total_success": 2,
    "total_attempted": 3
  }
}
```

### 3. Error Response (`error`)

**Format**:
```json
{
  "type": "error",
  "timestamp": 1234567890,
  "code": "AUTH_REQUIRED",
  "message": "Authentication required for this operation",
  "details": {
    "required_auth_level": "authenticated",
    "current_auth_level": "none"
  }
}
```

### 4. Configuration Update (`config`)

**Format**:
```json
{
  "type": "config",
  "timestamp": 1234567890,
  "action": "update",
  "changes": {
    "module_hidden": {
      "old": false,
      "new": true
    }
  },
  "success": true
}
```

## Field Specifications

### Status Fields

| Field | Type | Description | Values |
|-------|------|-------------|---------|
| `rootkit.version` | string | Rootkit version | e.g., "2.42.0" |
| `rootkit.authenticated` | boolean | Authentication status | true/false |
| `rootkit.module_hidden` | boolean | Module visibility | true/false |
| `connection.status` | string | Connection state | "connected", "connecting", "disconnected" |
| `persistence.enabled` | boolean | Overall persistence status | true/false |
| `persistence.methods.*.active` | boolean | Individual method status | true/false |

### Error Codes

| Code | Description |
|------|-------------|
| `AUTH_REQUIRED` | Authentication required |
| `PERMISSION_DENIED` | Insufficient permissions |
| `INVALID_COMMAND` | Unknown command |
| `SYSTEM_ERROR` | Internal system error |
| `NETWORK_ERROR` | Network communication error |

## Command Protocol

### Persistence Commands

#### Install All Persistence
```
Command: persist_install
Response: result with persistence details
```

#### Install Specific Method
```
Command: persist_modules | persist_cron | persist_profile
Response: result with method-specific details
```

#### Remove Persistence
```
Command: persist_remove
Response: result with removal details
```

### Stealth Commands

#### Hide Module
```
Command: hide_module
Response: config update
```

#### Unhide Module
```
Command: unhide_module
Response: config update
```

## Backward Compatibility

The protocol includes fallback parsing for legacy text-based responses to ensure compatibility during transition periods.

## Implementation Notes

### Rootkit Side
- Use `snprintf` with proper buffer sizing for JSON generation
- Validate JSON structure before sending
- Include timestamp for message ordering
- Handle memory allocation failures gracefully

### Web UI Side
- Parse JSON with error handling
- Implement fallback to legacy text parsing
- Validate message types and required fields
- Handle malformed JSON gracefully

