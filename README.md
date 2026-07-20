# OpenShip Skill for Hermes

**Autonomous Deployment & Infrastructure Management**

A production-ready skill that empowers Hermes (and compatible agents) to manage the entire OpenShip infrastructure lifecycle — end to end.

![OpenShip Skill for Hermes Banner](assets/banner.png)

## What This Skill Does

This skill turns Hermes into a capable OpenShip operator. Once installed, the agent can:

- Install and bootstrap OpenShip (CLI, Docker Compose, desktop, remote servers)
- Create and link projects
- Deploy applications with zero configuration
- Manage domains, automatic SSL (Let’s Encrypt), and CDN
- Handle databases, workers, backups, and mail
- Perform rollbacks, monitor health, and troubleshoot
- Work with production, preview, and staging environments
- Use CLI, REST API, and MCP interfaces

It is designed for full autonomy: the agent verifies state, executes multi-step workflows, reports outcomes, and only escalates when human input is required (e.g., DNS at the registrar).

## Quick Install for Hermes

1. Clone or download this repository.
2. Copy `SKILL.md` into your Hermes skills directory:

```bash
# Example location (adjust to your Hermes setup)
mkdir -p ~/.hermes/skills/openship
cp SKILL.md ~/.hermes/skills/openship/
```

3. Restart or reload Hermes skills if required.
4. The skill activates automatically on relevant requests (deploy, OpenShip, infrastructure management, etc.).

You can also point Hermes at this repository if your setup supports remote skill loading.

## Repository Structure

```
.
├── SKILL.md              # The skill itself (Hermes-ready)
├── README.md             # This file
├── LICENSE               # License
└── assets/
    └── banner.png        # Promotional banner
```

## Sources & Credits

This skill was created by thoroughly analyzing the official OpenShip project:

- **Primary source**: [oblien/openship](https://github.com/oblien/openship) (Apache-2.0)
- Official website: [openship.io](https://openship.io)
- Documentation: [openship.io/docs](https://openship.io/docs)
- CLI & installation references from the project README, CONTRIBUTING.md, and live documentation
- Architecture details drawn from the monorepo structure (apps/, packages/, Turborepo + Bun + pnpm)
- MCP support and agent protocol mentions from the project description

**Important**: This is an independent community skill. It is not officially affiliated with or endorsed by the OpenShip / Oblien team. All trademarks and project names belong to their respective owners.

OpenShip itself is an excellent open-source, self-hostable deployment platform that provides a Vercel-like experience on your own infrastructure. Strongly recommended.

## Keeping the Skill Updated

OpenShip is actively developed. Recommended practice:

- Watch the [oblien/openship releases](https://github.com/oblien/openship/releases)
- After significant releases, review changelog / new CLI commands / docs
- Update only durable operational knowledge (new commands, major features, authentication changes)
- Bump the version note inside `SKILL.md` metadata when you sync

A lightweight monitoring approach (weekly check or release notifications) is usually sufficient.

## License

This skill repository is released under the same spirit as OpenShip. See the `LICENSE` file.

The underlying OpenShip project remains under its original Apache-2.0 license.

## Author

Created and maintained by [Rishflips](https://github.com/Rishflips) (@rishflips).

Built for the Hermes agent ecosystem and the broader open agent community.

---

**Let Hermes handle the heavy lifting. You focus on building.**
