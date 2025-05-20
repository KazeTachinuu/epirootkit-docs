---
title: "Overview"
weight: 31
type: "docs"
description: "A detailed overview of the Attacking Program (C2 Server), its purpose, and key functionalities."
---

## TODO: Implement Additional C2 Server Features

- [ ] Add advanced client management (aliasing, grouping)
- [ ] Implement encrypted web UI integration
- [ ] Add audit logging and event history
- [ ] Implement advanced command scheduling
- [ ] Add more robust error handling and reporting

_This section will be updated with documentation once features are implemented and tested._

The Attacking Program is the command and control (C2) server for EpiRootkit. It is designed to run on a Linux-based attacking virtual machine and provides a command-line interface (CLI) to manage and interact with EpiRootkit instances on victim machines.

Key functions include:
*   Listening for client connections from EpiRootkit instances.
*   Securing communication using AES-256-GCM encryption.
*   Authenticating clients based on a SHA512 hashed password.
*   Managing connected clients (listing, identifying).
*   Providing an interactive CLI for sending commands to EpiRootkit instances. 