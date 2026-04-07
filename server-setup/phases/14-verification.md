# Phase 14: Final Verification

Run all checks and present results in a single table.

## Commands

Run each check via SSH and collect results:

```bash
echo "=== 1. SSH password auth ==="
grep -r "PasswordAuthentication" /etc/ssh/sshd_config.d/ 2>/dev/null | grep -v "^#"

echo "=== 2. Firewall ==="
ufw status | head -1

echo "=== 3. Fail2ban ==="
fail2ban-client status 2>/dev/null | head -2

echo "=== 4. Auto-updates ==="
systemctl is-active unattended-upgrades

echo "=== 5. Time sync ==="
chronyc tracking 2>/dev/null | grep "Leap status" || timedatectl | grep "synchronized"

echo "=== 6. Swap ==="
swapon --show | tail -1

echo "=== 7. TCP congestion ==="
sysctl net.ipv4.tcp_congestion_control | awk '{print $3}'

echo "=== 8. SYN cookies ==="
sysctl net.ipv4.tcp_syncookies | awk '{print $3}'

echo "=== 9. Reverse path filter ==="
sysctl net.ipv4.conf.all.rp_filter | awk '{print $3}'

echo "=== 10. Nginx ==="
nginx -v 2>&1 && curl -sI localhost 2>/dev/null | grep Server

echo "=== 11. Redis auth ==="
redis-cli ping 2>&1

echo "=== 12. App user ==="
ps aux | grep -E "node|python|java" | grep -v grep | awk '{print $1}' | sort -u

echo "=== 13. Disk ==="
df -h / | tail -1 | awk '{print $5}'

echo "=== 14. Pending updates ==="
apt list --upgradable 2>/dev/null | grep -c upgradable

echo "=== 15. AppArmor ==="
aa-status 2>/dev/null | head -1 || echo "not running"

echo "=== 16. Open ports ==="
ss -tlnp | grep LISTEN | awk '{print $4}' | sort
```

## Present Results

Show a table like:

| # | Check | Status | Value |
|---|-------|--------|-------|
| 1 | SSH password auth | PASS/FAIL | disabled/enabled |
| 2 | Firewall | PASS/FAIL | active/inactive |
| 3 | Fail2ban | PASS/FAIL | X jails active |
| 4 | Auto-updates | PASS/FAIL | active/inactive |
| 5 | Time sync | PASS/FAIL | synced/not synced |
| 6 | Swap | PASS/FAIL | XG configured |
| 7 | BBR congestion | PASS/FAIL | bbr/cubic |
| 8 | SYN cookies | PASS/FAIL | enabled/disabled |
| 9 | Reverse path filter | PASS/FAIL | strict/loose |
| 10 | Nginx version hidden | PASS/FAIL | hidden/exposed |
| 11 | Redis auth | PASS/FAIL | required/open |
| 12 | App running as | INFO | root/app-user |
| 13 | Disk usage | PASS/WARN | X% |
| 14 | Pending updates | PASS/WARN | X packages |
| 15 | AppArmor | PASS/FAIL | loaded/not running |
| 16 | Open ports | INFO | list |

Calculate overall score: X/15 checks passed.
