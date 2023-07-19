---
description: Here we will try our best to update peer and seeds list.
---

# Peers & Seeds

{% hint style="info" %}
Query your own PEER
{% endhint %}

{% hint style="info" %}
Set external P2P address
{% endhint %}

```bash
sed -i "/^external_address = .*/ s//external_address = \"${ADDRESS:="$(curl -fsSL https://ifconfig.me):${PORT:=26656}"}\"/" $HOME/.chaind/config/config.toml
```

```javascript
echo $(junod tendermint show-node-id)@$(curl -4 ifconfig.co):26656
```

{% hint style="info" %}
Add seeds with `sed`
{% endhint %}

```bash
SEEDS=<    >
```

```bash
sed -i "s/^\(seeds *= *\).*$/\1$SEEDS/" config.toml
```

{% hint style="info" %}
Query how many peers connected to your network
{% endhint %}

```bash
curl -s http://localhost:26657/net_info | jq '.result.n_peers'
```

{% hint style="info" %}
Query connected peers on your network&#x20;
{% endhint %}

```javascript
curl -s http://localhost:26657/net_info | jq '.result.peers[].node_info.id + "@" + .result.peers[].remote_ip'
```
