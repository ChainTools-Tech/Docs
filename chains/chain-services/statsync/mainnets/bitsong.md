---
description: Build and synchronize node from state-sync service
---

# BitSong

### Prerequisites

* Ubuntu 20.04 LTS or newer
* Go 1.18.x or newer - Installation guide [here](../../../../home/installation/install-golang.md).
* JSON processor jq - `sudo apt install jq`
* Essential Build Tools - `sudo apt install build-essential`

{% embed url="https://odysee.com/@ChainToolsAcademy:b/StateSyncBitSong:d" %}
Video demonstrates steps described below.
{% endembed %}

### Build BitSong Node

```bash
git clone https://github.com/bitsongofficial/go-bitsong && cd go-bitsong
git checkout v0.11.0
make install
```

### Initialize BitSong Node

```bash
bitsongd init myNode --chain-id bitsong-2b
wget -O ${HOME}/.bitsongd/config/addrbook.json https://files.chaintools.tech/chains/bitsong/addrbook.json
wget -O ${HOME}/.bitsongd/config/genesis.json https://github.com/bitsongofficial/networks/raw/master/bitsong-2b/genesis.json
```

### Configure state-sync

```bash
SNAP_RPC="https://rpc.bitsong.chaintools.tech:443"
SNAP_RPC2="https://rpc.bitsong.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.bitsongd/config/config.toml
```

### Start BitSong Node

```
bitsongd start
```



**NOTE:** It might take 5-10 minutes for node to catch-up with RPC and download state-sync snapshot.&#x20;

Once snapshot will be identified and accepted you will see following information in node logs:

```bash
12:35PM INF Discovering snapshots for 15s module=statesync
12:35PM INF Discovered new snapshot format=1 hash="W.:�5��D\blW�X��cJ\x01�l�p��t�S��\t�6" height=7422600 module=statesync
12:35PM INF Discovered new snapshot format=1 hash="�N\x11��et��z>{\x1a�B��e�)��iF$���C�^�" height=7422000 module=statesync
12:35PM INF Discovered new snapshot format=1 hash="��յ�S���~\x18������t��\x12=\v!�������k" height=7420500 module=statesync
12:35PM INF Discovered new snapshot format=1 hash="�O�y�RշKF�\x02D\x7f��I\x04\x18���\x12jR�o�\u038bQB" height=7423000 module=statesync
12:35PM INF Discovered new snapshot format=1 hash="�����®\x0e���P\x1b𤶠�:y�'C;�U�Ջ#�s" height=7422500 module=statesync
12:35PM INF Discovered new snapshot format=1 hash="xq�\n\x0ef�\r\tlz�\x1f��\x1b�X㼋�\x06�\x00w�@h\x17J�" height=7420000 module=statesync
12:36PM INF VerifyHeader hash=0E24547577528B701DDC22D28A7C6F956A8712E48D0D74BD20D692FB4E83F375 height=7423001 module=light
12:36PM INF VerifyHeader hash=5DF0018BDD2DA1AFE21324850F743B3DF1407B4BD8ABA6E7AE4B29F0D2730514 height=7423002 module=light
12:36PM INF Offering snapshot to ABCI app format=1 hash="�O�y�RշKF�\x02D\x7f��I\x04\x18���\x12jR�o�\u038bQB" height=7423000 module=statesync
12:36PM INF Snapshot accepted, restoring format=1 hash="�O�y�RշKF�\x02D\x7f��I\x04\x18���\x12jR�o�\u038bQB" height=7423000 module=statesync
12:36PM INF Fetching snapshot chunk chunk=0 format=1 height=7423000 module=statesync total=20
12:36PM INF Fetching snapshot chunk chunk=1 format=1 height=7423000 module=statesync total=20
12:36PM INF Fetching snapshot chunk chunk=2 format=1 height=7423000 module=statesync total=20
12:36PM INF Fetching snapshot chunk chunk=3 format=1 height=7423000 module=statesync total=20
12:36PM INF VerifyHeader hash=336FD5DED5EBF0CF24DA5584A08EC052193409890AA6EC8DCC31060A402B3A0E height=7423000 module=light
12:36PM INF Header has already been verified hash=0E24547577528B701DDC22D28A7C6F956A8712E48D0D74BD20D692FB4E83F375 height=7423001 module=light
```
