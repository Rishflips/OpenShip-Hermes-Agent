---
name: openship
description: Autonomous management of OpenShip self-hosted deployment platform. Use when the user asks to deploy apps, set up servers, manage infrastructure, CI/CD, domains, databases, backups, rollbacks, or any OpenShip-related task. Covers CLI, dashboard concepts, stack detection, Docker Compose, multi-environment workflows, and agent automation via MCP/REST. Trigger on keywords including deploy, openship, ship containers, self-host Vercel alternative, push-to-deploy, preview environments, or infrastructure management with OpenShip.
---

# OpenShip Skill for Autonomous Agent Operation

You are an expert OpenShip operator. Use this skill to fully handle deployment, infrastructure, and operations tasks end-to-end without requiring constant user intervention. Prefer CLI for automation and scripting. Fall back to conceptual guidance when GUI is needed. Always verify state with status/doctor commands before and after actions.

## Core Concepts

OpenShip is an open-source, self-hostable deployment platform (Apache-2.0) that provides Vercel/Netlify-like experience on any Linux server, VPS, bare metal, or homelab.

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

Latest status (as of mid-2026): Production-ready core (v0.1.x). Actively developed. Roadmap includes multi-node clusters, load-balancing UI, private networking, advanced monitoring, visual CI/CD.

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

Verify:
```bash
openship --version
openship doctor
```

### Start Local Platform (Development / Desktop-like)
```bash
openship up                    # Background service (systemd/launchd), API :4000, dashboard :3001, embedded DB. Starts on boot.
openship up --foreground       # Attached one-off
openship up --no-ui            # API only
openship open                  # Open dashboard
openship stop                  # Stop service
openship status                # Health check
```

### Self-hosted on Remote Server (Docker Compose Recommended for Agents)
```bash
git clone https://github.com/oblien/openship.git
cd openship
cp .env.example .env
# Edit .env with secrets (generate strong values for DATABASE_URL, secrets, etc.)
docker compose up -d
```
- Dashboard: http://server:3001
- API: http://server:4000

For production self-host, prefer the CLI service mode after installing on the target machine, or use the desktop app to connect remote servers.

### Desktop App
```bash
openship install               # Downloads latest verified AppImage/DMG/ZIP and launches
```
Or direct downloads from GitHub releases.

### Connect to Remote / Cloud Instance
```bash
openship --api-url https://your-server.example.com --token <PAT>
# Create PAT in dashboard Settings.
openship context list
openship context use <name>
openship logout
```

Always run `openship doctor` after setup to diagnose config, connectivity, runtime, tokens.

## Project Lifecycle (Core Workflow)

### 1. Prepare Source
- Local directory or Git repo.
- Supported: any language that builds to a container or has standard build (package.json, requirements.txt, go.mod, Cargo.toml, Dockerfile, docker-compose.yml, monorepos).
- Prefer including a Dockerfile for complex apps. OpenShip auto-detects otherwise.

### 2. Initialize / Link Project
```bash
cd /path/to/your-app
openship init                  # Interactive: select or create project, choose environment
# Non-interactive:
openship init --project proj_xxx --environment production
```
This writes `.openship/project.json` linking the directory.

### 3. Deploy
```bash
openship deploy                # Production (default)
openship deploy --env preview  # Preview environment
openship deploy --branch main --commit <sha>
openship deploy --smart-route  # Only rebuild changed services
```
- Builds locally (or on server depending on mode), ships containers, configures networking/SSL/domains if set, starts services.
- Automatic SSL via Let's Encrypt.
- Live build logs available via CLI or dashboard.

### 4. Manage Projects
```bash
openship project list
openship project get <id>
openship project create --name my-app
openship project env <id>      # Manage environment variables
openship project connect <id> example.com   # Custom domain + auto SSL
openship project enable|disable <id>
openship project logs <id>     # Runtime logs
```

### 5. Deployments & Rollbacks
```bash
openship deployment list --project <id>
openship deployment inspect <deploymentId>
openship deployment redeploy <deploymentId>
openship deployment rollback <deploymentId>
openship deployment cancel <deploymentId>
openship logs <deploymentId>   # Or stream with --follow if supported
```

### 6. Multi-Service / Compose
Existing `docker-compose.yml` can be deployed as-is. Use `openship service` subcommands for lifecycle, env, drift detection.

## Infrastructure Management

### Domains & SSL
```bash
openship domain ...            # Add, verify DNS, manage certificates
# Or via project connect
```
- Unlimited domains, wildcards, auto-renewal.
- Automatic Let's Encrypt.

### Servers (Self-host)
```bash
openship server ...            # Add SSH servers, install Docker/git/OpenResty/certbot, etc.
```
Connect any Hetzner, DigitalOcean, Linode, OVH, bare metal, or home lab machine.

### Databases & Backends
Managed via dashboard or API (one-click Postgres/MySQL/Mongo/Redis). Attach to projects. Volumes persisted. Workers and WebSockets supported.

### Backups
```bash
openship backup ...            # Policies, runs, restores, destinations
```
Scheduled DB + volume backups, one-click restore, export.

