# EpiRootkit Documentation

> Concise overview of EpiRootkit and its documentation site.

## TL;DR

EpiRootkit is a Linux kernel rootkit developed for Ubuntu 20.04 (kernel 5.4), featuring a Command‑and‑Control (C2) backend and a web‑based user interface.

It was created during my first year of engineering studies under the guidance of Jules Aubert, a goated professor of Advanced Linux Systems at EPITA.

## Overview

This repository contains the Hugo-based documentation site for the project. The docs explain how to set up, use, and understand the overall system:

- **Kernel Module (EpiRootkit)**: remote command execution, file transfer, authentication, XOR‑encrypted C2 traffic, DNS resolution, stealth features (module and file hiding), and persistence.
- **C2 Backend**: manages connected clients and routes commands.
- **Web UI**: graphical interface for monitoring clients and performing actions.

## Start here

- **Environment setup**: `content/docs/02-setup`
- **C2 backend**: `content/docs/03-attacking-program`
- **Web UI**: `content/docs/04-web-ui`
- **Rootkit**: `content/docs/05-epirootkit`

## Key sections

- **Rootkit features**: `content/docs/05-epirootkit/features`
- **Web UI features**: `content/docs/04-web-ui/features`
- **Web UI panels**: `content/docs/04-web-ui/features/panels`

## Live site

- Docs are published at: `https://epirootkit.com`

## Local development

This site uses Hugo Modules and the Lotus Docs theme.

### Requirements

- Hugo (extended) ≥ 0.142.0 (minimum supported by config: 0.100.0)
- Go (for Hugo Modules) — see `go.mod`

### Run the docs locally

```bash
# From the repo root
hugo server -D
```

Then open the local URL printed by Hugo (usually `http://localhost:1313`). Hot reload is enabled.

### Build the site

```bash
hugo --gc --minify
```

The static output is generated in `public/`.

## Deployment

GitHub Actions builds and deploys the site on pushes to `master`:

- Workflow: `.github/workflows/hugo.yaml`
- Build: Hugo (extended) 0.142.0 with `hugo --gc --minify`
- Publish branch: `gh-pages`
- Custom domain: `CNAME` → `epirootkit.com`

## Repository structure

- `content/docs/` — documentation content (chapters, features, panels)
- `layouts/` — theme/layout overrides if any
- `static/` — static assets copied as-is
- `hugo.toml` — site configuration (theme, menus, params)
- `.github/workflows/` — CI for build and deploy

## Team

Team: Tux Fan Club 🐧

Guidance: Jules Aubert (EPITA, Advanced Linux Systems)