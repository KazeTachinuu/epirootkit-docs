---
title: "Connection & Authentication"
weight: 41
description: "How EpiRootkit connects and authenticates with the C2 server."
---

For the EpiRootkit to be controlled, it must first establish a connection to the Attacking Program (C2 Server) and then authenticate itself.

## Connection to C2 Server

1.  **Target**: The EpiRootkit needs to know the IP address and port of the C2 server.
    *   This information might be hardcoded during compilation, passed as a module parameter during loading, or discovered through other means.
    *   The C2 server, by default, listens on port `4444` (configurable, see [C2 Configuration](../03-attacking-program/configuration.md)).

2.  **Protocol**: The connection is a standard TCP socket connection.

3.  **Retry Mechanism** (as per `subject.md`):
    *   If the EpiRootkit cannot connect to the C2 server (e.g., server is down, network issues), it must persistently retry the connection.
    *   The interval between retries is a design choice for the rootkit developer (e.g., every minute).

4.  **Communication Encryption**: All data sent over this TCP connection **must be encrypted** using AES-256-GCM, with the same `ENCRYPTION_KEY` configured on the C2 server.

## Authentication Process

Once a TCP connection is established and encrypted communication is possible, the EpiRootkit **must** authenticate before sending other messages or receiving commands (except for responding to an immediate error if it tries to send data pre-authentication).

1.  **Authentication Message (`AUTH`)**:
    *   The EpiRootkit sends its first message to the C2 server, which must be an `AUTH` message.
    *   **Format**: A JSON object, encrypted using AES-256-GCM.
        ```json
        {
          "type": "AUTH",
          "password": "your_plaintext_password"
        }
        ```
    *   The `"your_plaintext_password"` must be the password for which the C2 server holds the corresponding SHA512 hash in its `PASSWORD_HASH` configuration.

2.  **Server Verification**:
    *   The C2 server decrypts the message.
    *   It hashes the received `password` using SHA512.
    *   It compares this newly generated hash with its configured `PASSWORD_HASH`.

3.  **Server Response**:
    *   **On Success**: The C2 server marks the client as authenticated and sends back an `AUTH_SUCCESS` message (encrypted).
        ```json
        {
          "type": "AUTH_SUCCESS"
        }
        ```
        The EpiRootkit can now proceed to send other messages (e.g., `PING`, `COMMAND_RESULT`) and will receive commands from the C2.

    *   **On Failure**: The C2 server sends back an `AUTH_FAILED` message (encrypted), typically including an error reason, and will likely close the connection or ignore further messages from this client until a new successful authentication.
        ```json
        {
          "type": "AUTH_FAILED",
          "error": "Invalid password"
        }
        ```
        The C2 server will also log this failure internally.

Failure to authenticate correctly will prevent any further interaction with the C2 server. 