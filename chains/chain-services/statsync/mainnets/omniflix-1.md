# Omniflix

```javascript
sudo systemctl stop omniflix && omniflixhubd unsafe-reset-all

SNAP_RPC="https://rpc.omniflix.chaintools.tech:443"
SNAP_RPC2="https://rpc.omniflix.chaintools.tech:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

///echo command will return something like this below///

2213475 2212475 7A1D0E699A73345228C61554C9235C2FD8836FC738A639F3FA7D800D56DE6EF2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.omniflixhub/config/config.toml

peers="2df1f357f08049ba0c0dddfffe805f0e135e54ec@35.247.185.216:26656,6198ac4bc907f6d1a78309ef58491370afc49799@34.124.195.219:26656,574b37cc6e80663e70673cbe848147c2643ca48e@35.240.187.174:26656,8313c9d55006da030588f61806b3e056a113e6e8@34.87.18.204:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.omniflixhub/config/config.toml



sudo systemctl restart omniflix
sudo journalctl -u omniflix -f
```
