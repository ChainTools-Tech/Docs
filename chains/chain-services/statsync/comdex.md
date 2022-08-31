---
description: Build and synchronize node from state-sync service
---

# Comdex

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide here.
* JSON processor jq - sudo apt install jq
* Essential Build Tools - sudoapt install build-essential

### Build Comdex Node

```bash
git clone https://github.com/comdex-official/comdex && cd comdex
git checkout v0.1.1
make install
```

### Initialize BitSong Node

```bash
comdex init myNode --chain-id comdex-1
wget -O ${HOME}/.comdex/config/addrbook.json https://files.chaintools.tech/chains/comdex/addrbook.json
wget -O ${HOME}/.comdex/config/genesis.json https://raw.githubusercontent.com/comdex-official/networks/main/mainnet/comdex-1/genesis.json
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.comdex.chaintools.tech:443"
SNAP_RPC2="https://rpc.comdex.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.comdex/config/config.toml
```

### Start Comdex Node

```
comdex start
```

**NOTE:** It might take 5-10 minutes for node to catch-up with RPC and download state-sync snapshot.
