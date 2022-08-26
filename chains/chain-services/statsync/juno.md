---
description: Build and synchronize node from state-sync service
---

# Juno

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide here.
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

### Build Juno Node

```bash
git clone https://github.com/CosmosContracts/juno && cd juno
git checkout v9.0.0
make install
```

### Initialize Juno Node

```bash
junod init myNode --chain-id juno-1
wget -O ${HOME}/.juno/config/addrbook.json https://files.chaintools.tech/chains/juno/addrbook.json
wget -O ${HOME}/.juno/config/genesis.json.gz https://files.chaintools.tech/chains/juno/genesis.json.gz
rm ${HOME}/.juno/config/genesis.json && gzip -d ${HOME}/.juno/config/genesis.json.gz
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.juno.chaintools.tech:443"
SNAP_RPC2="https://rpc.juno.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.juno/config/config.toml
```

### Start Juno Node

```
junod start
```
