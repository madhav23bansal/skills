---
name: server-setup
description: Interactive production server hardening for Ubuntu VPS. SSH into a server, audit its security state, and apply hardening (SSH, firewall, fail2ban, sysctl, Nginx, Redis, Node.js, monitoring) step by step. Use when setting up a new VPS or hardening an existing one.
license: MIT
---

# Production Server Setup & Hardening

You are an expert Linux systems administrator. Your job is to interactively harden a production Ubuntu server via SSH.

## How This Works

1. Connect to the server via SSH
2. Run a comprehensive audit of the current state
3. Present findings and ask the user which phases to run
4. Execute selected phases one at a time, confirming each
5. Run a final verification

## Step 1: Connect and Audit

SSH target is provided as `$ARGUMENTS`. If not provided, ask the user:
- Server IP or hostname
- SSH user (default: `root`)
- SSH port (default: `22`)

Once connected, run the audit script to check the current state of the server. Run each command via SSH individually:

```
# System info
hostname && cat /etc/os-release | grep PRETTY_NAME && uptime

# Current user
whoami

# SSH config
grep -E "PermitRootLogin|PasswordAuthentication" /etc/ssh/sshd_config* 2>/dev/null | grep -v "^#"

# Firewall
ufw status 2>/dev/null || iptables -L -n 2>/dev/null | head -20

# Fail2ban
systemctl is-active fail2ban 2>/dev/null || echo "not installed"

# Auto-updates
systemctl is-active unattended-upgrades 2>/dev/null || echo "not configured"

# Time sync
systemctl is-active chrony 2>/dev/null || systemctl is-active systemd-timesyncd 2>/dev/null || echo "not configured"

# Swap
swapon --show 2>/dev/null || echo "no swap"

# Users
awk -F: '$3 >= 1000 && $3 < 65534 {print $1}' /etc/passwd

# Running services on ports
ss -tlnp | grep LISTEN

# Nginx
nginx -v 2>/dev/null && echo "installed" || echo "not installed"

# Redis
redis-cli ping 2>/dev/null && echo "no auth" || redis-cli -a test ping 2>/dev/null || echo "not installed"

# Docker
docker --version 2>/dev/null || echo "not installed"

# Node.js
node --version 2>/dev/null || echo "not installed"

# PM2
pm2 --version 2>/dev/null || echo "not installed"

# Sysctl values
sysctl net.ipv4.tcp_fin_timeout net.ipv4.tcp_keepalive_time net.core.netdev_max_backlog net.ipv4.tcp_congestion_control net.ipv4.conf.all.rp_filter 2>/dev/null

# Disk and memory
df -h / | tail -1
free -h | head -2

# Pending updates
apt list --upgradable 2>/dev/null | wc -l

# AppArmor
aa-status 2>/dev/null | head -3 || echo "not running"
```

## Step 2: Present Findings

After the audit, present a summary table showing:

| Category | Current State | Recommended | Status |
|----------|--------------|-------------|--------|
| OS Updates | X pending | All updated | ... |
| Auto-updates | enabled/disabled | enabled | ... |
| SSH | password/key-only | key-only | ... |
| Firewall | active/inactive | active | ... |
| ... | ... | ... | ... |

Use these status indicators:
- GOOD: Already properly configured
- NEEDS FIX: Needs attention
- MISSING: Not installed/configured

## Step 3: Ask What to Set Up

Present the available phases as a numbered list. Ask the user which phases to run. They can say "all", specific numbers, or "skip" individual items.

**Available Phases:**

1. **System Updates** - Apply pending updates, enable auto-security-updates
2. **Application User** - Create non-root user for running apps
3. **SSH Hardening** - Disable password auth, limit retries
4. **Firewall (UFW)** - Configure firewall rules
5. **Fail2ban** - Brute force protection
6. **Sysctl Hardening** - Kernel network tuning, BBR, SYN protection
7. **IPtables SYN Rate Limiting** - Kernel-level DDoS protection
8. **Nginx** - Install, harden, security headers, TLS
9. **Redis** - Install, set password, bind localhost
10. **Node.js Runtime** - NVM, Node, pnpm, PM2 (under app user)
11. **Monitoring** - Disk alerts, cert monitoring, log rotation
12. **Swap** - Configure swap space
13. **Disable IPv6** - Reduce attack surface (if not needed)
14. **Verification** - Run all security checks

Ask: "Which phases do you want to run? (e.g., 'all', '1,3,5,6', or 'all except 13')"

## Step 4: Execute Phases

For each selected phase, read the corresponding phase file from the `phases/` directory for detailed instructions. The phase files are at:

- `${CLAUDE_SKILL_DIR}/phases/01-system-updates.md`
- `${CLAUDE_SKILL_DIR}/phases/02-app-user.md`
- `${CLAUDE_SKILL_DIR}/phases/03-ssh-hardening.md`
- `${CLAUDE_SKILL_DIR}/phases/04-firewall.md`
- `${CLAUDE_SKILL_DIR}/phases/05-fail2ban.md`
- `${CLAUDE_SKILL_DIR}/phases/06-sysctl.md`
- `${CLAUDE_SKILL_DIR}/phases/07-iptables.md`
- `${CLAUDE_SKILL_DIR}/phases/08-nginx.md`
- `${CLAUDE_SKILL_DIR}/phases/09-redis.md`
- `${CLAUDE_SKILL_DIR}/phases/10-nodejs.md`
- `${CLAUDE_SKILL_DIR}/phases/11-monitoring.md`
- `${CLAUDE_SKILL_DIR}/phases/12-swap.md`
- `${CLAUDE_SKILL_DIR}/phases/13-disable-ipv6.md`
- `${CLAUDE_SKILL_DIR}/phases/14-verification.md`

### Execution Rules

1. **Always show what you're about to do before doing it.** Present commands in a table:

| Step | Command | Purpose |
|------|---------|---------|
| 1 | `apt update && apt upgrade -y` | Apply updates |
| 2 | ... | ... |

2. **Ask for confirmation** before executing each phase: "Ready to apply Phase X? (y/n)"

3. **Show before/after** for each change:

| Setting | Before | After |
|---------|--------|-------|
| PasswordAuthentication | yes | **no** |

4. **Never run destructive commands** without explicit confirmation
5. **All SSH commands** must use the connection details from Step 1
6. **If any command fails**, stop and diagnose before continuing
7. **Always verify** each phase worked before moving to the next

## Step 5: Final Summary

After all phases complete, show a comprehensive summary:

| Phase | Status | Details |
|-------|--------|---------|
| System Updates | DONE | 23 packages updated |
| SSH Hardening | DONE | Key-only, MaxAuthTries=3 |
| ... | ... | ... |

And remind the user of any manual follow-up tasks (e.g., DNS records, backup setup, etc.)
