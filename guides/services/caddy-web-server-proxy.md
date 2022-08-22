---
description: Install Caddy to a service
---

# Caddy Web Server/Proxy

Most people use Caddy as a web server or proxy, but at its core, Caddy is a server of servers. With the [requisite modules](https://caddyserver.com/docs/modules/), it can take on the role of any long-running process!

Configuration is both dynamic and exportable with [Caddy's API](https://caddyserver.com/docs/api). Although no config files required, you can still use them; most people's favorite way of configuring Caddy is using the [Caddyfile](https://caddyserver.com/docs/caddyfile). The format of the config document takes many forms with [config adapters](https://caddyserver.com/docs/config-adapters), but Caddy's native config language is [JSON](https://caddyserver.com/docs/json/).

Caddy compiles for all major platforms and has no runtime dependencies.

### **Install Caddy on to Ubuntu system**

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

### **Configuring Caddyfile**

Edit the config file which will be installed in `/etc/caddy/Caddyfile`

{% hint style="info" %}
You can either comment all lines with #.

Or just wipe whole content and create your own
{% endhint %}

### **Example Configuration**

```json
https://rpc.juno.chaintools.tech { 
  reverse_proxy http://<RPC-SERVER-IP>:PORT { 
     header_down Access-Control-Allow-Origin * 
  } log { 
         output file /var/log/caddy/rpc.juno.chaintools.tech.log
         } 
 } 
 
 https://api.juno.chaintools.tech { 
   reverse_proxy http://<API-SERVER-IP>:PORT { 
      header_down Access-Control-Allow-Origin * 
  } log { 
         output file /var/log/caddy/api.juno.chaintools.tech.log 
         } 
  }
```

### **Create a service file for Caddy**

```
cat /lib/systemd/system/caddy.service

# caddy.service
#
# For using Caddy with a config file.
#
# Make sure the ExecStart and ExecReload commands are correct
# for your installation.
#
# See https://caddyserver.com/docs/install for instructions.
#
# WARNING: This service does not use the --resume flag, so if you
# use the API to make changes, they will be overwritten by the
# Caddyfile next time the service is restarted. If you intend to
# use Caddy's API to configure it, add the --resume flag to the
# `caddy run` command or use the caddy-api.service file instead.

[Unit]
Description=Caddy
Documentation=https://caddyserver.com/docs/
After=network.target network-online.target
Requires=network-online.target

[Service]
Type=notify
User=caddy
Group=caddy
ExecStart=/usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
```

Once you are happy with the config and service file you can now start.

`sudo systemctl daemon-reload`

`sudo systemctl start caddy`
