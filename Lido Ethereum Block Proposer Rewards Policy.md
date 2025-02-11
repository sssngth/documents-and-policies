# Lido on Ethereum Standard Node Operator Protocol - Block Proposals & Auxiliary Proposer Mechanisms

```markdown!
STATUS: V3.0
```

## A - Purpose
This Standard Node Operator Protocol (SNOP) outlines the standards, infrastructure and Auxiliary Proposer Mechanisms (APMs) available for block proposals in Ethereum (see [Appendix A](#appendix-a---ethereum-block-proposal-standards-infrastructure--auxiliary-proposer-mechanisms)); the rules around the usage of this SNOP; the considerations that guide the expectations of Lido on Ethereum (LoE) with regard to block proposals and APMs; the responsibilities Node Operators (NOs) using LoE have in performing block proposals, providing APMs and distributing the rewards that accrue from those actions to the stakers; the solutions employed to monitor the NOs’ activities, and the actions that NOs can expected in case of non-conformance with this SNOP.

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
Outlined in [Hasu's proposal on Maximal Extractable Value (MEV)](https://research.lido.fi/t/proposal-optimal-mev-policy-for-lido/2489), the game-theoretical best approach for LoE to preserve the security of Ethereum's Proof-of-Stake (PoS) consensus against 51% attacks, stakers' low threshold access to Decentralized Finance (DeFi) opportunities, and the positive impact that the protocol can have on the network through efforts towards the decentralization and censorship resistance of the validator set, is by optimizing for LoE's economic competitiveness.

Pursuing this goal primarily calls for the maximization of the protocol's staking APR by enabling NOs using LoE to fulfill the Ethereum validator duties - attesting to and proposing new blocks, participating in sync committees, and reporting network violations of the slashing rules - as reliably, timely, correctly, and free of slashable offenses as possible.

Beyond that, LoE should continue on its path of leading the adoption of the latest native and Ethereum-aligned standards, infrastructure, and developments that could profoundly change the staking landscape and/or impact stakers economically - like historically it did with the introduction of proposer-builder separation (PBS) and, most recently, APMs.

Unlike rewards for the fulfillment of duties on the Consensus Layer (CL), compensations for services on the Execution Layer (EL) are not automatically issued to a validator's account by the Ethereum protocol, but accrue on an NO-determined address, i.a., in the form of MEV extracted or [priority fees](https://ethereum.org/en/developers/docs/gas/#priority-fee) directly sent by users to incentivize the inclusion of their transactions. This architectural design carries the risk that NOs using LoE could try to hide directly received EL rewards in an attempt to earn more than their rightful share of the protocol's smoothed rewards. In the best interest of stakers and honest NOs, LoE must thus make rewards stealing as prohibitively difficult to conceal and expensive to execute as possible. Irrespective of whether for a [bonded SM](https://operatorportal.lido.fi/modules/community-staking-module#block-88e6d7eca6364a758541dc1ee66a278f) such as CSM, with, by principle, more far-reaching options for automated enforcement of rules, or a module based on social reputation - to ensure fair conditions in all SMs, it is essential to seamlessly and transparently monitor NO behavior around block proposals and rewards distribution, as is realized for LoE via a multitude of community-built open-source [tools](https://operatorportal.lido.fi/existing-operator-portal/ethereum-onboarding/no-resources-tooling) and [dashboards](https://operatorportal.lido.fi/operator-statistics-and-metrics).

In the context of APMs, attention is to be paid to the careful consideration of potential implications the implementation of new mechanisms into the protocol may have on the block construction value chain, and that any benefits that may be gained in certain areas are measured against potential losses in others - specifically with respect to the resulting value of blocks proposed by NOs using LoE.

### C.2 - Decentralization
Decentralization is not only one of the core values of the Lido DAO, its prevalence, just like that of economic security, is an integral prerequisite for the robustness of the Ethereum network - only in a more technical-operational sense. For this reason, the hitherto outlined optimization of LoE block proposals solely for economic security must be expanded and weighed up with aspects of decentralization to provide the holistic guidance this SNOP aspires to offer.

The heeding of decentralization is most apparently reflected in LoE's continuous encouragement of NOs to run more diverse setups, for example in terms of the geographic dispersion, infrastructure and clients utilized to run nodes, and thus proactively mitigate the risks of local outages and isolated bugs, as well as to globally reduce Ethereum p2p latencies. These efforts are only possible due to LoE deliberately affording NOs a performance margin that allows even those from underrepresented regions and such with setups that are not primarily geared towards maximize rewards to equitably participate and valuably contribute to the system.

For the Lido community, the ongoing improvement of Ethereum is at the heart of all considerations. By LoE pursuing an approach to introduce the ecosystem's latest standards and mechanisms with foresight, the network has repeatedly benefited from being able to carry out critical transformations with the support of a broad set of well-coordinated and professional NOs with as little risk as possible. The prime example of this was the widespread adoption of MEV-Boost as the closest equivalent to the introduction of a native (“enshrined”) implementation of PBS, which Ethereum seeks to reduce the complexity of validation, to separate the authorities of block building and proposing, and to prevent the centralization of MEV.

An important addition is the mention of LoE's commitment to credible neutrality. This means that not only the first available or most popular implementation of an APM is supported by the protocol, but any solution that implements a mechanism's underlying standard(s) in a way that may be technically unique, but verifiably strives for the goals intended by the authors of the standard(s), is not in breach with the spirit of Ethereum or this SNOP, and whose desired application was proactively communicated.

The embodiment of the two aforementioned considerations is the launch of the SDVTM. The extensive upfront testing of infrastructure and potential NOs, as well as the careful stepwise rollout to mainnet, have significantly contributed to the growth of empirical knowledge about and the maturity of both the Obol and SSV solutions, leading to a present where everyone - but especially solo and community stakers - can benefit from the advantages of DVT in form of shared responsibilities and technical redundancy. Due to this success, many of the measures taken have and will continue to serve as blueprints for future LoE forays like the introduction of new SMs, APMs or the collaborative tuning of Ethereum parameters such as the block gas limit.

### C.3 - Censorship Resistance
A third topic of key importance for Ethereum and a factor for LoE to balance against the dimensions of economic security and decentralization, is censorship resistance. Even with the introduction of the current form of PBS and the associated dissolution of the validators' previous monopoly over the inclusion and ordering of transactions, Ethereum users can be, and are, censored at various points along the block construction and proposal pipelines. In fact, [it has been documented](https://censorship.pics/) that it is most rarely the validators that do not propose blocks that include certain transactions, but rather builders that disregard those transactions when constructing payloads, or relays that refuse to broadcast corresponding payloads to the network [which cause inclusion delays](https://eth.neutralitywatch.com/). But of course there are also some validators that are only connected to censoring relays and do not take all transactions into account when building local (“vanilla”) blocks.

As stated already in relation to the implementation of specific mechanisms in the section on decentralization, LoE is firmly committed to credible neutrality and therefore also rejects the censorship of transactions. Every user who respects the rules of the network should be able to participate in the public good that is Ethereum without restrictions. The community acknowledges that some NOs using LoE are not able to ensure unrestricted access to the network due to the regulatory requirements of the jurisdictions in which they operate, but calls on all NOs to continuously try push the boundaries towards more freedom through exemplary behavior.

On a related note, even if practically difficult to guarantee for payloads obtained from the open builder market, are the NOs using LoE hereby reminded to act fairly in local block construction and to not take any unethical measures to the detriment of users or solely to their own benefit - such as sandwich attacks.

## D - Node Operator Responsibilities
### D.1 - Must Use Some & Allowed MEV Relays Lists
Scope: All NOs of all Lido SR SMs <br>
Responsibility: Each NO must configure at least one entry of "must use some list", may configure any amount of entries of "allow list", but no relay (yet) unvetted from the Relay Maintenance Committee (RMC) <br>
References:
* Overview Node Operator Portal https://enchanted-direction-844.notion.site/6d369eb33f664487800b0dedfe32171e
* MEV Boost Relay Allowed List Mainnet Contract https://etherscan.io/address/0xf95f069f9ad107938f6ba802a3da87892298610e
* MEV Boost Relay Allowed List Holešky Contract https://holesky.etherscan.io/address/0x2d86C5855581194a386941806E38cA119E50aEA3
* RMC Research Forum Thread https://research.lido.fi/t/lido-on-ethereum-identify-and-constitute-relay-maintenance-committee/3386
* Fees Monitoring Payload Source Summary https://fees-monitoring.lido.fi/payloadSource

Potential breach, fees monitoring label & detection logic:
* No PBS/vetted relay(s) or only unvetted relay(s) configured
  * Label: Unknown Payload Source (PS)
  * Criteria 1: Value of proposed block is above allowed min-bid threshold
  * Criteria 2: Payload of proposed block is not found at "proposer payload delivered" endpoint of vetted relays' APIs
  * Criteria 3: Bid above allowed min-bid threshold was available from at least one vetted relays at t=? of slot
* No entry of "must use some list" configured
  * Label: None yet
  * Indicator: Blocks proposed from allowed list >> required list ~= 0 over certain period of time
  * Criteria 1: Value of proposed block is above allowed min-bid threshold
  * Criteria 2: Payload of proposed block is not found at "proposer payload delivered" endpoint of "must use some list" relays' APIs
  * Criteria 3: Bid above allowed min-bid threshold was available from at least one "must use some list" relay at t=? of slot
  * Criteria 4: Bid above payload value of bids of "allow list" relays was available from at least one "must use some list" relay at t=? of slot

Consequences of non-conformance & way to enforce:
* CM & SDVT - Currently no formalized consequence, once alerted, a NOM contributor reaches out to the NO to investigate the cause of the potential breach and may ask for a refund out of goodwill for a proven breach/non-refuted breach allegation
* CSM - Currently no formalized consequence, once alerted, where possible, a member of the CSM Committee reaches out to the NO to investigate the cause of the potential breach and may ask for a refund out of goodwill for a proven breach/non-refuted breach allegation; in the future it is possible that the CSM Committee will establish Easy Track rails to have the opportunity to burn part of the NO's bond as a penalty enforcement mechanism similar to the one available for rewards stealing today

Open Questions/Action Items:
* Define informative labels for every potential breach
* Define point of reference in time of slot to assess bid availability & sizes
* Formalize enforcement mechanisms and penalty structure for CM & SDVT as well as CSM

### D.2 - Min-Bid & Builder Boost Factor
Scope: All NOs of all Lido SR SMs <br>
Responsibility: Until Ethereum's implementation and LoE's adoption of EIP-7805: Fork-choice enforced Inclusion Lists (FOCIL), each NO may configure the validator client parameters "min-bid" and/or "builder boost factor" according to the socially agreed upon values to allow for a certain amount of local block building, including some otherwise censored transactions from the public mempool <br>

References:
* EIP-7805 FOCIL https://eips.ethereum.org/EIPS/eip-7805
* Lido Node Operator MEV Boost min-bid guidance https://research.lido.fi/t/lido-node-operator-mev-boost-min-bid-guidance/3347

Potential breach, fees monitoring label & detection logic:
* Exclusively used min-bid misconfigured
  * Label: Unknown Payload Source (PS)
  * Criteria 1: Value of proposed block is above allowed min-bid threshold
  * Criteria 2: Payload of proposed block is not found at "proposer payload delivered" endpoint of vetted relays' APIs
  * Criteria 3: Bid above allowed min-bid threshold was available from at least one vetted relays at t=? of slot
* Exclusively used builder boost factor misconfigured
  * Label: None yet
  * Criteria 1: Payload of proposed block is not found at "proposer payload delivered" endpoint of vetted relays' APIs
  * Criteria 2: Bid above allowed builder boost factor weighted payload value was available from at least one vetted relays at t=? of slot
* Hybrid use of min-bid & builder boost factor misconfigured
  * Label: None yet
  * Criteria 1: Value of proposed block is above allowed min-bid threshold
  * Criteria 2: Payload of proposed block is not found at "proposer payload delivered" endpoint of vetted relays' APIs
  * Criteria 3: Bid above allowed min-bid threshold was available from at least one vetted relays at t=? of slot
  * Criteria 4: Bid above allowed builder boost factor weighted payload value was available from at least one vetted relays at t=? of slot

Consequences of non-conformance & way to enforce:
* CM & SDVT - Currently no formalized consequence, once alerted, a NOM contributor reaches out to the NO to investigate the case and may ask for a refund out of goodwill for a proven breach/non-refuted allegation
* CSM - Currently no formalized consequence, once alerted, where possible, a member of the CSM Committee reaches out to the NO to investigate the case and may ask for a refund out of goodwill for a proven breach/non-refuted allegation; in the future it is possible that the CSM Committee will establish Easy Track rails to have the opportunity to burn part of the NO's bond as a penalty enforcement mechanism similar to the one available for rewards stealing today

Open Questions/Action Items:
* Agree on approach regarding either only allowing min-bid, builder boost factor, or a hybrid as well as method to determine/suggest target value(s). At, e.g., min-bid 0.07 ETH & a boost factor for external builders of 0.9 we would effectively weaken censorship resistance because payloads 0.07 ETH >= x > 0.63 ETH would be sourced externally instead of locally, like under exclusive min-bid use. Consequently, to strengthen CR we would need to increase the min-bid allowed in hybrid use to x = 10/9 * 0.07 ETH which would make monitoring more difficult. The challenging monitoring would also be the case for exclusive builder boost factor use, alongside a higher absolute revenue loss potential.
* Define informative labels for every potential breach
* Define point of reference in time of slot to assess bid availability & sizes
* Formalize enforcement mechanisms and penalty structure for CM & SDVT as well as CSM

### D.3 - Minimum Block Proposal Performance
Scope: All NOs of all Lido SR SMs <br>
Responsibility: Each NO, regardless of setup, must ensure a minimum level of block proposal performance of X% based on the performance measured for Y over time Z <br>

References:
* Lido on Ethereum Block Proposer Rewards Policy v2.0 https://lido.mypinata.cloud/ipfs/QmaLLjLE2iecf59kTQSVC1rHVPjjQwGDTCouR8A871BNRY#D4II-Monitoring-amp-Penalties-Specification
* Lido Node Operator MEV Boost min-bid guidance https://research.lido.fi/t/lido-node-operator-mev-boost-min-bid-guidance/3347
* CSM v2 Strike System regarding performance: https://hackmd.io/@lido/csm-v2-internal#Bad-performance-strikes

Potential breach, fees monitoring label & detection logic:
* Insufficient block proposal performance
  * Label: None yet
 
Open Questions/Action Items:
* Agree on approach
* Formalize enforcement mechanisms and penalty structure for CM & SDVT as well as CSM

### D.4 - APMs

### D.5 - Fee Recipient
Scope: All NOs of all Lido SR SMs <br>
Responsibility: Each NO must configure the fee recipient of the validators run for LoE to the `LidoExecutionLayerRewardsVault` of the respective Ethereum network to fairly distribute the priority fees directly received, MEV extracted, and APM compensations earned with the system. <br>
References:
* https://docs.lido.fi/deployed-contracts/
* https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297
* https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8
* https://sepolia.etherscan.io/address/0x94B1B8e2680882f8652882e7F196169dE3d9a3B2
* https://hackmd.io/cKUjOfoMSKyvxLu57KPdLg?view#Incorrect-MEV-configuration
* https://lido.mypinata.cloud/ipfs/QmZTMfmJZsYHz61f2FjhYdh5VNu6ifjYQJzYUGkysHs8Uu#Lido-on-Ethereum-Standard-Node-Operator-Protocol---Validator-Exits

Potential breach, fees monitoring label & detection logic:
* Fee recipient misconfigured / rewards stealing
  * Label: Unknown Fee Recipient (FR) / Block with unknown Fee Recipient
  * Criteria 1: `fee_recipient` of proposed block $\neq$ `LidoExecutionLayerRewardsVault`

Consequences of non-conformance & way to enforce:
* CM & SDVT - Currently no formalized consequence, once alerted, a NOM contributor reaches out to the NO to investigate the case and ask for a refund of the rewards stolen.
* CSM - Once detected, the NO is afforded a negotiation period with the CSM Committee to compensate for the stolen rewards and an additional fine of 0.1 ETH. If no compensation is made during the negotiation period, the CSM Committee initiates an Easy Track motion to propose to the Lido DAO that the portion of the NO's bond that was confiscated upon detection of the breach be burned as a penalty. Any of the NO's validators that fail to provide a full bond - i.e are unbonded - as a result of this measure are requested to exit by the Validators Exit Bus Oracle (VEBO). If subsequently the validators are not exited, they are marked stuck, and no operator rewards are distributed to the NO until the stuck validators are exited. After the implementation of EIP-7002: Execution Layer Triggerable Withdrawals into LoE, stuck validators will instead be forcefully ejected at Lido protocol level.

Open Questions/Action Items:
* Formalize enforcement mechanisms and penalty structure for CM & SDVT

### D.6 - Gas Limit
?

# Appendix
## Appendix A - Ethereum Block Proposal Standards, Infrastructure & Auxiliary Proposer Mechanisms
### A.1 - Beacon Node API
### A.2 - Local Block Building
### A.3 - Builder API
### A.4 - Auxiliary Proposer Mechanisms
#### A.4.1 - Out-Of-Protocol Proposer-Builder-Separation
##### A.4.1.1 - Dedicated PBS Implementations
##### A.4.1.2 - Consensus Clients
##### A.4.1.3 - Multi-Beacon-Node Validator Clients
##### A.4.1.4 - Distributed Validator Clients & Middleware
#### A.4.2 - Proposer Commitments
##### A.4.2.1 - Inclusion Preconfirmations
### A.5 - Block Builders, Relay & Proposer Commitment Protocol Operators
#### A.5.1 - Troubleshooting
#### A.5.2 - Responsibilities & Incident Management
