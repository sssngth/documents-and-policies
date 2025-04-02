# Lido on Ethereum Allow Lists - Auxiliary Proposer Mechanisms

```markdown!
STATUS: V1
```

## A - Validation Process

## B - Allowed APMs & Configurations
### B.1 - Out-Of-Protocol Proposer-Builder Separation
Entry|Type|Parameter|Value
---|---|---|---
[MEV-Boost](https://docs.flashbots.net/flashbots-mev-boost/introduction)|Protocol||
[MEV-Boost](https://github.com/flashbots/mev-boost)|MEV-Boost Implementation||
||Setting|`relay`|MEV Boost Relay Allowed List
||Setting|`relays`|MEV Boost Relay Allowed List
||Setting|`min-bid`|`0.07`
[Commit-Boost PBS module](https://github.com/Commit-Boost/commit-boost-client)|MEV-Boost Implementation||
||Setting|`relays`|MEV Boost Relay Allowed List
||Setting|`min_bid_eth`|`0.07`
[Grandine](https://grandine.io/)|Consensus Client||
||Setting|`builder-api-url`|MEV Boost Relay Allowed List
[Lighthouse](https://lighthouse.sigmaprime.io/)|Consensus Client||
||Setting|`builder`|MEV Boost Relay Allowed List
||Setting|`builder-boost-factor`|`100`
[Lodestar](https://lodestar.chainsafe.io/)|Consensus Client||
[Nimbus](https://nimbus.team/)|Consensus Client||
[Prysm](https://www.offchainlabs.com/prysm)|Consensus Client||
[Teku](https://consensys.io/teku)|Consensus Client||
[Vero](https://github.com/serenita-org/vero)|Multi-Beacon Node Validator Client||
||Setting|`builder-boost-factor`|`100`
||Setting|`use-external-builder`|MEV Boost Relay Allowed List
[Vouch](https://github.com/attestantio/vouch)|Multi-Beacon Node Validator Client||
||Setting|`blockrelay`|MEV Boost Relay Allowed List
||Setting|`builder-boost-factor`|`100`
[Charon](https://github.com/ObolNetwork/charon)|Distributed Validator Client & Middleware||
[SSV node](https://github.com/ObolNetwork/charon)|Distributed Validator Client & Middleware||
