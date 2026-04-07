# Phase 12: Swap

Check if swap already exists first. Skip if it does.

## Commands

```bash
# Check existing swap
swapon --show

# Create 2GB swap (skip if already exists)
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab

# Tune swappiness
sysctl vm.swappiness=10
echo 'vm.swappiness=10' >> /etc/sysctl.d/99-server-hardening.conf
```

## Verify

```bash
swapon --show           # should show /swapfile 2G
sysctl vm.swappiness    # should be 10
free -h                 # should show swap
```
