# Comdex

```javascript
sudo systemctl stop comdex && comdex unsafe-reset-all

SNAP_RPC="https://rpc.comdex.chaintools.tech:443"
SNAP_RPC2="https://rpc.comdex.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

/// echo command will return something like this below as an example. 

2213475 2212475 7A1D0E699A73345228C61554C9235C2FD8836FC738A639F3FA7D800D56DE6EF2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.comdex/config/config.toml

peers="ADD PEERS FROM OUT PEER LIST HERE"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.comdex/config/config.toml

wget https://files.chaintools.tech/addrbook.json

sudo systemctl restart comdex
sudo journalctl -u comdex -f
```
