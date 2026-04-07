# Phase 4: Firewall (UFW)

Ask the user:
1. What static IP should be whitelisted for SSH? (or "any" to keep SSH open to all)
2. What app ports should be blocked on the public interface? (e.g., 3000, 3003, 8080)
3. Are there any additional ports to open? (e.g., 4318 for OTEL)

## Commands

```bash
ufw default deny incoming
ufw default allow outgoing

# SSH access
ufw allow from <STATIC_IP> to any port 22 proto tcp comment "SSH"
# OR if no static IP:
# ufw allow 22/tcp comment "SSH"

# Web traffic
ufw allow 80/tcp comment "HTTP"
ufw allow 443/tcp comment "HTTPS"

# Block direct app port access on public interface (if applicable)
# ufw deny in on eth0 to any port 3000 comment "Block direct app access"

# Additional ports (as requested)
# ufw allow <PORT>/tcp comment "<DESCRIPTION>"

ufw --force enable
```

## Verify

```bash
ufw status numbered
```
