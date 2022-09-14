---
description: Build and synchronize node from state-sync service
---

# Rizon

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../../../home/guides/installation-guides/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

{% embed url="https://odysee.com/@ChainToolsAcademy:b/StateSyncRizon:9" %}
Video demonstrates steps described below.
{% endembed %}

### Build Rizon Node

```bash
git clone https://github.com/rizon-world/rizon.git && cd rizon
git checkout v0.3.0
make install
```

### Initialize Rizon Node

```bash
rizond init myNode --chain-id titan-1
wget -O ${HOME}/.rizon/config/addrbook.json https://files.chaintools.tech/chains/rizon/addrbook.json
wget -O ${HOME}/.rizon/config/genesis.json https://github.com/rizon-world/mainnet/raw/master/genesis.json
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.rizon.chaintools.tech:443"
SNAP_RPC2="https://rpc.rizon.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.rizon/config/config.toml
```

### Start Rizon Node

```
rizond start
```
