---
description: Build and synchronize node from state-sync service
---

# Flixnet-4

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../home/readme/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

### Build omniflixhub node

```java
git clone https://github.com/Omniflix/omniflixhub.git && cd omniflixhub
git fetch --all
git checkout v0.7.0
go mod tidy
make install
```

### Initialize omniflixhub Node

```javascript
omniflixhubd init myNode --chain-id omniflixhub-1
wget -O ${HOME}/.omniflixhub/config/addrbook.json https://files.chaintools.tech/chains/flixnet-4/addrbook.json
wget -O ${HOME}/.omniflixhub/config/genesis.json https://raw.githubusercontent.com/OmniFlix/testnets/main/flixnet-4/genesis.json
```

### Configure state-sync

```javascript
SNAP_RPC="181.41.142.78:11471"
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" /home/flixnet-4/.omniflixhub/config/config.toml
```

### Start Omniflixhub

```javascript
omniflixhubd start
```
