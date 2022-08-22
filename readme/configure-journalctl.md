# Configure journalctl

### Check how much disk space is allocated for journalctl

```bash
journalctl --disk-usage
```

### Adjust journalctl diskspace allocation

In file `/etc/systemd/journald.conf` locate and adjust line _**SystemMaxUse=50M**_. Allocation of 50M is an example. By default this parameter might be commented in configuration file.

### Restart journal to apply new settings and clean up history

```bash
sudo systemctl kill --kill-who=main --signal=SIGUSR2 systemd-journald.service
sudo systemctl restart systemd-journald.service
```
