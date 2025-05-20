---
title: "Installation"
weight: 32
description: "Essential setup for the C2 server."
---

# Attacking Program (C2 Server) Installation

> **Assumption:** You are starting from a fresh Ubuntu system. Complete [Host Environment Setup](../02-setup/environment.md) and [VM Installation](../02-setup/vm-installation.md) first.

## 1. Deployment

{{<tabs tabTotal="2">}}
{{% tab tabName="Deploy the Full Stack (Recommended)" %}}

The simplest way to build and launch both the frontend and backend is with the provided script:

```bash
./deploy.sh
```

This will:
- Build the frontend (webui)
- Copy the frontend build to the backend's `public/` directory
- Install backend dependencies
- Start the backend server with `pnpm start`

{{% /tab %}}
{{% tab tabName="Manual Setup (Advanced)" %}}

If you want to run the backend and frontend separately, follow these steps:

### Backend
```bash
cd attacking_program
pnpm install --prod
pnpm start
```

### Frontend (Web UI)
```bash
cd webui
pnpm install
pnpm build
```

Then copy the build to the backend if needed:
```bash
cp -r webui/dist/* attacking_program/public/
```

{{% /tab %}}
{{% /tabs %}}

## 2. Prepare Configuration

The C2 server is configured via a `.env` file. **You must set strong, secure values!**

1. Copy the example file:
   ```bash
   cp .env.example .env
   ```
2. Edit `.env` and set:
   - `C2_PORT` (default: 4444)
   - `ENCRYPTION_KEY` (must be a 32-byte random string)
   - `PASSWORD_HASH` (must be a 128-char SHA512 hex string)

> {{< alert context="warning" text="Never use the default ENCRYPTION_KEY or PASSWORD_HASH in production! See [Configuration](./configuration.md) for how to generate secure values." />}}

## 3. Troubleshooting
- If you see `Error: ENCRYPTION_KEY must be 32 bytes`, check your `.env` file.
- If you see `Error: PASSWORD_HASH must be 128 hex chars`, regenerate your hash (see [Configuration](./configuration.md)).
- If you see `EADDRINUSE`, another process is using the port. Change `C2_PORT` or stop the other process.
- For permission errors, ensure you are not running as root unless necessary.

## 4. Next Steps
- See [Configuration](./configuration.md) for secure setup.
- See [Usage](./usage.md) for CLI commands and workflow.
