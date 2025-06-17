---
title: "Installation"
description: "Install and start the Web UI"
icon: "download"
weight: 402
---

## Quick Start

```bash
./deploy_c2.sh # Builds and starts everything
```

**Access**: `http://localhost:3000`

## Manual Build

```bash
# Build frontend
cd webui && npm install && npm run build && cd ..

# Deploy to backend
mkdir -p attacking_program/public
cp -r webui/dist/* attacking_program/public/

# Start server
cd attacking_program && npm install && npm start
```

## Configuration

Password hash configured in `attacking_program/config.env`:

```env
C2_WEBUI_PASSWORD_HASH=348735696e74c45e7fbf9c6839d87f891486d19e5059db7e397d5086e486dc0051a533752805dc9288463673f0a6fcbf2a655548738a85305b2d571bae44a71e
```

Default password is `secret123`. Generate custom hash: `echo -n "yourpassword" | sha512sum`
