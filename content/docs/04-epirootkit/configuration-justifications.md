---
title: "Configuration Justifications"
description: "Rationale for Ubuntu 20.04 LTS / Linux 5.4.x"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-05-07T00:44:31+01:00"
draft: false
toc: true
weight: 43
---

{{< alert context="info" icon="⚙️" text="This page explains why we chose Ubuntu 20.04 LTS with the 5.4.x kernel for our rootkit development and testing." />}}

## Environment

- **OS**: Ubuntu 20.04 LTS (Focal Fossa)  
- **Kernel**: 5.4.x (LTS)

---

## Why Ubuntu 20.04 LTS?

{{< alert context="success" icon="check_circle" text="LTS release--five years of security updates and readily available VM images (ISO/QCOW2)." />}}

- Widely deployed on servers and desktops  
- Official cloud and container images simplify automation  
- Predictable package versions and a large user community  

---

## Why Kernel 5.4.x?

{{< alert context="primary" icon="🔧" text="Balances modern hooking APIs with exported symbols and no mandatory module signing." />}}

1. **Full ftrace support**  
   - Kernel 4.1+ stabilized the ftrace API (`ftrace_set_filter_ip()`, `register_ftrace_function()`).  
   - 5.4.x includes every hook we need.  

2. **Exported `kallsyms_lookup_name()`**  
   - Up through 5.6 the symbol remains exported.  
   - Allows us to resolve `sys_call_table` at runtime--no kernel recompilation.  

3. **Unsigned module loading**  
   - Kernels ≥ 5.7 often enforce module signatures or Secure Boot lockdown.  
   - Ubuntu 20.04’s 5.4.x ships without these restrictions by default.  

---

## Alternative Options

- **Kernel 5.6**  
  - Technically compatible, but lacks LTS support.  
- **Debian 11 (5.10.x)**  
  - Still exports our symbols--requires disabling module signature checks.  

{{< alert context="warning" icon="warning" text="If we experiment on kernels ≥ 5.7, we need to append `module.sig_enforce=0` to the boot parameters to allow unsigned modules." />}}

