---
description: Build and synchronize node from state-sync service
---

# KiChain

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../home/readme/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

{% embed url="https://odysee.com/@ChainToolsAcademy:b/StateSyncKiChain:3" %}
Video demonstrates steps described below.
{% endembed %}

### Build KiChain Node

```bash
git clone https://github.com/KiFoundation/ki-tools.git && cd ki-tools
git checkout 5.0.0
make install
```

### Initialize KiChain Node

```bash
kid init myNode --chain-id kichain-2
wget -O ${HOME}/.kid/config/addrbook.json https://files.chaintools.tech/chains/kichain/addrbook.json
wget -O ${HOME}/.kid/config/genesis.json https://github.com/KiFoundation/ki-networks/raw/v0.1/Mainnet/kichain-2/genesis.json
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.kichain.chaintools.tech:443"
SNAP_RPC2="https://rpc.kichain.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.kid/config/config.toml
```

### Start KiChain Node

```
kid start
```

**NOTE:** It might take 5-10 minutes for node to catch-up with RPC and download state-sync snapshot. Once snapshot will be identified and accepted you will see following information in node logs:

```bash
10:18AM INF Discovering snapshots for 15s module=statesync                                                              10:18AM INF Discovered new snapshot format=1 hash="D\x03I\x02!0B�\bE�&\\j���A�\x04t��(a��ڬ�,;" height=11294000 module=statesync
10:18AM INF Discovered new snapshot format=1 hash="G\x12���O,T�!��!<�|ӧd�F��}\\{_ˋx%�" height=11292000 module=statesync 10:18AM INF Discovered new snapshot format=1 hash="\x14wT9/�<���쿚��\x16\x17�T\x12�n\aQJ�(}Qm�5" height=11290000 module=statesync                                                                                                               10:18AM INF Discovered new snapshot format=1 hash="�ʈ���vXV#\x14�ĵ��)РPir[ߖ\x14\x18e��bL" height=11288000 module=statesync                                                                                                                      10:18AM INF Discovered new snapshot format=1 hash="G��'�@=\fDl����\x01�\v1\au�Gl�W@����#i" height=11286000 module=statesync                                                                                                                     10:18AM INF VerifyHeader hash=8DF7257D0ADE198F8D6BDA7E1E8963913D59A41BC1B73B2651EAE790F33ABF8F height=11294001 module=light                                                                                                                     10:18AM INF VerifyHeader hash=AFC1C3AE90C6C4746DB2A8066D9066E78C7675AB8844468E725C004BFCF03F65 height=11294002 module=light                                                                                                                     10:18AM INF Offering snapshot to ABCI app format=1 hash="D\x03I\x02!0B�\bE�&\\j���A�\x04t��(a��ڬ�,;" height=11294000 module=statesync                                                                                                           10:18AM INF Snapshot accepted, restoring format=1 hash="D\x03I\x02!0B�\bE�&\\j���A�\x04t��(a��ڬ�,;" height=11294000 module=statesync                                                                                                            10:18AM INF Fetching snapshot chunk chunk=0 format=1 height=11294000 module=statesync total=20                          10:18AM INF Fetching snapshot chunk chunk=2 format=1 height=11294000 module=statesync total=20                          10:18AM INF Fetching snapshot chunk chunk=1 format=1 height=11294000 module=statesync total=20                          10:18AM INF Fetching snapshot chunk chunk=3 format=1 height=11294000 module=statesync total=20               
```
