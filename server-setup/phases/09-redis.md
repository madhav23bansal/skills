# Phase 9: Redis

## Commands

```bash
apt install -y redis-server

# Generate and set password
REDIS_PASS=$(openssl rand -base64 32 | tr -d "/+=" | head -c 32)
redis-cli CONFIG SET requirepass "$REDIS_PASS"
redis-cli -a "$REDIS_PASS" CONFIG REWRITE

# Save password for reference
echo "$REDIS_PASS" > /root/.redis_password
chmod 600 /root/.redis_password

echo "Redis password: $REDIS_PASS"
echo "Redis URL: redis://:${REDIS_PASS}@localhost:6379"
```

Tell the user to save the Redis URL -- they'll need it in their app's `.env` file.

## Verify

```bash
redis-cli ping 2>&1 | grep -q NOAUTH && echo "Auth required (GOOD)" || echo "NO AUTH (BAD)"
redis-cli -a $(cat /root/.redis_password) ping  # should return PONG
grep "^bind" /etc/redis/redis.conf               # should be 127.0.0.1
```
