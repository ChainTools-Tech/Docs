---
description: Build and synchronise node from state-sync service
---

# Kujira

### Prerequisites

* Go 1.18.x or newer

### Build Kujira Node

```bash
git clone https://github.com/Team-Kujira/core && cd core
git checkout v0.5.0
make install
```

### Initialize Kujira Node

```bash
kujirad init myNode --chain-id kaiyo-1
rm ${HOME}/.kujira/config/addrbook.json && rm ${HOME}/.kujira/config/genesis.json
wget -O ${HOME}/.kujira/config/addrbook.json https://files.chaintools.tech/chains/kujira/addrbook.json
wget -O ${HOME}/.kujira/config/genesis.json https://raw.githubusercontent.com/Team-Kujira/networks/master/mainnet/kaiyo-1.json
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.kujira.chaintools.tech:443"
SNAP_RPC2="https://rpc.kujira.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.kujira/config/config.toml
```

### Start Kujira Node

```
kujirad start
```
