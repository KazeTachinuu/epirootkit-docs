---
title: "Attacking Program (C2 Server)"
weight: 30
type: "docs"
description: "The C2 server for EpiRootkit. This section details its overview, installation, usage, configuration, and security aspects."
---

The Attacking Program is the command and control (C2) server for EpiRootkit. It provides a command-line interface (CLI) to manage and interact with EpiRootkit instances on victim machines.

Key functions include listening for client connections, secure communication (AES-GCM encryption, SHA512 password hashing), client management, and remote command execution via an interactive CLI.

This section covers its installation, usage, configuration, and security. 