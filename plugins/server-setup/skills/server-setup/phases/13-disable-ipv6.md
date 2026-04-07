# Phase 13: Disable IPv6

Only apply if the server doesn't use IPv6. Check first.

## Pre-check

```bash
# Check if any service listens on IPv6
ss -tlnp | grep "\[::\]"
```

If services actively use IPv6, warn the user and skip.

## Commands

```bash
cat >> /etc/sysctl.d/99-server-hardening.conf << 'EOF'
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
EOF
sysctl --system
```

## Verify

```bash
cat /proc/sys/net/ipv6/conf/all/disable_ipv6  # should be 1
```
