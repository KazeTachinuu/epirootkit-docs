---
title: "EpiRootkit"
weight: 40
type: "docs"
description: "Overview of the EpiRootkit kernel module and its interaction with the C2 server."
---

The EpiRootkit is a Linux kernel module designed for pedagogical purposes, demonstrating various rootkit functionalities as outlined in the project subject. It communicates with the [Attacking Program (C2 Server)](../03-attacking-program) to receive commands and exfiltrate data.

## Core Objectives

*   **Establish Covert Communication**: Connect back to the C2 server using encrypted TCP sockets.
*   **Authenticate Securely**: Prove its identity to the C2 server using a shared secret.
*   **Execute Remote Commands**: Receive commands from the C2, execute them on the victim machine, and return the output (stdout, stderr, exit code).
*   **Implement Persistence**: (Planned) Ensure the rootkit remains active after system reboots.
*   **Evade Detection**: (Planned) Employ techniques to hide its presence (module, files, processes, network activity).
*   **Enable File Transfer**: (Planned) Allow operators to upload files to and download files from the victim machine via the C2.

This section will detail how the EpiRootkit is expected to interact with the C2 server for its core operations, focusing on the communication protocol, security measures, and data exchange formats from the C2 server's perspective. 