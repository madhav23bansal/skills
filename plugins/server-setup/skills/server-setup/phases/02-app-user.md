# Phase 2: Application User

Ask the user what they want to name their app user (default: `deploy`).

## Commands

```bash
APP_USER="<asked>"
useradd --system --shell /bin/bash --home-dir /opt/$APP_USER --create-home $APP_USER
mkdir -p /opt/$APP_USER/{app,logs,.ssh,.pm2}
chown -R $APP_USER:$APP_USER /opt/$APP_USER
chmod 750 /opt/$APP_USER

# Copy authorized keys so SSH works for this user
cp /root/.ssh/authorized_keys /opt/$APP_USER/.ssh/authorized_keys
chown $APP_USER:$APP_USER /opt/$APP_USER/.ssh/authorized_keys
chmod 600 /opt/$APP_USER/.ssh/authorized_keys

# Set process limits
cat > /etc/security/limits.d/$APP_USER.conf << EOF
$APP_USER soft nofile 65536
$APP_USER hard nofile 65536
$APP_USER soft nproc 4096
$APP_USER hard nproc 4096
EOF
```

## Verify

```bash
id $APP_USER
ls -la /opt/$APP_USER/
ssh -o ConnectTimeout=5 $APP_USER@localhost 'echo OK'  # test SSH as new user
```
