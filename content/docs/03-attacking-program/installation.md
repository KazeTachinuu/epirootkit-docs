---
title: "Installation"
description: "Install C2 server"
icon: "download"
weight: 302
---

## Quick Install

```bash
./deploy_c2.sh --c2    # Builds and starts everything
```

## Manual Install

```bash
# Build web interface
cd webui && npm install && npm run build && cd ..

# Copy to backend
mkdir -p attacking_program/public
cp -r webui/dist/* attacking_program/public/

# Install and start backend
cd attacking_program
npm install
npm start
```

## Configuration

Edit `attacking_program/config.env` if needed:

```env
C2_PORT=4444
WEB_PORT=3000
C2_WEBUI_PASSWORD_HASH=348735696e74c45e7fbf9c6839d87f891486d19e5059db7e397d5086e486dc0051a533752805dc9288463673f0a6fcbf2a655548738a85305b2d571bae44a71e
ENCRYPTION_KEY=0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef
```

**Generate your own hash**: `echo -n "yourpassword" | sha512sum`

This is the password that will be use in the [Auth Page]({{< relref "../04-web-ui/features/authentication.md" >}}) of the web interface. 
