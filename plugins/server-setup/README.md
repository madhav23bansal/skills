# Server Setup Plugin

Interactive production server hardening for Ubuntu VPS.

## What It Does

Claude SSHs into any Ubuntu server, audits its security posture, and applies hardening across 14 phases:

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

## Usage

```
"Set up my new server at root@203.0.113.10"
"Harden the production server at deploy@myserver.com"
```

Or invoke directly:

```
/server-setup root@203.0.113.10
```

## Requirements

- SSH access to an Ubuntu 22.04/24.04 server
