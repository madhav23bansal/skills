# Claude Code Skills

A collection of reusable [Claude Code](https://claude.ai/code) skills for DevOps, security, and development workflows.

Compatible with [skills.sh](https://skills.sh) and the [Agent Skills](https://agentskills.io/specification) open standard.

## Installation

### Via npx (recommended)

```bash
npx skills add madhav23bansal/skills@server-setup
```

### Via Claude Code

```
/install-skill https://github.com/madhav23bansal/skills
```

### Manual

```bash
# Clone the repo
git clone https://github.com/madhav23bansal/skills.git ~/.claude/skills-repo

# Add to Claude Code
# In Claude Code, run:
/add-dir ~/.claude/skills-repo
```

## Available Skills

| Skill | Command | Description |
|-------|---------|-------------|
| [server-setup](server-setup/) | `/server-setup root@IP` | Interactive production server hardening for Ubuntu VPS |

## Skill Details

### server-setup

Interactive production server hardening. SSHs into any Ubuntu server, audits its current state, and applies security hardening in 14 phases:

1. System updates + auto-security-updates
2. Application user (non-root)
3. SSH hardening (key-only, rate limiting)
4. Firewall (UFW)
5. Fail2ban (brute force protection)
6. Sysctl hardening (BBR, SYN cookies, TCP tuning)
7. IPtables SYN rate limiting
8. Nginx (headers, TLS, rate limiting, host protection)
9. Redis (password, localhost binding)
10. Node.js runtime (NVM, pnpm, PM2, systemd)
11. Monitoring (disk alerts, cert expiry, log rotation)
12. Swap configuration
13. IPv6 disable (optional)
14. Final verification (16-point security check)

**Usage:**

```
/server-setup root@203.0.113.10
/server-setup deploy@myserver.com
```

## Adding a New Skill

1. Create a directory: `my-skill/`
2. Add `SKILL.md` with frontmatter (see template below)
3. Add supporting files in subdirectories (`phases/`, `templates/`, `scripts/`)
4. Update this README

### SKILL.md Template

```yaml
---
name: my-skill
description: What this skill does and when to use it. Front-load the key use case. Max 250 chars for optimal display.
disable-model-invocation: true
argument-hint: "[arg1] [arg2]"
allowed-tools: Bash Read AskUserQuestion
effort: high
license: MIT
compatibility: Requirements (e.g., Requires Docker, Python 3.8+)
metadata:
  author: your-github-username
  version: "1.0.0"
  tags: comma, separated, tags
---

# Skill Instructions

Your instructions for Claude go here...
```

### Directory Structure

```
skills/
├── README.md
├── .gitignore
├── LICENSE
├── my-skill/
│   ├── SKILL.md              # Required: skill definition
│   ├── phases/               # Optional: step-by-step phases
│   │   ├── 01-first.md
│   │   └── 02-second.md
│   ├── templates/            # Optional: output templates
│   └── scripts/              # Optional: helper scripts
└── another-skill/
    └── SKILL.md
```

## License

MIT
