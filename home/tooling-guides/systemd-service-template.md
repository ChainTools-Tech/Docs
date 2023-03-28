# Systemd Service Template

### Service template with logging to file

```bash
[Unit]
Description=Service
After=network-online.target

[Service]
Type=simple
User=<USER>
Group=<GROUP>
ExecStart=/home/<USER>/.local/bin/<SERVICE>
LimitNOFILE=8192
Restart=on-failure
RestartSec=3
StandardOutput=append:/home/<USER>/.logs/output.log
StandardError=append:/home/<USER>/.logs/error.log

[Install]
WantedBy=multi-user.target
```

### Service template with logging to journal

```bash
[Unit]
Description=Service
After=network-online.target

[Service]
Type=simple
User=<USER>
Group=<GROUP>
ExecStart=/home/<USER>/.local/bin/<SERVICE>
LimitNOFILE=8192
Restart=on-failure
RestartSec=3
StandardOutput=journal
StandardError=journal
SyslogIdentifier=<SERVICE_NAME>

[Install]
WantedBy=multi-user.target
```
