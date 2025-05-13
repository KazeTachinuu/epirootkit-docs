---
title: "Overview"
weight: 31
type: "docs"
description: "A detailed overview of the Attacking Program (C2 Server), its purpose, and key functionalities."
---

The Attacking Program is the command and control (C2) server for EpiRootkit. It is designed to run on a Linux-based attacking virtual machine and provides a command-line interface (CLI) to manage and interact with EpiRootkit instances on victim machines.

Key functions include:
*   Listening for client connections from EpiRootkit instances.
*   Securing communication using AES-256-GCM encryption.
*   Authenticating clients based on a SHA512 hashed password.
*   Managing connected clients (listing, identifying).
*   Providing an interactive CLI for sending commands to EpiRootkit instances. 