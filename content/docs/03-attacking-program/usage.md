---
title: "Usage & CLI Commands"
weight: 33
description: "Running the C2 server and its CLI."
---

## Running the Server

In the `attacking_program` directory, run:

```bash
npm start
```

The server starts automatically and presents the `c2-server$ ` prompt. Real-time event logs appear in the console, providing visual alerts for significant events such as client connections and disconnections.

## CLI Commands

{{< table >}}
| Command                             | Aliases        | Description                                                                        |
|-------------------------------------|----------------|------------------------------------------------------------------------------------|
| `server start`                      | `start`        | Starts TCP server (Note: auto-starts with `npm start`).                              |
| `server stop`                       | `stop`         | Stops TCP server.                                                                  |
| `clients list`                      | `ls`, `list`   | Lists connected clients (alias, ID, auth status, last ping).                         |
| `send <clientAlias> <command...>`   |                | Sends `<command...>` to specified `<clientAlias>`.                                   |
| `exit`                              | `quit`         | Stops server and exits application.                                                |
{{< /table >}}

*   `<clientAlias>`: Target client alias (e.g., `Client-1` from `clients list`).
*   `<command...>`: Command string for the victim machine.

Event logs provide feedback on connections, authentication attempts, data exchanges, errors, and command results. When a command is executed on a rootkit and results (such as stdout, stderr, or exit status) are sent back, they will be displayed in these logs. 