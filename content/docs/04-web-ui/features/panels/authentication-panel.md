---
title: "Authentication Panel"
description: "Client authentication management and security interface"
icon: "key"
date: "2025-05-25T16:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 426
---

# Authentication Panel

Secure two-layer authentication system for rootkit client access and control.

{{< figure src="/images/webui/auth-panel.png" alt="Authentication Panel" class="img-fluid" >}}

## How It Works

The authentication system has two layers:

1. **Web UI Login**: JWT authentication to access the C2 interface
2. **Client Authentication**: Per-client password authentication for rootkit control

### Client Authentication Process

{{< figure src="/images/webui/auth-panel-login.png" alt="Authentication Panel Login" class="img-fluid" >}}

When you click "Authenticate" on a client card:

1. **Modal opens**: Password input dialog appears
2. **Credential submission**: Password sent to rootkit client via C2 server
3. **Rootkit verification**: Client validates password against stored hash
4. **Session establishment**: Authenticated session created for client operations



## Security Features

- **Password hashing**: SHA-512 verification on client side
- **Session timeout**: Authentication expires after inactivity
- **Per-client auth**: Each client requires individual authentication
- **Failed attempt tracking**: Monitor and log authentication failures
- **Secure transmission**: Encrypted credential transfer via C2 channel 