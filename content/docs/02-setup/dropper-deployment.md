---
title: "Dropper Deployment"
description: "Social engineering tool for automated rootkit deployment"
icon: "download"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-25T00:00:00+01:00"
draft: false
toc: true
weight: 205
---

## Overview

The dropper is a social engineering tool disguised as "NeoGeoLoc - Location Registration System". It downloads and installs the rootkit automatically on target systems.

## Prerequisites

1. **C2 Server**: [Attacker VM Setup]({{< relref "attacking-vm-setup.md" >}}) - Must be running
2. **Build**: `./deploy_c2.sh` builds the dropper binary

## Landing Page

**Access**: `http://192.168.200.11:8080` 

Download interface for the dropper binary.

{{< figure src="/images/dropper/landing.png" alt="Landing Page NGL" class="img-fluid" >}}

## Application Interface

The dropper presents itself as a location registration system:

{{< figure src="/images/dropper/application.png" alt="NeoGeoLoc Application Interface" class="img-fluid" >}}

Interface elements:
- Server address field (pre-filled with real C2 domain)
- "Register Location" button

## Process

```
User clicks "Register Location"
     ↓
Downloads rootkit from C2 server
     ↓
Executes deploy_rootkit.sh
     ↓
Installs kernel module
     ↓
Establishes C2 connection
     ↓
Shows "Registration successful"
```

The interface appears as a legitimate geo-location service to avoid suspicion.
