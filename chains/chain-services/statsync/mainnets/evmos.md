---
description: Build and synchronize node from state-sync service
---

# Evmos

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../../../home/guides/installation-guides/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

### Build Evmos Node

```bash
git clone https://github.com/evmos/evmos && cd evmos
git checkout v11.0.1
make install
```

### Initialize Evmos Node

```bash
evmosd init myNode --chain-id evmos_9001-2
wget -O ${HOME}/.evmosd/config/addrbook.json https://files.chaintools.tech/chains/evmos/addrbook.json
wget -O ${HOME}/.evmosd/config/genesis.json https://archive.evmos.org/mainnet/genesis.json
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.evmos.chaintools.tech:443"
SNAP_RPC2="https://rpc.evmos.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.evmosd/config/config.toml
```

### Start Evmos Node

```
evmosd start
```
