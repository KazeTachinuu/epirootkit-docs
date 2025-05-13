---
title: "Installation"
weight: 32
description: "Essential setup for the C2 server."
---

To run the Attacking Program (C2 Server):

## Prerequisites

*   **Node.js & npm**: Ensure Node.js (e.g., LTS v18.x+) and npm are installed.
    ```bash
    sudo apt update && sudo apt install nodejs npm
    ```

## Setup

1.  Navigate to the `attacking_program` directory within the project.

2.  Install Dependencies:
    ```bash
    npm install
    ```

3.  Review Critical Configuration: Before first use, ensure you have reviewed and appropriately set the `ENCRYPTION_KEY`, `PASSWORD_HASH`, and `C2_PORT`. Refer to the [Configuration](./configuration) page for detailed information on these settings and how to generate necessary values. It is crucial to change the default security keys and hashes.

Required directories (`uploads/`, `downloads/`, `logs/`) are created automatically.

Next: [Usage & CLI Commands](./usage). 