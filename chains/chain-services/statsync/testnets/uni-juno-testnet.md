---
description: Build and synchronize node from state-sync service
---

# Uni - Juno Testnet

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../../../home/guides/installation-guides/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

### Build Juno Node

```bash
git clone https://github.com/CosmosContracts/juno && cd juno
git checkout v10.0.0-alpha
make install
```

### Initialize Juno Node

```bash
junod init myNode --chain-id uni-3
wget -O ${HOME}/.juno/config/addrbook.json https://files.chaintools.tech/chains/uni/addrbook.json
wget -O ${HOME}/.juno/config/genesis.json https://raw.githubusercontent.com/CosmosContracts/testnets/main/uni-3/genesis.json
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.uni.chaintools.tech:443"
SNAP_RPC2="https://rpc.uni.chaintools.tech:443"

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

**Add these two PEERS to your `config.toml` **&#x20;

`ac9dd6db1d34c15b1de212b0c0c240615bfc2941@207.180.243.215:26656,f7d12b08210b7b759f65e381782d62b556598000@127.0.0.2:26656`&#x20;

### Start Omniflixhub Node

```
omniflixhubd start
```
