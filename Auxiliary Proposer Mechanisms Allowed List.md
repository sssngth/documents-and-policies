# Lido on Ethereum Allow Lists - Auxiliary Proposer Mechanisms

```markdown!
STATUS: V1
```

## A - Validation Process

## B - Allowed APMs & Configurations

### B.1 - Out-Of-Protocol PBS & MEV-Boost
Entry|Category|Parameter(s) #1|Parameter(s) #2
---|---|---|---
[MEV-Boost](https://github.com/flashbots/mev-boost)|MEV-Boost Client Implementation|`relay`,`relays`|`min-bid`
[Commit-Boost PBS module](https://github.com/Commit-Boost/commit-boost-client)|MEV-Boost Client Implementation|`relays`|`min_bid_eth`
[Grandine](https://grandine.io/)|Consensus Client|`builder-api-url`|
[Lighthouse](https://lighthouse.sigmaprime.io/)|Consensus Client|`builder`|`builder-boost-factor`
[Lodestar](https://lodestar.chainsafe.io/)|Consensus Client||
[Nimbus](https://nimbus.team/)|Consensus Client||
[Prysm](https://www.offchainlabs.com/prysm)|Consensus Client||
[Teku](https://consensys.io/teku)|Consensus Client||
[Vero](https://github.com/serenita-org/vero)|Multi-Beacon Node Validator Client|`use-external-builder`|`builder-boost-factor`
[Vouch](https://github.com/attestantio/vouch)|Multi-Beacon Node Validator Client|`blockrelay`|`builder-boost-factor`
[Charon](https://github.com/ObolNetwork/charon)|Distributed Validator Client||
[SSV node](https://github.com/ObolNetwork/charon)|Distributed Validator Middleware||

### B.2 - Out-Of-Protocol Proposer-Builder Separation
Entry|Category|Parameter|Value
---|---|---|---
[MEV-Boost](https://docs.flashbots.net/flashbots-mev-boost/introduction)|Protocol||
[MEV-Boost](https://github.com/flashbots/mev-boost)|MEV-Boost Client Implementation||
|||`relay`|MEV Boost Relay Allowed List
|||`relays`|MEV Boost Relay Allowed List
|||`min-bid`|`0.07`
[Commit-Boost PBS module](https://github.com/Commit-Boost/commit-boost-client)|MEV-Boost Client Implementation||
|||`relays`|MEV Boost Relay Allowed List
|||`min_bid_eth`|`0.07`
[Grandine](https://grandine.io/)|Consensus Client||
|||`builder-api-url`|MEV Boost Relay Allowed List
[Lighthouse](https://lighthouse.sigmaprime.io/)|Consensus Client||
|||`builder`|MEV Boost Relay Allowed List
|||`builder-boost-factor`|`100`
[Lodestar](https://lodestar.chainsafe.io/)|Consensus Client||
[Nimbus](https://nimbus.team/)|Consensus Client||
[Prysm](https://www.offchainlabs.com/prysm)|Consensus Client||
[Teku](https://consensys.io/teku)|Consensus Client||
[Vero](https://github.com/serenita-org/vero)|Multi-Beacon Node Validator Client||
|||`builder-boost-factor`|`100`
|||`use-external-builder`|MEV Boost Relay Allowed List
[Vouch](https://github.com/attestantio/vouch)|Multi-Beacon Node Validator Client||
|||`blockrelay`|MEV Boost Relay Allowed List
|||`builder-boost-factor`|`100`
[Charon](https://github.com/ObolNetwork/charon)|Distributed Validator Client||
[SSV node](https://github.com/ObolNetwork/charon)|Distributed Validator Middleware||
