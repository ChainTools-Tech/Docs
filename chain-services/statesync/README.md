# StateSync

### Enable StateSync

To enable StateSync adjust following parameters in app.toml:

`pruning-keep-every = "1000"`\
`[state-sync]`\
`snapshot-interval = 1000`\
`snapshot-keep-recent = 3`

Once changes are done restart node.

That will create snapshot every 1000 blocks and will keep 3 snapshots.

