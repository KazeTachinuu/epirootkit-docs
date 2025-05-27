---
title: "Installation & Setup"
weight: 62
description: "How to install and launch the Web UI."
---

# Web UI Installation & Setup

> **Assumption:** You are starting from a fresh Ubuntu system. Complete [Host Environment Setup](../02-setup/environment.md) first.

## 1. Install Node.js, npm, and pnpm
If not already installed, run:
```bash
sudo apt update
sudo apt install -y nodejs npm
sudo npm install -g pnpm
```

<<<<<<< HEAD
1. Navigate to the `attacking_program/` directory:
   ```bash
   cd attacking_program
   ```
2. Install dependencies:
   ```bash
   pnpm install
   ```
3. Start the C2 server and Web UI:
   ```bash
   pnpm start
   ```
4. Open your browser and go to [http://localhost:3000](http://localhost:3000) (or the port shown in your terminal).

> **Note:** The Web UI is now integrated and starts automatically with the C2 server. No separate process is required. 
=======
> {{< alert context="info" text="Check your versions: Node.js v18+ and pnpm v8+ are recommended. Use `node -v` and `pnpm -v` to verify." />}}

## 2. Navigate to the Web UI Directory
```bash
cd webui
```

## 3. Install Dependencies
```bash
pnpm install
# Or, if you prefer npm:
npm install
```

## 4. Start the Development Server
```bash
pnpm run dev
# Or
npm run dev
```

Open your browser and go to [http://localhost:5173](http://localhost:5173) (or the port shown in your terminal).

> {{< alert context="warning" text="The Web UI is currently frontend-only. Backend integration is planned for future releases." />}}

## 5. Troubleshooting
- If you see errors about missing dependencies, rerun `pnpm install` or `npm install`.
- If you see `EADDRINUSE`, another process is using the port. Change the port in `package.json` or stop the other process.
- For permission errors, avoid running as root unless necessary.

## 6. Next Steps
- See the [Overview](./overview.md) for features and usage tips. 
>>>>>>> d8205ef9e1984d8455d84e1cada77692ef2857c0
