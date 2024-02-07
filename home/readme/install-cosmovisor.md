# Install Cosmovisor

### Build Cosmovisor

<pre class="language-bash"><code class="lang-bash">cd ${HOME}
<strong>git clone https://github.com/cosmos/cosmos-sdk &#x26;&#x26; cd cosmos-sdk/tools/cosmovisor/
</strong>make
sudo cp cosmovisor /usr/local/bin
</code></pre>

#### For MultiNode set ups you can do the following&#x20;

```javascript
mkdir -p ${HOME}/.chain-folder(.juno)/cosmovisor/genesis/bin
```

```javascript
mkdir -p ${HOME}/.local/bin
cp cosmovisor ${HOME}/.local/bin

///check to see if cosmovisor binary is there 
ls ${HOME}/.local/bin

///move chain binary to cosmovisor dir
cp ${HOME}/go/bin/binary ${HOME}/.chain/cosmovisor/genesis/bin
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

```javascript
systemctl daemon-reload

systemctl start <service>

journalctl -u <service> -f
```
