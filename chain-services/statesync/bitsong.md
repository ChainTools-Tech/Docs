# BitSong

Synchronizing BitSong node from state-sync.



```bash
#stop the node and reset (this command is for rizond launched as a service)
sudo systemctl stop bitsong && rizond unsafe-reset-all

#set variables to bonded.zone RPCs
SNAP_RPC="https://rpc.bitsong.chaintools.tech:443"
SNAP_RPC2="https://rpc.bitsong.chaintools.tech:443"


#set variables $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
#this is one command 
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

#check variables
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
#if output is something like this one, then continue to the next step
#1769362 1768362 311AFA4925B213B366879589640F4CA03CCB58841DBE2CE85A7FC1D19BBEB909

#configure persistent peers 
peers="e2b9971222adf71f7199c670b7e85471c447e926@157.90.255.143:26656,120740c15a8a19c232b1aa4d80b20de248b33db3@135.181.129.94:26656,bbfb37b3c44c8148b6af7adfa016ec8fabff69d1@121.78.247.243:16656,d741773bc5eecbefb7b14fcca5e3e0fedd49d5a3@157.90.95.104:26656,6e93a30587671e2cecacbcbb27092809bb20249f@95.217.203.59:31656,adfe1cf240780cf8d58266171ced72fb4e9a7a6d@23.226.14.168:26656,f36d3a926ae0583e60f00e7bc54711f3cb7fe769@195.201.58.166:26656,9c9f030298bdda9ca69de7db8e9a3aef33972fba@135.181.16.236:31656,9806602afb65ba45d1048d65285d5c6e50285088@178.18.242.242:26656,4fdd438ea70927003022ecc308e36bc1924ec598@51.210.104.207:26656,3cf3effd3ecb33bdbb5c5e6528c88fde4869b97c@116.202.139.113:26656,075cf589e44c74687ef3a4df3a583f482bce57e0@46.166.143.79:26656,f9d318eaf38988ce2b65b795068d86b214866c91@141.94.170.26:26256,fa932748b327fdde6d235b28a9850f8b8bd3326a@95.217.119.101:31656,d52f6e4fe1819133474e977d7e1d73124d1f4af5@95.217.156.76:26656,5ebab02914638005773dac8026f441e06c115a44@74.207.226.176:26656,e5428ce29ccd26434828a577906ac9c413ca6a48@80.71.57.42:26656,2afc435e2246ff3f16ade85b52264367945d12b5@176.58.124.226:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.bitsongd/config/config.toml

#this is one command
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC2\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.bitsongd/config/config.toml

#start the node (this command is for rizond launched as a service)
sudo systemctl restart bitsong

#check logs
sudo journalctl -u bitsong -f
```
