# Install Hermes Relayer

Setup demonstrates how to setup Hermes relayer and configure it to relay assets between LumenX and Osmosis chains.

* Pre-requisites
  * Create user account
  * Required packages
  * Rust installation
* Relayer build & installation
* Hermes configuration
* Add relayer keys
* Create and start Hermes service

### Pre-requisites

All steps were executed and tested on:

* Ubuntu 22.04 LTS

#### Create user account

```bash
adduser relayer
```

#### Required packages

```bash
sudo apt install make clang pkg-config libssl-dev build-essential git jq llvm libudev-dev -y
```

#### Rust installation

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

**NOTE:** Install Rust as _**relayer**_ user created earlier

### Relayer build & installation

```bash
git clone https://github.com/informalsystems/ibc-rs.git && \
cd ibc-rs && \
git checkout v1.3.0 && \
cargo build --release --bin hermes
```

```bash
mkdir -p ${HOME}/.local/bin ; \
cp ${HOME}/ibc-rs/target/release/hermes ${HOME}/.local/bin
```

### Hermes configuration

Create folder for Hermes configuration

```bash
mkdir -p ${HOME}/.hermes
```

Then populate _**${HOME}/.hermes/config.toml**_ with following content:

```toml
[[chains]]
id = 'osmosis-1'
rpc_addr = 'https://rpc.service.for.osmosis:443'
grpc_addr = 'https://grpc.service.for.osmosis:443'
websocket_addr = 'ws://rpc.service.for.osmosis:443/websocket'
rpc_timeout = '10s'
account_prefix = 'osmo'
key_name = 'osmo'
address_type = { derivation = 'cosmos' }
store_prefix = 'ibc'
default_gas = 3000000
max_gas = 10000000
gas_price = { price = 0.25, denom = 'uosmo' }
gas_adjustment = 0.1
max_msg_num = 30
max_tx_size = 180000
clock_drift = '15s'
max_block_time = '10s'
trusting_period = '14days'
memo_prefix = ''
trust_threshold = { numerator = '1', denominator = '3' }
[chains.packet_filter]
policy = 'allow'
list = [
 ['transfer', 'channel-286'], # LumenX
]


[[chains]]
id = 'LumenX'
rpc_addr = 'http://rpc.service.for.lumenx:26727'
grpc_addr = 'http://rpc.service.for.lumenx:9160'
websocket_addr = 'ws://rpc.service.for.lumenx:26727/websocket'
rpc_timeout = '10s'
account_prefix = 'lumen'
key_name = 'lumen'
address_type = { derivation = 'cosmos' }
store_prefix = 'ibc'
default_gas = 3000000
max_gas = 10000000
gas_price = { price = 0.25, denom = 'ulumen' }
gas_adjustment = 0.1
max_msg_num = 30
max_tx_size = 180000
clock_drift = '15s'
max_block_time = '10s'
trusting_period = '14days'
memo_prefix = ''
trust_threshold = { numerator = '1', denominator = '3' }
[chains.packet_filter]
policy = 'allow'
list = [
 ['transfer', 'channel-3'], # osmosis-1
]
```

**NOTE:** Please note that apropriate addresses and ports for RPC, gRPC and WebSocket services must be adjusted accordingly to point to nodes providing those services.

### Add relayer keys

```bash
hermes-keys add --chain LumenX --mnemonic-file lumenx.txt
hermes-keys add --chain osmosis-1 --mnemonic-file osmosis.txt
```

**NOTE:** files _lumenx.txt_ and _osmosis.txt_ contain mnemonic for wallet which will be used for relayer fees. Wallets for relayer must have some amount of coins prior to starting relayer. Otherwise relayer will not be able to relay any transactions

### Create and start Hermes service

Service file _**/etc/systemd/system/hermes.service**_ should have at least following content:

```bash
[Unit]
Description=Hermes IBC Relayer

[Service]
User=relayer
Group=relayer
ExecStart=/home/relayer/.local/bin/hermes start
LimitNOFILE=180000
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload && \
sudo systemctl enable hermes && \
sudo systemctl restart hermes
```
