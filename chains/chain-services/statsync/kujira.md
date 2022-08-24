---
description: Build and synchronise node from state-sync service
---

# Kujira

### Prerequisites

* Go 1.18.x or newer - Installation guide [here](../../../guides/installation/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudoapt install build-essential`

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

```bash
kujirad start
```

**NOTE:** It might take 5-10 minutes for node to catch-up with RPC and download state-sync snapshot.

Once snapshot will be identified and accepted you will see following information in node logs:

```
5:07PM INF Snapshot accepted, restoring format=1 hash="�NU\t���B���T�\x1082�g��H��.�#�\x18>n�\x1d" height=2085000 module=statesync
5:07PM INF Fetching snapshot chunk chunk=0 format=1 height=2085000 module=statesync total=16
5:07PM INF Fetching snapshot chunk chunk=2 format=1 height=2085000 module=statesync total=16
5:07PM INF Fetching snapshot chunk chunk=3 format=1 height=2085000 module=statesync total=16
5:07PM INF Fetching snapshot chunk chunk=1 format=1 height=2085000 module=statesync total=16
5:07PM INF VerifyHeader hash=FD20A729BCBF40EAB447D7412EB0BEE46AF7B631C346DC37DC11AA0532700EED height=2085000 module=light
5:07PM INF Header has already been verified hash=C4738EC7831CF360ED7A055F9484BCEADA0A8BFEBDD78FF291E443B299E331AC height=2085001 module=light
5:07PM INF Header has already been verified hash=6707C03A54E24FAADD6A7220DE99302CEECC482DB5401B34CB3C7F0ACF105D57 height=2085002 module=light
5:07PM INF Header has already been verified hash=C4738EC7831CF360ED7A055F9484BCEADA0A8BFEBDD78FF291E443B299E331AC height=2085001 module=light
5:07PM INF Header has already been verified hash=FD20A729BCBF40EAB447D7412EB0BEE46AF7B631C346DC37DC11AA0532700EED height=2085000 module=light
5:07PM INF Fetching snapshot chunk chunk=4 format=1 height=2085000 module=statesync total=16
5:07PM INF Applied snapshot chunk to ABCI app chunk=0 format=1 height=2085000 module=statesync total=16                                                                                                     5:07PM INF Fetching snapshot chunk chunk=5 format=1 height=2085000 module=statesync total=16                                                                                                                5:07PM INF Fetching snapshot chunk chunk=6 format=1 height=2085000 module=statesync total=16
5:07PM INF Fetching snapshot chunk chunk=7 format=1 height=2085000 module=statesync total=16                                                                                                                5:07PM INF Applied snapshot chunk to ABCI app chunk=1 format=1 height=2085000 module=statesync total=16                                                                                                     5:07PM INF Applied snapshot chunk to ABCI app chunk=2 format=1 height=2085000 module=statesync total=16                                                                                                     5:07PM INF Applied snapshot chunk to ABCI app chunk=3 format=1 height=2085000 module=statesync total=16
5:07PM INF Fetching snapshot chunk chunk=8 format=1 height=2085000 module=statesync total=16
5:07PM INF Applied snapshot chunk to ABCI app chunk=4 format=1 height=2085000 module=statesync total=16
5:07PM INF Fetching snapshot chunk chunk=9 format=1 height=2085000 module=statesync total=16
5:07PM INF Applied snapshot chunk to ABCI app chunk=5 format=1 height=2085000 module=statesync total=16
5:07PM INF Fetching snapshot chunk chunk=10 format=1 height=2085000 module=statesync total=16
5:07PM INF Applied snapshot chunk to ABCI app chunk=6 format=1 height=2085000 module=statesync total=16
5:07PM INF Fetching snapshot chunk chunk=11 format=1 height=2085000 module=statesync total=16
```
