# OKP4 State Sync

## Info
#### Public RPC endpoint: http://135.181.178.53:26657/
#### Public API: http://135.181.178.53:26317/

## Guide to sync your node using State Sync:

### Copy the entire command
```
sudo systemctl stop okp4d
SNAP_RPC="http://95.216.2.219:26651"; \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash); \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.okp4d/config/config.toml

peers="3eef2dd3ba22c18cd4021e474f49b3338af96a21@http://95.216.2.219:26661" \
&& sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.okp4d/config/config.toml 

okp4d tendermint unsafe-reset-all --home ~/.okp4 && sudo systemctl restart okp4d && \
journalctl -u okp4d -f --output cat
```

### Turn off State Sync Mode after synchronization
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.okp4d/config/config.toml
```
