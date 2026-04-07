# Phase 8: Nginx

Ask the user:
1. What domains need to be proxied? (e.g., api.example.com -> localhost:3000)
2. Should we set up SSL via Certbot? (requires domain DNS already pointing to this server)
3. Any WebSocket endpoints? (need longer timeouts)
4. Custom Content-Security-Policy? (or use a sensible default)

## Commands

### Install

```bash
apt install -y nginx
```

### Harden nginx.conf

Add/modify inside the `http` block of `/etc/nginx/nginx.conf`:

```nginx
# Rate limiting zones
limit_req_zone $binary_remote_addr zone=general:10m rate=30r/s;
limit_conn_zone $binary_remote_addr zone=connlimit:10m;

# Hide version
server_tokens off;

# Body size limit
client_max_body_size 1m;

# Security headers
add_header X-Content-Type-Options "nosniff" always;
add_header X-Frame-Options "DENY" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Permissions-Policy "camera=(), microphone=(), geolocation=()" always;

# TLS
ssl_protocols TLSv1.2 TLSv1.3;

# SNI/Host mismatch protection
map $ssl_server_name:$host $sni_host_mismatch {
    default 1;
    "~^(.+):\\1$" 0;
    "~^:$" 0;
}
```

### Default server block

```bash
mkdir -p /etc/nginx/ssl
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/default.key \
  -out /etc/nginx/ssl/default.crt \
  -subj "/CN=_"

cat > /etc/nginx/sites-enabled/00-default << 'EOF'
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;
    return 444;
}
server {
    listen 443 ssl default_server;
    listen [::]:443 ssl default_server;
    server_name _;
    ssl_certificate /etc/nginx/ssl/default.crt;
    ssl_certificate_key /etc/nginx/ssl/default.key;
    return 444;
}
EOF
```

### Site config template

For each domain the user provides, create a site config:

```nginx
server {
    server_name <DOMAIN>;

    if ($sni_host_mismatch) { return 444; }

    limit_req zone=general burst=60 nodelay;
    limit_conn connlimit 30;

    if ($request_method = CONNECT) { return 444; }

    location / {
        proxy_pass http://127.0.0.1:<PORT>;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_read_timeout 60;
    }

    # Add for WebSocket endpoints:
    # location /ws {
    #     proxy_pass http://127.0.0.1:<PORT>;
    #     proxy_http_version 1.1;
    #     proxy_set_header Upgrade $http_upgrade;
    #     proxy_set_header Connection "upgrade";
    #     proxy_read_timeout 3600;
    #     proxy_send_timeout 3600;
    # }

    listen 80;
}
```

### SSL (optional)

```bash
apt install -y certbot python3-certbot-nginx
certbot --nginx -d <DOMAIN>
```

### Validate and reload

```bash
nginx -t && systemctl reload nginx
```

## Verify

```bash
curl -sI https://<DOMAIN>/ | grep -iE "server:|x-frame|x-content"
curl -s -o /dev/null -w "%{http_code}" -H "Host: evil.com" https://<DOMAIN>/  # should be 000
```
