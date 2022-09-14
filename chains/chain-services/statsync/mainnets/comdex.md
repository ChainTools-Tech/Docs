---
description: Build and synchronize node from state-sync service
---

# Comdex

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../../../guides/installation/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

{% embed url="https://odysee.com/@ChainToolsAcademy:b/StateSyncComdex:6" %}
Video demonstrates steps described below.
{% endembed %}

### Build Comdex Node

```bash
git clone https://github.com/comdex-official/comdex && cd comdex
git checkout v0.1.1
make install
```

### Initialize Comdex Node

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

**NOTE:** It might take 5-10 minutes for node to catch-up with RPC and download state-sync snapshot. Once snapshot will be identified and accepted you will see following information in node logs:

```bash
11:25AM INF Discovered new snapshot format=1 hash="\x06KA&v�w�� WI�\x1fk\x1f)�?���5�[��\t\a�i�" height=4014000 module=statesync                                                                                                                 11:25AM INF Discovered new snapshot format=1 hash="C9\x7f���G��\x19�*�\x1eMb��\x15\x16\x03\t��\x19{�^�]�r" height=4012500 module=statesync                                                                                                      11:25AM ERR dialing failed (attempts: 1): auth failure: secret conn failed: read tcp 192.168.6.132:34112->195.201.195.111:26656: i/o timeout addr={"id":"9a59b6dc59903d036dd476de26e8d2b9f1acf466","ip":"195.201.195.111","port":26656} module=pex
11:25AM INF VerifyHeader hash=1D8C280CA63402BCD87308A62FAB0C1BD61A03E92A07F24FE8F84BA0503C529D height=4014001 module=light
11:25AM INF VerifyHeader hash=B62C905575DBEBF139340BECD449B254F899B875244B42B0915B6682AB33A226 height=4014002 module=light
11:25AM INF Offering snapshot to ABCI app format=1 hash="\x06KA&v�w�� WI�\x1fk\x1f)�?���5�[��\t\a�i�" height=4014000 module=statesync
11:25AM INF Snapshot accepted, restoring format=1 hash="\x06KA&v�w�� WI�\x1fk\x1f)�?���5�[��\t\a�i�" height=4014000 module=statesync
11:25AM INF Fetching snapshot chunk chunk=0 format=1 height=4014000 module=statesync total=18
11:25AM INF Fetching snapshot chunk chunk=2 format=1 height=4014000 module=statesync total=18
11:25AM INF Fetching snapshot chunk chunk=1 format=1 height=4014000 module=statesync total=18
11:25AM INF Fetching snapshot chunk chunk=3 format=1 height=4014000 module=statesync total=18
11:25AM INF VerifyHeader hash=A7E9DDA1233FAA7D1344133433818035C5015FEA761C9E6EF25E1F6982A2B47D height=4014000 module=light
11:25AM INF Header has already been verified hash=1D8C280CA63402BCD87308A62FAB0C1BD61A03E92A07F24FE8F84BA0503C529D height=4014001 module=light
11:25AM INF Header has already been verified hash=B62C905575DBEBF139340BECD449B254F899B875244B42B0915B6682AB33A226 height=4014002 module=light
11:25AM INF Header has already been verified hash=1D8C280CA63402BCD87308A62FAB0C1BD61A03E92A07F24FE8F84BA0503C529D height=4014001 module=light
```
