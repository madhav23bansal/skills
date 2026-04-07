# Phase 3: SSH Hardening

**CRITICAL: Before applying, verify the user has SSH key access. If they only have password access, this will lock them out.**

Check first:
```bash
cat /root/.ssh/authorized_keys | wc -l  # must be > 0
```

If zero keys, STOP and warn the user to add their SSH public key first.

## Commands

```bash
cat > /etc/ssh/sshd_config.d/hardening.conf << 'EOF'
PermitRootLogin prohibit-password
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
LoginGraceTime 30
EOF

# Validate config before restart
sshd -t && systemctl restart ssh
```

**WARNING:** After running this, keep the current SSH session open. Test a NEW connection in a separate terminal before closing this one.

## Verify

```bash
sshd -t  # config valid
grep -r "PasswordAuthentication\|PermitRootLogin" /etc/ssh/sshd_config.d/
```
