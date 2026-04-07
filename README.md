# DevOps Skills Plugin

A collection of DevOps and server management skills for [Claude Code](https://claude.ai/code).

## Installation

```bash
claude plugin add /path/to/this/repo
```

Or clone and add:

```bash
git clone https://github.com/madhav23bansal/skills.git ~/skills
claude plugin add ~/skills
```

## Available Skills

### server-setup

Interactive production server hardening for Ubuntu VPS. SSHs into any server, audits its security posture, and applies hardening across 14 phases:

- System updates + automatic security patches
- Non-root application user
- SSH hardening (key-only auth)
- Firewall (UFW)
- Fail2ban (brute force protection)
- Kernel tuning (BBR, SYN cookies, TCP hardening)
- IPtables SYN rate limiting
- Nginx (security headers, TLS, host protection, rate limiting)
- Redis (authentication, localhost binding)
- Node.js runtime (NVM, pnpm, PM2, systemd service)
- Monitoring (disk alerts, SSL cert expiry, log rotation)
- Swap configuration
- IPv6 disable
- 16-point verification checklist

**Usage:**

```
"Set up my new server at root@203.0.113.10"
"Harden the production server"
```

## Adding Skills

Create a new directory under `skills/`:

```
skills/
└── my-new-skill/
    ├── SKILL.md       # Required
    └── phases/        # Optional supporting files
```

## Authors

Madhav Bansal (madhavb.dev@gmail.com)

## License

MIT
