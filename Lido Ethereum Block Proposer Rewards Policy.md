# Lido on Ethereum Standard Node Operator Protocol - Block Proposals

```markdown!
STATUS: V3.0
```

## A - Purpose
This Standard Node Operator Protocol (SNOP) outlines the standards, Auxiliary Proposer Mechanisms (APMs) and infrastructure available for block proposals in Ethereum (see [Appendix A](#appendix-a---ethereum-standards-auxiliary-proposer-mechanisms--infrastructure)), the rules around the usage of this SNOP, the considerations that guide the expectations of the Lido on Ethereum protocol ("Lido") with regard to block proposals and APMs, the responsibilities Node Operators (NOs) using Lido have in performing block proposals utilizing APMs and distributing the associated rewards to stakers, and the actions that NOs can expect in case of non-conformance with this SNOP.

## B - Scope
This SNOP applies to Lido, the NOs who use one or more of the [Staking Router (SR)'s](https://docs.lido.fi/contracts/staking-router) [Curated Module (CM)](https://operatorportal.lido.fi/modules/curated-module), [Simple Distributed Validator Technology Module (SDVTM)](https://operatorportal.lido.fi/modules/simple-dvt-module), [Community Staking Module (CSM)](https://operatorportal.lido.fi/modules/community-staking-module), future Staking Module (SM) or staking avenue, and the validators the NOs operate as part of the protocol.

The below stipulated operational flows and expectations apply to all above-mentioned SMs, except in cases where specific mentions are made to alternate flows or expectations for specific SMs due to differences in their technical or operational characteristics.

**_NOTE:_** As terms can have different meanings based on how they are used and in different contexts, the table below aims to clarify the usage of various terms for the purposes of this SNOP and how they apply within the context of Lido.

Term|Definition|Context
:---:|---|---
Node Operator|An entity, individual, or set thereof who operate(s) nodes while using one or more of Lido's SMs to run Ethereum validators|CM & CSM solo operation:</br>A single entity or individual operating validators</br>SDVTM operation:</br>A cluster of independent entities and/or individuals operating validators as a group</br>CSM DVT operation:</br>An entity, individual, or cluster thereof operating validators
Validator|A validator, also referred to as a "key" in this SNOP, is the technical component of participating in Ethereum's consensus, that - if active - represents a maximum effective balance ("stake weight") of between 32 and - in increments of 1 - up to 2048 ETH, is controlled by a public-private validator signing key pair and performs duties for the network -- attesting to and proposing new blocks, participating in sync committees, and reporting network violations of the slashing rules|CM & CSM solo operation:</br>A validator run by a single NO</br>SDVTM operation:</br>A Distributed Validator (DV) run by a cluster of independent NOs utilizing [Distributed Validator Technology (DVT)](https://ethereum.org/en/staking/dvt/#distributed-validator-technology)</br>CSM DVT operation:</br>A DV run by a single NO or cluster utilizing DVT

## C - Guiding Considerations
Constituted in the [Lido DAO Vibe](https://snapshot.org/#/s:lido-snapshot.eth/proposal/0x739fbe56425d355b5e41c22bb346b7a8217afd9e84aa49863648b1c641a482e3) and iterated upon by the community in regularly performed [Guided Open Objective Setting Exercises (GOOSE)](https://snapshot.org/#/s:lido-snapshot.eth/proposal/0x58bbc5d540e46081043ef29d4d1ee56b7df4dc1b0369aa78e0c15a2403549353), Lido strives to contribute to the decentralization, accessibility, and censorship resistance of Ethereum through the provision of simple and secure staking for the ecosystem. The following guiding considerations are derived from these overarching goals.

### C.1 - Economic Security
To protect Ethereum's Proof-of-Stake (PoS) consensus from 51% attacks and the positive impact that the protocol can have on the network's validator set, Lido focuses on economic competitiveness. This approach is based on [Hasu's Maximal Extractable Value (MEV) proposal](https://research.lido.fi/t/proposal-optimal-mev-policy-for-lido/2489).

Pursuing this goal primarily calls for the maximization of the protocol's staking APR by enabling NOs using Lido to fulfill the Ethereum validator duties - attesting to and proposing new blocks, participating in sync committees, and reporting network violations of the slashing rules - as reliably, timely, correctly, and free of slashable offenses as possible.

Additionally, Lido should adopt the latest Ethereum-aligned standards and infrastructure that may transform staking or impact stakers financially.

Execution Layer (EL) rewards accrual on an NO-determined address -- e.g. in the form of MEV extracted and [priority fees](https://ethereum.org/en/developers/docs/gas/#priority-fee), carries the risk that NOs using Lido could try to hide EL rewards directly received in an attempt to earn more than their rightful share of the protocol's smoothed rewards. In the best interest of stakers and honest NOs, rewards stealing in Lido must be as prohibitively difficult to conceal and expensive to execute as possible. Irrespective if for a [bonded SM](https://operatorportal.lido.fi/modules/community-staking-module#block-88e6d7eca6364a758541dc1ee66a278f) such as CSM or a module based on social reputation -- to ensure fair conditions in all SMs, it is essential to continuously and transparently monitor NO behavior around block proposals and rewards distribution. For this purpose, a multitude of community-built open-source [tools](https://operatorportal.lido.fi/existing-operator-portal/ethereum-onboarding/no-resources-tooling) and [dashboards](https://operatorportal.lido.fi/operator-statistics-and-metrics) are available for Lido.

In the context of APMs, attention is to be paid to the potential implications that implementing new mechanisms into the protocol may have on the block construction and proposal value chain, specifically with respect to the resulting value of blocks proposed by NOs using Lido.

### C.2 - Decentralization
Decentralization is one of the core values of the Lido DAO and its prevalence, just like that of economic security, an integral premise for the robustness of the Ethereum network. For this reason, the optimization of Lido solely for economic security must be expanded and weighed up with aspects of decentralization to provide holistic guidance to this SNOP.

Diverse setups, i.a., in terms of the geographic dispersion, infrastructure and clients utilized to run nodes, are critical to proactively mitigate risks of local outages, isolated bugs, and to globally reduce Ethereum p2p latencies. NOs using Lido are encouraged to drive these sustained efforts and are thus afforded a performance margin that allows those from underrepresented regions and such with setups not primarily geared towards rewards maximization to equitably participate and valuably contribute to the system.

Another important aspect of decentralization is credible neutrality. For Lido, this means that not only the first available or most popular implementation of an APM should be supported by the protocol, but any solution that is not in conflict with the spirit of Ethereum or this SNOP, demonstrably strives for the goals intended by the authors of the underlying standard(s), whose inclusion was publicly proposed - e.g. on the [Lido Research Forum](https://research.lido.fi/) - and whose technology has been thoroughly tested.

### C.3 - Censorship Resistance
A third attribute to balance the dimensions of economic security and decentralization against, is censorship resistance.

Even with the current form of out-of-protocol proposer-builder separation (PBS) and the resulting dissolution of the validators' former monopoly over transaction inclusion and ordering (see [Appendix A.4.1 ff](#a41---proposer-builder-separation)), Ethereum users can still be censored at various points along the block construction and proposal pipeline. In fact, [it has been shown](https://censorship.pics/) that recently it is most rarely the validators that do not propose blocks that include transactions from certain addresses, but rather builders that disregard them when constructing payloads and/or relays that refuse to broadcast such to the network, which cause [censorship latency ("inclusion delay")](https://eth.neutralitywatch.com/).

As outlined in section decentralization (see [C.2](#c2---decentralization)), for the operational health of Ethereum, Lido should foster credible neutrality, which logically extends to the rejection of the censorship of transactions. Every user who respects the rules of the network should be able to participate in the public good that is Ethereum without restrictions. It is acknowledged that some NOs using Lido are not able to provide unrestricted access to the network due to the regulatory requirements of the jurisdictions they operate in, yet all NOs are called upon to continually seek to push the boundaries towards greater freedom through thought leadership and exemplary behavior.

## D - Node Operator Responsibilities
Based on the guiding considerations, the following responsibilities arise for NOs using Lido in respect to the configuration of the validators run for the protocol to propose blocks. Moreover, this section outlines how potential breaches of the responsibilities are identified in the [Lido Fees Monitoring Dashboard](https://fees-monitoring.lido.fi/).

### D.1 - Fee Recipient
NOs have to configure for each validator run for the protocol in the consensus client (CC), validator client (VC) and/or signing service of choice the Lido Execution Layer Rewards Vault for the [relevant Ethereum network](https://docs.lido.fi/deployed-contracts/) as the `fee_recipient`.

In monitoring, proposed blocks labeled with an unknown fee recipient (FR) indicate either a misconfiguration of an NO's infrastructure in regard to the fee recipient, or a possible stealing attempt.

### D.2 - APMs Allowed List
NOs are permitted to run APMs that have been vetted by the community and are included in the [APMs Allowed List](). This list comprises APMs that have been assessed as sufficiently de-risked and demonstrably beneficial to both Lido and the broader Ethereum ecosystem. Any APM seeking inclusion in the list must follow the standard evaluation and approval procedure.

NOs operating APMs, for each validator run for the protocol using an APM, have to adhere to all relevant configuration requirements, security best practices, and guidelines related to the distribution of associated rewards defined in this SNOP and the APMs Allowed List.

### D.3 - MEV Boost Relay Allowed List
NOs have to configure for each validator run for the protocol in the CC or out-of-protocol PBS sidecar of choice (see [Appendix A.4.1 ff](#a41---proposer-builder-separation)) the address(es) of at least one vetted relay labeled "must use some" and any number of entries labeled "may use" of the MEV Boost Relay Allowed List - found on the [Node Operator Portal](https://enchanted-direction-844.notion.site/6d369eb33f664487800b0dedfe32171e?v=d255247c822c409f99c498aeb6a4e51d) or by querying the `get_relays` method of the smart contract for the [relevant Ethereum network](https://docs.lido.fi/deployed-contracts/).

In monitoring, proposed blocks labeled with an unknown payload source (PS) or - aside from vanilla ones (see [Appendix A.3](#a3---local-block-building)) - exclusively with blocks whose payloads were sourced from relays labeled "may use", although at the beginning of the allocated slots there were more valuable payloads available via relays labeled "must use some", indicate the potential misconfiguration of an NO's infrastructure in regard to the MEV Boost Relay Allowed List.

### D.4 - Local Block Building Allowance
NOs are permitted to configure their infrastructure on a per-validator basis to enable local block building (see [Appendix A.3](#a3---local-block-building)) in ways that most effectively balance the inclusion of otherwise disregarded transactions and the rewards foregone by Lido to strengthen Ethereum's censorship resistance. Given the evolving technical options and volatile market conditions of the network, the methods and values allowed to enable local block building are determined by social consensus between contributors to the Lido DAO and the remaining stakeholders of the protocol in the [Local Block Building Allowance thread]() on the Lido Research Forum.

NOs opting into the local block building allowance, for each validator run for the protocol using the allowance, have to adhere to all relevant configuration requirements defined in this SNOP and the Local Block Building Allowance thread.

In monitoring, proposed blocks labeled with an unknown payload source (PS) indicate the potential misconfiguration of an NO's infrastructure in regard to the local block building allowance.

## E - Consequences of Non-conformance

# Appendix
## Appendix A - Ethereum Standards, Auxiliary Proposer Mechanisms & Infrastructure
### A.1 - Advancing Ethereum's State
Advancing Ethereum's state revolves around attaching new blocks to the blockchain and begins with the PoS consensus pseudo-randomly allocating the block proposal duty for an upcoming slot to one of the active validators. Once the allocated slot arrives, the validator compiles a [plurality of consensus layer (CL) and EL data](https://ethereum.org/en/developers/docs/blocks/#block-anatomy) into a block, signs, and broadcasts it to the network. The other validators then check the proposed block and, if the required supermajority attests to its validity, it is appended to the blockchain, updating the global state.

Sometimes a validator fails to propose a block -- the slot then remains empty, the state of Ethereum unchanged, and the affected block is referred to as "missed". Another exceptional case is a block that is temporarily part of the blockchain, but replaced by another block before finalization through events such as reorganizations ("reorgs") or "forks" of Ethereum's [Gasper consensus mechanism](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/gasper/). This can happen, e.g., because the block was proposed to the network too late to reach and obtain the attestations of a sufficient number of validators, because the blockchain split due to changes in the protocol rules, or because of a community disagreement about the future of Ethereum. Blocks that are abandoned during finalization are called "orphans".

### A.2 - Beacon Node API
[Application programming interface (API)](https://ethereum.github.io/beacon-APIs) exposed by beacon nodes (BNs) - either as CC or VC - to query and participate in the Beacon Chain (BC) according to [Ethereum's consensus specification](https://github.com/ethereum/consensus-specs). The Beacon Node API is relevant to this SNOP both as the standard required to enable nodes to fulfill fundamental validator duties and to allow NOs using Lido to customize their block proposal setups.

### A.3 - Local Block Building
In local block building, the validator allocated to the proposal duty of a slot selects the transactions to be included in the block from the local mempool of its execution client (EC) and orders them to its preference. The transactions can originate from the validator itself, from users who sent them to the validator for inclusion, or Ethereum's public mempool through the EL gossip network.

In the allocated slot and initiated by a request of its CC, the validator selects the transactions to be included, taking into account the block's gas limit. The EC then locally executes the selected transactions in the specified order to obtain an updated state tree, whose root it sends to the CC along a list of the included transactions -- the execution payload. Following this, the CC takes the state root and execution payload received, and together with a range of additional data, wraps them into a block. In further steps, the block is forwarded to the VC handling the validator's private key for signing, and back to the CC for broadcasting to the network via the gossip protocol of the CL, so that finally the other validators can check the block's validity and vote on its appending. A block locally built by a validator is often referred to as "vanilla".

### A.4 - Auxiliary Proposer Mechanisms
APMs refer to supplementary mechanisms that validators can leverage to optimize the process of block proposals. These mechanisms are typically designed to enhance proposer decision-making, improve network efficiency, and capture additional value while maintaining alignment with Ethereum's core principles.

APMs can include block building pipelines, block auctions, proposer commitments, or other innovations that introduce optimizations for transaction inclusion and ordering.

The following APM framework provides a structured way to understand how block proposers can extend their default functionality using external mechanisms. It consists of three key components:

- Mechanisms, which define the high-level objectives -- e.g. block auctioning in PBS
- Protocols, which specify the rules governing the interactions between agents -- e.g. MEV-Boost protocol for out-of-protocol PBS
- Sidecars, which are software implementations enabling the participation in these protocols -- e.g. MEV-Boost as client-side relay tool

The framework allows for flexibility, as a single mechanism can have multiple protocol implementations, each supported by different sidecars.

#### A.4.1 - Proposer-Builder Separation
[PBS](https://notes.ethereum.org/@vbuterin/pbs_censorship_resistance) is a mechanism designated to solve the increasing complexity of Ethereum block proposals and the risk of their centralization by sophisticated actors, exacerbating inequalities and running counter to the decentralization goals of the ecosystem. PBS addresses this by separating two validator duties -- building the execution payloads and proposing the blocks for slots allocated by the protocol. The separation of roles reduces the complexity for the proposer, who - in contrast to local block building (see [Appendix A.3](#a3---local-block-building)) - only has to select the highest bid of specialized third-party builders competing for the construction of a slot's most valuable execution payload, wrap that payload in a block, and propose it to the network. Ethereum therefore ensures that even validators with limited resources can fulfill the block proposal duty and actively contribute to the security of the network and that, like builders and relays - the facilitators of the interaction between builders and proposers - they economically profit from the optimized extraction of MEV. In Lido, this also extends to stakers, who receive the majority of the rewards accrued from staking.

##### A.4.1.1 - Out-Of-Protocol PBS
In general, out-of-protocol implementations serve as functional equivalents and/or supplements to Ethereum in-protocol APMs. In the case of PBS, which - while being discussed since The Merge - has not yet been natively introduced to the protocol due to the extensive changes it would required to the consensus, out-of-protocol implementations represent the only available options until the arrival of, e.g., [enshrined proposer-builder separation (ePBS)](https://eips.ethereum.org/EIPS/eip-7732).

##### A.4.1.2 - MEV-Boost
[MEV-Boost](https://boost.flashbots.net/) is [Flashbot's reference design proposal](https://ethresear.ch/t/mev-boost-merge-ready-flashbots-architecture/11177) for a [PBS-friendly MEV marketplace](https://ethresear.ch/t/proposer-block-builder-separation-friendly-fee-market-designs/9725), whose architecture served as basis for the specification of Ethereum's Builder API (see [Appendix A.4.1.3](#a413---builder-api)). By making the optimized extraction of MEV accessible to all operators with the distribution of a validator sidecar that automatically selects the highest bid for a slot and permissionlessly handles the orchestration of the proposer's interactions with builders and relays, the MEV-Boost implementation has become the de facto standard for out-of-protocol PBS in Ethereum.

MEV-Boost has also addressed concerns about a weakness of PBS in terms of protecting Ethereum's censorship resistance that had already been expressed for in-protocol variants. The [minimum bid value ("min-bid")](https://www.flashbots.net/#9f18e7f297d6443c895d870df3ee5d2a) is a setting that allows proposers to forego a limited amount of rewards and, instead of sourcing externally constructed execution payloads for slots where the highest bid is below the min-bid configured by the proposer, create payloads locally -- ensuring the inclusion of otherwise censored transactions.

##### A.4.1.3 - Builder API

##### A.4.1.4 - Sidecars

### A.5 - APM Infrastructure
#### A.5.1 - Out-Of-Protocol PBS Builders & Relays
##### A.5.1.1 - Troubleshooting
##### A.5.1.2 - Responsibilities & Incident Management
