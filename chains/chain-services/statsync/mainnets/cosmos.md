---
description: Build and synchronize node from state-sync service
---

# Cosmos

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../../../home/guides/installation-guides/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

### Build Juno Node

<pre class="language-bash"><code class="lang-bash"><strong>git clone https://github.com/cosmos/gaia &#x26;&#x26; cd gaia
</strong><strong>git checkout v7.1.0
</strong><strong>make install</strong></code></pre>

### Initialize Cosmos Node

```bash
gaiad init myNode --chain-id cosmoshub-4
wget -O ${HOME}/.gaia/config/addrbook.json https://files.chaintools.tech/chains/cosmos/addrbook.json
wget -O ${HOME}/.gaia/config/genesis.json https://files.chaintools.tech/chains/cosmos/genesis.json.gz
rm ${HOME}/.gaia/config/genesis.json && gzip -d ${HOME}/.gaia/config/genesis.json.gz
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.cosmos.chaintools.tech:443"
SNAP_RPC2="https://rpc.cosmos.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.cosmos/config/config.toml
```

### Start Cosmos Node

```
cosmos start
```
