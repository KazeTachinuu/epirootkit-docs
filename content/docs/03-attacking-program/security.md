---
title: "Security Features"
weight: 35
description: "Overview of C2 server security: AES-GCM and SHA512."
---

## TODO: Implement Security Features

- [ ] Enforce end-to-end encryption for all C2 traffic
- [ ] Add configurable password policies
- [ ] Implement multi-factor authentication for operators
- [ ] Add rate limiting and brute-force protection
- [ ] Document all security mechanisms in detail

_This section will be updated with documentation once features are implemented and tested._

The C2 server employs two main security mechanisms:

## 1. Message Encryption: AES-256-GCM

*   Purpose: Encrypts all TCP C2 traffic for confidentiality, integrity, and authenticity.
*   Key: Uses a 32-byte `ENCRYPTION_KEY` (must be changed from default and kept secret).
*   Format: Encrypted messages are transmitted as a colon-separated string: `iv_hex:authtag_hex:ciphertext_hex`.
*   Details: See `src/utils/encryption.js` and [Configuration](./configuration).

## 2. Client Authentication: SHA512 Hashing

*   Purpose: Verifies EpiRootkit clients before allowing interaction.
*   Method: Client sends a password; server hashes it with SHA512 and compares against a stored `PASSWORD_HASH`.
*   Hash: The `PASSWORD_HASH` (must be changed from default) is a 128-char lowercase SHA512 hex string.
*   Details: See `src/handlers/messageHandlers.js` and [Configuration](./configuration).
