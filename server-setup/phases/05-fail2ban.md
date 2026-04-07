# Phase 5: Fail2ban

Ask the user for their static IP to add to ignoreip (so they don't ban themselves).

## Commands

```bash
apt install -y fail2ban

cat > /etc/fail2ban/jail.local << 'EOF'
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 86400
findtime = 600
bantime.increment = true
bantime.factor = 2
bantime.maxtime = 604800
ignoreip = 127.0.0.1/8 ::1 <STATIC_IP>

[nginx-limit-req]
enabled = true
port = http,https
filter = nginx-limit-req
logpath = /var/log/nginx/error.log
backend = auto
maxretry = 5
bantime = 86400
findtime = 60
ignoreip = 127.0.0.1/8 ::1 <STATIC_IP>
EOF

systemctl enable fail2ban
systemctl restart fail2ban
```

## Verify

```bash
fail2ban-client status
fail2ban-client status sshd
```
