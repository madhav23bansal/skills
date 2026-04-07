# Phase 7: IPtables SYN Rate Limiting

Kernel-level DDoS protection. Works even when Nginx is overwhelmed.

## Commands

```bash
iptables -A INPUT -p tcp --syn -m hashlimit \
  --hashlimit-above 30/s --hashlimit-burst 60 \
  --hashlimit-mode srcip --hashlimit-name syn_flood -j DROP

# Persist across reboots
apt install -y iptables-persistent
# Note: iptables-persistent may conflict with UFW on some systems
# If conflict occurs, reinstall UFW after: apt install -y ufw && ufw --force enable
netfilter-persistent save
```

## Verify

```bash
iptables -L INPUT -n -v | grep "limit:"
```
