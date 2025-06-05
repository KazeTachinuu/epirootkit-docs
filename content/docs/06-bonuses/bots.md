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

## VPN Access

I set up a zero trust VPN access (Twingate) to restrict access to the C2 server UI.


While connected to the VPN you can access the web interface with : `http://c2.epirootkit.com:3000`

{{< figure src="/images/bots/c2-twingate.png" alt="Twingate VPN setup" >}}

In addition with iptables rules, this should be enough to block the bots : 

```bash
ubuntu@sys2:~/epirootkit$ sudo iptables -nvL                      LABEL_DEPLOYED_BY="linux" bash
Chain INPUT (policy DROP 959 packets, 177K bytes)
 pkts bytes target     prot opt in     out     source               destination
10778 1690K ACCEPT     0    --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
   24  1516 ACCEPT     0    --  lo     *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     6    --  *      *       REDACTED             0.0.0.0/0            tcp dpt:22
    0     0 ACCEPT     6    --  *      *       163.5.3.68           0.0.0.0/0            tcp dpt:22
    0     0 ACCEPT     6    --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:4444
    0     0 ACCEPT     17   --  *      *       0.0.0.0/0            0.0.0.0/0            udp dpt:4444

Chain FORWARD (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 11067 packets, 2343K bytes)
 pkts bytes target     prot opt in     out     source               destination
 ```

 {{<table>}}
 | Port | Service | Allowed Source IPs | Access Rule |
 |------|---------|-------------------|-------------|
 | 22 | SSH | REDACTED, 163.5.3.68 | ACCEPT (trusted IPs only) |
 | 4444 | Rootkit Client | 0.0.0.0/0 | ACCEPT (rootkit client connections) |
 | * | All other services | * | DROP (all other traffic blocked) |
 {{< /table >}}



{{< alert context="info" >}}
I still have around 2-3 connections every hour on port 4444. That gets cleans up after timeout.
{{< /alert >}}

I'm considering putting a honeypot on the server to catch and analyse them.


