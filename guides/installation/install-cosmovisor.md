# Install Cosmovisor

### Build Cosmovisor

```bash
cd ${HOME}
git clone https://github.com/cosmos/cosmos-sdk && cd cosmos-sdk/cosmovisor/
make
sudo cp cosmovisor /usr/local/bin
```

### Service file template

```bash
[Unit]
Description=Chain Node Name
After=network-online.target

[Service]
User=<USER>
Group=<GROUP>
ExecStart=/usr/local/bin/cosmovisor run start
Restart=always
RestartSec=3
LimitNOFILE=4096
Environment="DAEMON_NAME=<NODE_BINARY_NAME>"
Environment="DAEMON_HOME=<NODE_HOME_FOLDER>"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_LOG_BUFFER_SIZE=512"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
```
