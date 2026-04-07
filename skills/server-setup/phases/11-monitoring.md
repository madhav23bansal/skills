# Phase 11: Monitoring

Ask the user:
1. Slack webhook URL for alerts? (or skip alerting)
2. What domains to monitor SSL certs for?

## Commands

### Disk usage alerts

```bash
cat > /usr/local/bin/disk-alert.sh << 'SCRIPT'
#!/bin/bash
THRESHOLD=85
USAGE=$(df / | tail -1 | awk '{print $5}' | tr -d '%')
if [ "$USAGE" -gt "$THRESHOLD" ]; then
  curl -s -X POST -H 'Content-type: application/json' \
    --data "{\"text\":\"Disk usage at ${USAGE}% on $(hostname)\"}" \
    "$SLACK_WEBHOOK_URL"
fi
SCRIPT
chmod +x /usr/local/bin/disk-alert.sh

# Every 15 minutes
(crontab -l 2>/dev/null; echo "*/15 * * * * SLACK_WEBHOOK_URL='<WEBHOOK>' /usr/local/bin/disk-alert.sh") | crontab -
```

### SSL cert expiry monitoring

```bash
cat > /usr/local/bin/cert-check.sh << 'SCRIPT'
#!/bin/bash
for DOMAIN in "$@"; do
  EXPIRY=$(echo | openssl s_client -servername "$DOMAIN" -connect "$DOMAIN":443 2>/dev/null | openssl x509 -noout -enddate 2>/dev/null | cut -d= -f2)
  EXPIRY_EPOCH=$(date -d "$EXPIRY" +%s 2>/dev/null)
  NOW_EPOCH=$(date +%s)
  if [ -n "$EXPIRY_EPOCH" ]; then
    DAYS_LEFT=$(( (EXPIRY_EPOCH - NOW_EPOCH) / 86400 ))
    if [ "$DAYS_LEFT" -lt 14 ]; then
      curl -s -X POST -H 'Content-type: application/json' \
        --data "{\"text\":\"TLS cert for $DOMAIN expires in $DAYS_LEFT days\"}" \
        "$SLACK_WEBHOOK_URL"
    fi
  fi
done
SCRIPT
chmod +x /usr/local/bin/cert-check.sh

# Daily at 9am
(crontab -l 2>/dev/null; echo "0 9 * * * SLACK_WEBHOOK_URL='<WEBHOOK>' /usr/local/bin/cert-check.sh <DOMAIN1> <DOMAIN2>") | crontab -
```

### Verify certbot auto-renewal

```bash
systemctl status certbot.timer
certbot renew --dry-run
```

## Verify

```bash
crontab -l | grep -E "disk-alert|cert-check"
/usr/local/bin/disk-alert.sh && echo "Disk alert script works"
```
