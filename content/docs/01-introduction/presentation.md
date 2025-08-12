---
title: "Presentation"
description: "Project overview, context, objectives, tech stack, team, and quick-start"
icon: "code"
date: "2025-05-07T00:44:31+01:00"
lastmod: "2025-08-12T00:00:00+01:00"
draft: false
toc: true
weight: 100
---

## Project Overview

EpiRootkit is a Linux kernel rootkit developed as part of an academic project. It demonstrates end‑to‑end concepts behind a Command & Control (C2) system:
- Kernel module running on a victim host (Ubuntu 20.04, kernel 5.4)
- C2 backend to manage clients and issue commands
- Web UI (frontend) to administer clients visually

This documentation explains the architecture, safety constraints, and provides a reproducible environment to explore the techniques in a controlled, ethical way.

## Contexte du projet

Projet réalisé dans un cadre scolaire. L’objectif est d’illustrer des mécanismes de bas niveau (module noyau, persistance, dissimulation) ainsi que l’implémentation d’une chaîne C2 complète (backend + interface Web), tout en respectant une démarche d’étude et de sécurité.

## Objectives

- Show a cohesive architecture from kernel space to UI
- Provide a local, reproducible setup for experimentation
- Document features and their limitations clearly
- Emphasize ethical use and responsible disclosure

## Scope (What this project is / is not)

- Is: Educational exploration of kernel‑level techniques and C2 orchestration
- Is not: A production‑grade or stealth‑perfect malware; not intended for real‑world unauthorized use

## Architecture at a glance

- Kernel module: communication, command execution, DNS resolution, stealth and persistence primitives
- C2 backend: issues commands, handles auth, aggregates client state
- Web UI: dashboards and panels for day‑to‑day operations

See: [EpiRootkit Overview](/docs/05-epirootkit/overview/), [C2 Backend](/docs/03-attacking-program/), and [Web UI](/docs/04-web-ui/).

## Tech Stack

- Kernel module: C (Ubuntu 20.04 / 5.4)
- Backend (C2): Node.js
- Frontend (UI): React
- Documentation: Hugo + Lotus Docs

## Quick start

- Setup: [Environment & VMs](/docs/02-setup/)
- Deploy: [Rootkit build & load](/docs/05-epirootkit/deployment/)
- Use: [Command reference](/docs/03-attacking-program/commands/), [Web UI panels](/docs/04-web-ui/features/panels/)

## Team

{{< crew >}}

## Ethics and Legal Notice

This project is for research and educational purposes only. Use exclusively in isolated environments you own and control, with explicit authorization. Any misuse is strictly discouraged and the authors disclaim responsibility for improper use.

{{< figure src="/images/introduction/alltuxes.png" alt="Tux" >}}