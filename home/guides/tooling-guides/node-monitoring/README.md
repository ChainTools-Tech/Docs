---
description: Installation of Prometheus and Node Exporter
---

# Node Monitoring

The Prometheus [**Node Exporter**](https://github.com/prometheus/node\_exporter) exposes a wide variety of hardware- and kernel-related metrics.

In this guide, you will:

* Start up a Node Exporter on `localhost`
* Start up a Prometheus instance on `localhost` that's configured to scrape metrics from the running Node Exporter

### Installing Prometheus & Node Exporter



#### Prometheus instance install

```javascript
wget https://github.com/prometheus/prometheus/releases/download/v*/prometheus-*.*-amd64.tar.gz
tar xvf prometheus-*.*-amd64.tar.gz
cd prometheus-*.*
```



Copy tmp config to etc

```javascript
/tmp/prometheus-2.31.1.linux-amd64# cp /tmp/prometheus-2.31.1.linux-amd64/prometheus.yml  /etc/prometheus
```



Create service file for Prometheus to run&#x20;



```javascript
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

```javascript
sudo systemctl daemon-reload
sudo systemctl restart prometheus.service
```



As you have dedicated monitoring system. It is wise to add rules to firewall which will only allow prometheus server to scrape data.

```javascript
sudo ufw allow from "ip-of-your-monitoring/32 proto tcp to any port 9100 comment "Prometheus Node Exporter"

sudo ufw allow from "ip-of-your-monitoring/32 proto tcp to any port 26660 comment "Prometheus Tendermint Exporter"
```



#### Configuring your Prometheus instances <a href="#configuring-your-prometheus-instances" id="configuring-your-prometheus-instances"></a>

```javascript
global:
  scrape_interval: 15s

scrape_configs:
- job_name: node
  static_configs:
  - targets: ['localhost:9100']
```

### Install Node\_Exporter

```javascript
wget https://github.com/prometheus/node_exporter/releases/download/v1.2.2/node_exporter-1.2.2.linux-amd64.tar.gz

/// see whats been downloaded
ls

/// tar the folder
tar xvfz node_exporter-.amd64.tar.gz

/// cd into the folder
cd node_exporter-.amd64

/// run the instance to make sure it is working
./node_exporter

/// check to see scrapes are working 
curl http://localhost:9100/metrics

/// check from another server 
curl http://"ip-of-server-with-node-exporter":9100/metrics

/// rename the folder correctly 
mv node_exporter-1.2.2.linux-amd64 node_exporter

/// if you need to find where the node_exporter binary is you can
sudo find / -name "node_exporter*”

/// set some UFW rules
sudo ufw allow from 134.209.186.97/32 proto tcp to any port 9100 comment "Prometheus Node Exporter"

sudo ufw allow from 134.209.186.97/32 proto tcp to any port 26660 comment "Prometheus Tendermint Exporter"
```

### Create a service file&#x20;

```javascript
sudo nano /lib/systemd/system/node_exporter.service

/// inside the file
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=monitoring
Group=monitoring
Type=simple
ExecStart=/home/monitoring/node_exporter/node_exporter


[Install]
WantedBy=multi-user.target
```

```javascript
sudo systemctl daemon-reload

sudo systemctl start node_exporter.service
```

#### Troubleshooting&#x20;



Some useful commands

```javascript
  ps aux | grep prometheus
  
  ufw status numbered
  
  ufw delete RULE_NUMBER
```
