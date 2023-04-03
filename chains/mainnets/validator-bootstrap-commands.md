---
description: Some useful quick commands to help bootstrapping validator nodes
---

# Validator bootstrap commands

#### config

<pre class="language-bash"><code class="lang-bash">homed config chain-id chain_name --home $NAME_HOME

# seeds and peers
seeds=""
sed -i "s/^seeds *=.*/seeds = \"$seeds\"/" $NAME_HOME/config/config.toml

peers=""
sed -i "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $NAME_HOME/config/config.toml

# indexing off
indexer="null"
sed -i "s/^indexer *=.*/indexer = \"$indexer\"/" $NAME_HOME/config/config.toml

# min-gas
min_gas_price=""
<strong>sed -i "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"$min_gas_price\"/" $NAME_HOME/config/app.toml
</strong>
# prunning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"

sed -i "s/^pruning *=.*/pruning = \"$pruning\"/" $NAME_HOME/config/app.toml
sed -i "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $NAME_HOME/config/app.toml
<strong>sed -i "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $NAME_HOME/config/app.toml
</strong>sed -i "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $NAME_HOME/config/app.toml

# enable snapshots if needed.
sed -i 's/snapshot-interval *=.*/snapshot-interval = 5000/' $NAME_HOME/config/app.toml

</code></pre>
