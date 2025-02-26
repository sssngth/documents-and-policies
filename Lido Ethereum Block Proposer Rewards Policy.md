# Lido on Ethereum Standard Node Operator Protocol - Block Proposals

```markdown!
STATUS: V3.0
```

## A - Purpose
This Standard Node Operator Protocol (SNOP) outlines the standards, infrastructure and Auxiliary Proposer Mechanisms (APMs) available for block proposals in Ethereum (see [Appendix A](#appendix-a---ethereum-standards-infrastructure--auxiliary-proposer-mechanisms)); the rules around the usage of this SNOP; the considerations that guide the expectations of Lido on Ethereum (LoE) with regard to block proposals and APMs; the responsibilities Node Operators (NOs) using LoE have in performing block proposals, providing APMs and distributing the rewards that accrue from those actions to the stakers; the solutions employed to monitor the NOs’ activities, and the actions that NOs can expected in case of non-conformance with this SNOP.

## B - Scope
This SNOP applies to LoE, the NOs who use one or more of the [Staking Router (SR)'s](https://docs.lido.fi/contracts/staking-router) [Curated Module (CM)](https://operatorportal.lido.fi/modules/curated-module), [Simple Distributed Validator Technology Module (SDVTM)](https://operatorportal.lido.fi/modules/simple-dvt-module), [Community Staking Module (CSM)](https://operatorportal.lido.fi/modules/community-staking-module), and any future Staking Module (SM), and the validators the NOs operate as part of the Liquid Staking Protocol (LSP).

The below stipulated operational flows and expectations apply to all above-mentioned SMs, except in cases where specific mentions are made to alternate flows or expectations for specific SMs due to differences in their technical or operational characteristics.

**_NOTE:_** As terms can have different meanings based on how they are used and in different contexts, the below table aims to clarify the usage of various terms for the purposes of this SNOP and how they apply within the context of LoE.

Term|Definition|Context
:---:|---|---
Node Operator|An entity, individual, or set thereof who operate(s) nodes while using one or more of LoE's SMs to run Ethereum validators|CM & CSM solo operation:</br>A single entity or individual operating validators</br>SDVTM operation:</br>A cluster of independent entities and/or individuals operating validators as a group</br>CSM DVT operation:</br>An entity, individual, or cluster thereof operating validators
Validator|A validator, also referred to as a "key" in this SNOP, is the technical component of participating in Ethereum's consensus, that - if active - represents a stake weight of up to 32 ETH, is controlled by a public-private validator signing key pair and performs duties for the network -- attesting to and proposing new blocks, participating in sync committees, and reporting network violations of the slashing rules|CM & CSM solo operation:</br>A validator run by a single NO</br>SDVTM operation:</br>A Distributed Validator (DV) run by a cluster of independent NOs utilizing [Distributed Validator Technology (DVT)](https://ethereum.org/en/staking/dvt/#distributed-validator-technology)</br>CSM DVT operation:</br>A DV run by a single NO or cluster utilizing DVT

## C - Guiding Considerations
Constituted in the [Lido DAO Vibe](https://snapshot.org/#/s:lido-snapshot.eth/proposal/0x739fbe56425d355b5e41c22bb346b7a8217afd9e84aa49863648b1c641a482e3) and iterated upon by the community in a regularly performed [Guided Open Objective Setting Exercise (GOOSE)](https://snapshot.org/#/s:lido-snapshot.eth/proposal/0x58bbc5d540e46081043ef29d4d1ee56b7df4dc1b0369aa78e0c15a2403549353), Lido strives to contribute to the decentralization, accessibility, and censorship resistance of Ethereum through the provision of simple and secure staking for the ecosystem. The following guiding considerations for the expectations, practical implementation, and utilization of LoE are derived from these overarching goals.

### C.1 - Economic Security
Outlined in [Hasu's proposal on Maximal Extractable Value (MEV)](https://research.lido.fi/t/proposal-optimal-mev-policy-for-lido/2489), the game-theoretical best approach for LoE to preserve the security of Ethereum's Proof-of-Stake (PoS) consensus against 51% attacks, stakers' low threshold access to Decentralized Finance (DeFi), and the positive impact that the protocol can have on the network through efforts towards the decentralization and censorship resistance of the validator set, is by optimizing for LoE's economic competitiveness.

Pursuing this goal primarily calls for the maximization of the protocol's staking APR by enabling NOs using LoE to fulfill the Ethereum validator duties - attesting to and proposing new blocks, participating in sync committees, and reporting network violations of the slashing rules - as reliably, timely, correctly, and free of slashable offenses as possible.

Beyond that, the leading adoption of the latest native and Ethereum-aligned standards, infrastructure, and developments that have the potential to profoundly change the staking landscape and/or impact stakers economically should be continued.

Unlike rewards for the fulfillment of duties on the Consensus Layer (CL), the compensations for services on the Execution Layer (EL) are not automatically issued to a validator's account by the Ethereum protocol, but accrue on an NO-determined address -- e.g. in the form of MEV extracted and [priority fees](https://ethereum.org/en/developers/docs/gas/#priority-fee) sent by users to incentivize the inclusion of their transactions. This architecture carries the risk that NOs using LoE could try to hide EL rewards directly received in an attempt to earn more than their rightful share of the protocol's smoothed rewards. In the best interest of stakers and honest NOs, rewards stealing in LoE must be as prohibitively difficult to conceal and expensive to execute as possible. Irrespective if for a [bonded SM](https://operatorportal.lido.fi/modules/community-staking-module#block-88e6d7eca6364a758541dc1ee66a278f) such as CSM, with by design more options for automated enforcement of rules, or a module based on social reputation -- to ensure fair conditions in all SMs, it is essential to continuously and transparently monitor NO behavior around block proposals and rewards distribution. For this purpose, a multitude of community-built open-source [tools](https://operatorportal.lido.fi/existing-operator-portal/ethereum-onboarding/no-resources-tooling) and [dashboards](https://operatorportal.lido.fi/operator-statistics-and-metrics) are available for LoE.

In the context of APMs, attention is to be paid to the careful consideration of potential implications the implementation of new mechanisms into the protocol may have on the block construction and proposal value chain, and that any benefits that may be gained in certain areas are measured against potential losses in others -- specifically with respect to the resulting value of blocks proposed by NOs using LoE.

### C.2 - Decentralization
Decentralization is one of the core values of the Lido DAO and its prevalence, just like that of economic security, an integral premise for the robustness of the Ethereum network -- comparatively only in a more technical-operational sense. For this reason, the hitherto outlined optimization of LoE solely for economic security must be expanded and weighed up with aspects of decentralization to provide the holistic guidance this SNOP seeks to offer.

Diverse setups, i.a., in terms of the geographic dispersion, infrastructure and clients utilized to run nodes, are critical to proactively mitigate risks of local outages, isolated bugs, and to globally reduce Ethereum p2p latencies. NOs using LoE are encouraged to drive the sustained efforts to achieve those goals and are thus afforded a performance margin that allows even those from underrepresented regions and such with setups that are not primarily geared towards rewards maximization to equitably participate and valuably contribute to the system.

For the Lido community, the ongoing improvement of Ethereum is at the heart of all interests. Therefore, the active involvement in discussions on the latest developments and their directional introduction into LoE should continue, so that in the process of sensitive transformations the network can remain to count on the assistance of a broad set of well-coordinated and proficient NOs.

Another important aspect of decentralization is credible neutrality. For LoE, this means that not only the first available or most popular implementation of an APM should be supported by the protocol, but any solution that is not in conflict with the spirit of Ethereum or this SNOP, demonstrably strives for the goals intended by the authors of the underlying standard(s), the inclusion of which was publicly proposed - e.g. on the [Lido Research Forum](https://research.lido.fi/) - and whose technology has been thoroughly tested.

### C.3 - Censorship Resistance
A third attribute of key importance to Ethereum, and a factor for LoE to balance the dimensions of economic security and decentralization against, is censorship resistance.

Even with the current form of out-of-protocol proposer-builder separation (PBS) and the resulting dissolution of the validators' former monopoly over transaction inclusion and ordering (see [Appendix A.4.1 ff](#a41---proposer-builder-separation)), Ethereum users can still be censored at various points along the block construction and proposal pipeline. In fact, [it has been shown](https://censorship.pics/) that recently it is most rarely the validators that do not propose blocks that include transactions from certain addresses, but rather builders that disregard them when constructing payloads and/or relays that refuse to broadcast such to the network, that cause [censorship latency ("inclusion delay")](https://eth.neutralitywatch.com/). Of course, though, there are also validators that are only connected to censoring relays and/or do not take all transactions into account when building local blocks (see [Appendix A.3](#a3---local-block-building)).

As outlined in section decentralization (see [C.2](#c2---decentralization)), for the operational health of Ethereum, LoE should foster credible neutrality, which - in regards to the users - logically extends to the rejection of the censorship of transactions. Every user who respects the rules of the network should be able to participate in the public good that is Ethereum without restrictions. It is acknowledged that some NOs using LoE are not able to provide unrestricted access to the network due to the regulatory requirements of the jurisdictions they operate in, yet all NOs are called upon to continually seek to push the boundaries towards greater freedom through thought leadership and exemplary behavior.

## D - Node Operator Responsibilities
Based on the guiding considerations, the following responsibilities arise for the NOs using LoE in respect to the configuration of their validators for the purpose of proposing blocks, and the minimum expected EL performance when fulfilling fundamental Ethereum duties as well as optional APM services. Moreover, this section outlines which consequences the NOs can expect in the event of non-conformance with this SNOP and in which cases the measures taken might differ between SMS due to their technical or operational differences.

### D.1 - MEV Boost Relay Allowed List
To foster Ethereum's decentralization and empower NOs who would otherwise not be able to economically viable compete with actors who pursue sophisticated strategies for maximal extraction of MEV, LoE supports out-of-protocol PBS according to the MEV-Boost specification (see [Appendix A.4.1 ff](#a41---proposer-builder-separation)). To ensure their reliability and values-alignment with Ethereum, each relay applying to serve LoE undergoes a vetting process by the [Relay Maintenance Committee (RMC)](https://research.lido.fi/t/lido-on-ethereum-identify-and-constitute-relay-maintenance-committee/3386).

It is the responsibility of each NO using LoE, at all active duty times, to have configured in the CC or out-of-protocol PBS sidecar of choice, for each validator run for the protocol, regardless of SM, but depending on whether a key is operated on [Mainnet](https://etherscan.io/address/0xf95f069f9ad107938f6ba802a3da87892298610e#readContract) or [Holešky](https://holesky.etherscan.io/address/0x2d86C5855581194a386941806E38cA119E50aEA3#readContract), the address(es) of at least one vetted relay labeled “must use some”, as well as any number of entries labeled "may use" of the MEV Boost Relay Allowed List - found on the [Node Operator Portal](https://enchanted-direction-844.notion.site/6d369eb33f664487800b0dedfe32171e?v=d255247c822c409f99c498aeb6a4e51d) or by querying the `get_relays` method of the respective smart contract.

Proposals of blocks that get flagged by monitoring because of their unknown payload source (PS), or - aside from locally built ones - exclusively of blocks whose payloads were sourced from relays labeled “may use”, although early in the relevant slots there were more valuable payloads available via relays labeled “must use some”, indicate the potential misconfiguration of an NO's validator(s).

In cases of an NO of the CM or SDVTM, contributors to the Node Operator Mechanisms (NOM) workstream of the Lido DAO will reach out to the NO and ask for a clarification of the situation. If force majeure can be ruled out as the cause of the incident and no other plausible explanation can be provided, it must be assumed that an unvetted relay has been configured by the NO or that the “must use some” selection has been ignored. Consequently, depending on the severity of the offense, contributors may consider raising a formal issue with the NO on the Lido Research Forum, requesting remediative action, and seeking the reimbursement of missed rewards.

In cases of an NO of the CSM, the approach is basically the same, with the two exceptions that reaching the NO is only possible if a contact was voluntarily shared with the community, and that instead of contributors to NOM such to the [Community Staking Module Committee](https://research.lido.fi/t/community-staking-module-committee/8333) will reach out to the NO.

**_NOTE:_** For CSMv2, an automation of measures in response to offences is being contemplated.

### D.2 - Censorship Resistance Allowance

### D.3 - Minimum EL Performance

### D.4 - Fee Recipient
To protect the economic competitiveness of LoE, rewards stealing must be prevented.

It is the responsibility of each NO using LoE, at all active duty times, to have configured in the CC, VC and/or signing service of choice, for each validator run for the protocol, regardless of SM, but depending on whether a key is operated on [Mainnet](https://docs.lido.fi/deployed-contracts/), [Holešky](https://docs.lido.fi/deployed-contracts/holesky/) or [Sepolia](https://docs.lido.fi/deployed-contracts/sepolia/), the `LidoExecutionLayerRewardsVault` as the `fee_recipient` for the block rewards, comprising of the priority fees received from user for transaction inclusion, the MEV extracted through PBS and the compensations for optional APM services provided.

Proposals of blocks that get flagged by monitoring because of their unknown fee recipient (FR), indicate the potential misconfiguration of an NO's validator(s).

In cases of an NO of the CM or SDVTM, contributors to NOM will reach out to the NO and ask for a clarification of the situation. If force majeure can be ruled out as the cause of the incident and no other plausible explanation can be provided, it must be assumed that the FR has been incorrectly configured by the NO and the proposed block's rewards have been stolen. Consequently, depending on the severity of the offense, contributors may consider raising a formal issue with the NO on the Lido Research Forum, requesting remediative action, and seeking the reimbursement of the stolen rewards.

In cases of an NO of the CSM, upon detection of a proposal with an unknown FR, an amount of the affected NO's bond equal to the block rewards and an additional disincentive of 0.1 ETH is locked for 8 weeks, and the NO is afforded a challenge period to discuss the situation in the [CSM Support category](https://research.lido.fi/c/csm-support/21) of the Lido Research Forum. Once the NO compensates LoE for the incident or upon expiry of the challenge period, if an agreement has been reached with the community that force majeure was the cause of the event, the lock on the bond is lifted. If otherwise neither an agreement can be reached, nor a compensation is being paid, the CSM Committee will initiate an [Easy Track motion](https://lido.fi/governance#easy-track) to propose to the Lido DAO that the locked portion of the NO's bond be burned as a penalty. Any of the NO's validators that fail to provide a full bond - i.e. become unbonded - as a result of this measure are [requested to exit the protocol](https://lido.mypinata.cloud/ipfs/QmZTMfmJZsYHz61f2FjhYdh5VNu6ifjYQJzYUGkysHs8Uu). If the requested validators are not exited, they are marked stuck, and no operator rewards are distributed to the NO until the stuck keys are exited.

**_NOTE:_** After the implementation of [EIP-7002: Execution Layer Triggerable Withdrawals](https://eips.ethereum.org/EIPS/eip-7002) into LoE, stuck validators will instead be forcefully ejected at the protocol level.

# Appendix
## Appendix A - Ethereum Standards, Infrastructure & Auxiliary Proposer Mechanisms
### A.1 - Beacon Node API
[Application programming interface (API)](https://ethereum.github.io/beacon-APIs) exposed by a beacon node (BN) - either as consensus client (CC) or validator client (VC) implementation - to query and participate in the Beacon Chain (BC) according to [Ethereum's consensus specification](https://github.com/ethereum/consensus-specs). In the context of this SNOP, relevant both as a prerequisite for validator nodes to fulfill fundamental duties and to allow NOs using LoE to customize their block proposal approaches. 

### A.2 - Advancing the State of Ethereum
The process of advancing Ethereum's state is based on attaching new blocks of information to the blockchain and begins with the PoS consensus pseudo-randomly assigning the block proposal duty for an upcoming slot to one of the active validators. Once the slot arrives, the assigned validator proposes by compiling a [plurality of CL and EL data](https://ethereum.org/en/developers/docs/blocks/#block-anatomy) into a block, signing, and broadcasting it to the network. Subsequently, the other validators check the proposed block and, if the required supermajority attests to its validity, the block is appended to the blockchain, updating the global state.

Sometimes a validator fails to propose a block for an assigned slot -- the slot then remains empty, the state of Ethereum unchanged, and the affected block is referred to as missed. Another exception is a block that is temporarily part of the blockchain, but replaced before finalization by another block through mechanisms such as reorganizations (reorgs) or forks of [Ethereum's Gasper consensus algorithm](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/gasper/). This can happen, e.g., because the proposed block was shared with the network too late to reach and obtain the attestations of a sufficient amount of validators, or because the chain split due to changes in the protocol rules or community disagreements about the future of Ethereum. Blocks that are abandoned during finalization, are called orphans.

### A.3 - Local Block Building
In local block building, the validator assigned for the proposal duty of a slot selects and orders to its preference the transactions to be included in the block from the local mempool of its execution client (EC). Those transactions may originate from the validator itself, from users who have sent them to the validator for inclusion, or from Ethereum's public mempool. The EC has access to the latter by participating in the EL gossip network, which allows the EC to collect transactions for its local mempool that are broadcast by other validators that have verified the validity of those transactions by successfully executing them on their local nodes.

In the assigned slot and initiated by a request of its CC, the validator selects the transactions to be included, taking into account the gas limit of the block. The EC then locally executes the selected transactions in the specified order to obtain an updated state tree, the root of which it sends to the CC along a list of the included transactions -- the execution payload. Following this, the CC takes the state root and execution payload received, and together with a range of additional data, wraps them into a block. In further steps, the block is forwarded to the signing service or VC handling the validator's private key for signing, and back to the CC for broadcasting to the network via the gossip protocol of the CL, so that finally the other validators can check the block's validity and vote on its appending.

A block locally built on the node(s) of a validator is often referred to as vanilla.

* https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/
* https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/block-proposal/
* https://ethereum.org/en/developers/docs/networking-layer/#when-consensus-client-is-block-producer

### A.4 - Auxiliary Proposer Mechanisms
* https://www.notion.so/Preconfirmations-Landscape-and-Guiding-Considerations-184bf633d0c98069a5ccdc87e5770495#18bbf633d0c980929723c9eeff36840d

#### A.4.1 - Proposer-Builder Separation
[PBS](https://notes.ethereum.org/@vbuterin/pbs_censorship_resistance) is Ethereum's answer to the risk of block proposal centralization in the hands of the actors with the most resources to pursue sophisticated strategies to maximally extract MEV at the expense of those in the ecosystem, who are not able to manage the involved complexity equally efficiently, or who - directly or indirectly - have to pay for the cost of the measures taken by the sophisticated actors. PBS fixes this by separating two authorities that previously both resided with the validator -- to build the execution payload and to propose the block for a slot assigned by the network. (see [Local Block Building](#a3---local-block-building))

##### A.4.1.1 - Builder API
The [Builder API](https://ethereum.github.io/builder-specs) is [Ethereum's specification](https://github.com/ethereum/builder-specs) of a PBS solution that, while requiring greater trust assumptions, temporarily addresses block proposal centralization concerns by allowing the CCs of validators to source execution payloads from external builders until full ePBS becomes available.

##### A.4.1.2 - Out-Of-Protocol PBS Implementations
Out-of-protocol implementations serve as functionally as close as possible equivalents to a native Ethereum PBS mechanism, and define the standards and rules around the interactions of actors along the specialized block construction and proposal pipeline until [enshrined proposer-builder separation (ePBS)](https://eips.ethereum.org/EIPS/eip-7732) becomes implemented in the base protocol.

MEV-Boost is the original implementation of out-of-protocol PBS, whose architecture was later used as basis for both the development of Ethereum's Builder API and Commit-Boost's PBS module, which, like Flashbot's own client, is a validator sidecar -- the node-side software that allows validators to permissionlessly participate in the MEV-Boost protocol.

##### A.4.1.3 - Consensus Clients
* Grandine (not production-ready)
* Lighthouse
* Lodestar
* Nimbus
* Prysm
* Teku

##### A.4.1.4 - Multi-Beacon-Node Validator Clients
* Vero
* Vouch

##### A.4.1.5 - Distributed Validator Clients & Middleware
* Obol Charon
* SSV

#### A.4.2 - Credible Proposer Commitments
### A.5 - Builder, Relay & Credible Proposer Commitment Protocol Operators
#### A.5.1 - Troubleshooting
#### A.5.2 - Responsibilities & Incident Management
