---
name: openship
description: Autonomous management of OpenShip self-hosted deployment platform. Use when the user asks to deploy apps, set up servers, manage infrastructure, CI/CD, domains, databases, backups, rollbacks, or any OpenShip-related task. Covers CLI, dashboard concepts, stack detection, Docker Compose, multi-environment workflows, and agent automation via MCP/REST. Trigger on keywords including deploy, openship, ship containers, self-host Vercel alternative, push-to-deploy, preview environments, or infrastructure management with OpenShip.
---

# OpenShip Skill for Autonomous Agent Operation

You are an expert OpenShip operator. Use this skill to fully handle deployment, infrastructure, and operations tasks end-to-end without requiring constant user intervention. Prefer CLI for automation and scripting. Fall back to conceptual guidance when GUI is needed. Always verify state with status/doctor commands before and after actions.

## Core Concepts

OpenShip is an open-source, self-hostable deployment platform (Apache-2.0) that provides a Vercel/Netlify-like experience on any Linux server, VPS, bare metal, or homelab.

- **Zero-config philosophy**: Point at a Git repo or local directory. Automatic stack detection (Node, Python, Go, Rust, PHP, Ruby, Java, .NET, Docker, monorepos). Builds, configures, and deploys without YAML pipelines or config files.
- **Interfaces**:
  - CLI (`openship`) — primary for agents and automation.
  - Desktop app — GUI with real-time logs.
  - Web dashboard — browser UI (local or remote).
  - REST API + MCP (Model Context Protocol) — for AI agents and external tooling.
- **Deployment modes**:
  - Local (desktop/CLI builds on your machine, ships only final containers).
  - Self-hosted on any Linux server (Docker-based).
  - Openship Cloud (managed, powered by Oblien infra).
- **Full-stack services** (one-click or managed): Postgres, MySQL, MongoDB, Redis, workers, WebSockets, object storage, built-in SMTP (DKIM/SPF/DMARC), CDN (edge caching, HTTP/3, Brotli), automatic Let's Encrypt SSL (wildcards, unlimited domains), scheduled backups + one-click restore, monitoring (live logs, metrics), auto-scaling (cloud) / multi-node ready (self-host).
- **Environments**: production (default), preview (per branch/PR), staging.
- **Portability**: Standard Docker containers. Export and migrate freely. No lock-in.
- **Architecture**: Monorepo with Turborepo + pnpm + Bun. Apps: `api` (Hono), `dashboard` (Next.js), `web` (marketing), `cli`, `desktop`. Packages: `adapters` (DockerAdapter for self-host, OblienAdapter for cloud), `core`, `db` (Drizzle), `ui`.

**Latest status (as of 2026-07-21)**: Production-ready core on **v0.2.x**. Actively developed (5.6k+ stars). Roadmap includes multi-node clusters, load-balancing UI, private networking, advanced monitoring, and visual CI/CD pipelines.

Official resources:
- Repo: https://github.com/oblien/openship
- Docs: https://openship.io/docs
- Site: https://openship.io
- Install script: https://get.openship.io

## Installation & Bootstrap (Always Start Here)

### System Requirements (Self-hosted Server)
- CPU: 2+ cores (4+ recommended)
- RAM: 2 GB min (4+ GB recommended)
- Disk: 20 GB min (50+ GB SSD recommended)
- OS: Ubuntu 22.04+ (24.04 preferred). Any modern Linux with Docker.

### Install CLI (Agent Preference)
```bash
curl -fsSL https://get.openship.io | sh
# Alternatives:
# npm i -g openship
# pnpm add -g openship
# bun add -g openship
```

Verify and keep updated:
```bash
openship --version
openship doctor
openship update          # update CLI + server components
```

### Start Local Platform
```bash
openship up                    # Background service (systemd/launchd), API :4000, dashboard :3001. Starts on boot.
openship up --foreground       # Attached one-off
openship up --no-ui            # API only
openship open                  # Open dashboard
openship stop                  # Stop service
openship status                # Health check
```

### Self-hosted on Remote Server (Docker Compose)
```bash
git clone https://github.com/oblien/openship.git
cd openship
cp .env.example .env
# Edit .env with secrets
docker compose up -d
```
- Dashboard: http://server:3001
- API: http://server:4000

### Desktop App
```bash
openship install               # Downloads latest verified AppImage/DMG/ZIP and launches
openship install cache         # Manage local download cache (list / verify / clean)
```

