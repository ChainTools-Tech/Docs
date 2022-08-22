# Comdex

```javascript
sudo systemctl stop comdex && junod unsafe-reset-all

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

peers="ba89aa161ae33ddffa508d57368bd2666a97bbe2@164.68.115.226:26656,3f9f2b2b857c13c89d6d7a88d5ee90fc0a8d3736@144.91.64.59:26656,fa39785c5c0cda07a0b0b61a686401da78909034@5.9.19.119:26656,0eeb0232a4883c4a57a52af006fc2ad5b64b59b3@49.12.176.139:26656,21d8a9da7d963db00814447979ffa2ef0f65fee9@54.216.124.105:26656,14ccff2e3eb02f0d78d16fd147a24e569edbaa43@18.206.130.37:26656,2ed6df7c98ca4ef9c40fcdce255daf56e3e502d5@51.81.208.3:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.comdex/config/config.toml

wget https://files.chaintools.tech/addrbook.json

sudo systemctl restart comdex
sudo journalctl -u comdex -f
```
