---
title: "Installation"
description: "Install and configure the C2 attacking program"
icon: "download"
date: "2025-05-25T00:00:00+01:00"
lastmod: "2025-05-25T16:00:00+01:00"
draft: false
toc: true
weight: 302
---



## Prerequisites

Before installing the attacking program, ensure you have:

- **Node.js 14+** and **npm** (installed via setup script)
- **Linux environment** (Ubuntu recommended)
- **Network access** between attacker and victim machines

## Quick Start

### 1. Automatic Setup (Recommended)

If you're using the pre-built attacker VM or have run the setup script:

```bash
# Navigate to attacking program directory
cd attacking_program

# Install dependencies
npm install

# Start the C2 server
npm start
```

The server will start on:
- **C2 Server**: `localhost:4444` (for client connections)
- **Web Interface**: `localhost:3000` (for operator access)

### 2. Manual Installation

For fresh systems or custom setups:

```bash
# Install system dependencies
sudo apt update
sudo apt install -y nodejs npm gcc make linux-headers-$(uname -r)

# Navigate to project directory
cd attacking_program

# Install Node.js dependencies
npm install

# Start the server
npm start
```

## Configuration

### Environment Variables

Create a `.env` file in the `attacking_program` directory:

```env
# Server Configuration
C2_PORT=4444
WEB_PORT=3000
AUTH_PASSWORD=password

# Security Settings
ENCRYPTION_KEY=your-32-character-encryption-key
JWT_SECRET=your-jwt-secret-key

# Upload Settings
UPLOAD_DIR=./uploads
MAX_FILE_SIZE=50MB
```

### Custom Configuration

Edit `src/config/index.js` for custom settings:

```javascript
module.exports = {
  C2_PORT: process.env.C2_PORT || 4444,
  WEB_PORT: process.env.WEB_PORT || 3000,
  AUTH_PASSWORD: process.env.AUTH_PASSWORD || 'password',
  // ... other settings
};
```


## Production Deployment

```bash
# Install only production dependencies
npm install --production

# Start the server
cd attacking_program && npm start
```

## Next Steps

Once installed:
1. [Learn how to use the attacking program](../usage)
2. [Set up the rootkit](../../05-epirootkit/overview)
3. [Access the web interface](../../04-web-ui/overview)