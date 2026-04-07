# Phase 1: System Updates

## Commands

```bash
DEBIAN_FRONTEND=noninteractive apt-get update -qq
DEBIAN_FRONTEND=noninteractive apt-get upgrade -y -qq

# Enable automatic security updates
apt install -y unattended-upgrades apt-listchanges

cat > /etc/apt/apt.conf.d/50unattended-upgrades << 'EOF'
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
};
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "04:00";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
EOF

systemctl enable unattended-upgrades
```

## Verify

```bash
apt list --upgradable 2>/dev/null | wc -l  # should be 1 (just the header)
systemctl is-active unattended-upgrades    # should be active
```
