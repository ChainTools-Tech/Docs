---
description: Here we will try our best to update peer and seeds list.
---

# Peers & Seeds

{% hint style="info" %}
Query your own PEER
{% endhint %}

```javascript
echo $(junod tendermint show-node-id)@$(curl -4 ifconfig.co):26656
```

{% hint style="info" %}
Add peers or seeds with `sed`
{% endhint %}

```bash
SEEDS=<    >
```

```bash
sed -i "s/^\(seeds *= *\).*$/\1$SEEDS/" config.toml
```

{% hint style="info" %}
Query connected peers on your network&#x20;
{% endhint %}

```javascript
curl localhost:26657/net_info | jq '"(.result.peers[] | .node_info.id + "@" + .remote_ip)"'
```
