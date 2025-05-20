---
title: "Installation"
weight: 32
description: "Essential setup for the C2 server."
---

# Attacking Program (C2 Server) Installation

> **Assumption:** You are starting from a fresh Ubuntu system. Complete [Host Environment Setup](../02-setup/environment.md) and [VM Installation](../02-setup/vm-installation.md) first.

## 1. Install Node.js, npm, and pnpm

If not already installed, run:
```bash
sudo apt update
sudo apt install -y nodejs npm
sudo npm install -g pnpm
```

> {{< alert context="info" text="Check your versions: Node.js v18+ and pnpm v8+ are recommended. Use `node -v` and `pnpm -v` to verify." />}}

## 2. Navigate to the Attacking Program Directory
```bash
cd attacking_program
```

## 3. Install Dependencies
```bash
pnpm install
# Or, if you prefer npm:
npm install
```

## 4. Prepare Configuration

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


## 5. Start the C2 Server
```bash
pnpm start
# Or
npm start
```

You should see the C2 CLI prompt and log messages. If you see errors about missing config or directories, check the steps above.

## 7. Troubleshooting
- If you see `Error: ENCRYPTION_KEY must be 32 bytes`, check your `.env` file.
- If you see `Error: PASSWORD_HASH must be 128 hex chars`, regenerate your hash (see [Configuration](./configuration.md)).
- If you see `EADDRINUSE`, another process is using the port. Change `C2_PORT` or stop the other process.
- For permission errors, ensure you are not running as root unless necessary.

## 8. Next Steps
- See [Configuration](./configuration.md) for secure setup.
- See [Usage](./usage.md) for CLI commands and workflow.
