# Phase 6: Sysctl Hardening

## Commands

```bash
cat > /etc/sysctl.d/99-server-hardening.conf << 'EOF'
# TCP tuning
net.ipv4.tcp_fin_timeout = 15
net.ipv4.tcp_keepalive_time = 300
net.ipv4.tcp_keepalive_intvl = 30
net.ipv4.tcp_keepalive_probes = 5

# DDoS protection
net.core.netdev_max_backlog = 50000
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65536
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1

# BBR congestion control
net.core.default_qdisc = fq
net.ipv4.tcp_congestion_control = bbr

# Anti-spoofing
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
EOF

modprobe tcp_bbr
echo tcp_bbr >> /etc/modules-load.d/bbr.conf
sysctl -p /etc/sysctl.d/99-server-hardening.conf
```

## Verify

```bash
sysctl net.ipv4.tcp_congestion_control  # should be bbr
sysctl net.ipv4.tcp_fin_timeout         # should be 15
sysctl net.ipv4.conf.all.rp_filter      # should be 1
```
