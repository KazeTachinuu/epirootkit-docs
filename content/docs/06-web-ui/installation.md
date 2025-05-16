---
title: "Installation & Setup"
weight: 62
description: "How to install and launch the Web UI."
---

## Prerequisites

- Node.js (LTS recommended)
- npm

## Setup Steps

1. Navigate to the `webui/` directory:
   ```bash
   cd webui
   ```
2. Install dependencies:
   ```bash
   pnpm install
   ```
3. Start the development server:
   ```bash
   pnpm run dev
   ```
4. Open your browser and go to [http://localhost:5173](http://localhost:5173) (or the port shown in your terminal).

> **Note:** The Web UI is currently frontend-only. Backend integration is planned for future releases. 