### Authentication & Contexts (Important for Agents)
```bash
openship login                 # Interactive or with --token <opsh_pat_...>
openship logout
openship token                 # Manage Personal Access Tokens

# Named connection contexts (recommended for multi-server / cloud)
openship context list          # or: openship ctx ls
openship context use <name>
openship context add <name> --api-url https://... --token <pat> --use
openship context rm <name>
```
Always run `openship doctor` after setup or context switches.

## Project Lifecycle (Core Workflow)

### 1. Prepare Source
Local directory or Git repo. Supports any language that builds to a container or has standard build files. Prefer a Dockerfile for complex apps.

### 2. Initialize / Link Project
```bash
cd /path/to/your-app
openship init                  # Interactive
openship init --project proj_xxx --environment production
```
Writes `.openship/project.json`.

### 3. Deploy
```bash
openship deploy                # Production (default)
openship deploy --env preview
openship deploy --branch main --commit <sha>
openship deploy --smart-route  # Rebuild only changed services
```

### 4. Manage Projects
```bash
openship project list
openship project get <id>
openship project create --name my-app
openship project env <id>
openship project connect <id> example.com
openship project enable|disable <id>
openship project logs <id>
```

### 5. Deployments & Rollbacks
```bash
openship deployment list --project <id>
openship deployment inspect <deploymentId>
openship deployment redeploy <deploymentId>
openship deployment rollback <deploymentId>
openship deployment cancel <deploymentId>
openship logs <deploymentId>
```

### 6. Multi-Service / Compose
Existing `docker-compose.yml` deploys as-is. Use `openship service` subcommands for lifecycle, env, and drift.

## Infrastructure Management

### Domains & SSL
```bash
openship domain ...
# or via: openship project connect <id> example.com
```
Unlimited domains, wildcards, automatic Let's Encrypt + renewal.

### Servers (Self-host)
```bash
openship server ...            # Add SSH servers, bootstrap Docker/git/OpenResty/certbot
```

### Databases & Backends
Managed via dashboard or API (Postgres, MySQL, MongoDB, Redis). Volumes persisted.

### Backups
```bash
openship backup ...            # Policies, runs, restores, destinations
```

### Mail
```bash
openship mail ...              # Built-in SMTP (DKIM/SPF/DMARC)
```

### System / Instance
```bash
openship system ...            # Instance settings, onboarding, migration, data transfer
```

### Monitoring & Scaling
Real-time metrics, live logs. Auto-scaling on cloud, multi-node ready on self-host.

## Automation & Agent Integration

- Prefer non-interactive CLI flags and `--json` where available.
- Use `openship api <path>` for direct authenticated API calls (similar to `gh api`).
- MCP server is available for higher-level agent tool use. Prefer MCP when connected; fall back to CLI.
- Always verify with `openship status` + `openship doctor` before and after major actions.

## Common Autonomous Workflows

**Deploy new app**  
1. Ensure platform is running / correct context is active.  
2. `openship init` → set env vars → `openship deploy`.  
3. Optionally connect domain.  
4. Verify status + logs + HTTP.

**Preview / PR environments**  
`openship deploy --env preview`

**Custom domain + SSL**  
Ensure DNS points correctly → `openship project connect <id> domain.com` → wait for cert.

**Self-hosted server bootstrap**  
Provision VPS → `openship server ...` → deploy projects → configure backups.

**Troubleshooting sequence**  
1. `openship doctor`  
2. `openship status`  
3. Logs / `deployment inspect`  
4. Redeploy or rollback  
5. Check DNS / firewall / resources for SSL or connectivity issues.

## Best Practices for Agent Operation

- Always start with `openship doctor` and `openship status`.
- Prefer named contexts over ad-hoc flags for multi-environment work.
- Never hardcode PATs. Use `openship login` / context system.
- Prefer `--smart-route` and incremental deploys when possible.
- After any task: report what was done, current URLs/IDs/health, and next recommended steps.
- Escalate only when human action is required (DNS at registrar, domain purchase, cloud billing, etc.).

## Decision Guide

- Simple local test → `openship up` + `openship deploy`
- Production on own VPS → CLI service mode or Docker Compose + remote context
- Team / multi-user → Web dashboard + PATs + contexts
- Full automation → CLI + REST / MCP
- Complex multi-service → Docker Compose support + `openship service`
- Database / mail / backups → Use built-in managed services

This skill equips you to treat OpenShip as a first-class, fully autonomous deployment backend. Execute tasks completely, verify outcomes, and only escalate when human decision is required.
