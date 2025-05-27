---
title: "Installation & Setup"
weight: 62
description: "How to install and launch the Web UI."
---

## Prerequisites

- Node.js (LTS recommended)
- npm

## Setup Steps

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