---
description: >-
  Cosmos SDK State Sync Guide The recent Tendermint Core 0.34 release includes
  support for state sync, which allows a new node to join a network by fetching
  a snapshot of the application state
---

# State-Sync

#### Enable StateSync <a href="#enable-statesync" id="enable-statesync"></a>

To enable StateSync adjust following parameters in `app.toml:`

`pruning-keep-every = "1000"`\
`[state-sync]`\
`snapshot-interval = 1000`\
`snapshot-keep-recent = 3`

``

Once changes are done restart node.

That will create snapshot every 1000 blocks and will keep 3 snapshots.

``
