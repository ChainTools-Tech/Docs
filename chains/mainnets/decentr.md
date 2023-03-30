---
description: Build and synchronize node from state-sync service
---

# Decentr

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../home/installation-guides/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

{% embed url="https://odysee.com/@ChainToolsAcademy:b/StateSyncDecentr:6" %}
Video demonstrates steps described below.
{% endembed %}

### Build Decentr Node

<pre class="language-bash"><code class="lang-bash"><strong>git clone https://github.com/Decentr-net/decentr &#x26;&#x26; cd decentr
</strong>git checkout v1.6.2
make install
</code></pre>

### Initialize Decentr Node

```bash
decentrd init myNode --chain-id mainnet-3
wget -O ${HOME}/.decentr/config/addrbook.json https://files.chaintools.tech/chains/decentr/addrbook.json
wget -O ${HOME}/.decentr/config/genesis.json https://raw.githubusercontent.com/Decentr-net/mainnets/master/3.0/genesis.json
```

### Add Seed Nodes to config

```
sed -E -i 's/seeds = \".*\"/seeds = \"7708addcfb9d4ff394b18fbc6c016b4aaa90a10a@ares.mainnet.decentr.xyz:26656,8a3485f940c3b2b9f0dd979a16ea28de154f14dd@calliope.mainnet.decentr.xyz:26656,87490fd832f3226ac5d090f6a438d402670881d0@euterpe.mainnet.decentr.xyz:26656,3261bff0b7c16dcf6b5b8e62dd54faafbfd75415@hera.mainnet.decentr.xyz:26656,5f3cfa2e3d5ed2c2ef699c8593a3d93c902406a9@hermes.mainnet.decentr.xyz:26656,a529801b5390f56d5c280eaff4ae95b7163e385f@melpomene.mainnet.decentr.xyz:26656,385129dbe71bceff982204afa11ed7fa0ee39430@poseidon.mainnet.decentr.xyz:26656,35a934228c32ad8329ac917613a25474cc79bc08@terpsichore.mainnet.decentr.xyz:26656,0fd62bcd1de6f2e3cfc15852cdde9f3f8a7987e4@thalia.mainnet.decentr.xyz:26656,bd99693d0dbc855b0367f781fb48bf1ca6a6a58b@zeus.mainnet.decentr.xyz:26656\"/' $HOME/.decentr/config/config.toml
```

### Configure state-sync

```bash
SNAP_RPC="http://75.119.157.167:28657"
SNAP_RPC2="http://75.119.157.167:28657"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.decentr/config/config.toml
```

### Start Decentr Node

```
decentrd start
```



**NOTE:** It might take 5-10 minutes for node to catch-up with RPC and download state-sync snapshot.&#x20;
