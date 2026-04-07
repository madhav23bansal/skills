# Phase 10: Node.js Runtime

Ask the user:
1. What Node.js version? (default: 20 LTS)
2. Package manager? (pnpm / npm / yarn)
3. App user name? (from Phase 2, default: `deploy`)

All installations are done under the app user, NOT root.

## Commands

```bash
APP_USER="<from phase 2>"
NODE_VERSION="<asked, default 20>"

# Install NVM
sudo -u $APP_USER bash -c "
export NVM_DIR=/opt/$APP_USER/.nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
source /opt/$APP_USER/.nvm/nvm.sh
nvm install $NODE_VERSION
nvm alias default $NODE_VERSION
node --version
"

# Install package manager
sudo -u $APP_USER bash -c "
cd /opt/$APP_USER
source /opt/$APP_USER/.nvm/nvm.sh
corepack enable
corepack prepare pnpm@latest --activate
pnpm --version
"

# Install PM2
sudo -u $APP_USER bash -c "
source /opt/$APP_USER/.nvm/nvm.sh
npm install -g pm2
pm2 --version
"

# PM2 log rotation
sudo -u $APP_USER bash -c "
source /opt/$APP_USER/.nvm/nvm.sh
pm2 install pm2-logrotate
pm2 set pm2-logrotate:max_size 50M
pm2 set pm2-logrotate:retain 14
pm2 set pm2-logrotate:compress true
"

# Create PM2 systemd service
NODE_BIN="/opt/$APP_USER/.nvm/versions/node/v${NODE_VERSION}.*/bin"
# Get exact path
NODE_BIN=$(sudo -u $APP_USER bash -c "source /opt/$APP_USER/.nvm/nvm.sh && dirname \$(which node)")

cat > /etc/systemd/system/pm2-$APP_USER.service << EOF
[Unit]
Description=PM2 process manager
After=network.target redis-server.service

[Service]
Type=forking
User=$APP_USER
Group=$APP_USER
Environment=PATH=${NODE_BIN}:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
Environment=PM2_HOME=/opt/$APP_USER/.pm2
PIDFile=/opt/$APP_USER/.pm2/pm2.pid
ExecStart=${NODE_BIN}/pm2 resurrect
ExecReload=${NODE_BIN}/pm2 reload all
ExecStop=${NODE_BIN}/pm2 kill
Restart=on-failure
RestartSec=10
LimitNOFILE=65536
LimitNPROC=65536
NoNewPrivileges=true
ProtectSystem=full

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable pm2-$APP_USER
```

## Verify

```bash
sudo -u $APP_USER bash -c "source /opt/$APP_USER/.nvm/nvm.sh && node --version && pnpm --version && pm2 --version"
systemctl is-enabled pm2-$APP_USER  # should be enabled
```