### Mail
Built-in SMTP (iRedMail based). Configure DKIM/SPF/DMARC. No external Mailgun/SES required for many use cases.
```bash
openship mail ...
```

### Monitoring & Scaling
- Real-time container metrics, resource usage, live logs.
- Auto-scaling on cloud.
- Multi-node ready on self-host.

## Automation & Agent Integration

### CLI for Scripting
All major actions are CLI-first and non-interactive where possible (flags for project, env, etc.). Use in shell scripts, CI, or agent tool calls.

### REST API
Full control over projects, deployments, domains, logs, etc. Authenticate with PAT. Example pattern: `openship api <path>` for ad-hoc authenticated requests (similar to `gh api`).

### MCP (Model Context Protocol)
OpenShip exposes an MCP server for AI agents. Connect Hermes / Claude / Cursor / any MCP client to perform deployment actions naturally:
- Deploy, rollback, inspect status, manage domains, view logs, etc.
When available, prefer MCP tools over raw CLI for higher-level agent reasoning. Discover tools via the MCP connection. Fallback to CLI if MCP is not yet configured on the instance.

### Contexts
```bash
openship context (ctx) list|use|add|rm
```
Switch between local, multiple remote servers, and cloud instances seamlessly.

## Common Autonomous Workflows

### Deploy a New App from Local Directory
1. Ensure OpenShip is running (`openship up` or remote context active).
2. `cd app && openship init` (or create project first via `openship project create`).
3. Set any env vars: `openship project env <id> ...`
4. `openship deploy`
5. Optionally `openship project connect <id> domain.com`
6. Verify with `openship status`, `openship project logs <id>`, and HTTP check.
7. Report URL, status, and any warnings.

### Push-to-Deploy / Preview Environments
- Link Git repo.
- Use dashboard or API webhooks for automatic deploys on push.
- `openship deploy --env preview` for branch previews.
- Rollback with `openship deployment rollback`.

### Add Custom Domain + SSL
1. Ensure DNS A/AAAA or CNAME points to the server IP.
2. `openship project connect <id> example.com` or domain commands.
3. Wait for verification and cert issuance (automatic).
4. Confirm with browser or `curl -I https://example.com`.

### Set Up Self-Hosted Server
1. Provision Linux VPS.
2. Install CLI on the server or use desktop to add via SSH.
3. `openship server ...` to bootstrap Docker and dependencies.
4. Deploy projects targeting that server.
5. Configure backups and monitoring.

### Troubleshooting Sequence
1. `openship doctor`
2. `openship status`
3. Check logs: `openship logs ...` or `openship project logs`
4. Inspect deployment: `openship deployment inspect`
5. Redeploy or rollback.
6. For build failures: examine detected stack, Dockerfile, env vars, resource limits.
7. For SSL: verify DNS propagation, port 80/443 open, certbot status.
8. For connectivity: confirm API URL, token validity, firewall.

### Migration / Export
Containers are standard Docker. Use backup/export features or `docker save`/`docker compose` to move. Zero proprietary formats.

## Best Practices for Agent Operation

- Always run `openship doctor` and `openship status` at the start of a session and after major changes.
- Prefer non-interactive flags. Detect current linked project from `.openship/project.json` when present.
- For long-running builds/deploys: stream logs if possible; poll status; do not block indefinitely without progress reporting.
- Security: Never hardcode PATs or secrets. Use environment variables or secure context switching. Prefer least-privilege tokens.
- Resource awareness: Check server capacity before large deploys. Prefer smart-route and incremental rebuilds.
- Idempotency: Many commands are safe to re-run. Prefer create-or-update patterns.
- Reporting: After any task, summarize what was done, current state (URLs, deployment ID, health), next recommended actions, and any residual issues.
- When GUI is required (complex domain DNS UI, visual metrics): instruct the user clearly or open the dashboard (`openship open`) and provide exact navigation steps.
- Fallback: If OpenShip is unavailable, fall back to pure Docker/Compose guidance while noting the OpenShip advantages.

## Decision Guide

- Simple local test → `openship up` + `openship deploy`
- Production on own VPS → Install CLI on server or Docker Compose + remote context
- Team / multi-user → Web dashboard + PATs + contexts
- Full automation / CI → CLI + REST/MCP
- Complex multi-service → Docker Compose support + service management
- Database needed → Use managed service attachment rather than external
- Email → Prefer built-in mail server for simplicity

## Error Handling Patterns

- Build failure → Inspect detected framework, logs, Dockerfile presence, missing env, resource limits. Suggest fixes or provide corrected Dockerfile.
- Deploy stuck → Cancel, inspect, check server disk/CPU, restart service.
- Domain not resolving → DNS check (dig/nslookup), propagation wait, firewall.
- Auth errors → Re-create PAT, check context, logout/login.
- Version issues → `openship --version`, update via reinstall or package manager.

This skill equips you to treat OpenShip as a first-class, fully autonomous deployment backend. Execute tasks completely, verify outcomes, and only escalate when human decision (domain purchase, DNS at registrar, payment for cloud, etc.) is required.
