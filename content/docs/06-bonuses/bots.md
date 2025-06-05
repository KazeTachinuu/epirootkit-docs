---
title: "Unwelcome Guests"
description: "C2 server scanning attempts detected"
icon: "smart_toy"
date: "2025-06-05T00:44:31+01:00"
lastmod: "2025-06-05T00:44:31+01:00"
draft: false
toc: true
weight: 601
---

## The C2 Server Setup

To create a realistic Command & Control infrastructure for our rootkit project, we deployed a C2 server on a Scaleway VPS (51.159.97.114). 

The server was configured to run both the C2 backend and web interface, with DNS records pointing the domain `epirootkit.com` to it. We set up a specific subdomain `jules_chef_de_majeur.epirootkit.com` for rootkit client communications.

{{< alert context="warning" >}}
After a good night's sleep, I discovered something concerning in the logs - multiple rootkit connections attempts. Those are basically socket connections on port 4444.
{{< /alert >}}

{{< figure src="/images/bots/c2-logs.png" alt="Server logs showing unauthorized connection attempts" >}}

Upon investigation, these connections originated from US-based VPN IP addresses `65.49.1.*`, likely automated bots scanning for open ports. 

| Port | Service | Purpose |
|------|---------|---------|
| 22 | SSH | Remote server administration |
| 3000 | C2 Web Interface | Rootkit control panel |
| 4444 | Rootkit Client | Client connections |

And of course there is a password to access the C2 web interface. And anyway.. I set it up so that nobody else can access it right... ?

{{< alert context="warning" >}}
Wait, we properly secured the server access before deployment, right? RIGHT ???? ABSOLUTELY NOT IT WAS WIDE OPEN LOL.
{{< /alert >}}

```bash
ubuntu@sys2:~/epirootkit$ sudo iptables -nvL
Chain INPUT (policy DROP 10 packets, 528 bytes)
 pkts bytes target     prot opt in     out     source               destination
  465 44214 ACCEPT     0    --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
    0     0 ACCEPT     0    --  lo     *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     6    --  *      *       90.16.24.187         0.0.0.0/0            tcp dpt:22
    1    60 ACCEPT     6    --  *      *       163.5.3.68           0.0.0.0/0            tcp dpt:22
    0     0 ACCEPT     6    --  *      *       90.16.24.187         0.0.0.0/0            tcp dpt:3000
    0     0 ACCEPT     17   --  *      *       90.16.24.187         0.0.0.0/0            udp dpt:3000
    0     0 ACCEPT     6    --  *      *       163.5.3.68           0.0.0.0/0            tcp dpt:3000
    0     0 ACCEPT     17   --  *      *       163.5.3.68           0.0.0.0/0            udp dpt:3000
    0     0 ACCEPT     6    --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:4444
    0     0 ACCEPT     17   --  *      *       0.0.0.0/0            0.0.0.0/0            udp dpt:4444

Chain FORWARD (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 462 packets, 48283 bytes)
 pkts bytes target     prot opt in     out     source               destination
 ```

I quickly implemented these iptables rules to restrict access to critical services. Port 3000 (the C2 web interface) is now limited to only two trusted IP addresses - my home network and EPITA's infrastructure. This ensures that only authorized users can access the rootkit control panel (There is a password to access it but I rather just block everything for my peace of mind).


{{< alert context="info" >}}
I still have around 2-3 connections every hour on port 4444. That gets cleans up after timeout.
{{< /alert >}}

I'm considering putting a honeypot on the server to catch and analyse them.




