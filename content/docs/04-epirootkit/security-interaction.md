---
title: "Security Interaction with C2"
weight: 44
description: "Security protocols EpiRootkit must follow when interacting with the C2 server."
---

To maintain a secure channel and controlled access, the EpiRootkit must adhere to the security protocols established by the Attacking Program (C2 Server).

## 1. Encrypted Communication (AES-256-GCM)

*   **Requirement**: All TCP communication with the C2 server **must** be encrypted using AES-256-GCM.
*   **Shared Secret**: The EpiRootkit must be compiled or configured with the exact same 32-byte `ENCRYPTION_KEY` that the C2 server is using (see [C2 Security Features](../03-attacking-program/security.md) and [C2 Configuration](../03-attacking-program/configuration.md)).
*   **Payloads**: This applies to all messages sent by the rootkit (e.g., `AUTH`, `PING`, `COMMAND_RESULT`) and all messages received from the C2 (e.g., `AUTH_SUCCESS`, `AUTH_FAILED`, `COMMAND`, `PONG`).
*   **Format**: The encrypted payload consists of the Initialization Vector (IV), the GCM authentication tag, and the ciphertext. The C2 server expects this in the format: `iv_hex:authtag_hex:ciphertext_hex`.

## 2. Client Authentication (Password-Based)

*   **Requirement**: The EpiRootkit **must** send an `AUTH` message as its first payload after establishing an encrypted connection.
*   **Password**: The `AUTH` message contains a plaintext password (see [Connection & Authentication](./connection-authentication.md) for format).
*   **Server-Side Hashing**: The C2 server will hash this received password using SHA512 and compare it against its configured `PASSWORD_HASH`.
*   **Consequences of Failure**: If authentication fails, the C2 server will respond with an `AUTH_FAILED` message and will not process further commands from the unauthenticated rootkit.

## 3. Message Types and Structure

While not strictly a security protocol, adhering to the defined JSON message structures for different types (`AUTH`, `PING`, `COMMAND_RESULT`, etc.) is crucial for successful interaction. The C2 server expects these specific types and fields.

*   **`type` field**: Always required to identify the message's purpose.
*   **Payload fields**: Specific to each message type (e.g., `password` for `AUTH`, `commandId` and `output` for `COMMAND_RESULT`).

Any deviation from these protocols will likely result in communication errors, authentication failures, or unprocessed commands.

Refer to the specific documentation pages for [C2 Security](../03-attacking-program/security.md) and EpiRootkit message formats ([Connection & Authentication](./connection-authentication.md), [Command Execution](./command-execution.md)) for more details. 