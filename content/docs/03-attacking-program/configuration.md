---
title: "Configuration"
weight: 34
description: "Key C2 server configuration settings."
---

# C2 Server Configuration

> **You must configure the C2 server before first use.**

## 1. Configuration File: `.env`

All critical settings are loaded from a `.env` file in the `attacking_program/` directory. If you haven't already, copy the example:
```bash
cp .env.example .env
```

Open `.env` in your editor and set the following:

### Required Settings

- `C2_PORT` — TCP port for EpiRootkit connections (default: 4444)
- `ENCRYPTION_KEY` — **Must be a 32-byte (256-bit) random string**
- `PASSWORD_HASH` — **Must be a 128-character lowercase SHA512 hex string**

### How to Generate Secure Values

#### Generate a 32-byte ENCRYPTION_KEY
```bash
head -c 32 /dev/urandom | base64 | head -c 32
```
Paste this value into your `.env` as `ENCRYPTION_KEY`.

#### Generate a SHA512 PASSWORD_HASH
Choose a strong password, then:
        ```bash
echo -n "your_strong_password" | sha512sum | awk '{print $1}'
        ```
Paste the resulting 128-character hex string into `.env` as `PASSWORD_HASH`.

> {{< alert context="warning" text="Never use the default ENCRYPTION_KEY or PASSWORD_HASH! Anyone with these values can control your rootkit." />}}

### Example `.env`
```
C2_PORT=4444
ENCRYPTION_KEY=your32bytelongrandomstringhere!!
PASSWORD_HASH=your128characterlongsha512hexstring...
```

## 2. Optional Settings
- `WEB_PORT` — Port for future web UI (default: 3000)
- `UPLOAD_DIR`, `DOWNLOAD_DIR`, `LOG_DIR` — Paths for file operations and logs

## 3. Validation
- The server will refuse to start if `ENCRYPTION_KEY` is not exactly 32 bytes or `PASSWORD_HASH` is not 128 hex chars.
- If you see errors, double-check your `.env` file.

## 4. Troubleshooting
- If you see `Error: ENCRYPTION_KEY must be 32 bytes`, regenerate your key.
- If you see `Error: PASSWORD_HASH must be 128 hex chars`, regenerate your hash.
- If you see port conflicts, change `C2_PORT`.

## 5. Next Steps
- [Installation](./installation.md): How to install and run the C2 server
- [Usage](./usage.md): CLI commands and workflow 