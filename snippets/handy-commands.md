# Handy commands

### Global consensus state

```bash
curl http://localhost:26657/consensus_state | jq '.result.round_state.height_vote_set[0].prevotes_bit_array'
```

### Validator pre-voting status

```bash
curl -s http://localhost:26657/dump_consensus_state | jq '.result.round_state.votes[0].prevotes' | grep $(curl -s http://localhost:26657/status | jq -r '.result.validator_info.address[:12]')
```

### Validator list

```bash
gaiad q staking validators  --node http://localhost:26657 -o json --limit=1000 | jq '.validators[]' | jq -r '.tokens + " - " + .status + " - " +  .description.moniker + " - " + .operator_address' | sort -gr | nl
```

### Number of connected peers

```bash
curl -s http://127.0.0.1:26677/metrics | grep '^tendermint_p2p_peers'
```

### Number of peers

```bash
curl -s 127.0.0.1:26657/net_info  | grep n_peers
```

### Validator voting power

```bash
curl -s localhost:26657/status | jq .result.validator_info.voting_power
```

### Peer list

```bash
curl -s http://127.0.0.1:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | paste -sd,
```

### Check Chain ID via REST API

```bash
curl --location --request GET 'https://NODE_IP/node_info' -s | jq '.node_info.network' | tr -d '"'
```

### Download Genesis from RPC

```bash
curl https://NODE_IP/genesis | jq '.result.genesis' > genesis.json
```
