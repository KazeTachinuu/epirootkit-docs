---
title: "Configuration"
weight: 34
description: "Key C2 server configuration settings."
---

The C2 server is configured via `src/config/index.js` or environment variables.

## Critical Settings

*   `C2_PORT` (Default: `4444` | Env: `C2_PORT`)
    *   TCP port for EpiRootkit connections.

*   `ENCRYPTION_KEY` (Default: `mysecretkey...` (32 bytes) | Env: `ENCRYPTION_KEY`)
    *   Must be a 32-byte string for AES-256-GCM.
    *   Crucial for security: Change the default to a strong, random key.
    *   Must match the key on EpiRootkit clients.

*   `PASSWORD_HASH` (Default: SHA512 hash of `"password"` | Env: `PASSWORD_HASH`)
    *   Must be a 128-char lowercase SHA512 hex string.
    *   Crucial for security: Change the default. Use a strong password and hash it:
        ```bash
        # Example to generate hash for a new password:
        echo -n "your_new_strong_password" | sha512sum | awk '{print $1}'
        ```

## Other Settings

*   `WEB_PORT`: For a potential future web UI (Default: `3000`).
*   `UPLOAD_DIR`, `DOWNLOAD_DIR`, `LOG_DIR`: Default paths for server-side file operations and logs.

Configuration validation ensures key formats (e.g., key lengths) are correct on startup. 